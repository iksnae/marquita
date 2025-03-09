# Proyecto Marquita

> Kit de inicio de negocios de código abierto con gestión asistida por IA, despliegue en GitHub Pages e integraciones con terceros para emprendedores sin conocimientos técnicos.

## Descripción general

El Proyecto Marquita es un conjunto de herramientas diseñadas para capacitar a emprendedores sin conocimientos técnicos para establecer y mantener su presencia comercial en línea sin requerir conocimientos técnicos especializados o intermediarios costosos. El proyecto combina el poder del alojamiento de GitHub Pages con el desarrollo asistido por IA para crear una solución de costo cero a bajo para propietarios de pequeñas empresas en todo el mundo.

## Propuesta de valor principal

- **Despliegue de costo cero a bajo**: Aprovecha el alojamiento gratuito de GitHub Pages con dominio personalizado y soporte SSL
- **Gestión asistida por IA**: Permite a usuarios no técnicos construir y actualizar su presencia en línea a través de conversaciones con asistentes de IA (OpenAI, Claude)
- **CI/CD automatizado**: Proporciona un pipeline completamente automatizado para construir, probar y desplegar cambios en el sitio web
- **Herramientas de marketing integradas**: Incluye capacidades para marketing en redes sociales para dirigir tráfico
- **Integraciones modulares con terceros**: Admite procesadores de pago, funcionalidad de comercio electrónico y alojamiento de medios a través de arquitectura de plugins

## Documentación

### Arquitectura y diseño
- [Descripción general de la arquitectura](docs/architecture.es.md) - Arquitectura técnica central
- [Sistema de plantillas](docs/template-system.es.md) - Diseño del sistema de plantillas
- [Desarrollo asistido por IA](docs/ai-assisted-development.es.md) - Cómo los agentes de IA gestionan la creación y actualización del sitio

### Integraciones de pago
- [Descripción general de integración de pagos](docs/payment-integrations/index.es.md) - Enfoque general de integración de pagos
- [Argentina](docs/payment-integrations/argentina.es.md) - Mercado Pago, Rapipago/Pago Fácil y tarjetas con pagos en cuotas
- [Brasil](docs/payment-integrations/brazil.es.md) - Pix, Boleto y métodos de pago brasileños
- [Chile](docs/payment-integrations/chile.es.md) - Webpay Plus, Khipu, Multicaja y MACH
- [Colombia](docs/payment-integrations/colombia.es.md) - Integración con PSE y DaviPlata
- [Costa Rica](docs/payment-integrations/costa-rica.es.md) - SINPE Móvil, transferencias SINPE y tarjetas locales
- [Ecuador](docs/payment-integrations/ecuador.es.md) - Tarjetas de crédito/débito, Payphone y redes de pago en efectivo
- [Guatemala](docs/payment-integrations/guatemala.es.md) - Tarjetas de crédito/débito, transferencias bancarias y billeteras móviles
- [México](docs/payment-integrations/mexico.es.md) - SPEI, Oxxo Pay y pagos con tarjeta en cuotas
- [Panamá](docs/payment-integrations/panama.es.md) - Tarjetas de crédito/débito, transferencias ACH, Yappy y Nequi
- [Perú](docs/payment-integrations/peru.es.md) - PagoEfectivo, Yape, transferencias CCI y SafetyPay
- [EE.UU.](docs/payment-integrations/usa.es.md) - Tarjeta de crédito, ACH y métodos de pago alternativos

### Guías
- [Cómo empezar](docs/getting-started.es.md) - Guía de inicio rápido para nuevos usuarios
- [Hoja de ruta del proyecto](docs/roadmap.es.md) - Plan de desarrollo e hitos

## Componentes del proyecto

Marquita consta de varios repositorios interconectados:

