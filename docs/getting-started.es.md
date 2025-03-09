# Cómo empezar con el Proyecto Marquita

## Requisitos previos

Antes de comenzar, asegúrate de tener lo siguiente:

- Una cuenta de GitHub
- Comprensión básica de tecnologías web (útil pero no obligatoria)
- Una idea de negocio o un negocio existente para llevar en línea

## Inicio rápido

### 1. Elige una plantilla

Marquita ofrece varias plantillas prediseñadas para diferentes tipos de negocios:

- **Tienda**: Para negocios basados en productos
- **Servicios**: Para proveedores de servicios (consultores, agencias, etc.)
- **Portafolio**: Para profesionales creativos
- **Página de aterrizaje**: Para campañas de marketing o negocios de un solo producto

### 2. Haz un fork del repositorio de la plantilla

1. Navega al repositorio de la plantilla de tu elección
2. Haz clic en el botón "Fork"
3. Nombra tu repositorio (esto será parte de la URL de tu sitio web)

### 3. Configura tu sitio

1. Edita el archivo `config.json` para personalizar:
   - Nombre del negocio
   - Esquema de colores
   - Logo e imágenes
   - Información de contacto
   - Productos/servicios

### 4. Despliega con GitHub Pages

1. En tu repositorio, ve a Configuración > Pages
2. Selecciona la rama principal como fuente
3. Haz clic en Guardar
4. Tu sitio estará en vivo en `https://[nombre-usuario].github.io/[nombre-repositorio]/`

### 5. Configura un dominio personalizado (Opcional)

1. Compra un dominio en cualquier registrador de dominios
2. En tu repositorio, ve a Configuración > Pages
3. Ingresa tu dominio personalizado
4. Actualiza la configuración DNS de tu dominio según las instrucciones

## Uso de asistencia de IA

Marquita está diseñado para trabajar con asistentes de IA para ayudar a gestionar y actualizar tu sitio:

### Creación de contenido

Pide a un asistente de IA (como Claude):

```
Crea descripciones de productos para mi tienda en línea basadas en estas características clave: [lista de características]
```

### Actualizaciones del sitio

Pide a un asistente de IA:

```
Actualiza mi archivo config.json para cambiar el horario de mi tienda a: Lunes a Viernes de 9am a 5pm
```

### Configuración de integración

Pide a un asistente de IA:

```
Ayúdame a configurar la integración de pagos de Colombia para mi negocio. Necesito opciones tanto de PSE como de DaviPlata.
```

## Siguientes pasos

- [Configurar procesamiento de pagos](./payment-integrations)
- [Configurar automatización de marketing](./marketing)
- [Personalizar tu diseño](./design-customization)