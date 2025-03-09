# Integraciones de Pago de Argentina

## Descripción general

Este documento describe la integración de métodos de pago específicos de Argentina en la plataforma Marquita. Argentina tiene características de pago únicas influenciadas por su entorno económico, incluyendo alta inflación y restricciones cambiarias. Los métodos de pago locales son esenciales para el éxito en el mercado argentino, ofreciendo mejores tasas de conversión y comisiones más bajas que las opciones internacionales.

## Métodos de Pago Soportados

### Mercado Pago

**Descripción**: La plataforma de pago dominante en Argentina (propiedad de MercadoLibre), que ofrece una amplia gama de opciones de pago.

**Beneficios**:
- Penetración de mercado extremadamente alta en Argentina
- Soporte para múltiples métodos de pago bajo una sola integración
- Implementación sencilla con APIs y SDKs amigables para desarrolladores
- Confianza y familiaridad del consumidor
- Protección antifraude incorporada

**Implementación**:
- Integración API para checkout directo
- Checkout Pro para implementación simplificada
- SDKs móviles para pagos basados en aplicaciones
- Pagos con código QR para transacciones presenciales

### Rapipago / Pago Fácil

**Descripción**: Redes de pago en efectivo que permiten a los clientes pagar compras online en persona en miles de ubicaciones en toda Argentina.

**Beneficios**:
- Atiende a clientes que prefieren efectivo y no tienen cuentas bancarias
- Amplia red de puntos de pago en todo el país
- Método de pago offline confiable
- No requiere información financiera del cliente
- Buena opción en un país con una población significativa no bancarizada

**Implementación**:
- Generar comprobantes de pago con identificadores únicos
- Seguimiento del estado del pago a través de callbacks de API
- Establecer ventanas de vencimiento apropiadas (típicamente 3-5 días)

### Transferencias Bancarias / Pagos por CBU

**Descripción**: Transferencias bancarias directas utilizando CBU (Clave Bancaria Uniforme) o cuentas virtuales.

**Beneficios**:
- Costos de transacción más bajos comparados con tarjetas de crédito
- Método preferido para transacciones de alto valor
- Confirmación inmediata de fondos
- Evita comisiones de cambio de divisas de tarjetas de crédito
- Ampliamente utilizado en transacciones B2B

**Implementación**:
- Proporcionar CBU único o alias para transferencias
- Implementar sistemas de reconciliación para emparejar transferencias entrantes
- Notificación automatizada cuando se reciben transferencias

### Tarjetas de Crédito/Débito Argentinas

**Descripción**: Soporte para tarjetas locales argentinas con opciones de pago en cuotas.

**Beneficios**:
- Soporte esencial para pago en cuotas con o sin interés
- Adquirencia local para mejores tasas de aprobación
- Soporte para tarjetas exclusivamente locales como Tarjeta Naranja, Cabal, etc.
- Cumplimiento con las regulaciones del Banco Central

**Implementación**:
- Integración con procesadores de pago locales
- Soporte para planes de cuotas (3, 6, 12, 18 cuotas)
- Visualización clara de costos de cuotas e intereses

### Criptomonedas

**Descripción**: Soporte para pagos con criptomonedas, que son cada vez más populares en Argentina como protección contra la inflación.

**Beneficios**:
- Evitar restricciones cambiarias y preocupaciones por la inflación
- Creciente adopción en Argentina como alternativa al peso
- Menores comisiones de transacción para pagos transfronterizos
- Atraer a consumidores con conocimientos tecnológicos
- Las opciones de stablecoins proporcionan estabilidad de precios

**Implementación**:
- Integración con procesadores de pago cripto
- Soporte para monedas y stablecoins populares (BTC, ETH, USDT)
- Conversión de tipo de cambio en tiempo real

## Arquitectura de Integración

### Implementación de API Deno Deploy

