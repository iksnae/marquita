# Brazil Payment Integrations

## Overview

This document outlines the integration of Brazil-specific payment methods into the Marquita platform. Brazil has unique payment requirements and preferences that are essential for success in the Brazilian e-commerce market. Local payment methods have high adoption rates and often offer lower fees than international options.

## Supported Payment Methods

### Pix

**Description**: An instant payment system created and managed by the Central Bank of Brazil, allowing for 24/7 real-time transfers.

**Benefits**:
- Instant transfers (24/7, including weekends and holidays)
- Low or zero fees for consumers and businesses
- High security with end-to-end encryption
- Wide adoption (over 100 million users in Brazil)
- Works via QR codes or key lookups (email, phone number, tax ID)

**Implementation**:
- Generate QR codes and payment keys for customers
- Receive real-time notifications via webhooks when payment is completed
- No card details or sensitive data to store

### Boleto Bancário

**Description**: A traditional Brazilian payment method that generates a voucher (boleto) that can be paid at banks, ATMs, convenience stores, or via internet banking.

**Benefits**:
- No need for a bank account or credit card to make purchases
- Widely accepted and trusted payment method in Brazil
- Lower fraud rates than credit cards
- Preferred by customers without bank accounts or credit cards
- Cash option for customers who prefer not to use digital payments

**Implementation**:
- Generate boleto vouchers with payment information
- Track payment status using boleto ID
- Handle expiration dates (typically 1-3 days)

### Brazilian Credit Cards

**Description**: Support for local Brazilian credit cards, including installment payments ("parcelamento").

**Benefits**:
- Support for local-only card networks like Elo and Hipercard
- Installment payment support (crucial for Brazilian market)
- Integration with anti-fraud systems tailored to Brazilian market

**Implementation**:
- Support for installment options (2-12x payments)
- Clear display of installment plans and interest
- Local acquiring banks for better approval rates

### Digital Wallets / Crypto

**Description**: Support for PicPay, Mercado Pago, and local cryptocurrency payment options.

**Benefits**:
- Popular among younger, tech-savvy consumers
- Often have promotional offers to encourage usage
- Lower fees than traditional credit card processing
- Growing cryptocurrency adoption in Brazil

**Implementation**:
- API integration with popular wallet providers
- QR code generation for in-app payments
- Handling of payment confirmation callbacks

## Integration Architecture

### Deno Deploy API Implementation

```typescript
// Example implementation structure for Brazil payment methods
import { serve } from "https://deno.land/std/http/server.ts";
import { PixGateway } from "./gateways/pix.ts";
import { BoletoGateway } from "./gateways/boleto.ts";
import { BrazilCardGateway } from "./gateways/brazil-cards.ts";
import { config } from "../config.ts";

serve(async (req) => {
  const url = new URL(req.url);
  const path = url.pathname;
  
  // PIX integration endpoint
  if (path === "/api/payments/pix") {
    const pix = new PixGateway(config.pix);
    return await pix.generatePayment(req);
  }
  
  // Boleto integration endpoint
  if (path === "/api/payments/boleto") {
    const boleto = new BoletoGateway(config.boleto);
    return await boleto.generateVoucher(req);
  }
  
  // Brazilian credit card processing with installments
  if (path === "/api/payments/credit-card") {
    const cardProcessor = new BrazilCardGateway(config.creditCard);
    return await cardProcessor.processWithInstallments(req);
  }
  
  // Payment notification webhooks
  if (path === "/api/notifications") {
    const paymentType = req.headers.get("X-Payment-Type");
    
    switch (paymentType) {
      case "pix":
        return await handlePixNotification(req);
      case "boleto":
        return await handleBoletoNotification(req);
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
   - Selects payment method (Pix, Boleto, Credit Card, etc.)

2. **Pix Flow**:
   - System generates Pix QR code and copy/paste key
   - User scans QR code with banking app or copies the Pix key
   - Payment confirmation happens in real-time
   - User receives confirmation on merchant site

3. **Boleto Flow**:
   - System generates Boleto voucher with payment instructions
   - User receives Boleto via email/download
   - User pays Boleto at bank/lottery house/banking app
   - Payment confirmation occurs within 1-2 business days
   - Order processing begins after payment confirmation

4. **Credit Card Flow with Installments**:
   - User enters card details
   - Selects number of installments (1x to 12x)
   - System displays installment amount with interest (if applicable)
   - Payment processor handles authorization and installment setup
   - User receives confirmation on merchant site

## Configuration

Payment gateways are configured through environment variables or config files:

```json
{
  "brazil": {
    "pix": {
      "apiKey": "PIX_API_KEY",
      "merchantId": "PIX_MERCHANT_ID",
      "certPath": "certificates/pix_cert.pem",
      "webhookUrl": "https://api.yourdomain.com/notifications",
      "merchantName": "Your Store Name",
      "merchantCity": "São Paulo"
    },
    "boleto": {
      "apiKey": "BOLETO_API_KEY",
      "token": "BOLETO_TOKEN",
      "expirationDays": 3,
      "webhookUrl": "https://api.yourdomain.com/notifications",
      "instructions": "Pay at any bank or lottery house"
    },
    "creditCard": {
      "apiKey": "CARD_API_KEY",
      "merchantId": "MERCHANT_ID",
      "maxInstallments": 12,
      "interestRate": 1.99,
      "interestStartsAt": 2, // Interest starts at 2 installments
      "antifraudEnabled": true
    }
  }
}
```

## Security Considerations

- API keys stored securely in Deno Deploy environment variables
- HTTPS enforced for all transactions
- Digital signatures for Pix transactions
- Webhook validation for payment notifications
- Compliance with LGPD (Lei Geral de Proteção de Dados) - Brazilian data protection law

## Testing

Test environments are available for all payment methods:

- Pix sandbox environment: [Developer Portal](https://developers.bcb.gov.br/sandbox)
- Boleto test generation: [Boleto Testing](https://www.boletobancario.com/boleto/sandbox)
- Credit card test environment: Available through payment gateway providers like Cielo, PagSeguro, etc.

## Implementation Timeline

1. **Phase 1**: Pix integration (1-2 weeks)
2. **Phase 2**: Credit card processing with installments (2 weeks)
3. **Phase 3**: Boleto integration (1 week)
4. **Phase 4**: Testing and optimization (1 week)

## References

- [Pix API Documentation (Banco Central do Brasil)](https://www.bcb.gov.br/estabilidadefinanceira/pix)
- [Boleto Integration Guide](https://www.boletobancario.com/documentacao)
- [Brazilian Credit Card Processing](https://developers.cielo.com.br/api-portal/en/documentation/credit-card)
- [LGPD Compliance Guide](https://www.gov.br/cidadania/pt-br/acesso-a-informacao/lgpd)