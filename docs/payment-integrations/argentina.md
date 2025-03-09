# Argentina Payment Integrations

## Overview

This document outlines the integration of Argentina-specific payment methods into the Marquita platform. Argentina has unique payment characteristics influenced by its economic environment, including high inflation and currency restrictions. Local payment methods are essential for success in the Argentine market, offering better conversion rates and lower fees than international options.

## Supported Payment Methods

### Mercado Pago

**Description**: The dominant payment platform in Argentina (owned by MercadoLibre), offering a comprehensive suite of payment options.

**Benefits**:
- Extremely high market penetration in Argentina
- Support for multiple payment methods under one integration
- Easy implementation with developer-friendly APIs and SDKs
- Consumer trust and familiarity
- Built-in anti-fraud protection

**Implementation**:
- API integration for direct checkout
- Checkout Pro for simplified implementation
- Mobile SDKs for app-based payments
- QR code payments for in-person transactions

### Rapipago / Pago Fácil

**Description**: Cash-based payment networks that allow customers to pay for online purchases in person at thousands of locations across Argentina.

**Benefits**:
- Caters to cash-preferring customers without bank accounts
- Wide network of payment locations throughout the country
- Trusted offline payment method
- No need for customer financial information
- Good option in a country with significant unbanked population

**Implementation**:
- Generate payment vouchers with unique identifiers
- Track payment status through API callbacks
- Set appropriate expiration windows (typically 3-5 days)

### Bank Transfers / CBU Payments

**Description**: Direct bank transfers using CBU (Clave Bancaria Uniforme) or virtual accounts.

**Benefits**:
- Lower transaction costs compared to credit cards
- Preferred method for high-value transactions
- Immediate confirmation of funds
- Avoids credit card foreign exchange fees
- Widely used in B2B transactions

**Implementation**:
- Provide unique CBU or alias for transfers
- Implement reconciliation systems to match incoming transfers
- Automated notification when transfers are received

### Argentine Credit/Debit Cards

**Description**: Support for local Argentine cards with installment payment options ("cuotas").

**Benefits**:
- Essential installment payment support ("cuotas") with or without interest
- Local acquiring for better approval rates
- Support for local-only cards like Tarjeta Naranja, Cabal, etc.
- Compliance with Central Bank regulations

**Implementation**:
- Integration with local payment processors
- Support for installment plans (3, 6, 12, 18 cuotas)
- Clear display of installment costs and interest

### Cryptocurrency

**Description**: Support for cryptocurrency payments, which are increasingly popular in Argentina as a hedge against inflation.

**Benefits**:
- Bypass currency restrictions and inflation concerns
- Growing adoption in Argentina as an alternative to peso
- Lower transaction fees for cross-border payments
- Attract tech-savvy consumers
- Stablecoin options provide price stability

**Implementation**:
- Integration with crypto payment processors
- Support for popular coins and stablecoins (BTC, ETH, USDT)
- Real-time exchange rate conversion

## Integration Architecture

### Deno Deploy API Implementation

```typescript
// Example implementation structure for Argentina payment methods
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
  
  // Mercado Pago integration endpoint
  if (path === "/api/payments/mercadopago") {
    const mp = new MercadoPagoGateway(config.mercadoPago);
    return await mp.processPayment(req);
  }
  
  // Rapipago/Pago Fácil integration endpoint
  if (path === "/api/payments/cash-payment") {
    const cashPayment = new RapipagoGateway(config.cashPayment);
    return await cashPayment.generateVoucher(req);
  }
  
  // Bank transfer processing
  if (path === "/api/payments/bank-transfer") {
    const bankTransfer = new BankTransferGateway(config.bankTransfer);
    return await bankTransfer.generateTransferInstructions(req);
  }
  
  // Argentine credit card processing with installments
  if (path === "/api/payments/credit-card") {
    const cardProcessor = new ArgentinaCardGateway(config.creditCard);
    return await cardProcessor.processWithInstallments(req);
  }
  
  // Cryptocurrency payment processing
  if (path === "/api/payments/crypto") {
    const crypto = new CryptoGateway(config.crypto);
    return await crypto.generatePaymentAddress(req);
  }
  
  // Payment notification webhooks
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
        return new Response("Unknown payment type", { status: 400 });
    }
  }
  
  return new Response("Not found", { status: 404 });
});
```

