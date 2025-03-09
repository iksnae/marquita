# Panama Payment Integrations

## Overview

This document outlines the integration of Panama-specific payment methods into the Marquita platform. Panama has a well-developed banking system and growing digital payment adoption. As a dollarized economy with strong international business connections, Panama offers unique payment opportunities. Integrating local payment methods is essential for success in the Panamanian market.

## Supported Payment Methods

### Credit and Debit Cards

**Description**: Processing of Panamanian credit and debit cards from local and international banks.

**Benefits**:
- High adoption rates among Panamanian consumers
- Familiar payment experience
- Support for local and international networks
- Immediate payment confirmation
- Trusted by consumers

**Implementation**:
- Integration with local payment processors
- Support for major card networks (Visa, Mastercard, American Express)
- Support for local bank cards (Clave, etc.)

### ACH Transfers

**Description**: Direct bank transfers using Panama's ACH system (Telered).

**Benefits**:
- Lower transaction fees compared to cards
- Trusted payment method for higher-value purchases
- High banking penetration in Panama
- No chargebacks
- Widely used by local businesses

**Implementation**:
- Account information and reference number generation
- Automated reconciliation through ACH network
- Multi-bank support

### Yappy

**Description**: A popular mobile payment platform in Panama operated by Banco General.

**Benefits**:
- Rapidly growing user base in Panama
- Simple payment experience via mobile app
- Lower transaction costs than traditional cards
- Real-time payment confirmation
- Strong customer trust (backed by Panama's largest bank)

**Implementation**:
- API integration for e-commerce
- QR code generation
- Direct app-to-app integration

### Nequi

**Description**: Digital wallet and mobile banking solution popular in Panama.

**Benefits**:
- Reaching younger, tech-savvy consumers
- Growing user base in Panama
- Simple phone number-based payments
- Immediate payment confirmation
- Lower transaction fees than cards

**Implementation**:
- API integration for payment requests
- Mobile SDK for in-app integration
- QR code payment capability

## Integration Architecture

### Deno Deploy API Implementation

```typescript
// Example implementation structure for Panama payment methods
import { serve } from "https://deno.land/std/http/server.ts";
import { CardGateway } from "./gateways/card.ts";
import { AchGateway } from "./gateways/ach.ts";
import { YappyGateway } from "./gateways/yappy.ts";
import { NequiGateway } from "./gateways/nequi.ts";
import { config } from "../config.ts";

serve(async (req) => {
  const url = new URL(req.url);
  const path = url.pathname;
  
  // Credit/debit card processing
  if (path === "/api/payments/card") {
    const cardProcessor = new CardGateway(config.panama.card);
    return await cardProcessor.processPayment(req);
  }
  
  // ACH transfer processing
  if (path === "/api/payments/ach") {
    const ach = new AchGateway(config.panama.ach);
    return await ach.generateInstructions(req);
  }
  
  // Yappy payment processing
  if (path === "/api/payments/yappy") {
    const yappy = new YappyGateway(config.panama.yappy);
    return await yappy.generatePayment(req);
  }
  
  // Nequi payment processing
  if (path === "/api/payments/nequi") {
    const nequi = new NequiGateway(config.panama.nequi);
    return await nequi.processPayment(req);
  }
  
  // Payment notification webhooks
  if (path === "/api/notifications") {
    const paymentType = req.headers.get("X-Payment-Type");
    
    switch (paymentType) {
      case "card":
        return await handleCardNotification(req);
      case "ach":
        return await handleAchNotification(req);
      case "yappy":
        return await handleYappyNotification(req);
      case "nequi":
        return await handleNequiNotification(req);
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
   - Selects payment method (Card, ACH, Yappy, Nequi)

2. **Credit/Debit Card Flow**:
   - User enters card details
   - Payment is processed
   - User receives confirmation

3. **ACH Transfer Flow**:
   - System displays bank account details and reference number
   - User makes ACH transfer via their online banking
   - System receives confirmation when transfer clears
   - Order processing begins after confirmation
   - User receives confirmation email

4. **Yappy Flow**:
   - User selects Yappy
   - System generates QR code or payment link
   - User scans QR code or clicks link to open Yappy app
   - User confirms payment in Yappy app
   - System receives confirmation
   - User is redirected to confirmation page

5. **Nequi Flow**:
   - User selects Nequi
   - System generates payment request
   - User receives notification in Nequi app
   - User confirms payment
   - System receives confirmation
   - User is redirected to confirmation page

## Configuration

Payment gateways are configured through environment variables or config files:

```json
{
  "panama": {
    "card": {
      "merchantId": "CARD_MERCHANT_ID",
      "apiKey": "CARD_API_KEY",
      "environment": "production",
      "webhookUrl": "https://api.yourdomain.com/notifications"
    },
    "ach": {
      "accountNumber": "ACH_ACCOUNT_NUMBER",
      "accountName": "COMPANY_NAME",
      "bankName": "BANK_NAME",
      "bankCode": "BANK_CODE",
      "notificationEmail": "payments@yourdomain.com"
    },
    "yappy": {
      "merchantId": "YAPPY_MERCHANT_ID",
      "secretKey": "YAPPY_SECRET_KEY",
      "successUrl": "https://yourdomain.com/checkout/confirmation",
      "failureUrl": "https://yourdomain.com/checkout/failed",
      "webhookUrl": "https://api.yourdomain.com/notifications"
    },
    "nequi": {
      "apiKey": "NEQUI_API_KEY",
      "merchantId": "NEQUI_MERCHANT_ID",
      "webhookUrl": "https://api.yourdomain.com/notifications"
    }
  }
}
```

## Security Considerations

- API keys stored securely in Deno Deploy environment variables
- HTTPS enforced for all transactions
- Webhook signature validation for payment notifications
- Compliance with local data protection regulations
- Implementation of anti-fraud measures
- Regular security audits

## Testing

Test environments are available for all payment methods:

- Card processing sandbox: Available through payment processor
- ACH testing: Test accounts available through partner banks
- Yappy testing environment: Provided by Banco General
- Nequi sandbox: Available through developer portal

## References

- [Superintendency of Banks of Panama](https://www.superbancos.gob.pa)
- [ACH Panama (Telered)](https://www.telered.com.pa)
- [Yappy Developer Documentation](https://www.yappy.com.pa/developers)
- [Nequi Developer Portal](https://developer.nequi.panama.com)
