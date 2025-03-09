# Peru Payment Integrations

## Overview

This document outlines the integration of Peru-specific payment methods into the Marquita platform. Peru has a diverse payment landscape with a mix of traditional banking methods, cash-based options, and emerging digital solutions. Offering local payment methods is essential for businesses targeting the Peruvian market, as many consumers either prefer local options or don't have access to international payment methods.

## Supported Payment Methods

### PagoEfectivo

**Description**: A payment solution that allows customers to pay for online purchases in cash at over 140,000 physical locations throughout Peru, including banks, convenience stores, and pharmacies.

**Benefits**:
- Reaches the large unbanked population in Peru (approximately 70%)
- No bank account or credit card required
- High conversion rates for specific demographics
- Trusted local payment method
- Widespread acceptance and recognition
- Available 24/7 through multiple channels (banks, agents, app)

**Implementation**:
- Generate a unique payment code (CIP) for each transaction
- Customer receives the code and pays at any affiliated location
- Real-time notification when payment is completed
- Payment confirmation within 24 hours
- Automatic expiration of unpaid codes (configurable, typically 1-7 days)

### Yape

**Description**: Peru's leading mobile payment app that allows instant transfers between users through QR codes or phone numbers. Operated by BCP (Banco de Crédito del Perú), Peru's largest bank.

**Benefits**:
- Instant transfers with immediate confirmation
- Rapidly growing user base in Peru
- No transaction fees for basic usage
- Simple QR code-based payment flow
- Strong integration with Peru's largest bank
- Mobile-first payment experience

**Implementation**:
- Generate a unique QR code for payment
- Provide business account details (phone number)
- Customer scans QR or sends payment to phone number
- Real-time webhook notifications when payment is completed
- Simple integration with e-commerce flow

### Local Credit/Debit Cards

**Description**: Processing for Peruvian bank-issued cards, including support for local networks and specific requirements.

**Benefits**:
- Familiar payment method for Peruvian consumers
- Support for local card networks 
- Installment payment options (cuotas)
- Higher approval rates through local processing
- Lower decline rates than international processors
- Reduced exchange rate fees

**Implementation**:
- Integration with Peruvian payment processors (e.g., Niubiz, Izipay)
- Support for installment payments (3, 6, 12 months)
- Local currency (Peruvian Sol) processing
- Compliance with local banking regulations
- Enhanced fraud prevention for local cards

### Bank Transfers (CCI)

**Description**: Direct bank transfers using CCI (Código de Cuenta Interbancario), Peru's interbank account number system for electronic transfers between banks.

**Benefits**:
- Trusted and secure method for larger transactions
- Lower fees than credit card processing
- Same-day processing for most transfers
- Preferred method for businesses and repeat customers
- No need for card information or cash handling

**Implementation**:
- Provide CCI account number for transfers
- Generate unique reference codes for each transaction
- Monitor incoming transfers and match with orders
- Implement automated or manual verification
- Support for reconciliation and accounting processes

### SafetyPay

**Description**: An alternative payment platform that allows customers to make online purchases using their online banking credentials without sharing financial information with merchants.

**Benefits**:
- Bank-level security without sharing financial details
- Available in most major Peruvian banks
- Supports both online banking and cash payments
- Real-time authorization and confirmation
- Lower fraud rates than card payments
- Available for international transactions

**Implementation**:
- API integration with the SafetyPay platform
- Redirect customers to their bank's login page
- Automatic notification when payment is completed
- Support for both online and cash payment options
- Currency conversion handling for international merchants

## Integration Architecture

### Deno Deploy API Implementation

