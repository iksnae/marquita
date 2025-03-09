# Mexico Payment Integrations

## Overview

This document outlines the integration of Mexico-specific payment methods into the Marquita platform. Mexico has a diverse payment ecosystem with a mix of traditional and innovative payment options. Integration with local payment methods is crucial for businesses looking to succeed in the Mexican market, as many consumers prefer local options over international payment methods.

## Supported Payment Methods

### SPEI (Sistema de Pagos Electrónicos Interbancarios)

**Description**: Mexico's electronic interbank payment system, similar to ACH in the US but with same-day processing.

**Benefits**:
- Instant or same-day bank transfers
- Lower transaction fees compared to credit cards (typically 1-2%)
- Widely trusted and used across Mexico
- Works with all Mexican banks
- High security as it's regulated by Banco de México

**Implementation**:
- Generate a CLABE (bank account number) for each transaction
- Customer initiates a bank transfer through their online banking portal
- Real-time notifications via webhooks when payment is completed
- Automatic reconciliation of payments

### Oxxo Pay

**Description**: Cash-based payment system leveraging Mexico's largest convenience store chain, Oxxo.

**Benefits**:
- Reaches the ~60% of Mexican consumers without bank accounts
- No need for a bank account or credit card
- High conversion rate for certain demographics
- Trusted payment method nationwide
- 19,000+ physical locations throughout Mexico

**Implementation**:
- Generate a unique payment reference/barcode for each transaction
- Customer receives the reference and pays in cash at any Oxxo store
- Payment confirmation usually within 24-48 hours
- Expires after a configurable period (typically 3-7 days)

### Mexican Domestic Cards

**Description**: Support for local Mexican card networks and specifics of Mexican credit/debit card processing.

**Benefits**:
- Support for domestic-only cards like Carnet
- Optimization for "meses sin intereses" (months without interest) installment plans
- Lower decline rates by using local processing
- Reduced foreign exchange fees
- Better fraud detection for Mexican cards

**Implementation**:
- Integration with local payment processors (e.g., Conekta, OPENPAY)
- Support for installment payments (3, 6, 9, 12, 18 months)
- Proper handling of Mexico-specific security requirements

### Digital Wallets

**Description**: Popular digital payment methods in Mexico including Mercado Pago, CoDi, and Kueski Pay.

**Benefits**:
- Growing user base, particularly among younger, tech-savvy consumers
- Faster checkout experience
- Often include loyalty and rewards programs
- Enhanced security features
- Alternative credit options in some cases (Kueski Pay)

**Implementation**:
- API integration with each wallet provider
- QR code generation for in-person payments
- Redirect flows for wallet authentication
- Webhook processing for payment notifications

## Integration Architecture

### Deno Deploy API Implementation

```typescript
// Example implementation structure for Mexico payment methods
import { serve } from "https://deno.land/std/http/server.ts";
import { SPEIGateway } from "./gateways/spei.ts";
import { OxxoGateway } from "./gateways/oxxo.ts";
import { MexicanCardGateway } from "./gateways/cards.ts";
import { config } from "../config.ts";

serve(async (req) => {
  const url = new URL(req.url);
  const path = url.pathname;
  
  // SPEI integration endpoint
  if (path === "/api/payments/spei") {
    const spei = new SPEIGateway(config.mexico.spei);
    return await spei.generateReference(req);
  }
  
  // Oxxo integration endpoint
  if (path === "/api/payments/oxxo") {
    const oxxo = new OxxoGateway(config.mexico.oxxo);
    return await oxxo.generateVoucher(req);
  }
  
  // Mexican cards integration endpoint
  if (path === "/api/payments/cards") {
    const cards = new MexicanCardGateway(config.mexico.cards);
    return await cards.processWithInstallments(req);
  }
  
  // Payment notification webhooks
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
        return new Response("Unknown payment type", { status: 400 });
    }
  }
  
  return new Response("Not found", { status: 404 });
});
```

## User Journey

1. **Checkout Initiation**:
   - User navigates to checkout page
   - Selects payment method (SPEI, Oxxo, card, etc.)

2. **SPEI Flow**:
   - System generates a unique CLABE account number
   - User receives the CLABE and bank information
   - User initiates transfer from their bank's portal
   - Merchant receives notification when transfer is completed
   - Order is processed automatically

3. **Oxxo Flow**:
   - System generates an Oxxo voucher with payment reference
   - User receives the voucher via email/SMS
   - User takes voucher to an Oxxo store and pays in cash
   - Merchant receives notification when payment is made
   - Order processing begins after payment confirmation

4. **Credit Card Flow with Installments**:
   - User enters card details
   - User selects installment option if applicable (3, 6, 12 months)
   - System shows interest/fees for installment selection
   - Payment is processed through local acquiring bank
   - User receives confirmation with installment schedule

## Configuration

Payment gateways are configured through environment variables or config files:

```json
{
  "mexico": {
    "spei": {
      "apiKey": "SPEI_API_KEY",
      "secretKey": "SPEI_SECRET_KEY",
      "webhookUrl": "https://api.yourdomain.com/notifications",
      "returnUrl": "https://yourdomain.com/checkout/complete",
      "expiry": 72 // Hours
    },
    "oxxo": {
      "apiKey": "OXXO_API_KEY",
      "webhookUrl": "https://api.yourdomain.com/notifications",
      "expiry": 168, // Hours (7 days)
      "description": "Payment for Order #"
    },
    "cards": {
      "apiKey": "CARDS_API_KEY",
      "privateKey": "CARDS_PRIVATE_KEY",
      "publicKey": "CARDS_PUBLIC_KEY",
      "merchantId": "MERCHANT_ID",
      "installmentOptions": [3, 6, 9, 12, 18],
      "defaultInterestRate": 0, // For MSI promotions
      "webhookUrl": "https://api.yourdomain.com/notifications",
      "supportedNetworks": ["visa", "mastercard", "amex", "carnet"]
    }
  }
}
```

## Security Considerations

- API keys stored securely in Deno Deploy environment variables
- HTTPS enforced for all transactions
- Compliance with Mexican data protection law (Ley Federal de Protección de Datos Personales)
- Digital signatures for SPEI reference validation
- Webhook signature verification to prevent fraudulent notifications
- Proper handling of PCI DSS compliance for card payments
- Regular security audits

## Testing

Test environments are available for all payment methods:

- SPEI sandbox: Simulated bank transfers with instant responses
- Oxxo test environment: Virtual vouchers that can be "paid" in the sandbox
- Credit card test accounts: Set of test cards for different scenarios (approval, decline, etc.)

## Implementation Timeline

1. **Phase 1**: SPEI integration (2 weeks)
2. **Phase 2**: Oxxo implementation (1 week)
3. **Phase 3**: Credit card processing with installments (2 weeks)
4. **Phase 4**: Digital wallet integrations (2 weeks)
5. **Phase 5**: Testing and optimization (1 week)

## References

- [SPEI Official Documentation](https://www.banxico.org.mx/servicios/sistema-pagos-electronicos-interbancarios-spei.html)
- [Oxxo Pay API Documentation](https://www.conekta.com/docs/api#oxxo-cash)
- [Conekta Payment Gateway Documentation](https://www.conekta.com/docs/api)
- [OPENPAY Developer Resources](https://www.openpay.mx/docs/api/)
- [Banco de México Regulatory Guidelines](https://www.banxico.org.mx)