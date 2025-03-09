# Ecuador Payment Integrations

## Overview

This document outlines the integration of Ecuador-specific payment methods into the Marquita platform. Ecuador presents a unique payment landscape with its dollarized economy and a mix of traditional and emerging digital payment methods. Integrating local payment solutions is essential for success in the Ecuadorian market, providing better conversion rates and addressing local consumer preferences.

## Supported Payment Methods

### Credit and Debit Cards

**Description**: Processing of Ecuadorian credit and debit cards, with support for installment payments ("diferido").

**Benefits**:
- Familiar payment method for Ecuadorian consumers
- Support for local card networks
- Installment payment options essential for higher-value purchases
- Wide acceptance among middle and upper-income consumers
- Compliance with Ecuadorian financial regulations

**Implementation**:
- Integration with local payment processors
- Support for installment plans (3, 6, 9, 12 months)
- Clear display of installment costs

### Payphone

**Description**: A popular Ecuadorian mobile payment platform that allows users to make payments using their smartphones.

**Benefits**:
- Growing user base in Ecuador
- Simple payment experience for customers
- Lower transaction fees compared to credit cards
- No need for a physical card or bank account
- QR code payments for in-person transactions
- API for online integration

**Implementation**:
- API integration for e-commerce
- QR code generation
- Mobile payment button
- Real-time payment confirmation

### Bank Transfers

**Description**: Direct bank transfers using the Ecuadorian banking system.

**Benefits**:
- Widely trusted payment method
- Lower transaction fees than card payments
- Good for high-value transactions
- Preferred by many businesses for B2B payments
- No chargebacks

**Implementation**:
- Unique transfer reference generation
- Manual or automated reconciliation
- Clear payment instructions for customers

### Cash Payment Networks

**Description**: Integration with cash payment networks like Facilito, Western Union, and ServiPagos that allow users to pay for online purchases with cash at physical locations.

**Benefits**:
- Reaches the significant unbanked population in Ecuador
- Trusted payment method for those without cards or digital banking
- Wide network of payment points throughout the country
- No need for financial information from customers
- Higher conversion rates for certain demographics

**Implementation**:
- Generate payment vouchers with reference codes
- Payment status tracking via API
- Defined expiration window (typically 24-72 hours)

## Integration Architecture

### Deno Deploy API Implementation

```typescript
// Example implementation structure for Ecuador payment methods
import { serve } from "https://deno.land/std/http/server.ts";
import { CardGateway } from "./gateways/card.ts";
import { PayphoneGateway } from "./gateways/payphone.ts";
import { BankTransferGateway } from "./gateways/bank-transfer.ts";
import { CashPaymentGateway } from "./gateways/cash-payment.ts";
import { config } from "../config.ts";

serve(async (req) => {
  const url = new URL(req.url);
  const path = url.pathname;
  
  // Credit/debit card processing
  if (path === "/api/payments/card") {
    const cardProcessor = new CardGateway(config.card);
    return await cardProcessor.processWithInstallments(req);
  }
  
  // Payphone integration
  if (path === "/api/payments/payphone") {
    const payphone = new PayphoneGateway(config.payphone);
    return await payphone.processPayment(req);
  }
  
  // Bank transfer instructions
  if (path === "/api/payments/bank-transfer") {
    const bankTransfer = new BankTransferGateway(config.bankTransfer);
    return await bankTransfer.generateInstructions(req);
  }
  
  // Cash payment voucher
  if (path === "/api/payments/cash-payment") {
    const cashPayment = new CashPaymentGateway(config.cashPayment);
    return await cashPayment.generateVoucher(req);
  }
  
  // Payment notification webhooks
  if (path === "/api/notifications") {
    const paymentType = req.headers.get("X-Payment-Type");
    
    switch (paymentType) {
      case "card":
        return await handleCardNotification(req);
      case "payphone":
        return await handlePayphoneNotification(req);
      case "bank-transfer":
        return await handleBankTransferNotification(req);
      case "cash-payment":
        return await handleCashPaymentNotification(req);
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
   - Selects payment method (Card, Payphone, Bank Transfer, Cash Payment)

2. **Credit/Debit Card Flow**:
   - User enters card details
   - Selects number of installments if applicable
   - System displays installment plan and total cost
   - User confirms payment
   - Payment is processed
   - User receives confirmation

3. **Payphone Flow**:
   - User selects Payphone
   - System generates payment request
   - User scans QR code or clicks payment link
   - User authorizes payment in Payphone app
   - System receives confirmation
   - User is redirected to confirmation page

4. **Bank Transfer Flow**:
   - System displays bank account details and reference number
   - User makes transfer via their bank
   - System verifies transfer (manually or automatically)
   - Order processing begins after verification
   - User receives confirmation email

5. **Cash Payment Flow**:
   - System generates payment voucher with unique reference
   - User receives voucher via email or download
   - User pays at any affiliated payment location
   - System is notified when payment is made
   - Order processing begins after payment confirmation
   - User receives confirmation email

## Configuration

Payment gateways are configured through environment variables or config files:

```json
{
  "ecuador": {
    "card": {
      "merchantId": "MERCHANT_ID",
      "apiKey": "CARD_API_KEY",
      "environment": "production",
      "maxInstallments": 12,
      "interestRates": {
        "3": 4.5,
        "6": 8.9,
        "9": 13.5,
        "12": 18.0
      },
      "webhookUrl": "https://api.yourdomain.com/notifications"
    },
    "payphone": {
      "clientId": "PAYPHONE_CLIENT_ID",
      "clientSecret": "PAYPHONE_CLIENT_SECRET",
      "storeId": "PAYPHONE_STORE_ID",
      "webhookUrl": "https://api.yourdomain.com/notifications",
      "returnUrl": "https://yourdomain.com/checkout/confirmation"
    },
    "bankTransfer": {
      "accountNumber": "BANK_ACCOUNT_NUMBER",
      "accountName": "COMPANY_NAME",
      "bankName": "BANK_NAME",
      "accountType": "savings", // or "checking"
      "identificationNumber": "RUC_NUMBER",
      "notificationEmail": "payments@yourdomain.com"
    },
    "cashPayment": {
      "providerId": "PROVIDER_ID",
      "apiKey": "CASH_PAYMENT_API_KEY",
      "expirationHours": 48,
      "servicePointsUrl": "https://yourdomain.com/payment-locations",
      "webhookUrl": "https://api.yourdomain.com/notifications"
    }
  }
}
```
