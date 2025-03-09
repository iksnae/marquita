# Integraciones de Pago de Colombia

## Descripción general

Este documento describe la integración de métodos de pago específicos de Colombia en la plataforma Marquita. Colombia tiene requisitos y preferencias de pago únicos que difieren de los estándares internacionales, lo que hace que la integración local sea esencial para las empresas que operan en este mercado.

## Métodos de Pago Soportados

### PSE (Pago Seguro en Línea)

**Descripción**: Una solución de pago por transferencia bancaria ampliamente utilizada en Colombia para transacciones online.

**Beneficios**:
- Sin comisiones para los consumidores
- Las comisiones por transacción para las empresas oscilan entre el 1% y el 2,5%
- Pagos locales, eliminando las comisiones internacionales
- Sistema seguro y regulado por el gobierno

**Implementación**:
- Los usuarios serán redirigidos a la página segura de su banco donde pueden autorizar el pago
- Al completarse, los usuarios serán devueltos a la plataforma de comercio electrónico con una confirmación de pago

### DaviPlata

**Descripción**: Una solución de pago móvil de Bancolombia, utilizada principalmente para pagos móviles y transferencias bancarias.

**Beneficios**:
- Sin comisiones para transferencias nacionales entre cuentas de Bancolombia
- Experiencia optimizada para móviles
- Transferencia fácil de fondos
- Sistema de pago local sin comisiones internacionales

**Implementación**:
- Los usuarios seleccionan DaviPlata como su método de pago
- El pago se completa a través de la aplicación DaviPlata en dispositivos móviles
- La confirmación de la transacción se muestra inmediatamente después de completarse

## Arquitectura de Integración

### Implementación de API Deno Deploy

```typescript
// Estructura de implementación de ejemplo para métodos de pago de Colombia
import { serve } from "https://deno.land/std/http/server.ts";
import { PSEGateway } from "./gateways/pse.ts";
import { DaviPlataGateway } from "./gateways/daviplata.ts";
import { config } from "../config.ts";

serve(async (req) => {
  const url = new URL(req.url);
  const path = url.pathname;
  
  // Endpoint de integración PSE
  if (path === "/api/payments/pse") {
    const pse = new PSEGateway(config.pse);
    return await pse.processPayment(req);
  }
  
  // Endpoint de integración DaviPlata
  if (path === "/api/payments/daviplata") {
    const daviplata = new DaviPlataGateway(config.daviplata);
    return await daviplata.processPayment(req);
  }
  
  // Endpoint de estado de pago
  if (path === "/api/payments/status") {
    // Lógica de verificación de estado de pago
  }
  
  return new Response("No encontrado", { status: 404 });
});
```

## Recorrido del Usuario

1. **Inicio del Checkout**:
   - El usuario navega a la página de checkout
   - Selecciona el método de pago (PSE o DaviPlata)

2. **Flujo de PSE**:
   - El usuario selecciona un banco del menú desplegable
   - Es redirigido a la página de inicio de sesión segura del banco
   - Completa la autenticación y confirma el pago
   - Regresa al sitio del comerciante con la confirmación

3. **Flujo de DaviPlata**:
   - El usuario recibe instrucciones de pago
   - Completa el pago en la aplicación DaviPlata
   - La API de Deno Deploy confirma el estado del pago
   - El usuario ve la confirmación en el sitio del comerciante

## Configuración

Los gateways de pago se configuran a través de variables de entorno o archivos de configuración:

```json
{
  "colombia": {
    "pse": {
      "merchantId": "TU_ID_DE_COMERCIANTE",
      "apiKey": "TU_CLAVE_API",
      "redirectUrl": "https://tudominio.com/checkout/complete",
      "notificationUrl": "https://api.tudominio.com/payments/pse/webhook"
    },
    "daviplata": {
      "merchantId": "TU_ID_DAVIPLATA",
      "apiKey": "TU_CLAVE_API_DAVIPLATA",
      "callbackUrl": "https://api.tudominio.com/payments/daviplata/callback"
    }
  }
}
```

## Consideraciones de Seguridad

- Claves API almacenadas de forma segura en variables de entorno de Deno Deploy
- HTTPS obligatorio para todas las transacciones
- No se almacenan datos de pago sensibles en el código frontend
- Validación de webhook para prevenir notificaciones fraudulentas

## Pruebas

Hay cuentas de prueba disponibles para ambos métodos de pago:

- Entorno de prueba de PSE: [Enlace al portal del desarrollador]
- Sandbox de DaviPlata: [Enlace al portal del desarrollador]

## Cronograma de Implementación

1. **Fase 1**: Integración básica de PSE (2 semanas)
2. **Fase 2**: Integración básica de DaviPlata (2 semanas)
3. **Fase 3**: Funciones avanzadas y manejo de errores (1 semana)
4. **Fase 4**: Pruebas y optimización (1 semana)

## Referencias

- [Documentación de la API de PSE](https://pseapi.com/docs)
- [Guía de Integración de DaviPlata](https://daviplata.com/developers)
- [Portal para Desarrolladores de Bancolombia](https://developer.bancolombia.com)