```typescript
// Estructura de implementación de ejemplo para métodos de pago de Argentina
import { serve } from "https://deno.land/std/http/server.ts";
import { MercadoPagoGateway } from "./gateways/mercadopago.ts";
import { RapipagoGateway } from "./gateways/rapipago.ts";
import { BankTransferGateway } from "./gateways/bank-transfer.ts";
import { ArgentinaCardGateway } from "./gateways/argentina-cards.ts";
import { CryptoGateway } from "./gateways/crypto.ts";
import { config } from "../config.ts";

serve(async (req) => {
  const url = new URL(req.url);
  const path = url.pathname;
  
  // Endpoint de integración de Mercado Pago
  if (path === "/api/payments/mercadopago") {
    const mp = new MercadoPagoGateway(config.mercadoPago);
    return await mp.processPayment(req);
  }
  
  // Endpoint de integración de Rapipago/Pago Fácil
  if (path === "/api/payments/cash-payment") {
    const cashPayment = new RapipagoGateway(config.cashPayment);
    return await cashPayment.generateVoucher(req);
  }
  
  // Procesamiento de transferencia bancaria
  if (path === "/api/payments/bank-transfer") {
    const bankTransfer = new BankTransferGateway(config.bankTransfer);
    return await bankTransfer.generateTransferInstructions(req);
  }
  
  // Procesamiento de tarjeta de crédito argentina con cuotas
  if (path === "/api/payments/credit-card") {
    const cardProcessor = new ArgentinaCardGateway(config.creditCard);
    return await cardProcessor.processWithInstallments(req);
  }
  
  // Procesamiento de pago con criptomonedas
  if (path === "/api/payments/crypto") {
    const crypto = new CryptoGateway(config.crypto);
    return await crypto.generatePaymentAddress(req);
  }
  
  // Webhooks de notificación de pago
  if (path === "/api/notifications") {
    const paymentType = req.headers.get("X-Payment-Type");
    
    switch (paymentType) {
      case "mercadopago":
        return await handleMercadoPagoNotification(req);
      case "rapipago":
        return await handleRapipagoNotification(req);
      case "bank-transfer":
        return await handleBankTransferNotification(req);
      case "credit-card":
        return await handleCardNotification(req);
      case "crypto":
        return await handleCryptoNotification(req);
      default:
        return new Response("Tipo de pago desconocido", { status: 400 });
    }
  }
  
  return new Response("No encontrado", { status: 404 });
});
```

## Recorrido del Usuario

1. **Inicio del Checkout**:
   - El usuario navega a la página de checkout
   - Selecciona el método de pago (Mercado Pago, Rapipago, Transferencia Bancaria, Tarjeta de Crédito, Cripto)

2. **Flujo de Mercado Pago**:
   - El usuario selecciona Mercado Pago
   - Es redirigido al checkout de Mercado Pago o procesa en la aplicación
   - Elige sub-método dentro de Mercado Pago (tarjeta, saldo de cuenta, etc.)
   - Completa el pago
   - Es redirigido de vuelta al sitio del comerciante con confirmación

3. **Flujo de Rapipago/Pago Fácil**:
   - El sistema genera un comprobante de pago con código único
   - El usuario recibe el comprobante vía email/descarga
   - El usuario paga en una ubicación de Rapipago/Pago Fácil dentro del período de vencimiento
   - El comerciante recibe notificación cuando se realiza el pago
   - El procesamiento del pedido comienza después de la confirmación del pago

4. **Flujo de Transferencia Bancaria**:
   - El sistema proporciona CBU/alias e instrucciones de transferencia
   - El usuario realiza la transferencia a través de su banca online
   - El sistema reconcilia la transferencia entrante
   - El procesamiento del pedido comienza después de la confirmación de la transferencia

5. **Flujo de Tarjeta de Crédito con Cuotas**:
   - El usuario introduce los detalles de la tarjeta
   - Selecciona el número de cuotas (1x, 3x, 6x, 12x, 18x)
   - El sistema muestra el monto de la cuota con interés (si aplica)
   - El procesador de pago maneja la autorización y la configuración de cuotas
   - El usuario recibe confirmación en el sitio del comerciante

