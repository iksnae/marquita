# Integrações de Pagamento do Brasil

## Visão Geral

Este documento descreve a integração de métodos de pagamento específicos do Brasil na plataforma Marquita. O Brasil possui requisitos e preferências de pagamento únicos que são essenciais para o sucesso no mercado de e-commerce brasileiro. Os métodos de pagamento locais têm altas taxas de adoção e frequentemente oferecem taxas mais baixas do que as opções internacionais.

## Métodos de Pagamento Suportados

### Pix

**Descrição**: Um sistema de pagamento instantâneo criado e gerenciado pelo Banco Central do Brasil, permitindo transferências em tempo real 24/7.

**Benefícios**:
- Transferências instantâneas (24/7, incluindo fins de semana e feriados)
- Taxas baixas ou zero para consumidores e empresas
- Alta segurança com criptografia de ponta a ponta
- Ampla adoção (mais de 100 milhões de usuários no Brasil)
- Funciona via códigos QR ou chaves de busca (e-mail, número de telefone, CPF)

**Implementação**:
- Geração de códigos QR e chaves de pagamento para clientes
- Recebimento de notificações em tempo real via webhooks quando o pagamento é concluído
- Sem detalhes de cartão ou dados sensíveis para armazenar

### Boleto Bancário

**Descrição**: Um método de pagamento tradicional brasileiro que gera um comprovante (boleto) que pode ser pago em bancos, caixas eletrônicos, lojas de conveniência ou via internet banking.

**Benefícios**:
- Não é necessário ter conta bancária ou cartão de crédito para fazer compras
- Método de pagamento amplamente aceito e confiável no Brasil
- Taxas de fraude mais baixas do que cartões de crédito
- Preferido por clientes sem contas bancárias ou cartões de crédito
- Opção em dinheiro para clientes que preferem não usar pagamentos digitais

**Implementação**:
- Geração de boletos com informações de pagamento
- Acompanhamento do status do pagamento usando o ID do boleto
- Tratamento de datas de vencimento (normalmente 1-3 dias)

### Cartões de Crédito Brasileiros

**Descrição**: Suporte para cartões de crédito locais brasileiros, incluindo pagamentos parcelados ("parcelamento").

**Benefícios**:
- Suporte para redes de cartão exclusivamente locais como Elo e Hipercard
- Suporte para pagamento parcelado (crucial para o mercado brasileiro)
- Integração com sistemas antifraude adaptados ao mercado brasileiro

**Implementação**:
- Suporte para opções de parcelamento (pagamentos de 2-12x)
- Exibição clara de planos de parcelamento e juros
- Bancos adquirentes locais para melhores taxas de aprovação

### Carteiras Digitais / Cripto

**Descrição**: Suporte para PicPay, Mercado Pago e opções locais de pagamento com criptomoedas.

**Benefícios**:
- Popular entre consumidores mais jovens e familiarizados com tecnologia
- Frequentemente têm ofertas promocionais para incentivar o uso
- Taxas mais baixas do que o processamento tradicional de cartão de crédito
- Crescente adoção de criptomoedas no Brasil

**Implementação**:
- Integração de API com provedores populares de carteira
- Geração de código QR para pagamentos no aplicativo
- Tratamento de callbacks de confirmação de pagamento

## Arquitetura de Integração

### Implementação da API Deno Deploy

