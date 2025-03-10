# Chile Payment Integrations

## Overview

This document outlines the integration of Chile-specific payment methods into the Marquita platform. Chile has one of Latin America's most developed digital payment ecosystems, with high banking penetration and significant adoption of electronic payment methods. Integrating local payment solutions is crucial for success in the Chilean market, providing better conversion rates and customer experiences.

## Supported Payment Methods

### Webpay Plus

**Description**: The dominant online payment gateway in Chile operated by Transbank, supporting credit and debit cards with additional security features.

**Benefits**:
- Extremely high market recognition and consumer trust
- Support for all major Chilean credit and debit cards
- Integration with Chile's major banks
- Multi-currency support
- Enhanced security with 3D Secure 2.0
- One-click payments option for returning customers

**Implementation**:
- REST API integration
- Hosted payment page option
- Direct API integration for customized checkout
- Robust transaction management and reporting

### Khipu

**Description**: A popular Chilean payment method allowing users to pay directly from their bank accounts through online banking.

**Benefits**:
- No need for credit cards
- Lower transaction fees compared to card payments
- High security (users authenticate directly with their bank)
- Real-time payment confirmation
- Widely used for services, utilities, and education payments
- Trusted brand in the Chilean market

**Implementation**:
- API integration
- Mobile-friendly payment flow
- QR code payment option
- Instant payment notification via webhooks

### Multicaja

**Description**: A payment processing service providing multiple payment options including bank transfers, prepaid cards, and cash options.

**Benefits**:
- Reaches banked and unbanked populations
- Cash payment option through extensive physical network
- Prepaid virtual cards for online payments
- Simplified integration for multiple payment types
- Support for recurring billing

**Implementation**:
- Single API integration for multiple payment methods
- Payment status tracking through API calls
- Automated reconciliation services

### MACH

**Description**: A popular Chilean digital wallet and prepaid card that doesn't require a traditional bank account.

**Benefits**:
- Reaches younger, tech-savvy consumers
- Growing user base (over 3 million users in Chile)
- Lower transaction costs
- Easy integration for mobile checkout
- No traditional bank account required for users
- Enables immediate payments

**Implementation**:
- API integration for direct payments
- QR code payment capability
- Mobile SDK for in-app integrations

### Chilean Credit/Debit Cards

**Description**: Direct processing of Chilean cards with support for installment payments ("cuotas sin interés").

**Benefits**:
- Essential installment payment options (3, 6, 12 cuotas)
- Local acquiring for better approval rates
- Support for local card networks
- Compliance with Chilean financial regulations
- Better exchange rates with local processing

**Implementation**:
- Integration with local payment processors
- Support for installment plans
- Clear display of installment options

## Integration Architecture

### Deno Deploy API Implementation

```typescript
// Example implementation structure for Chile payment methods
import { serve } from "https://deno.land/std/http/server.ts";
import { WebpayGateway } from "./gateways/webpay.ts";
import { KhipuGateway } from "./gateways/khipu.ts";
import { MulticajaGateway } from "./gateways/multicaja.ts";
import { MachGateway } from "./gateways/mach.ts";
import { ChileCardGateway } from "./gateways/chile-cards.ts";
import { config } from "../config.ts";

serve(async (req) => {
  const url = new URL(req.url);
  const path = url.pathname;
  
  // Webpay integration endpoint
  if (path === "/api/payments/webpay") {
    const webpay = new WebpayGateway(config.webpay);
    return await webpay.createTransaction(req);
  }
  
  // Khipu integration endpoint
  if (path === "/api/payments/khipu") {
    const khipu = new KhipuGateway(config.khipu);
    return await khipu.createPayment(req);
  }
  
  // Multicaja integration endpoint
  if (path === "/api/payments/multicaja") {
    const multicaja = new MulticajaGateway(config.multicaja);
    return await multicaja.processPayment(req);
  }
  
  // MACH integration endpoint
  if (path === "/api/payments/mach") {
    const mach = new MachGateway(config.mach);
    return await mach.generateQrPayment(req);
  }
  
  // Chilean credit card processing with installments
  if (path === "/api/payments/credit-card") {
    const cardProcessor = new ChileCardGateway(config.creditCard);
    return await cardProcessor.processWithInstallments(req);
  }
  
  // Payment notification webhooks
  if (path === "/api/notifications") {
    const paymentType = req.headers.get("X-Payment-Type");
    
    switch (paymentType) {
      case "webpay":
        return await handleWebpayNotification(req);
      case "khipu":
        return await handleKhipuNotification(req);
      case "multicaja":
        return await handleMulticajaNotification(req);
      case "mach":
        return await handleMachNotification(req);
      case "credit-card":
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
   - Selects payment method (Webpay, Khipu, Multicaja, MACH, Credit Card)

2. **Webpay Flow**:
   - User selects Webpay
   - Either redirected to Webpay hosted page or stays on site (direct integration)
   - Enters card details and completes 3D Secure verification if required
   - Transaction is processed
   - User is redirected back to merchant site with confirmation

3. **Khipu Flow**:
   - User selects Khipu
   - System creates Khipu payment request
   - User is redirected to Khipu or shown QR code
   - User selects their bank and authenticates
   - Transaction is processed through user's bank
   - User is redirected back to merchant site with confirmation

4. **Multicaja Flow**:
   - User selects Multicaja
   - Chooses payment sub-method (bank transfer, prepaid, cash)
   - For cash: voucher is generated for payment at physical locations
   - For bank/prepaid: user completes payment online
   - User is redirected back to merchant site with confirmation

5. **MACH Flow**:
   - User selects MACH
   - System generates QR code or payment link
   - User scans QR or follows link to complete payment in MACH app
   - Transaction is processed
   - User returns to merchant site with confirmation

6. **Credit Card Flow with Installments**:
   - User enters card details
   - Selects number of installments (1x, 3x, 6x, 12x)
   - System displays installment plan
   - Payment processor handles authorization and installment setup
   - User receives confirmation on merchant site

## Configuration

Payment gateways are configured through environment variables or config files:

```json
{
  "chile": {
    "webpay": {
      "commerceCode": "WEBPAY_COMMERCE_CODE",
      "apiKey": "WEBPAY_API_KEY",
      "environment": "production",
      "webhookUrl": "https://api.yourdomain.com/notifications",
      "returnUrl": "https://yourdomain.com/checkout/confirmation"
    },
    "khipu": {
      "receiverId": "KHIPU_RECEIVER_ID",
      "secretKey": "KHIPU_SECRET_KEY",
      "webhookUrl": "https://api.yourdomain.com/notifications",
      "returnUrl": "https://yourdomain.com/checkout/confirmation"
    },
    "multicaja": {
      "apiKey": "MULTICAJA_API_KEY",
      "merchantId": "MULTICAJA_MERCHANT_ID",
      "webhookUrl": "https://api.yourdomain.com/notifications",
      "returnUrl": "https://yourdomain.com/checkout/confirmation"
    },
    "mach": {
      "apiKey": "MACH_API_KEY",
      "merchantId": "MACH_MERCHANT_ID",
      "webhookUrl": "https://api.yourdomain.com/notifications"
    },
    "creditCard": {
      "acquirerId": "ACQUIRER_ID",
      "merchantId": "MERCHANT_ID",
      "terminalId": "TERMINAL_ID",
      "maxInstallments": 12,
      "interestFree": true
    }
  }
}
```
