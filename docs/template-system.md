# Marquita Template System

This document outlines the template system for Project Marquita, based on insights from the knockoutmde prototype.

## Template Architecture

The Marquita template system is built around SvelteKit's static site generation capabilities, optimized for deployment to GitHub Pages with minimal technical overhead.

### Core Components

1. **Base Template Repository**
   - Foundation for all business templates
   - Core layout structure and components
   - GitHub Actions workflow for automated deployment
   - Testing framework and configuration

2. **Business-Specific Template Variants**
   - E-commerce/Product-based business (storefront)
   - Service-based business
   - Portfolio/Creative professional
   - Landing page/Single product

3. **Configuration System**
   - Central configuration file for brand information
   - Theme customization through variables
   - Content schema for different business types
   - Region-specific configurations

4. **Integration Points**
   - Deno API service connections
   - Payment processing component slots
   - Marketing automation hooks
   - Analytics integration

## Base Template Structure

The base template includes the following directory structure:

```
marquita-base-template/
├── .github/
│   └── workflows/
│       └── deploy.yml          # GitHub Pages deployment workflow
├── src/
│   ├── app.css                 # Global CSS with Tailwind
│   ├── app.html                # HTML template
│   ├── lib/
│   │   ├── components/         # Reusable UI components
│   │   │   ├── Header.svelte
│   │   │   ├── Footer.svelte
│   │   │   ├── LanguageSwitcher.svelte
│   │   │   └── ... 
│   │   ├── config/
│   │   │   ├── brand.js        # Brand configuration
│   │   │   ├── navigation.js   # Navigation structure
│   │   │   └── theme.js        # Theme configuration
│   │   ├── i18n/
│   │   │   ├── index.ts        # i18n setup
│   │   │   └── locales/        # Translation files
│   │   └── utils/              # Utility functions
│   └── routes/                 # SvelteKit routes
├── static/                     # Static assets
├── config/                     # AI-editable configuration files
│   ├── site.json              # Site metadata
│   ├── pages.json             # Page content
│   ├── products.json          # Product information (e-commerce)
│   └── services.json          # Service information (service-based)
├── svelte.config.js
├── tailwind.config.js
├── tsconfig.json
└── vite.config.ts
```

## AI-Assisted Customization

The template is designed to be easily customizable through AI assistance:

### Configuration-Driven Approach

1. **Brand Configuration** (`config/site.json`):
   ```json
   {
     "name": "Business Name",
     "tagline": "Your memorable tagline",
     "logo": {
       "text": "Business Name", 
       "image": "/logo.png",
       "useImage": false
     },
     "colors": {
       "primary": "#e11d48",
       "secondary": "#4b5563"
     },
     "contact": {
       "email": "info@example.com",
       "phone": "+1 234 567 8900",
       "address": "123 Main St, City, Country"
     },
     "social": {
       "instagram": "username",
       "facebook": "pagename",
       "twitter": "handle"
     }
   }
   ```

2. **Content Configuration** (`config/pages.json`):
   ```json
   {
     "home": {
       "hero": {
         "title": "Welcome to Business Name",
         "subtitle": "Your compelling subtitle here",
         "ctaPrimary": {
           "text": "Get Started",
           "link": "/products/"
         },
         "ctaSecondary": {
           "text": "Learn More",
           "link": "/about/"
         }
       },
       "sections": [
         {
           "type": "feature",
           "title": "What We Offer",
           "items": [
             {
               "title": "Feature 1",
               "description": "Description of feature 1"
             },
             {
               "title": "Feature 2",
               "description": "Description of feature 2"
             }
           ]
         }
       ]
     }
   }
   ```

### AI-Specific Comments

All templates include specific comments to guide AI modifications:

```svelte
<!-- AI-EDIT-POINT: Hero Title -->
<h1 class="text-4xl font-bold">{hero.title}</h1>

<!-- AI-EDIT-POINT: Navigation Items
     To modify navigation, edit the config/navigation.js file
     or directly modify the items below. Each item should have:
     - text: Display text
     - href: Link destination
     - external: (optional) Set to true for external links
-->
```

## Template Variants

### E-commerce Template

Focus on product display, shopping cart, and checkout experience:

- Product catalog with filtering/sorting
- Product detail pages
- Shopping cart functionality
- Checkout process with payment integration
- Order confirmation and tracking

### Service Business Template

Focused on service offerings and lead generation:

- Service showcase
- Pricing tables
- Booking/appointment scheduling
- Testimonials and case studies
- Contact forms with lead routing

### Portfolio Template

Designed for creative professionals:

- Project/work showcase
- Filterable portfolio gallery
- About/bio section
- Client testimonials
- Contact information

### Landing Page Template

Optimized for conversions:

- Compelling hero section
- Feature highlights
- Social proof section
- FAQ accordion
- Strong call-to-action elements

## Integration with Deno API Services

Each template includes integration points for connecting to Deno Deploy services:

1. **Payment Processing**:
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
       console.error('Payment processing error:', error);
       throw new Error('Payment failed to process');
     }
   }
   ```

2. **Form Handling**:
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
       console.error('Form submission error:', error);
       throw new Error('Form failed to submit');
     }
   }
   ```

## GitHub Pages Deployment

The template includes a standard GitHub Actions workflow for automated deployment:

```yaml
name: Deploy to GitHub Pages

on:
  push:
    branches: ["main"]
  workflow_dispatch:

# Sets permissions of the GITHUB_TOKEN to allow deployment to GitHub Pages
permissions:
  contents: read
  pages: write
  id-token: write

# Allow only one concurrent deployment
concurrency:
  group: "pages"
  cancel-in-progress: true

jobs:
  # Test job
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

  # Build job
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

  # Deployment job
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

## AI-Assisted Workflow

The typical workflow for creating a new site with Marquita:

1. **Template Selection**:
   - AI guides the user to select the appropriate template variant
   - User provides basic business information

2. **Repository Creation**:
   - AI creates a new repository from the selected template
   - Sets up GitHub Pages with proper configuration

3. **Brand Customization**:
   - AI helps the user customize the configuration files
   - Updates brand colors, logo, and content

4. **Content Creation**:
   - AI assists with creating page content
   - Helps with copy, image selection, and layout

5. **Integration Setup**:
   - AI configures necessary third-party integrations
   - Sets up payment processing for the user's region

6. **Deployment and Testing**:
   - AI triggers the deployment workflow
   - Verifies the site is working properly

## Implementation Roadmap

1. **Phase 1: Base Template Development** (2 weeks)
   - Extract core components from knockoutmde
   - Create configuration system
   - Set up basic theme customization
   - Implement i18n framework

2. **Phase 2: Variant Templates** (3 weeks)
   - Develop e-commerce template
   - Create service business template
   - Build portfolio template
   - Design landing page template

3. **Phase 3: Integration Points** (2 weeks)
   - Implement Deno API service connections
   - Create payment processing components
   - Set up form handling and submissions
   - Add analytics integration

4. **Phase 4: AI Assistance Layer** (2 weeks)
   - Add AI-specific comments and guidance
   - Create documentation for AI interaction
   - Develop helper utilities for common operations
   - Build example prompts for site creation

5. **Phase 5: Testing and Refinement** (1 week)
   - Test all templates across devices
   - Verify deployment process
   - Optimize performance
   - Document the entire system