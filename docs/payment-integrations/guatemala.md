# Guatemala Payment Integrations

## Overview

This document outlines the integration of Guatemala-specific payment methods into the Marquita platform. Guatemala has unique payment preferences with a mix of traditional and digital payment options. Integrating local payment methods is essential for success in the Guatemalan market, providing better conversion rates and addressing local consumer needs.

## Supported Payment Methods

### Credit and Debit Cards

**Description**: Processing of Guatemalan credit and debit cards, including local bank cards.

**Benefits**:
- Widely used among middle to upper-income consumers
- Familiar payment experience
- Support for local and international cards
- Immediate payment confirmation
- Trusted by consumers

**Implementation**:
- Integration with local payment processors
- Support for major card networks (Visa, Mastercard)
- Support for local bank cards

### Bank Transfers

**Description**: Direct bank transfers using the Guatemalan ACH system.

**Benefits**:
- Lower transaction fees compared to cards
- Trusted payment method for higher-value purchases
- Preferred for B2B transactions
- No need for card information
- Wide banking network coverage

**Implementation**:
- Account information and reference number generation
- Manual or automated reconciliation
- Clear payment instructions for customers

### Cash Payment Networks

**Description**: Integration with networks like Pago Fácil and VisaNet that allow customers to pay in cash at physical locations.

**Benefits**:
- Reaches unbanked population (approximately 60% of Guatemalans)
- Trusted payment method for security-conscious consumers
- Wide network of payment points in urban and rural areas
- No need for banking or card information
- Familiar process for many consumers

**Implementation**:
- Generate payment vouchers with reference codes
- Payment status tracking via API or webhook
- Set appropriate expiration windows (24-72 hours)

### Mobile Wallets

**Description**: Integration with mobile money services like Tigo Money.

**Benefits**:
- Growing user base, especially in areas with limited banking access
- Lower transaction costs compared to cards
- Simple payment experience via phone number
- Immediate payment confirmation
- Reduced fraud risk compared to cards

**Implementation**:
- API integration for direct processing
- Mobile number-based payment flow
- Real-time payment notifications

## Integration Architecture

### Deno Deploy API Implementation

```typescript
// Example implementation structure for Guatemala payment methods
import { serve } from "https://deno.land/std/http/server.ts";
import { CardGateway } from "./gateways/card.ts";
import { BankTransferGateway } from "./gateways/bank-transfer.ts";
import { CashPaymentGateway } from "./gateways/cash-payment.ts";
import { MobileWalletGateway } from "./gateways/mobile-wallet.ts";
import { config } from "../config.ts";

serve(async (req) => {
  const url = new URL(req.url);
  const path = url.pathname;
  
  // Credit/debit card processing
  if (path === "/api/payments/card") {
    const cardProcessor = new CardGateway(config.guatemala.card);
    return await cardProcessor.processPayment(req);
  }
  
  // Bank transfer instructions
  if (path === "/api/payments/bank-transfer") {
    const bankTransfer = new BankTransferGateway(config.guatemala.bankTransfer);
    return await bankTransfer.generateInstructions(req);
  }
  
  // Cash payment voucher
  if (path === "/api/payments/cash-payment") {
    const cashPayment = new CashPaymentGateway(config.guatemala.cashPayment);
    return await cashPayment.generateVoucher(req);
  }
  
  // Mobile wallet payment
  if (path === "/api/payments/mobile-wallet") {
    const mobileWallet = new MobileWalletGateway(config.guatemala.mobileWallet);
    return await mobileWallet.processPayment(req);
  }
  
  // Payment notification webhooks
  if (path === "/api/notifications") {
    const paymentType = req.headers.get("X-Payment-Type");
    
    switch (paymentType) {
      case "card":
        return await handleCardNotification(req);
      case "bank-transfer":
        return await handleBankTransferNotification(req);
      case "cash-payment":
        return await handleCashPaymentNotification(req);
      case "mobile-wallet":
        return await handleMobileWalletNotification(req);
      default:
        return new Response("Unknown payment type", { status: 400 });
    }
  }
  
  return new Response("Not found", { status: 404 });
});
```

## Configuration

Payment gateways are configured through environment variables or config files:

```json
{
  "guatemala": {
    "card": {
      "merchantId": "CARD_MERCHANT_ID",
      "apiKey": "CARD_API_KEY",
      "environment": "production",
      "webhookUrl": "https://api.yourdomain.com/notifications"
    },
    "bankTransfer": {
      "accountNumber": "BANK_ACCOUNT_NUMBER",
      "accountName": "COMPANY_NAME",
      "bankName": "BANK_NAME",
      "notificationEmail": "payments@yourdomain.com"
    },
    "cashPayment": {
      "providerId": "PROVIDER_ID",
      "apiKey": "CASH_PAYMENT_API_KEY",
      "expirationHours": 48,
      "webhookUrl": "https://api.yourdomain.com/notifications"
    },
    "mobileWallet": {
      "merchantId": "MOBILE_WALLET_MERCHANT_ID",
      "apiKey": "MOBILE_WALLET_API_KEY",
      "provider": "tigo", // or other provider
      "webhookUrl": "https://api.yourdomain.com/notifications"
    }
  }
}
```

## Security Considerations

- API keys stored securely in Deno Deploy environment variables
- HTTPS enforced for all transactions
- Webhook signature validation for payment notifications
- Compliance with local data protection laws
- Regular security audits and monitoring

## Testing

Test environments are available for all payment methods:

- Card processing sandbox: Available through payment processor
- Bank transfer testing: Simulated transfer confirmation
- Cash payment testing: Test voucher generation and confirmation
- Mobile wallet sandbox: Available through provider developer portals

## References

- [Guatemalan Banking Association](https://www.abg.org.gt)
- [Superintendency of Banks Guatemala](https://www.sib.gob.gt)
- [VisaNet Guatemala](https://www.visanet.com.gt)
- [Tigo Money Guatemala](https://www.tigo.com.gt/tigo-money)
