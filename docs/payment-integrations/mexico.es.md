# Integraciones de Pago de México

## Descripción general

Este documento describe la integración de métodos de pago específicos de México en la plataforma Marquita. México tiene un ecosistema de pagos diverso con una mezcla de opciones de pago tradicionales e innovadoras. La integración con métodos de pago locales es crucial para las empresas que buscan tener éxito en el mercado mexicano, ya que muchos consumidores prefieren opciones locales sobre métodos de pago internacionales.

## Métodos de Pago Soportados

### SPEI (Sistema de Pagos Electrónicos Interbancarios)

**Descripción**: Sistema de pago electrónico interbancario de México, similar al ACH en EE.UU. pero con procesamiento en el mismo día.

**Beneficios**:
- Transferencias bancarias instantáneas o en el mismo día
- Comisiones de transacción más bajas comparadas con tarjetas de crédito (típicamente 1-2%)
- Ampliamente confiable y utilizado en todo México
- Funciona con todos los bancos mexicanos
- Alta seguridad al estar regulado por el Banco de México

**Implementación**:
- Generar una CLABE (número de cuenta bancaria) para cada transacción
- El cliente inicia una transferencia bancaria a través de su portal de banca en línea
- Notificaciones en tiempo real vía webhooks cuando se completa el pago
- Reconciliación automática de pagos

### Oxxo Pay

**Descripción**: Sistema de pago en efectivo que aprovecha la cadena de tiendas de conveniencia más grande de México, Oxxo.

**Beneficios**:
- Alcanza al ~60% de los consumidores mexicanos sin cuentas bancarias
- No se necesita una cuenta bancaria o tarjeta de crédito
- Alta tasa de conversión para ciertos segmentos demográficos
- Método de pago confiable a nivel nacional
- Más de 19,000 ubicaciones físicas en todo México

**Implementación**:
- Generar una referencia/código de barras único para cada transacción
- El cliente recibe la referencia y paga en efectivo en cualquier tienda Oxxo
- Confirmación de pago generalmente dentro de 24-48 horas
- Vence después de un período configurable (normalmente 3-7 días)

### Tarjetas Domésticas Mexicanas

**Descripción**: Soporte para redes de tarjetas locales mexicanas y especificidades del procesamiento de tarjetas de crédito/débito mexicanas.

**Beneficios**:
- Soporte para tarjetas exclusivamente nacionales como Carnet
- Optimización para planes de pago en "meses sin intereses" (MSI)
- Menores tasas de rechazo al utilizar procesamiento local
- Reducción de comisiones por cambio de divisa
- Mejor detección de fraude para tarjetas mexicanas

**Implementación**:
- Integración con procesadores de pago locales (ej., Conekta, OPENPAY)
- Soporte para pagos en cuotas (3, 6, 9, 12, 18 meses)
- Manejo adecuado de requisitos de seguridad específicos de México

### Billeteras Digitales

**Descripción**: Métodos de pago digital populares en México incluyendo Mercado Pago, CoDi y Kueski Pay.

**Beneficios**:
- Base de usuarios creciente, particularmente entre consumidores jóvenes y con conocimientos tecnológicos
- Experiencia de pago más rápida
- A menudo incluyen programas de fidelización y recompensas
- Características de seguridad mejoradas
- Opciones de crédito alternativas en algunos casos (Kueski Pay)

**Implementación**:
- Integración de API con cada proveedor de billetera
- Generación de códigos QR para pagos presenciales
- Flujos de redireccionamiento para autenticación de billetera
- Procesamiento de webhooks para notificaciones de pago

## Arquitectura de Integración

### Implementación de API Deno Deploy