6. **Flujo de Criptomonedas**:
   - El usuario selecciona la opción de pago con criptomonedas
   - El sistema genera una dirección de wallet y el monto de pago
   - El usuario envía el monto exacto de cripto desde su wallet
   - El sistema confirma la transacción en la blockchain
   - El procesamiento del pedido comienza después de la confirmación

## Configuración

Los gateways de pago se configuran a través de variables de entorno o archivos de configuración:

```json
{
  "argentina": {
    "mercadoPago": {
      "publicKey": "MP_PUBLIC_KEY",
      "accessToken": "MP_ACCESS_TOKEN",
      "webhookUrl": "https://api.tudominio.com/notifications",
      "redirectUrl": "https://tudominio.com/checkout/confirmation"
    },
    "cashPayment": {
      "apiKey": "CASH_PAYMENT_API_KEY",
      "providerId": "PROVIDER_ID",
      "expirationDays": 5,
      "webhookUrl": "https://api.tudominio.com/notifications"
    },
    "bankTransfer": {
      "accountHolder": "Nombre de Tu Empresa",
      "cbu": "NUMERO_CBU",
      "alias": "EMPRESA.ALIAS",
      "bankName": "Nombre del Banco",
      "reconciliationEmail": "pagos@tudominio.com"
    },
    "creditCard": {
      "apiKey": "CARD_API_KEY",
      "merchantId": "MERCHANT_ID",
      "maxInstallments": 18,
      "interestRates": {
        "3": 10.5,
        "6": 21.9,
        "12": 45.6,
        "18": 70.1
      }
    },
    "crypto": {
      "apiKey": "CRYPTO_API_KEY",
      "supportedCoins": ["BTC", "ETH", "USDT"],
      "confirmations": 2,
      "exchangeRateBuffer": 0.05
    }
  }
}
```

## Consideraciones de Seguridad

- Claves API almacenadas de forma segura en variables de entorno de Deno Deploy
- HTTPS obligatorio para todas las transacciones
- Validación de firma de webhook para notificaciones de pago
- Cumplimiento con las leyes argentinas de protección de datos
- Auditorías y monitoreo de seguridad regulares
- Implementación de medidas antifraude para transacciones con tarjeta
- Verificación de geolocalización basada en IP
- Puntuación de riesgo para transacciones de alto valor

## Pruebas

Hay entornos de prueba disponibles para todos los métodos de pago:

- Sandbox de Mercado Pago: [Portal del Desarrollador](https://developers.mercadopago.com)
- Entorno de prueba de Rapipago/Pago Fácil: Disponible a través de agregadores de pago
- Pruebas de integración bancaria: Proporcionadas por partners de gateway
- Pruebas de tarjeta de crédito: Tarjetas de prueba disponibles a través de procesadores
- Pruebas de criptomonedas: Usar redes testnet

## Cronograma de Implementación

1. **Fase 1**: Integración de Mercado Pago (1-2 semanas)
2. **Fase 2**: Procesamiento de tarjetas de crédito con cuotas (2 semanas)
3. **Fase 3**: Opciones de pago en efectivo (Rapipago/Pago Fácil) (1 semana)
4. **Fase 4**: Integración de transferencia bancaria (1 semana)
5. **Fase 5**: Opciones de pago con criptomonedas (1 semana)
6. **Fase 6**: Pruebas y optimización (2 semanas)

## Referencias

- [Documentación para Desarrolladores de Mercado Pago](https://developers.mercadopago.com)
- [Regulaciones de Pago Electrónico del Banco Central Argentino](https://www.bcra.gob.ar)
- [Guía de Integración de Rapipago](https://www.rapipago.com.ar/rapipagoWeb/for-business)
- [Estadísticas de Comercio Electrónico Argentino](https://www.cace.org.ar)
- [Procesamiento de Pagos con Criptomonedas](https://business.crypto.com)