1. **Framework central** (este repositorio): Documentación, guías y coordinación del proyecto
2. **Plantilla base**: [marquita-base-template](https://github.com/iksnae/marquita-base-template) - Base para todas las plantillas de sitios web de negocios
3. **Plantillas específicas para negocios** (próximamente):
   - Plantilla de comercio electrónico
   - Plantilla de negocio de servicios
   - Plantilla de portafolio
   - Plantilla de página de aterrizaje
4. **Servicios API de Deno** (próximamente): Servicios backend para manejar integraciones y operaciones seguras
5. **Automatización de marketing** (próximamente): Herramientas para distribución de contenido en plataformas

## Cómo empezar

Para emprendedores que buscan crear un sitio con Marquita:

1. Revisa la guía de [Cómo empezar](docs/getting-started.es.md)
2. Elige una plantilla que se adapte a las necesidades de tu negocio
3. Sigue el proceso de configuración guiado con asistencia de IA
4. Configura tu sitio con la información de tu negocio
5. Despliega en GitHub Pages y conecta tu dominio

Para desarrolladores que buscan contribuir:

1. Revisa la documentación del [Sistema de plantillas](docs/template-system.es.md)
2. Explora la [Descripción general de la arquitectura](docs/architecture.es.md)
3. Consulta la [Hoja de ruta del proyecto](docs/roadmap.es.md) para conocer las áreas de enfoque actuales
4. Sigue nuestras [directrices de contribución](CONTRIBUTING.es.md)

## Plantillas

Marquita ofrece varias plantillas especializadas para diferentes necesidades de negocio:

- **Plantilla de comercio electrónico**: Para negocios basados en productos, con catálogos de productos, carritos de compra y flujos de pago
- **Plantilla de negocio de servicios**: Para proveedores de servicios, con sistemas de reserva y showcases de servicios
- **Plantilla de portafolio**: Para profesionales creativos, con galerías de proyectos y exhibición de casos de estudio
- **Plantilla de página de aterrizaje**: Para campañas de marketing, con diseños optimizados para conversión

## Soporte regional

Marquita está diseñado para funcionar globalmente, con optimizaciones especiales para:

- **Argentina**: Integración con Mercado Pago, Rapipago/Pago Fácil y pagos en cuotas
- **Brasil**: Integración con Pix, Boleto y pagos en cuotas
- **Chile**: Soporte para Webpay Plus, Khipu, Multicaja y MACH
- **Colombia**: Integración con sistemas de pago PSE y DaviPlata
- **Costa Rica**: Soporte para SINPE Móvil, transferencias SINPE y tarjetas locales
- **Ecuador**: Soporte para tarjetas de crédito/débito, Payphone y redes de pago en efectivo
- **Guatemala**: Integración con tarjetas locales, transferencias bancarias y billeteras móviles
- **México**: Soporte para SPEI, Oxxo Pay y tarjetas con pagos en cuotas
- **Panamá**: Soporte para tarjetas, transferencias ACH, Yappy y Nequi
- **Perú**: Soporte para PagoEfectivo, Yape, transferencias CCI y SafetyPay
- **EE.UU.**: Soporte para ACH, tarjetas de crédito y billeteras digitales

Se añadirán regiones adicionales basadas en contribuciones de la comunidad.

## Stack tecnológico

- **Frontend**: SvelteKit 2+ / Svelte 5 para generación de sitios estáticos
- **Estilos**: Tailwind CSS para diseño responsivo
- **Despliegue**: GitHub Pages para alojamiento gratuito
- **CI/CD**: GitHub Actions para despliegue automatizado
- **APIs Backend**: Deno Deploy para funciones serverless
- **Internacionalización**: Soporte i18n incorporado para múltiples idiomas

## Contribuir

Damos la bienvenida a contribuciones de desarrolladores, diseñadores y emprendedores. Consulta nuestras [directrices de contribución](CONTRIBUTING.es.md) para comenzar.

## Licencia

Este proyecto está licenciado bajo la Licencia MIT - consulta el archivo [LICENSE](LICENSE) para más detalles.