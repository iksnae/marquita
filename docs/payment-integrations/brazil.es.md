# Integraciones de Pago de Brasil

## Descripción general

Este documento describe la integración de métodos de pago específicos de Brasil en la plataforma Marquita. Brasil tiene requisitos y preferencias de pago únicos que son esenciales para el éxito en el mercado de comercio electrónico brasileño. Los métodos de pago locales tienen altas tasas de adopción y a menudo ofrecen tarifas más bajas que las opciones internacionales.

## Métodos de Pago Soportados

### Pix

**Descripción**: Un sistema de pago instantáneo creado y gestionado por el Banco Central de Brasil, que permite transferencias en tiempo real las 24 horas, los 7 días de la semana.

**Beneficios**:
- Transferencias instantáneas (24/7, incluyendo fines de semana y días festivos)
- Tarifas bajas o nulas para consumidores y empresas
- Alta seguridad con cifrado de extremo a extremo
- Amplia adopción (más de 100 millones de usuarios en Brasil)
- Funciona a través de códigos QR o búsquedas de claves (correo electrónico, número de teléfono, identificación fiscal)

**Implementación**:
- Generación de códigos QR y claves de pago para clientes
- Recepción de notificaciones en tiempo real vía webhooks cuando se completa el pago
- Sin detalles de tarjeta ni datos sensibles que almacenar

### Boleto Bancário

**Descripción**: Un método de pago tradicional brasileño que genera un comprobante (boleto) que puede pagarse en bancos, cajeros automáticos, tiendas de conveniencia o a través de la banca por internet.

**Beneficios**:
- No es necesario tener una cuenta bancaria o tarjeta de crédito para realizar compras
- Método de pago ampliamente aceptado y confiable en Brasil
- Tasas de fraude más bajas que las tarjetas de crédito
- Preferido por clientes sin cuentas bancarias o tarjetas de crédito
- Opción en efectivo para clientes que prefieren no usar pagos digitales

**Implementación**:
- Generación de boletos con información de pago
- Seguimiento del estado del pago mediante ID de boleto
- Manejo de fechas de vencimiento (típicamente 1-3 días)

### Tarjetas de Crédito Brasileñas

**Descripción**: Soporte para tarjetas de crédito locales brasileñas, incluidos pagos en cuotas ("parcelamento").

**Beneficios**:
- Soporte para redes de tarjetas exclusivamente locales como Elo e Hipercard
- Soporte para pagos en cuotas (crucial para el mercado brasileño)
- Integración con sistemas antifraude adaptados al mercado brasileño

**Implementación**:
- Soporte para opciones de pago en cuotas (pagos de 2-12x)
- Visualización clara de planes de cuotas e intereses
- Bancos adquirentes locales para mejores tasas de aprobación

### Billeteras Digitales / Cripto

**Descripción**: Soporte para PicPay, Mercado Pago y opciones de pago con criptomonedas locales.

**Beneficios**:
- Popular entre consumidores jóvenes y con conocimientos tecnológicos
- A menudo tienen ofertas promocionales para fomentar su uso
- Tarifas más bajas que el procesamiento tradicional de tarjetas de crédito
- Creciente adopción de criptomonedas en Brasil

**Implementación**:
- Integración de API con proveedores populares de billeteras
- Generación de códigos QR para pagos en aplicaciones
- Manejo de callbacks de confirmación de pago

## Arquitectura de Integración

### Implementación de API Deno Deploy

```typescript
// Estructura de implementación de ejemplo para métodos de pago de Brasil
import { serve } from "https://deno.land/std/http/server.ts";
import { PixGateway } from "./gateways/pix.ts";
import { BoletoGateway } from "./gateways/boleto.ts";
import { BrazilCardGateway } from "./gateways/brazil-cards.ts";
import { config } from "../config.ts";

serve(async (req) => {
  const url = new URL(req.url);
  const path = url.pathname;
  
  // Endpoint de integración PIX
  if (path === "/api/payments/pix") {
    const pix = new PixGateway(config.pix);
    return await pix.generatePayment(req);
  }
  
  // Endpoint de integración de Boleto
  if (path === "/api/payments/boleto") {
    const boleto = new BoletoGateway(config.boleto);
    return await boleto.generateVoucher(req);
  }
  
  // Procesamiento de tarjeta de crédito brasileña con cuotas
  if (path === "/api/payments/credit-card") {
    const cardProcessor = new BrazilCardGateway(config.creditCard);
    return await cardProcessor.processWithInstallments(req);
  }
  
  // Webhooks de notificación de pago
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
        return new Response("Tipo de pago desconocido", { status: 400 });
    }
  }
  
  return new Response("No encontrado", { status: 404 });
});
```

