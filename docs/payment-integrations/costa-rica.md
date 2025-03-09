# Costa Rica Payment Integrations

## Overview

This document outlines the integration of Costa Rica-specific payment methods into the Marquita platform. Costa Rica has a unique payment ecosystem that combines traditional banking methods with emerging digital solutions. For businesses targeting the Costa Rican market, understanding and integrating local payment options is essential for maximizing conversion rates and providing a seamless customer experience.

## Supported Payment Methods

### SINPE Móvil

**Description**: A real-time mobile payment system operated by the Costa Rican Central Bank that allows transfers between local bank accounts using only a phone number.

**Benefits**:
- Instant transfers between bank accounts
- Only requires a phone number to make payments
- Extremely popular among Costa Ricans (high adoption rate)
- Lower fees compared to credit card processing
- Mobile-first payment experience aligned with local consumer habits
- Daily transaction limits of up to ₡2,000,000 (approximately $3,700 USD)

**Implementation**:
- Generate a unique payment reference for each transaction
- Provide the business's registered SINPE Móvil phone number
- Customer completes payment through their banking app
- Verify payment through account notifications or callbacks
- Manual or automated reconciliation based on reference codes

### Local Credit/Debit Cards

**Description**: Processing for Costa Rican bank-issued cards, including support for local networks like Visa and Mastercard processed through local acquirers.

**Benefits**:
- Familiar payment method for Costa Rican consumers
- Support for local-only cards
- Higher approval rates through local processing
- Lower processing fees with local acquirers
- Reduced fraud rates with local bank security measures

**Implementation**:
- Integration with Costa Rican payment processors (e.g., Credomatic, BAC)
- Support for local 3D Secure authentication
- Proper handling of local currency (Costa Rican Colón)
- Options for installments when available
- Compliance with local banking regulations

### Bank Transfers (SINPE)

**Description**: Direct bank transfers through SINPE (Sistema Nacional de Pagos Electrónicos), Costa Rica's electronic interbank payment system.

**Benefits**:
- Trusted and secure method for larger transactions
- Lower transaction fees compared to credit cards
- Same-day processing for transfers
- Well-established system with high reliability
- Supported by all Costa Rican banks

**Implementation**:
- Generate a unique account reference for each transaction
- Provide bank account details for transfer
- Implement webhook notifications when transfers are completed
- Automated reconciliation of payments
- Support for manual verification when needed

### Digital Wallets

**Description**: Support for emerging digital wallets in Costa Rica including Wink, Tapp, and Tuyo.

**Benefits**:
- Growing popularity among younger, tech-savvy consumers
- Faster checkout experience
- Enhanced security features
- Integration with loyalty programs
- Alternative to traditional banking for some users

**Implementation**:
- API integration with each wallet provider
- Generation of payment links or QR codes
- Support for mobile payment flows
- Webhook processing for payment notifications
- Balance maintenance in platform-specific accounts

## Integration Architecture

### Deno Deploy API Implementation

