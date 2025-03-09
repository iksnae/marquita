# Integrações de Pagamento do México

## Visão Geral

Este documento descreve a integração de métodos de pagamento específicos do México na plataforma Marquita. O México possui um ecossistema de pagamento diversificado com uma mistura de opções de pagamento tradicionais e inovadoras. A integração com métodos de pagamento locais é crucial para empresas que buscam sucesso no mercado mexicano, já que muitos consumidores preferem opções locais em vez de métodos de pagamento internacionais.

## Métodos de Pagamento Suportados

### SPEI (Sistema de Pagos Electrónicos Interbancarios)

**Descrição**: Sistema de pagamento interbancário eletrônico do México, semelhante ao TED no Brasil, mas com processamento no mesmo dia.

**Benefícios**:
- Transferências bancárias instantâneas ou no mesmo dia
- Taxas de transação mais baixas em comparação com cartões de crédito (tipicamente 1-2%)
- Amplamente confiável e utilizado em todo o México
- Funciona com todos os bancos mexicanos
- Alta segurança por ser regulado pelo Banco do México

**Implementação**:
- Gerar um número CLABE (número de conta bancária) para cada transação
- Cliente inicia uma transferência bancária através do seu portal de internet banking
- Notificações em tempo real via webhooks quando o pagamento é concluído
- Reconciliação automática de pagamentos

### Oxxo Pay

**Descrição**: Sistema de pagamento em dinheiro que aproveita a maior rede de lojas de conveniência do México, Oxxo.

**Benefícios**:
- Alcança ~60% dos consumidores mexicanos sem contas bancárias
- Não é necessário ter conta bancária ou cartão de crédito
- Alta taxa de conversão para certos segmentos demográficos
- Método de pagamento confiável em todo o país
- Mais de 19.000 localizações físicas em todo o México

**Implementação**:
- Gerar uma referência/código de barras único para cada transação
- Cliente recebe a referência e paga em dinheiro em qualquer loja Oxxo
- Confirmação de pagamento geralmente dentro de 24-48 horas
- Expira após um período configurável (tipicamente 3-7 dias)

### Cartões Domésticos Mexicanos

**Descrição**: Suporte para redes de cartões locais mexicanos e especificidades do processamento de cartões de crédito/débito mexicanos.

**Benefícios**:
- Suporte para cartões exclusivamente nacionais como Carnet
- Otimização para planos "meses sin intereses" (meses sem juros)
- Menores taxas de recusa usando processamento local
- Redução de taxas de câmbio
- Melhor detecção de fraude para cartões mexicanos

**Implementação**:
- Integração com processadores de pagamento locais (ex. Conekta, OPENPAY)
- Suporte para pagamentos parcelados (3, 6, 9, 12, 18 meses)
- Tratamento adequado dos requisitos de segurança específicos do México

### Carteiras Digitais

**Descrição**: Métodos de pagamento digital populares no México, incluindo Mercado Pago, CoDi e Kueski Pay.

**Benefícios**:
- Base de usuários crescente, particularmente entre consumidores jovens e com conhecimento tecnológico
- Experiência de checkout mais rápida
- Frequentemente incluem programas de fidelidade e recompensas
- Recursos de segurança aprimorados
- Opções de crédito alternativas em alguns casos (Kueski Pay)

**Implementação**:
- Integração de API com cada provedor de carteira
- Geração de código QR para pagamentos presenciais
- Fluxos de redirecionamento para autenticação da carteira
- Processamento de webhooks para notificações de pagamento

## Arquitetura de Integração

### Implementação da API Deno Deploy

```typescript
// Estrutura de implementação de exemplo para métodos de pagamento do México
import { serve } from "https://deno.land/std/http/server.ts";
import { SPEIGateway } from "./gateways/spei.ts";
import { OxxoGateway } from "./gateways/oxxo.ts";
import { MexicanCardGateway } from "./gateways/cards.ts";
import { config } from "../config.ts";

serve(async (req) => {
  const url = new URL(req.url);
  const path = url.pathname;
  
  // Endpoint de integração SPEI
  if (path === "/api/payments/spei") {
    const spei = new SPEIGateway(config.mexico.spei);
    return await spei.generateReference(req);
  }
  
  // Endpoint de integração Oxxo
  if (path === "/api/payments/oxxo") {
    const oxxo = new OxxoGateway(config.mexico.oxxo);
    return await oxxo.generateVoucher(req);
  }
  
  // Endpoint de integração de cartões mexicanos
  if (path === "/api/payments/cards") {
    const cards = new MexicanCardGateway(config.mexico.cards);
    return await cards.processWithInstallments(req);
  }
  
  // Webhooks de notificação de pagamento
  if (path === "/api/notifications") {
    const paymentType = req.headers.get("X-Payment-Type");
    
    switch (paymentType) {
      case "spei":
        return await handleSPEINotification(req);
      case "oxxo":
        return await handleOxxoNotification(req);
      case "cards":
        return await handleCardNotification(req);
      default:
        return new Response("Tipo de pagamento desconhecido", { status: 400 });
    }
  }
  
  return new Response("Não encontrado", { status: 404 });
});
```