```typescript
// Estructura de implementación de ejemplo para métodos de pago de México
import { serve } from "https://deno.land/std/http/server.ts";
import { SPEIGateway } from "./gateways/spei.ts";
import { OxxoGateway } from "./gateways/oxxo.ts";
import { MexicanCardGateway } from "./gateways/cards.ts";
import { config } from "../config.ts";

serve(async (req) => {
  const url = new URL(req.url);
  const path = url.pathname;
  
  // Endpoint de integración SPEI
  if (path === "/api/payments/spei") {
    const spei = new SPEIGateway(config.mexico.spei);
    return await spei.generateReference(req);
  }
  
  // Endpoint de integración Oxxo
  if (path === "/api/payments/oxxo") {
    const oxxo = new OxxoGateway(config.mexico.oxxo);
    return await oxxo.generateVoucher(req);
  }
  
  // Endpoint de integración de tarjetas mexicanas
  if (path === "/api/payments/cards") {
    const cards = new MexicanCardGateway(config.mexico.cards);
    return await cards.processWithInstallments(req);
  }
  
  // Webhooks de notificación de pago
  if (path === "/api/notifications") {
    const paymentType = req.headers.get("X-Payment-Type");
    
    switch (paymentType) {
      case "spei":
        return await handleSPEINotification(req);
      case "oxxo":
        return await handleOxxoNotification(req);
      case "cards":
        return await handleCardNotification(req);
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
   - Selecciona el método de pago (SPEI, Oxxo, tarjeta, etc.)

2. **Flujo de SPEI**:
   - El sistema genera un número de cuenta CLABE único
   - El usuario recibe la CLABE y la información bancaria
   - El usuario inicia la transferencia desde el portal de su banco
   - El comerciante recibe notificación cuando se completa la transferencia
   - El pedido se procesa automáticamente

3. **Flujo de Oxxo**:
   - El sistema genera un comprobante de Oxxo con referencia de pago
   - El usuario recibe el comprobante vía email/SMS
   - El usuario lleva el comprobante a una tienda Oxxo y paga en efectivo
   - El comerciante recibe notificación cuando se realiza el pago
   - El procesamiento del pedido comienza después de la confirmación del pago

4. **Flujo de Tarjeta de Crédito con Cuotas**:
   - El usuario introduce los detalles de la tarjeta
   - El usuario selecciona la opción de cuotas si aplica (3, 6, 12 meses)
   - El sistema muestra intereses/comisiones para la selección de cuotas
   - El pago se procesa a través de un banco adquirente local
   - El usuario recibe confirmación con el calendario de cuotas

## Configuración

Los gateways de pago se configuran a través de variables de entorno o archivos de configuración:

```json
{
  "mexico": {
    "spei": {
      "apiKey": "SPEI_API_KEY",
      "secretKey": "SPEI_SECRET_KEY",
      "webhookUrl": "https://api.tudominio.com/notifications",
      "returnUrl": "https://tudominio.com/checkout/complete",
      "expiry": 72 // Horas
    },
    "oxxo": {
      "apiKey": "OXXO_API_KEY",
      "webhookUrl": "https://api.tudominio.com/notifications",
      "expiry": 168, // Horas (7 días)
      "description": "Pago por Pedido #"
    },
    "cards": {
      "apiKey": "CARDS_API_KEY",
      "privateKey": "CARDS_PRIVATE_KEY",
      "publicKey": "CARDS_PUBLIC_KEY",
      "merchantId": "MERCHANT_ID",
      "installmentOptions": [3, 6, 9, 12, 18],
      "defaultInterestRate": 0, // Para promociones MSI
      "webhookUrl": "https://api.tudominio.com/notifications",
      "supportedNetworks": ["visa", "mastercard", "amex", "carnet"]
    }
  }
}
```

## Consideraciones de Seguridad

- Claves API almacenadas de forma segura en variables de entorno de Deno Deploy
- HTTPS obligatorio para todas las transacciones
- Cumplimiento con la ley mexicana de protección de datos (Ley Federal de Protección de Datos Personales)
- Firmas digitales para validación de referencias SPEI
- Verificación de firma de webhook para prevenir notificaciones fraudulentas
- Manejo adecuado del cumplimiento PCI DSS para pagos con tarjeta
- Auditorías de seguridad regulares

## Pruebas

Hay entornos de prueba disponibles para todos los métodos de pago:

- Sandbox SPEI: Transferencias bancarias simuladas con respuestas instantáneas
- Entorno de prueba de Oxxo: Comprobantes virtuales que pueden ser "pagados" en el sandbox
- Cuentas de prueba de tarjeta de crédito: Conjunto de tarjetas de prueba para diferentes escenarios (aprobación, rechazo, etc.)

## Cronograma de Implementación

1. **Fase 1**: Integración de SPEI (2 semanas)
2. **Fase 2**: Implementación de Oxxo (1 semana)
3. **Fase 3**: Procesamiento de tarjeta de crédito con cuotas (2 semanas)
4. **Fase 4**: Integraciones de billeteras digitales (2 semanas)
5. **Fase 5**: Pruebas y optimización (1 semana)

## Referencias

- [Documentación Oficial de SPEI](https://www.banxico.org.mx/servicios/sistema-pagos-electronicos-interbancarios-spei.html)
- [Documentación de API de Oxxo Pay](https://www.conekta.com/docs/api#oxxo-cash)
- [Documentación del Gateway de Pago Conekta](https://www.conekta.com/docs/api)
- [Recursos para Desarrolladores de OPENPAY](https://www.openpay.mx/docs/api/)
- [Directrices Regulatorias del Banco de México](https://www.banxico.org.mx)