# Colombia Payment Integrations

## Overview

This document outlines the integration of Colombia-specific payment methods into the Marquita platform. Colombia has unique payment requirements and preferences that differ from international standards, making local integration essential for businesses operating in this market.

## Supported Payment Methods

### PSE (Pago Seguro en Línea)

**Description**: A bank transfer payment solution widely used in Colombia for online transactions.

**Benefits**:
- No fees for consumers
- Transaction fees for businesses range between 1% and 2.5%
- Local payments, eliminating international fees
- Secure, government-regulated system

**Implementation**:
- Users will be redirected to their bank's secure page where they can authorize the payment
- Upon completion, users will be returned to the eCommerce platform with a payment confirmation

### DaviPlata

**Description**: A mobile-first payment solution by Bancolombia, primarily used for mobile payments and bank transfers.

**Benefits**:
- No fees for domestic transfers within Bancolombia accounts
- Mobile-optimized experience
- Easy transfer of funds
- Local payment system with no international fees

**Implementation**:
- Users select DaviPlata as their payment method
- Payment is completed via the DaviPlata app on mobile devices
- Transaction confirmation displayed immediately after completion

## Integration Architecture

### Deno Deploy API Implementation

```typescript
// Example implementation structure for Colombia payment methods
import { serve } from "https://deno.land/std/http/server.ts";
import { PSEGateway } from "./gateways/pse.ts";
import { DaviPlataGateway } from "./gateways/daviplata.ts";
import { config } from "../config.ts";

serve(async (req) => {
  const url = new URL(req.url);
  const path = url.pathname;
  
  // PSE integration endpoint
  if (path === "/api/payments/pse") {
    const pse = new PSEGateway(config.pse);
    return await pse.processPayment(req);
  }
  
  // DaviPlata integration endpoint
  if (path === "/api/payments/daviplata") {
    const daviplata = new DaviPlataGateway(config.daviplata);
    return await daviplata.processPayment(req);
  }
  
  // Payment status endpoint
  if (path === "/api/payments/status") {
    // Check payment status logic
  }
  
  return new Response("Not found", { status: 404 });
});
```

## User Journey

1. **Checkout Initiation**:
   - User navigates to checkout page
   - Selects payment method (PSE or DaviPlata)

2. **PSE Flow**:
   - User selects bank from dropdown
   - Redirected to bank's secure login page
   - Completes authentication and confirms payment
   - Returned to merchant site with confirmation

3. **DaviPlata Flow**:
   - User receives payment instructions
   - Completes payment in DaviPlata app
   - Deno Deploy API confirms payment status
   - User sees confirmation on merchant site

## Configuration

Payment gateways are configured through environment variables or config files:

```json
{
  "colombia": {
    "pse": {
      "merchantId": "YOUR_MERCHANT_ID",
      "apiKey": "YOUR_API_KEY",
      "redirectUrl": "https://yourdomain.com/checkout/complete",
      "notificationUrl": "https://api.yourdomain.com/payments/pse/webhook"
    },
    "daviplata": {
      "merchantId": "YOUR_DAVIPLATA_ID",
      "apiKey": "YOUR_DAVIPLATA_API_KEY",
      "callbackUrl": "https://api.yourdomain.com/payments/daviplata/callback"
    }
  }
}
```

## Security Considerations

- API keys stored securely in Deno Deploy environment variables
- HTTPS enforced for all transactions
- No sensitive payment data stored in frontend code
- Webhook validation to prevent fraudulent notifications

## Testing

Test accounts are available for both payment methods:

- PSE test environment: [Developer portal link]
- DaviPlata sandbox: [Developer portal link]

## Implementation Timeline

1. **Phase 1**: Basic PSE integration (2 weeks)
2. **Phase 2**: Basic DaviPlata integration (2 weeks)
3. **Phase 3**: Advanced features and error handling (1 week)
4. **Phase 4**: Testing and optimization (1 week)

## References

- [PSE API Documentation](https://pseapi.com/docs)
- [DaviPlata Integration Guide](https://daviplata.com/developers)
- [Bancolombia Developer Portal](https://developer.bancolombia.com)