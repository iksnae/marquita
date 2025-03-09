# Sistema de Plantillas de Marquita

Este documento describe el sistema de plantillas para el Proyecto Marquita, basado en las ideas del prototipo knockoutmde.

## Arquitectura de Plantillas

El sistema de plantillas de Marquita está construido en torno a las capacidades de generación de sitios estáticos de SvelteKit, optimizado para el despliegue en GitHub Pages con mínima sobrecarga técnica.

### Componentes Principales

1. **Repositorio de Plantilla Base**
   - Fundamento para todas las plantillas de negocios
   - Estructura de diseño principal y componentes
   - Flujo de trabajo de GitHub Actions para despliegue automatizado
   - Framework de pruebas y configuración

2. **Variantes de Plantillas Específicas para Negocios**
   - Negocio basado en comercio electrónico/productos (tienda)
   - Negocio basado en servicios
   - Portafolio/Profesional creativo
   - Página de aterrizaje/Producto único

3. **Sistema de Configuración**
   - Archivo de configuración central para información de marca
   - Personalización de tema a través de variables
   - Esquema de contenido para diferentes tipos de negocios
   - Configuraciones específicas por región

4. **Puntos de Integración**
   - Conexiones de servicios API Deno
   - Slots de componentes para procesamiento de pagos
   - Hooks de automatización de marketing
   - Integración de analíticas

## Estructura de Plantilla Base

La plantilla base incluye la siguiente estructura de directorios:

```
marquita-base-template/
├── .github/
│   └── workflows/
│       └── deploy.yml          # Flujo de trabajo de despliegue en GitHub Pages
├── src/
│   ├── app.css                 # CSS global con Tailwind
│   ├── app.html                # Plantilla HTML
│   ├── lib/
│   │   ├── components/         # Componentes UI reutilizables
│   │   │   ├── Header.svelte
│   │   │   ├── Footer.svelte
│   │   │   ├── LanguageSwitcher.svelte
│   │   │   └── ... 
│   │   ├── config/
│   │   │   ├── brand.js        # Configuración de marca
│   │   │   ├── navigation.js   # Estructura de navegación
│   │   │   └── theme.js        # Configuración de tema
│   │   ├── i18n/
│   │   │   ├── index.ts        # Configuración i18n
│   │   │   └── locales/        # Archivos de traducción
│   │   └── utils/              # Funciones de utilidad
│   └── routes/                 # Rutas SvelteKit
├── static/                     # Activos estáticos
├── config/                     # Archivos de configuración editables por IA
│   ├── site.json              # Metadatos del sitio
│   ├── pages.json             # Contenido de páginas
│   ├── products.json          # Información de productos (e-commerce)
│   └── services.json          # Información de servicios (basado en servicios)
├── svelte.config.js
├── tailwind.config.js
├── tsconfig.json
└── vite.config.ts
```

## Personalización Asistida por IA

La plantilla está diseñada para ser fácilmente personalizable a través de asistencia de IA:

### Enfoque Basado en Configuración

1. **Configuración de Marca** (`config/site.json`):
   ```json
   {
     "name": "Nombre del Negocio",
     "tagline": "Tu eslogan memorable",
     "logo": {
       "text": "Nombre del Negocio", 
       "image": "/logo.png",
       "useImage": false
     },
     "colors": {
       "primary": "#e11d48",
       "secondary": "#4b5563"
     },
     "contact": {
       "email": "info@ejemplo.com",
       "phone": "+1 234 567 8900",
       "address": "Calle Principal 123, Ciudad, País"
     },
     "social": {
       "instagram": "usuario",
       "facebook": "nombrepagina",
       "twitter": "usuario"
     }
   }
   ```

2. **Configuración de Contenido** (`config/pages.json`):
   ```json
   {
     "home": {
       "hero": {
         "title": "Bienvenido a Nombre del Negocio",
         "subtitle": "Tu subtítulo convincente aquí",
         "ctaPrimary": {
           "text": "Comenzar",
           "link": "/productos/"
         },
         "ctaSecondary": {
           "text": "Saber Más",
           "link": "/acerca/"
         }
       },
       "sections": [
         {
           "type": "feature",
           "title": "Lo Que Ofrecemos",
           "items": [
             {
               "title": "Característica 1",
               "description": "Descripción de característica 1"
             },
             {
               "title": "Característica 2",
               "description": "Descripción de característica 2"
             }
           ]
         }
       ]
     }
   }
   ```

### Comentarios Específicos para IA

Todas las plantillas incluyen comentarios específicos para guiar las modificaciones de IA:

```svelte
<!-- AI-EDIT-POINT: Título del Hero -->
<h1 class="text-4xl font-bold">{hero.title}</h1>

<!-- AI-EDIT-POINT: Elementos de Navegación
     Para modificar la navegación, edita el archivo config/navigation.js
     o modifica directamente los elementos abajo. Cada elemento debe tener:
     - text: Texto de visualización
     - href: Destino del enlace
     - external: (opcional) Establecer a true para enlaces externos
-->
```

## Variantes de Plantillas

### Plantilla de Comercio Electrónico

Enfocada en visualización de productos, carrito de compras y experiencia de checkout:

- Catálogo de productos con filtrado/ordenación
- Páginas de detalle de productos
- Funcionalidad de carrito de compras
- Proceso de checkout con integración de pagos
- Confirmación y seguimiento de pedidos

### Plantilla de Negocio de Servicios

Centrada en ofertas de servicios y generación de leads:

- Presentación de servicios
- Tablas de precios
- Programación de reservas/citas
- Testimonios y casos de estudio
- Formularios de contacto con enrutamiento de leads

