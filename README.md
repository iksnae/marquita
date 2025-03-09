# Project Marquita

> Open-source business starter kit with AI-assisted management, GitHub Pages deployment, and third-party integrations for non-technical entrepreneurs.

## Overview

Project Marquita is a suite of tools designed to empower non-technical entrepreneurs to establish and maintain their online business presence without requiring specialized technical knowledge or expensive intermediaries. The project combines the power of GitHub Pages hosting with AI-assisted development to create a zero-to-low cost solution for small business owners worldwide.

## Core Value Proposition

- **Zero-to-Low Cost Deployment**: Leverages free GitHub Pages hosting with custom domain and SSL support
- **AI-Assisted Management**: Enables non-technical users to build and update their online presence through conversations with AI assistants (OpenAI, Claude)
- **Automated CI/CD**: Provides a fully automated pipeline for building, testing, and deploying website changes
- **Integrated Marketing Tools**: Includes capabilities for marketing across social networks to drive traffic
- **Modular Third-Party Integrations**: Supports payment processors, e-commerce functionality, and media hosting through plugin architecture

## Documentation

### Architecture & Design
- [Architecture Overview](docs/architecture.md) - Core technical architecture
- [Template System](docs/template-system.md) - Design of the template system
- [AI-Assisted Development](docs/ai-assisted-development.md) - How AI agents manage site creation and updates

### Payment Integrations
- [Payment Integration Overview](docs/payment-integrations/index.md) - General payment integration approach
- [Colombia](docs/payment-integrations/colombia.md) - PSE and DaviPlata integration
- [USA](docs/payment-integrations/usa.md) - Credit card, ACH, and alternative payment methods
- [Brazil](docs/payment-integrations/brazil.md) - Pix, Boleto, and Brazilian payment methods

### Guides
- [Getting Started](docs/getting-started.md) - Quick start guide for new users
- [Project Roadmap](docs/roadmap.md) - Development plan and milestones

## Project Components

Marquita consists of several interconnected repositories:

1. **Core Framework** (this repository): Documentation, guides, and project coordination
2. **Website Templates**: Collection of customizable templates for different business types
3. **Integration Modules**: Connectors for third-party services (payments, analytics, etc.)
4. **Deno API Services**: Backend services for handling integrations and secure operations
5. **Marketing Automation**: Tools for content distribution across platforms

## Getting Started

For entrepreneurs looking to create a site with Marquita:

1. Review the [Getting Started](docs/getting-started.md) guide
2. Choose a template that matches your business needs
3. Follow the guided setup process with AI assistance
4. Configure your site with your business information
5. Deploy to GitHub Pages and connect your domain

For developers looking to contribute:

1. Review the [Template System](docs/template-system.md) documentation
2. Explore the [Architecture Overview](docs/architecture.md)
3. Check the [Project Roadmap](docs/roadmap.md) for current focus areas
4. Follow our [contribution guidelines](CONTRIBUTING.md)

## Templates

Marquita offers several specialized templates for different business needs:

- **E-commerce Template**: For product-based businesses, featuring product catalogs, shopping carts, and checkout flows
- **Service Business Template**: For service providers, with booking systems and service showcases
- **Portfolio Template**: For creative professionals, with project galleries and case study displays
- **Landing Page Template**: For marketing campaigns, featuring conversion-optimized layouts

## Regional Support

Marquita is designed to work globally, with special optimizations for:

- **Colombia**: Integration with PSE and DaviPlata payment systems
- **USA**: Support for ACH, credit cards, and digital wallets
- **Brazil**: Integration with Pix, Boleto, and installment payments

Additional regions will be added based on community contributions.

## Technology Stack

- **Frontend**: SvelteKit 2+ / Svelte 5 for static site generation
- **Styling**: Tailwind CSS for responsive design
- **Deployment**: GitHub Pages for free hosting
- **CI/CD**: GitHub Actions for automated deployment
- **Backend APIs**: Deno Deploy for serverless functions
- **Internationalization**: Built-in i18n support for multiple languages

## Contributing

We welcome contributions from developers, designers, and entrepreneurs. Check out our [contribution guidelines](CONTRIBUTING.md) to get started.

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.