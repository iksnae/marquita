# Integrações de Pagamento da Argentina

## Visão Geral

Este documento descreve a integração de métodos de pagamento específicos da Argentina na plataforma Marquita. A Argentina possui características de pagamento únicas influenciadas por seu ambiente econômico, incluindo alta inflação e restrições cambiais. Os métodos de pagamento locais são essenciais para o sucesso no mercado argentino, oferecendo melhores taxas de conversão e taxas mais baixas do que as opções internacionais.

## Métodos de Pagamento Suportados

### Mercado Pago

**Descrição**: A plataforma de pagamento dominante na Argentina (propriedade do MercadoLibre), oferecendo um conjunto abrangente de opções de pagamento.

**Benefícios**:
- Penetração de mercado extremamente alta na Argentina
- Suporte para múltiplos métodos de pagamento em uma única integração
- Implementação fácil com APIs e SDKs amigáveis para desenvolvedores
- Confiança e familiaridade do consumidor
- Proteção antifraude incorporada

**Implementação**:
- Integração de API para checkout direto
- Checkout Pro para implementação simplificada
- SDKs móveis para pagamentos baseados em aplicativos
- Pagamentos com código QR para transações presenciais

### Rapipago / Pago Fácil

**Descrição**: Redes de pagamento em dinheiro que permitem aos clientes pagar por compras online pessoalmente em milhares de locais em toda a Argentina.

**Benefícios**:
- Atende a clientes que preferem dinheiro e não possuem contas bancárias
- Ampla rede de locais de pagamento em todo o país
- Método de pagamento offline confiável
- Não necessita de informações financeiras do cliente
- Boa opção em um país com população significativa não bancarizada

**Implementação**:
- Gerar vouchers de pagamento com identificadores únicos
- Acompanhar o status do pagamento através de callbacks de API
- Definir janelas de expiração apropriadas (normalmente 3-5 dias)

### Transferências Bancárias / Pagamentos CBU

**Descrição**: Transferências bancárias diretas usando CBU (Clave Bancaria Uniforme) ou contas virtuais.

**Benefícios**:
- Custos de transação mais baixos comparados aos cartões de crédito
- Método preferido para transações de alto valor
- Confirmação imediata de fundos
- Evita taxas de câmbio de moeda estrangeira dos cartões de crédito
- Amplamente utilizado em transações B2B

**Implementação**:
- Fornecer CBU único ou alias para transferências
- Implementar sistemas de reconciliação para corresponder transferências recebidas
- Notificação automática quando transferências são recebidas

### Cartões de Crédito/Débito Argentinos

**Descrição**: Suporte para cartões locais argentinos com opções de pagamento parcelado ("cuotas").

**Benefícios**:
- Suporte essencial para pagamento parcelado com ou sem juros
- Adquirência local para melhores taxas de aprovação
- Suporte para cartões exclusivamente locais como Tarjeta Naranja, Cabal, etc.
- Conformidade com as regulamentações do Banco Central

**Implementação**:
- Integração com processadores de pagamento locais
- Suporte para planos de parcelamento (3, 6, 12, 18 parcelas)
- Exibição clara dos custos e juros de parcelamento

### Criptomoedas

**Descrição**: Suporte para pagamentos com criptomoedas, que estão se tornando cada vez mais populares na Argentina como proteção contra a inflação.

**Benefícios**:
- Contornar restrições cambiais e preocupações com inflação
- Crescente adoção na Argentina como alternativa ao peso
- Taxas de transação mais baixas para pagamentos transfronteiriços
- Atrair consumidores com conhecimento tecnológico
- Opções de stablecoins proporcionam estabilidade de preço

**Implementação**:
- Integração com processadores de pagamento de criptomoedas
- Suporte para moedas e stablecoins populares (BTC, ETH, USDT)
- Conversão de taxa de câmbio em tempo real

## Arquitetura de Integração

### Implementação da API Deno Deploy