## User Journey

1. **Checkout Initiation**:
   - User navigates to checkout page
   - Selects payment method (Mercado Pago, Rapipago, Bank Transfer, Credit Card, Crypto)

2. **Mercado Pago Flow**:
   - User selects Mercado Pago
   - Redirected to Mercado Pago checkout or processes in-app
   - Chooses sub-method within Mercado Pago (card, account balance, etc.)
   - Completes payment
   - Redirected back to merchant site with confirmation

3. **Rapipago/Pago Fácil Flow**:
   - System generates payment voucher with unique code
   - User receives voucher via email/download
   - User pays at Rapipago/Pago Fácil location within expiration window
   - Merchant receives notification when payment is made
   - Order processing begins after payment confirmation

4. **Bank Transfer Flow**:
   - System provides CBU/alias and transfer instructions
   - User makes transfer via their online banking
   - System reconciles incoming transfer
   - Order processing begins after transfer confirmation

5. **Credit Card Flow with Installments**:
   - User enters card details
   - Selects number of installments (1x, 3x, 6x, 12x, 18x)
   - System displays installment amount with interest (if applicable)
   - Payment processor handles authorization and installment setup
   - User receives confirmation on merchant site

6. **Cryptocurrency Flow**:
   - User selects cryptocurrency payment option
   - System generates wallet address and payment amount
   - User sends exact crypto amount from their wallet
   - System confirms transaction on blockchain
   - Order processing begins after confirmation

## Configuration

Payment gateways are configured through environment variables or config files:

```json
{
  "argentina": {
    "mercadoPago": {
      "publicKey": "MP_PUBLIC_KEY",
      "accessToken": "MP_ACCESS_TOKEN",
      "webhookUrl": "https://api.yourdomain.com/notifications",
      "redirectUrl": "https://yourdomain.com/checkout/confirmation"
    },
    "cashPayment": {
      "apiKey": "CASH_PAYMENT_API_KEY",
      "providerId": "PROVIDER_ID",
      "expirationDays": 5,
      "webhookUrl": "https://api.yourdomain.com/notifications"
    },
    "bankTransfer": {
      "accountHolder": "Your Company Name",
      "cbu": "CBU_NUMBER",
      "alias": "COMPANY.ALIAS",
      "bankName": "Bank Name",
      "reconciliationEmail": "payments@yourdomain.com"
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

## Security Considerations

- API keys stored securely in Deno Deploy environment variables
- HTTPS enforced for all transactions
- Webhook signature validation for payment notifications
- Compliance with Argentine data protection laws
- Regular security audits and monitoring
- Implementation of anti-fraud measures for card transactions
- IP-based geolocation verification
- Risk scoring for high-value transactions

## Testing

Test environments are available for all payment methods:

- Mercado Pago sandbox: [Developer Portal](https://developers.mercadopago.com)
- Rapipago/Pago Fácil test environment: Available through payment aggregators
- Banking integration testing: Provided by gateway partners
- Credit card testing: Test cards available through processors
- Cryptocurrency testing: Use testnet networks

## Implementation Timeline

1. **Phase 1**: Mercado Pago integration (1-2 weeks)
2. **Phase 2**: Credit card processing with installments (2 weeks)
3. **Phase 3**: Cash payment options (Rapipago/Pago Fácil) (1 week)
4. **Phase 4**: Bank transfer integration (1 week)
5. **Phase 5**: Cryptocurrency payment options (1 week)
6. **Phase 6**: Testing and optimization (2 weeks)

## References

- [Mercado Pago Developer Documentation](https://developers.mercadopago.com)
- [Argentine Central Bank E-Payment Regulations](https://www.bcra.gob.ar)
- [Rapipago Integration Guide](https://www.rapipago.com.ar/rapipagoWeb/for-business)
- [Argentine E-Commerce Statistics](https://www.cace.org.ar)
- [Cryptocurrency Payment Processing](https://business.crypto.com)
