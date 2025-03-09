# USA Payment Integrations

## Overview

This document outlines the integration of US-specific payment methods into the Marquita platform. The United States has a robust payment infrastructure with several widely-used options that can be readily integrated into e-commerce platforms.

## Supported Payment Methods

### ACH (Automated Clearing House)

**Description**: A network that coordinates electronic payments and automated transfers between U.S. financial institutions.

**Benefits**:
- Lower transaction fees compared to credit cards (typically $0.20-$1.50 per transaction)
- High security standards with bank-level encryption
- Reduced chargeback risk compared to credit cards
- Good for recurring payments and subscriptions

**Implementation**:
- Users provide their bank account and routing number
- Alternatively, can connect via secure integrations with Plaid
- Payments typically settle in 1-3 business days

### Stripe / Square / PayPal

**Description**: Popular payment processors that support credit cards, digital wallets, and other payment methods.

**Benefits**:
- Comprehensive all-in-one payment solutions
- Support for various payment methods (credit cards, Apple Pay, Google Pay)
- Well-documented APIs and SDKs
- Built-in fraud protection

**Implementation**:
- Embed checkout directly on site or redirect to payment processor
- Webhook support for payment status updates
- SDK integration for web and mobile applications

### Buy Now, Pay Later (BNPL)

**Description**: Payment options like Affirm, Klarna, and Afterpay that allow customers to split payments over time.

**Benefits**:
- Increases conversion rates, especially for higher-priced items
- Attracts price-sensitive customers
- No risk to merchant (provider assumes all credit risk)
- Growing in popularity in the US market

**Implementation**:
- Add BNPL provider checkout buttons at checkout
- API integration for order validation and payment confirmation
- Webhook handling for payment status updates

### Venmo / Cash App / Zelle

**Description**: Person-to-person payment apps that have expanded to business payments.

**Benefits**:
- Familiar interfaces for many US consumers
- Lower fees than traditional credit card processing
- Fast transfers (often same-day)
- Built-in social component (Venmo)

**Implementation**:
- Integration through APIs where available
- QR code generation for in-person payments
- Webhook support for payment confirmation

## Integration Architecture

### Deno Deploy API Implementation

```typescript
// Example implementation structure for US payment methods
import { serve } from "https://deno.land/std/http/server.ts";
import { StripeGateway } from "./gateways/stripe.ts";
import { ACHGateway } from "./gateways/ach.ts";
import { BNPLGateway } from "./gateways/bnpl.ts";
import { config } from "../config.ts";

serve(async (req) => {
  const url = new URL(req.url);
  const path = url.pathname;
  
  // Stripe integration endpoint
  if (path === "/api/payments/stripe") {
    const stripe = new StripeGateway(config.stripe);
    return await stripe.processPayment(req);
  }
  
  // ACH integration endpoint
  if (path === "/api/payments/ach") {
    const ach = new ACHGateway(config.ach);
    return await ach.processPayment(req);
  }
  
  // BNPL (Affirm/Klarna/etc) integration endpoint
  if (path === "/api/payments/bnpl") {
    const bnpl = new BNPLGateway(config.bnpl);
    return await bnpl.processPayment(req);
  }
  
  // Payment status webhook handling
  if (path === "/api/webhooks/payment-status") {
    // Process payment status updates from various providers
    const provider = url.searchParams.get("provider");
    
    switch (provider) {
      case "stripe":
        return await handleStripeWebhook(req);
      case "ach":
        return await handleACHWebhook(req);
      case "affirm":
      case "klarna":
        return await handleBNPLWebhook(req, provider);
      default:
        return new Response("Unknown provider", { status: 400 });
    }
  }
  
  return new Response("Not found", { status: 404 });
});
```

## User Journey

1. **Checkout Initiation**:
   - User navigates to checkout page
   - Selects payment method (Credit Card, ACH, BNPL, etc.)

2. **Credit Card / Stripe Flow**:
   - User enters card details in secure form
   - Payment processor handles authorization and capture
   - User receives confirmation on merchant site

3. **ACH Flow**:
   - User enters bank account details or connects via Plaid
   - ACH authorization is processed
   - User receives confirmation with expected settlement time

4. **BNPL Flow**:
   - User selects BNPL provider (Affirm, Klarna, etc.)
   - Redirected to BNPL provider to complete application and approval
   - Returned to merchant site with approval confirmation

## Configuration

Payment gateways are configured through environment variables or config files:

```json
{
  "usa": {
    "stripe": {
      "publicKey": "pk_test_...",
      "secretKey": "sk_test_...",
      "webhookSecret": "whsec_...",
      "successUrl": "https://yourdomain.com/checkout/success",
      "cancelUrl": "https://yourdomain.com/checkout/cancel"
    },
    "ach": {
      "apiKey": "YOUR_ACH_API_KEY",
      "environment": "production",
      "webhookUrl": "https://api.yourdomain.com/webhooks/ach"
    },
    "bnpl": {
      "affirm": {
        "publicKey": "PUBLIC_KEY",
        "privateKey": "PRIVATE_KEY",
        "sandboxMode": false
      },
      "klarna": {
        "username": "PK12345...",
        "password": "PASSWORD",
        "region": "US"
      }
    }
  }
}
```

## Security Considerations

- PCI DSS compliance for credit card processing (use Stripe/PayPal to minimize scope)
- API keys stored securely in Deno Deploy environment variables
- HTTPS enforced for all transactions
- Appropriate validation of webhook signatures
- Secure handling of banking information (preferably via trusted third parties)

## Testing

Test accounts are available for all payment methods:

- Stripe test mode: [Stripe Dashboard](https://dashboard.stripe.com/test/dashboard)
- ACH testing: [Plaid Sandbox](https://plaid.com/docs/sandbox/)
- BNPL test environments:
  - [Affirm Testing](https://docs.affirm.com/affirm-developers/docs/testing)
  - [Klarna Testing](https://developers.klarna.com/documentation/testing-environment/)

## Implementation Timeline

1. **Phase 1**: Credit card processing via Stripe (1 week)
2. **Phase 2**: ACH integration (1 week)
3. **Phase 3**: BNPL options (2 weeks)
4. **Phase 4**: Digital wallet support (Apple Pay, Google Pay) (1 week)

## References

- [Stripe API Documentation](https://stripe.com/docs/api)
- [Plaid API Documentation](https://plaid.com/docs/api/)
- [Affirm Developer Documentation](https://docs.affirm.com/affirm-developers/docs)
- [Klarna API Documentation](https://developers.klarna.com/api/)
- [PayPal Developer Documentation](https://developer.paypal.com/docs/)