```typescript
// Estrutura de implementação de exemplo para métodos de pagamento da Argentina
import { serve } from "https://deno.land/std/http/server.ts";
import { MercadoPagoGateway } from "./gateways/mercadopago.ts";
import { RapipagoGateway } from "./gateways/rapipago.ts";
import { BankTransferGateway } from "./gateways/bank-transfer.ts";
import { ArgentinaCardGateway } from "./gateways/argentina-cards.ts";
import { CryptoGateway } from "./gateways/crypto.ts";
import { config } from "../config.ts";

serve(async (req) => {
  const url = new URL(req.url);
  const path = url.pathname;
  
  // Endpoint de integração do Mercado Pago
  if (path === "/api/payments/mercadopago") {
    const mp = new MercadoPagoGateway(config.mercadoPago);
    return await mp.processPayment(req);
  }
  
  // Endpoint de integração Rapipago/Pago Fácil
  if (path === "/api/payments/cash-payment") {
    const cashPayment = new RapipagoGateway(config.cashPayment);
    return await cashPayment.generateVoucher(req);
  }
  
  // Processamento de transferência bancária
  if (path === "/api/payments/bank-transfer") {
    const bankTransfer = new BankTransferGateway(config.bankTransfer);
    return await bankTransfer.generateTransferInstructions(req);
  }
  
  // Processamento de cartão de crédito argentino com parcelamento
  if (path === "/api/payments/credit-card") {
    const cardProcessor = new ArgentinaCardGateway(config.creditCard);
    return await cardProcessor.processWithInstallments(req);
  }
  
  // Processamento de pagamento com criptomoedas
  if (path === "/api/payments/crypto") {
    const crypto = new CryptoGateway(config.crypto);
    return await crypto.generatePaymentAddress(req);
  }
  
  // Webhooks de notificação de pagamento
  if (path === "/api/notifications") {
    const paymentType = req.headers.get("X-Payment-Type");
    
    switch (paymentType) {
      case "mercadopago":
        return await handleMercadoPagoNotification(req);
      case "rapipago":
        return await handleRapipagoNotification(req);
      case "bank-transfer":
        return await handleBankTransferNotification(req);
      case "credit-card":
        return await handleCardNotification(req);
      case "crypto":
        return await handleCryptoNotification(req);
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
   - Seleciona o método de pagamento (Mercado Pago, Rapipago, Transferência Bancária, Cartão de Crédito, Cripto)

2. **Fluxo do Mercado Pago**:
   - Usuário seleciona Mercado Pago
   - É redirecionado para o checkout do Mercado Pago ou processa no aplicativo
   - Escolhe sub-método dentro do Mercado Pago (cartão, saldo da conta, etc.)
   - Completa o pagamento
   - É redirecionado de volta ao site do comerciante com confirmação

3. **Fluxo Rapipago/Pago Fácil**:
   - Sistema gera um voucher de pagamento com código único
   - Usuário recebe o voucher via email/download
   - Usuário paga em um local Rapipago/Pago Fácil dentro da janela de expiração
   - Comerciante recebe notificação quando o pagamento é feito
   - Processamento do pedido começa após a confirmação do pagamento

4. **Fluxo de Transferência Bancária**:
   - Sistema fornece CBU/alias e instruções de transferência
   - Usuário faz transferência através do seu internet banking
   - Sistema reconcilia a transferência recebida
   - Processamento do pedido começa após a confirmação da transferência

5. **Fluxo de Cartão de Crédito com Parcelamento**:
   - Usuário insere os detalhes do cartão
   - Seleciona o número de parcelas (1x, 3x, 6x, 12x, 18x)
   - Sistema exibe o valor da parcela com juros (se aplicável)
   - Processador de pagamento lida com autorização e configuração do parcelamento
   - Usuário recebe confirmação no site do comerciante

6. **Fluxo de Criptomoedas**:
   - Usuário seleciona opção de pagamento com criptomoedas
   - Sistema gera endereço de carteira e valor do pagamento
   - Usuário envia valor exato de cripto da sua carteira
   - Sistema confirma a transação na blockchain
   - Processamento do pedido começa após a confirmação

## Configuração

Gateways de pagamento são configurados através de variáveis de ambiente ou arquivos de configuração:

```json
{
  "argentina": {
    "mercadoPago": {
      "publicKey": "MP_PUBLIC_KEY",
      "accessToken": "MP_ACCESS_TOKEN",
      "webhookUrl": "https://api.seudominio.com/notifications",
      "redirectUrl": "https://seudominio.com/checkout/confirmation"
    },
    "cashPayment": {
      "apiKey": "CASH_PAYMENT_API_KEY",
      "providerId": "PROVIDER_ID",
      "expirationDays": 5,
      "webhookUrl": "https://api.seudominio.com/notifications"
    },
    "bankTransfer": {
      "accountHolder": "Nome da Sua Empresa",
      "cbu": "NUMERO_CBU",
      "alias": "EMPRESA.ALIAS",
      "bankName": "Nome do Banco",
      "reconciliationEmail": "pagamentos@seudominio.com"
    },
    "creditCard": {
      "apiKey": "CARD_API_KEY",
      "merchantId": "MERCHANT_ID",
      "maxInstallments": 18,
      "interestRates": {
        "3": 10.5,
        "6": 21.9,
        "12": 45.6,
        "18": 70.1
      }
    },
    "crypto": {
      "apiKey": "CRYPTO_API_KEY",
      "supportedCoins": ["BTC", "ETH", "USDT"],
      "confirmations": 2,
      "exchangeRateBuffer": 0.05
    }
  }
}
```

## Considerações de Segurança

- Chaves de API armazenadas com segurança em variáveis de ambiente do Deno Deploy
- HTTPS obrigatório para todas as transações
- Validação de assinatura de webhook para notificações de pagamento
- Conformidade com as leis argentinas de proteção de dados
- Auditorias e monitoramento de segurança regulares
- Implementação de medidas antifraude para transações com cartão
- Verificação de geolocalização baseada em IP
- Pontuação de risco para transações de alto valor

## Testes

Ambientes de teste estão disponíveis para todos os métodos de pagamento:

- Sandbox do Mercado Pago: [Portal do Desenvolvedor](https://developers.mercadopago.com)
- Ambiente de teste Rapipago/Pago Fácil: Disponível através de agregadores de pagamento
- Teste de integração bancária: Fornecido por parceiros de gateway
- Teste de cartão de crédito: Cartões de teste disponíveis através de processadores
- Teste de criptomoedas: Usar redes testnet

## Cronograma de Implementação

1. **Fase 1**: Integração do Mercado Pago (1-2 semanas)
2. **Fase 2**: Processamento de cartão de crédito com parcelamento (2 semanas)
3. **Fase 3**: Opções de pagamento em dinheiro (Rapipago/Pago Fácil) (1 semana)
4. **Fase 4**: Integração de transferência bancária (1 semana)
5. **Fase 5**: Opções de pagamento com criptomoedas (1 semana)
6. **Fase 6**: Testes e otimização (2 semanas)

## Referências

- [Documentação para Desenvolvedores do Mercado Pago](https://developers.mercadopago.com)
- [Regulamentações de Pagamento Eletrônico do Banco Central Argentino](https://www.bcra.gob.ar)
- [Guia de Integração do Rapipago](https://www.rapipago.com.ar/rapipagoWeb/for-business)
- [Estatísticas de Comércio Eletrônico Argentino](https://www.cace.org.ar)
- [Processamento de Pagamentos com Criptomoedas](https://business.crypto.com)