## Recorrido del Usuario

1. **Inicio del Checkout**:
   - El usuario navega a la página de checkout
   - Selecciona el método de pago (Pix, Boleto, Tarjeta de Crédito, etc.)

2. **Flujo de Pix**:
   - El sistema genera el código QR de Pix y la clave para copiar/pegar
   - El usuario escanea el código QR con la aplicación bancaria o copia la clave Pix
   - La confirmación del pago ocurre en tiempo real
   - El usuario recibe confirmación en el sitio del comerciante

3. **Flujo de Boleto**:
   - El sistema genera el boleto con instrucciones de pago
   - El usuario recibe el Boleto vía email/descarga
   - El usuario paga el Boleto en el banco/casa de lotería/app bancaria
   - La confirmación del pago ocurre dentro de 1-2 días hábiles
   - El procesamiento del pedido comienza después de la confirmación del pago

4. **Flujo de Tarjeta de Crédito con Cuotas**:
   - El usuario introduce los detalles de la tarjeta
   - Selecciona el número de cuotas (1x a 12x)
   - El sistema muestra el monto de la cuota con intereses (si aplica)
   - El procesador de pago maneja la autorización y la configuración de cuotas
   - El usuario recibe confirmación en el sitio del comerciante

## Configuración

Los gateways de pago se configuran a través de variables de entorno o archivos de configuración:

```json
{
  "brazil": {
    "pix": {
      "apiKey": "PIX_API_KEY",
      "merchantId": "PIX_MERCHANT_ID",
      "certPath": "certificates/pix_cert.pem",
      "webhookUrl": "https://api.tudominio.com/notifications",
      "merchantName": "Nombre de Tu Tienda",
      "merchantCity": "São Paulo"
    },
    "boleto": {
      "apiKey": "BOLETO_API_KEY",
      "token": "BOLETO_TOKEN",
      "expirationDays": 3,
      "webhookUrl": "https://api.tudominio.com/notifications",
      "instructions": "Pague en cualquier banco o casa de lotería"
    },
    "creditCard": {
      "apiKey": "CARD_API_KEY",
      "merchantId": "MERCHANT_ID",
      "maxInstallments": 12,
      "interestRate": 1.99,
      "interestStartsAt": 2, // Los intereses comienzan a partir de 2 cuotas
      "antifraudEnabled": true
    }
  }
}
```

## Consideraciones de Seguridad

- Claves API almacenadas de forma segura en variables de entorno de Deno Deploy
- HTTPS obligatorio para todas las transacciones
- Firmas digitales para transacciones Pix
- Validación de webhook para notificaciones de pago
- Cumplimiento con la LGPD (Lei Geral de Proteção de Dados) - ley brasileña de protección de datos

## Pruebas

Hay entornos de prueba disponibles para todos los métodos de pago:

- Entorno sandbox de Pix: [Portal del Desarrollador](https://developers.bcb.gov.br/sandbox)
- Generación de prueba de Boleto: [Prueba de Boleto](https://www.boletobancario.com/boleto/sandbox)
- Entorno de prueba de tarjeta de crédito: Disponible a través de proveedores de gateway de pago como Cielo, PagSeguro, etc.

## Cronograma de Implementación

1. **Fase 1**: Integración de Pix (1-2 semanas)
2. **Fase 2**: Procesamiento de tarjeta de crédito con cuotas (2 semanas)
3. **Fase 3**: Integración de Boleto (1 semana)
4. **Fase 4**: Pruebas y optimización (1 semana)

## Referencias

- [Documentación de la API de Pix (Banco Central de Brasil)](https://www.bcb.gov.br/estabilidadefinanceira/pix)
- [Guía de Integración de Boleto](https://www.boletobancario.com/documentacao)
- [Procesamiento de Tarjeta de Crédito Brasileña](https://developers.cielo.com.br/api-portal/es-es/documentacion/tarjeta-de-credito)
- [Guía de Cumplimiento con la LGPD](https://www.gov.br/cidadania/pt-br/acesso-a-informacao/lgpd)