```typescript
// Estrutura de implementação de exemplo para métodos de pagamento do Brasil
import { serve } from "https://deno.land/std/http/server.ts";
import { PixGateway } from "./gateways/pix.ts";
import { BoletoGateway } from "./gateways/boleto.ts";
import { BrazilCardGateway } from "./gateways/brazil-cards.ts";
import { config } from "../config.ts";

serve(async (req) => {
  const url = new URL(req.url);
  const path = url.pathname;
  
  // Endpoint de integração PIX
  if (path === "/api/payments/pix") {
    const pix = new PixGateway(config.pix);
    return await pix.generatePayment(req);
  }
  
  // Endpoint de integração de Boleto
  if (path === "/api/payments/boleto") {
    const boleto = new BoletoGateway(config.boleto);
    return await boleto.generateVoucher(req);
  }
  
  // Processamento de cartão de crédito brasileiro com parcelamento
  if (path === "/api/payments/credit-card") {
    const cardProcessor = new BrazilCardGateway(config.creditCard);
    return await cardProcessor.processWithInstallments(req);
  }
  
  // Webhooks de notificação de pagamento
  if (path === "/api/notifications") {
    const paymentType = req.headers.get("X-Payment-Type");
    
    switch (paymentType) {
      case "pix":
        return await handlePixNotification(req);
      case "boleto":
        return await handleBoletoNotification(req);
      case "credit-card":
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
   - Seleciona o método de pagamento (Pix, Boleto, Cartão de Crédito, etc.)

2. **Fluxo do Pix**:
   - Sistema gera código QR do Pix e chave para copiar/colar
   - Usuário escaneia o código QR com o aplicativo bancário ou copia a chave Pix
   - A confirmação do pagamento acontece em tempo real
   - Usuário recebe confirmação no site do comerciante

3. **Fluxo do Boleto**:
   - Sistema gera o boleto com instruções de pagamento
   - Usuário recebe o Boleto via e-mail/download
   - Usuário paga o Boleto no banco/casa lotérica/app bancário
   - A confirmação do pagamento ocorre dentro de 1-2 dias úteis
   - O processamento do pedido começa após a confirmação do pagamento

4. **Fluxo de Cartão de Crédito com Parcelamento**:
   - Usuário insere os detalhes do cartão
   - Seleciona o número de parcelas (1x a 12x)
   - Sistema exibe o valor da parcela com juros (se aplicável)
   - Processador de pagamento lida com autorização e configuração de parcelamento
   - Usuário recebe confirmação no site do comerciante

## Configuração

Gateways de pagamento são configurados através de variáveis de ambiente ou arquivos de configuração:

```json
{
  "brazil": {
    "pix": {
      "apiKey": "PIX_API_KEY",
      "merchantId": "PIX_MERCHANT_ID",
      "certPath": "certificates/pix_cert.pem",
      "webhookUrl": "https://api.seudominio.com/notifications",
      "merchantName": "Nome da Sua Loja",
      "merchantCity": "São Paulo"
    },
    "boleto": {
      "apiKey": "BOLETO_API_KEY",
      "token": "BOLETO_TOKEN",
      "expirationDays": 3,
      "webhookUrl": "https://api.seudominio.com/notifications",
      "instructions": "Pague em qualquer banco ou casa lotérica"
    },
    "creditCard": {
      "apiKey": "CARD_API_KEY",
      "merchantId": "MERCHANT_ID",
      "maxInstallments": 12,
      "interestRate": 1.99,
      "interestStartsAt": 2, // Juros começam em 2 parcelas
      "antifraudEnabled": true
    }
  }
}
```

## Considerações de Segurança

- Chaves de API armazenadas com segurança em variáveis de ambiente do Deno Deploy
- HTTPS obrigatório para todas as transações
- Assinaturas digitais para transações Pix
- Validação de webhook para notificações de pagamento
- Conformidade com a LGPD (Lei Geral de Proteção de Dados)

## Testes

Ambientes de teste estão disponíveis para todos os métodos de pagamento:

- Ambiente sandbox do Pix: [Portal do Desenvolvedor](https://developers.bcb.gov.br/sandbox)
- Geração de teste de Boleto: [Teste de Boleto](https://www.boletobancario.com/boleto/sandbox)
- Ambiente de teste de cartão de crédito: Disponível através de provedores de gateway de pagamento como Cielo, PagSeguro, etc.

## Cronograma de Implementação

1. **Fase 1**: Integração do Pix (1-2 semanas)
2. **Fase 2**: Processamento de cartão de crédito com parcelamento (2 semanas)
3. **Fase 3**: Integração de Boleto (1 semana)
4. **Fase 4**: Testes e otimização (1 semana)

## Referências

- [Documentação da API do Pix (Banco Central do Brasil)](https://www.bcb.gov.br/estabilidadefinanceira/pix)
- [Guia de Integração de Boleto](https://www.boletobancario.com/documentacao)
- [Processamento de Cartão de Crédito Brasileiro](https://developers.cielo.com.br/api-portal/pt-br/documentacao/cartao-de-credito)
- [Guia de Conformidade com a LGPD](https://www.gov.br/cidadania/pt-br/acesso-a-informacao/lgpd)