## Jornada do Usuário

1. **Início do Checkout**:
   - Usuário navega para a página de checkout
   - Seleciona o método de pagamento (SPEI, Oxxo, cartão, etc.)

2. **Fluxo SPEI**:
   - Sistema gera um número de conta CLABE único
   - Usuário recebe o CLABE e as informações bancárias
   - Usuário inicia a transferência a partir do portal do seu banco
   - Comerciante recebe notificação quando a transferência é concluída
   - Pedido é processado automaticamente

3. **Fluxo Oxxo**:
   - Sistema gera um voucher Oxxo com referência de pagamento
   - Usuário recebe o voucher via email/SMS
   - Usuário leva o voucher a uma loja Oxxo e paga em dinheiro
   - Comerciante recebe notificação quando o pagamento é feito
   - Processamento do pedido começa após a confirmação do pagamento

4. **Fluxo de Cartão de Crédito com Parcelamento**:
   - Usuário insere os detalhes do cartão
   - Usuário seleciona a opção de parcelamento, se aplicável (3, 6, 12 meses)
   - Sistema mostra juros/taxas para a seleção de parcelamento
   - Pagamento é processado através de banco adquirente local
   - Usuário recebe confirmação com cronograma de parcelamento

## Configuração

Gateways de pagamento são configurados através de variáveis de ambiente ou arquivos de configuração:

```json
{
  "mexico": {
    "spei": {
      "apiKey": "SPEI_API_KEY",
      "secretKey": "SPEI_SECRET_KEY",
      "webhookUrl": "https://api.seudominio.com/notifications",
      "returnUrl": "https://seudominio.com/checkout/complete",
      "expiry": 72 // Horas
    },
    "oxxo": {
      "apiKey": "OXXO_API_KEY",
      "webhookUrl": "https://api.seudominio.com/notifications",
      "expiry": 168, // Horas (7 dias)
      "description": "Pagamento para Pedido #"
    },
    "cards": {
      "apiKey": "CARDS_API_KEY",
      "privateKey": "CARDS_PRIVATE_KEY",
      "publicKey": "CARDS_PUBLIC_KEY",
      "merchantId": "MERCHANT_ID",
      "installmentOptions": [3, 6, 9, 12, 18],
      "defaultInterestRate": 0, // Para promoções sem juros
      "webhookUrl": "https://api.seudominio.com/notifications",
      "supportedNetworks": ["visa", "mastercard", "amex", "carnet"]
    }
  }
}
```

## Considerações de Segurança

- Chaves de API armazenadas com segurança em variáveis de ambiente do Deno Deploy
- HTTPS obrigatório para todas as transações
- Conformidade com a lei mexicana de proteção de dados (Ley Federal de Protección de Datos Personales)
- Assinaturas digitais para validação de referência SPEI
- Verificação de assinatura de webhook para prevenir notificações fraudulentas
- Tratamento adequado da conformidade PCI DSS para pagamentos com cartão
- Auditorias de segurança regulares

## Testes

Ambientes de teste estão disponíveis para todos os métodos de pagamento:

- Sandbox SPEI: Transferências bancárias simuladas com respostas instantâneas
- Ambiente de teste Oxxo: Vouchers virtuais que podem ser "pagos" no sandbox
- Contas de teste de cartão de crédito: Conjunto de cartões de teste para diferentes cenários (aprovação, recusa, etc.)

## Cronograma de Implementação

1. **Fase 1**: Integração SPEI (2 semanas)
2. **Fase 2**: Implementação Oxxo (1 semana)
3. **Fase 3**: Processamento de cartão de crédito com parcelamento (2 semanas)
4. **Fase 4**: Integrações de carteiras digitais (2 semanas)
5. **Fase 5**: Testes e otimização (1 semana)

## Referências

- [Documentação Oficial SPEI](https://www.banxico.org.mx/servicios/sistema-pagos-electronicos-interbancarios-spei.html)
- [Documentação da API Oxxo Pay](https://www.conekta.com/docs/api#oxxo-cash)
- [Documentação do Gateway de Pagamento Conekta](https://www.conekta.com/docs/api)
- [Recursos para Desenvolvedores OPENPAY](https://www.openpay.mx/docs/api/)
- [Diretrizes Regulatórias do Banco do México](https://www.banxico.org.mx)