### Plantilla de Portafolio

Diseñada para profesionales creativos:

- Presentación de proyectos/trabajos
- Galería de portafolio filtrable
- Sección de acerca de/biografía
- Testimonios de clientes
- Información de contacto

### Plantilla de Página de Aterrizaje

Optimizada para conversiones:

- Sección hero atractiva
- Destacados de características
- Sección de prueba social
- Acordeón de preguntas frecuentes
- Elementos fuertes de llamada a la acción

## Integración con Servicios API Deno

Cada plantilla incluye puntos de integración para conectarse a servicios Deno Deploy:

1. **Procesamiento de Pagos**:
   ```javascript
   // src/lib/api/payment.js
   import { config } from '$lib/config';

   export async function processPayment(paymentDetails) {
     const apiUrl = config.api.payment;
     
     try {
       const response = await fetch(apiUrl, {
         method: 'POST',
         headers: {
           'Content-Type': 'application/json',
         },
         body: JSON.stringify(paymentDetails),
       });
       
       return await response.json();
     } catch (error) {
       console.error('Error de procesamiento de pago:', error);
       throw new Error('El pago falló al procesar');
     }
   }
   ```

2. **Manejo de Formularios**:
   ```javascript
   // src/lib/api/forms.js
   import { config } from '$lib/config';

   export async function submitForm(formData, formType = 'contact') {
     const apiUrl = config.api.forms;
     
     try {
       const response = await fetch(apiUrl, {
         method: 'POST',
         headers: {
           'Content-Type': 'application/json',
           'X-Form-Type': formType,
         },
         body: JSON.stringify(formData),
       });
       
       return await response.json();
     } catch (error) {
       console.error('Error de envío de formulario:', error);
       throw new Error('El formulario falló al enviar');
     }
   }
   ```

## Despliegue en GitHub Pages

La plantilla incluye un flujo de trabajo estándar de GitHub Actions para despliegue automatizado:

```yaml
name: Deploy to GitHub Pages

on:
  push:
    branches: ["main"]
  workflow_dispatch:

# Establece permisos del GITHUB_TOKEN para permitir despliegue en GitHub Pages
permissions:
  contents: read
  pages: write
  id-token: write

# Permite solo un despliegue concurrente
concurrency:
  group: "pages"
  cancel-in-progress: true

jobs:
  # Trabajo de pruebas
  test:
    name: Run Tests
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4
      
      - name: Setup Node
        uses: actions/setup-node@v4
        with:
          node-version: "20"
          cache: 'npm'
      
      - name: Install dependencies
        run: npm install
      
      - name: Run tests
        run: npm test
        env:
          NODE_ENV: test

  # Trabajo de construcción
  build:
    runs-on: ubuntu-latest
    needs: test
    if: github.ref == 'refs/heads/main'
    steps:
      - name: Checkout
        uses: actions/checkout@v4
      
      - name: Setup Node
        uses: actions/setup-node@v4
        with:
          node-version: "20"
          cache: 'npm'
      
      - name: Install dependencies
        run: npm install
      
      - name: Build
        run: npm run build
        env:
          NODE_ENV: production
      
      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: ./build

  # Trabajo de despliegue
  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    needs: build
    if: github.ref == 'refs/heads/main'
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

## Flujo de Trabajo Asistido por IA

El flujo de trabajo típico para crear un nuevo sitio con Marquita:

1. **Selección de Plantilla**:
   - La IA guía al usuario para seleccionar la variante de plantilla apropiada
   - El usuario proporciona información básica del negocio

2. **Creación de Repositorio**:
   - La IA crea un nuevo repositorio a partir de la plantilla seleccionada
   - Configura GitHub Pages con la configuración adecuada

3. **Personalización de Marca**:
   - La IA ayuda al usuario a personalizar los archivos de configuración
   - Actualiza colores de marca, logo y contenido

4. **Creación de Contenido**:
   - La IA asiste con la creación de contenido de página
   - Ayuda con el texto, selección de imágenes y diseño

5. **Configuración de Integración**:
   - La IA configura las integraciones de terceros necesarias
   - Establece el procesamiento de pagos para la región del usuario

6. **Despliegue y Pruebas**:
   - La IA desencadena el flujo de trabajo de despliegue
   - Verifica que el sitio funcione correctamente

## Hoja de Ruta de Implementación

1. **Fase 1: Desarrollo de Plantilla Base** (2 semanas)
   - Extraer componentes principales de knockoutmde
   - Crear sistema de configuración
   - Configurar personalización básica de tema
   - Implementar framework i18n

2. **Fase 2: Plantillas Variantes** (3 semanas)
   - Desarrollar plantilla de e-commerce
   - Crear plantilla de negocio de servicios
   - Construir plantilla de portafolio
   - Diseñar plantilla de página de aterrizaje

3. **Fase 3: Puntos de Integración** (2 semanas)
   - Implementar conexiones de servicios API Deno
   - Crear componentes de procesamiento de pagos
   - Configurar manejo y envío de formularios
   - Añadir integración de analíticas

4. **Fase 4: Capa de Asistencia IA** (2 semanas)
   - Añadir comentarios y guías específicas para IA
   - Crear documentación para interacción con IA
   - Desarrollar utilidades auxiliares para operaciones comunes
   - Construir ejemplos de prompts para creación de sitios

5. **Fase 5: Pruebas y Refinamiento** (1 semana)
   - Probar todas las plantillas en diferentes dispositivos
   - Verificar proceso de despliegue
   - Optimizar rendimiento
   - Documentar el sistema completo