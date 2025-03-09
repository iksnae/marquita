# Proyecto Marquita - Descripción General de la Arquitectura

## Componentes Principales

### 1. Generación de Sitios Estáticos

El Proyecto Marquita utiliza generadores de sitios estáticos modernos para crear sitios web seguros y de alto rendimiento que pueden alojarse en GitHub Pages. Nuestras principales elecciones técnicas son:

- **SvelteKit**: Para aplicaciones web interactivas y sitios con contenido dinámico
- **Astro**: Para sitios web centrados en contenido con necesidades mínimas de JavaScript

Ambos frameworks soportan la generación de sitios estáticos, lo que permite el despliegue en GitHub Pages mientras se mantienen excelentes características de rendimiento y seguridad.

### 2. Capa de Servicios API

Para admitir funcionalidades dinámicas sin requerir infraestructura de servidor, Marquita aprovecha:

- **Deno Deploy**: Para endpoints de API serverless que manejan:
  - Integraciones con terceros
  - Operaciones seguras
  - Transformaciones de datos
  - Autenticación

Deno Deploy proporciona una solución rentable con un generoso nivel gratuito (100K solicitudes/día), haciéndolo accesible para emprendedores que inician sus negocios.

### 3. Framework de Integración

Marquita proporciona un sistema modular de integración para conectar con servicios de terceros:

- **Procesamiento de Pagos**: Pasarelas de pago específicas por región (PSE/DaviPlata para Colombia, etc.)
- **Plataformas de Comercio Electrónico**: Catálogo de productos y gestión de inventario
- **Alojamiento de Medios**: Soluciones de almacenamiento de imágenes, videos y archivos
- **Analítica**: Seguimiento de visitantes e insights de negocio

### 4. Gestión Asistida por IA

Un diferenciador clave para Marquita es su capacidad de gestión asistida por IA:

- Interfaz de lenguaje natural para actualizaciones del sitio
- Generación y despliegue automatizado de código
- Creación y optimización de contenido
- Recomendaciones de SEO e implementación

## Arquitectura de Despliegue

```
+------------------+      +------------------+      +------------------+
|                  |      |                  |      |                  |
|  Repositorio     |      |  GitHub Actions  |      |  Alojamiento     |
|  GitHub          +----->+  Pipeline CI/CD  +----->+  GitHub Pages    |
|                  |      |                  |      |                  |
+------------------+      +------------------+      +------------------+
         ^                                                  ^
         |                                                  |
         |                                                  |
         |                                                  |
+--------+---------+                              +---------v--------+
|                  |                              |                  |
|  Asistente de IA |                              |  Sitio Web       |
|  (Claude/OpenAI) |                              |  Estático        |
|                  |                              |                  |
+------------------+                              +------------------+
                                                          ^
                                                          |
                                                          |
                                                  +-------+--------+
                                                  |                |
                                                  |  Servicios API |
                                                  |  Deno Deploy   |
                                                  |                |
                                                  +----------------+
```

## Personalización Regional

Marquita está diseñado para apoyar negocios globalmente, con adaptaciones específicas para requisitos regionales:

- **Sistemas de Pago**: Pasarelas y métodos de pago específicos por región
- **Cumplimiento**: Plantillas para requisitos legales regionales (políticas de privacidad, términos de servicio)
- **Localización**: Soporte multi-idioma y plantillas de contenido regional

## Consideraciones de Seguridad

La arquitectura prioriza la seguridad a través de:

- Generación de sitios estáticos (reduciendo la superficie de ataque)
- Aplicación de HTTPS a través de GitHub Pages
- Gestión de claves API mediante variables de entorno
- Ámbitos de permisos mínimos para integraciones de terceros

## Escalabilidad

La arquitectura soporta la escalabilidad desde proyectos personales hasta negocios en crecimiento:

- GitHub Pages proporciona capacidades de CDN para entrega global de contenido
- Deno Deploy escala automáticamente según la demanda
- Integración con servicios profesionales a medida que crecen las necesidades del negocio