```typescript
// Example implementation structure for Costa Rica payment methods
import { serve } from "https://deno.land/std/http/server.ts";
import { SINPEMovilGateway } from "./gateways/sinpe-movil.ts";
import { CostaRicanCardGateway } from "./gateways/cr-cards.ts";
import { SINPETransferGateway } from "./gateways/sinpe-transfer.ts";
import { config } from "../config.ts";

serve(async (req) => {
  const url = new URL(req.url);
  const path = url.pathname;
  
  // SINPE Móvil integration endpoint
  if (path === "/api/payments/sinpe-movil") {
    const sinpeMovil = new SINPEMovilGateway(config.costaRica.sinpeMovil);
    return await sinpeMovil.generatePaymentReference(req);
  }
  
  // Local card processing endpoint
  if (path === "/api/payments/card") {
    const cardProcessor = new CostaRicanCardGateway(config.costaRica.cards);
    return await cardProcessor.processPayment(req);
  }
  
  // SINPE transfer endpoint
  if (path === "/api/payments/sinpe-transfer") {
    const sinpeTransfer = new SINPETransferGateway(config.costaRica.sinpeTransfer);
    return await sinpeTransfer.generateAccountReference(req);
  }
  
  // Payment notification/verification endpoint
  if (path === "/api/payments/verify") {
    const paymentType = req.headers.get("X-Payment-Type");
    
    switch (paymentType) {
      case "sinpe-movil":
        return await verifySINPEMovilPayment(req);
      case "card":
        return await verifyCardPayment(req);
      case "sinpe-transfer":
        return await verifySINPETransferPayment(req);
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
   - Selects payment method (SINPE Móvil, card, SINPE transfer, etc.)

2. **SINPE Móvil Flow**:
   - System generates a unique payment reference
   - User receives SINPE Móvil number and payment reference
   - User initiates payment through their banking app using the merchant's phone number
   - User includes the payment reference in the payment description
   - Merchant receives notification when payment is completed
   - Order is processed after payment verification

3. **Local Card Flow**:
   - User enters card details
   - System processes payment through local acquirer
   - 3D Secure verification if required
   - User receives confirmation on successful payment
   - Order processing begins immediately

4. **SINPE Transfer Flow**:
   - System provides bank account details and a unique reference
   - User initiates bank transfer through their online banking
   - User includes the payment reference in the transfer
   - Merchant verifies payment receipt (automated or manual)
   - Order processing begins after verification

## Configuration

Payment gateways are configured through environment variables or config files:

```json
{
  "costaRica": {
    "sinpeMovil": {
      "phoneNumber": "8888-8888",
      "accountName": "Your Business Name",
      "bankCode": "BAC",
      "notificationEmail": "payments@yourbusiness.com",
      "referencePrefix": "SM",
      "webhookUrl": "https://api.yourdomain.com/payments/verify"
    },
    "cards": {
      "apiKey": "YOUR_API_KEY",
      "merchantId": "YOUR_MERCHANT_ID",
      "processorUrl": "https://secure.credomatic.com/api",
      "currency": "CRC",
      "webhookUrl": "https://api.yourdomain.com/payments/verify",
      "threeDSecureEnabled": true
    },
    "sinpeTransfer": {
      "accountNumber": "CR1234567890123456",
      "accountName": "Your Business Name",
      "bankName": "Banco Nacional de Costa Rica",
      "currency": "CRC",
      "referencePrefix": "ST",
      "notificationEmail": "payments@yourbusiness.com"
    }
  }
}
```

## Security Considerations

- Account details stored securely in Deno Deploy environment variables
- HTTPS enforced for all transactions
- Reference codes to track and validate payments
- Compliance with Costa Rican data protection laws
- Regular reconciliation of payments
- Validation of payment amounts against order totals
- Timeout handling for pending payments

## Testing

Test environments are available for the major payment methods:

- SINPE Móvil test mode: Simulate payments without actual transfers
- Card testing environment: Test cards for various scenarios (approval, decline, etc.)
- SINPE transfer sandbox: Simulated bank environment for testing transfers

## Implementation Timeline

1. **Phase 1**: SINPE Móvil integration (1-2 weeks)
2. **Phase 2**: Local card processing implementation (2 weeks)
3. **Phase 3**: SINPE transfer integration (1 week)
4. **Phase 4**: Digital wallet support (2 weeks)
5. **Phase 5**: Testing and optimization (1 week)

## References

- [Banco Central de Costa Rica - SINPE](https://www.bccr.fi.cr/seccion-sistema-de-pagos/SINPE)
- [SINPE Móvil Documentation](https://www.bancobcr.com/wps/portal/bcr/bancobcr/personas/servicios/sinpe_movil/)
- [BAC Credomatic Developer Resources](https://www.baccredomatic.com)
- [Costa Rican Payment Regulations](https://www.sugef.fi.cr)