```typescript
// Example implementation structure for Peru payment methods
import { serve } from "https://deno.land/std/http/server.ts";
import { PagoEfectivoGateway } from "./gateways/pago-efectivo.ts";
import { YapeGateway } from "./gateways/yape.ts";
import { PeruCardGateway } from "./gateways/peru-cards.ts";
import { CCITransferGateway } from "./gateways/cci-transfer.ts";
import { SafetyPayGateway } from "./gateways/safety-pay.ts";
import { config } from "../config.ts";

serve(async (req) => {
  const url = new URL(req.url);
  const path = url.pathname;
  
  // PagoEfectivo integration endpoint
  if (path === "/api/payments/pago-efectivo") {
    const pagoEfectivo = new PagoEfectivoGateway(config.peru.pagoEfectivo);
    return await pagoEfectivo.generateCIP(req);
  }
  
  // Yape integration endpoint
  if (path === "/api/payments/yape") {
    const yape = new YapeGateway(config.peru.yape);
    return await yape.generateQRCode(req);
  }
  
  // Local card processing endpoint
  if (path === "/api/payments/card") {
    const cardProcessor = new PeruCardGateway(config.peru.cards);
    return await cardProcessor.processWithInstallments(req);
  }
  
  // CCI transfer endpoint
  if (path === "/api/payments/cci-transfer") {
    const cciTransfer = new CCITransferGateway(config.peru.cciTransfer);
    return await cciTransfer.generateReference(req);
  }
  
  // SafetyPay endpoint
  if (path === "/api/payments/safety-pay") {
    const safetyPay = new SafetyPayGateway(config.peru.safetyPay);
    return await safetyPay.initiatePayment(req);
  }
  
  // Payment notification endpoint
  if (path === "/api/notifications") {
    const paymentType = req.headers.get("X-Payment-Type");
    
    switch (paymentType) {
      case "pago-efectivo":
        return await handlePagoEfectivoNotification(req);
      case "yape":
        return await handleYapeNotification(req);
      case "card":
        return await handleCardNotification(req);
      case "cci-transfer":
        return await handleCCITransferNotification(req);
      case "safety-pay":
        return await handleSafetyPayNotification(req);
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
   - Selects payment method (PagoEfectivo, Yape, card, CCI transfer, SafetyPay)

2. **PagoEfectivo Flow**:
   - System generates a unique CIP code
   - User receives code via email/SMS
   - User pays at any affiliated location or through online banking
   - Real-time notification when payment is completed
   - Order processing begins after payment confirmation

3. **Yape Flow**:
   - System generates a QR code or displays Yape number
   - User scans QR code with Yape app or sends payment to number
   - Immediate confirmation of payment
   - Order is processed after payment verification

4. **Card Payment Flow with Installments**:
   - User enters card details
   - User selects number of installments (1, 3, 6, 12 months)
   - System displays total payment amount with any applicable interest
   - Payment is processed through local gateway
   - Order processing begins immediately after approval

5. **CCI Transfer Flow**:
   - System provides CCI account number and reference code
   - User initiates bank transfer through online banking
   - User includes reference code in transfer details
   - Manual or automated verification of transfer
   - Order processing begins after verification

6. **SafetyPay Flow**:
   - User redirected to SafetyPay platform
   - User selects their bank
   - User logs in to online banking and approves payment
   - Immediate confirmation sent to merchant
   - Order processing begins

## Configuration

Payment gateways are configured through environment variables or config files:

```json
{
  "peru": {
    "pagoEfectivo": {
      "apiKey": "YOUR_API_KEY",
      "secretKey": "YOUR_SECRET_KEY",
      "serviceUrl": "https://api.pagoefectivo.pe",
      "expirationDays": 3,
      "currencyCode": "PEN",
      "notificationUrl": "https://api.yourdomain.com/notifications",
      "userEmail": "payments@yourbusiness.com"
    },
    "yape": {
      "merchantPhone": "+51999999999",
      "merchantName": "Your Business Name",
      "notificationUrl": "https://api.yourdomain.com/notifications",
      "webhookSecret": "YOUR_WEBHOOK_SECRET"
    },
    "cards": {
      "merchantId": "YOUR_MERCHANT_ID",
      "apiKey": "YOUR_API_KEY",
      "secretKey": "YOUR_SECRET_KEY",
      "processorUrl": "https://api.niubiz.com.pe/v2",
      "currency": "PEN",
      "installmentOptions": [1, 3, 6, 12],
      "notificationUrl": "https://api.yourdomain.com/notifications"
    },
    "cciTransfer": {
      "accountNumber": "XXXXXXXXXXXXXXXXXXX",
      "accountName": "Your Business Name",
      "bankName": "BCP",
      "referencePrefix": "ORD-",
      "notificationEmail": "payments@yourbusiness.com"
    },
    "safetyPay": {
      "apiKey": "YOUR_API_KEY",
      "secretKey": "YOUR_SECRET_KEY",
      "merchantId": "YOUR_MERCHANT_ID",
      "currency": "PEN",
      "expirationHours": 24,
      "notificationUrl": "https://api.yourdomain.com/notifications",
      "redirectUrl": "https://yourdomain.com/payment/complete"
    }
  }
}
```

## Security Considerations

- API keys stored securely in Deno Deploy environment variables
- HTTPS enforced for all transactions
- Webhook signature verification
- Proper handling of customer financial data
- Compliance with Peruvian data protection laws
- Regular reconciliation of transactions
- Timeout handling for pending payments
- Validation of payment amounts against order totals

## Testing

Test environments are available for all payment methods:

- PagoEfectivo sandbox: Generate test CIP codes without actual payments
- Yape test environment: Simulated QR payments
- Card testing environment: Test cards for approval, decline, and fraud scenarios
- SafetyPay test environment: Simulate both banking and cash payments

## Implementation Timeline

1. **Phase 1**: PagoEfectivo and Yape integrations (2-3 weeks)
2. **Phase 2**: Local card processing (2 weeks)
3. **Phase 3**: CCI transfers (1 week)
4. **Phase 4**: SafetyPay integration (1-2 weeks)
5. **Phase 5**: Testing and optimization (1-2 weeks)

## References

- [PagoEfectivo API Documentation](https://pagoefectivo.pe/developers)
- [Yape for Business](https://yape.com.pe/empresas)
- [Niubiz (VisaNet Peru) Developer Center](https://www.niubiz.com.pe/integracion-commerce/)
- [BCP CCI Transfer Information](https://www.viabcp.com/canales/banca-por-internet/transferencias)
- [SafetyPay Developer Documentation](https://developers.safetypay.com/)