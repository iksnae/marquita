# AI-Assisted Development for Marquita

This document outlines how AI agents (like Claude and OpenAI assistants) interact with and manage Marquita-based websites, enabling non-technical entrepreneurs to create, update, and maintain their online presence without writing code.

## Core Principles

1. **Configuration Over Coding**: AI agents modify JSON configuration files rather than diving into code directly
2. **Natural Language Interface**: Users describe what they want in plain language
3. **Guided Customization**: AI provides step-by-step guidance through complex processes
4. **Automated Implementation**: Changes are automatically deployed via CI/CD

## AI Agent Responsibilities

AI assistants in the Marquita ecosystem have several key responsibilities:

### 1. Site Creation & Setup

- Guide users through template selection
- Create GitHub repositories from templates
- Configure initial site settings
- Set up custom domains
- Configure brand elements

### 2. Content Management

- Create and update page content
- Optimize content for SEO
- Create and manage product listings
- Translate content for multi-language sites
- Suggest improvements to existing content

### 3. Design Customization

- Update color schemes and typography
- Modify layouts within template constraints
- Implement responsive design adjustments
- Optimize images for web use
- Create custom graphics and icons

### 4. Integration Management

- Set up payment processing for specific regions
- Connect to analytics platforms
- Configure marketing automation tools
- Integrate with external services
- Troubleshoot integration issues

### 5. Deployment & Maintenance

- Deploy changes through GitHub Actions
- Test site functionality
- Monitor site performance
- Fix common issues
- Update dependencies securely

## Conversation Patterns

Typical conversation flows between users and AI assistants:

### Site Creation Conversation

```
User: "I want to create a website for my handmade jewelry business."

AI: "I'd be happy to help you create a website for your handmade jewelry business using Marquita. Let's start by gathering some basic information:

1. What's the name of your business?
2. Where are you located? (This helps with regional payment setup)
3. Do you already have a domain name?
4. Would you primarily be selling products online, or is this more of a portfolio site?"

User: "My business is called 'Gemstone Creations'. I'm in Brazil. I don't have a domain yet. I want to sell my jewelry online."

AI: "Great! Gemstone Creations sounds perfect for an e-commerce site. Since you're in Brazil, we'll set up with Pix and other Brazilian payment methods.

I recommend the e-commerce template for your jewelry business. This will give you:
- A product catalog to showcase your jewelry
- Shopping cart functionality
- Secure checkout with Brazilian payment methods
- Order management
- Mobile-responsive design

Would you like to proceed with creating this site?"

User: "Yes, that sounds perfect!"

AI: [Creates repository from e-commerce template]
"I've created your website repository! Now let's customize it for Gemstone Creations..."
```

### Content Update Conversation

```
User: "I need to update my homepage to announce our summer collection."

AI: "I'd be happy to help you update your homepage to announce your summer collection. Looking at your current site, I can see several options:

1. Add a banner announcement at the top of the page
2. Update the main hero section with summer collection information
3. Add a new featured products section specifically for the summer items

Which approach would you prefer?"

User: "Let's update the hero section."

AI: "Great choice! The hero section will give your summer collection maximum visibility. 

Tell me about your summer collection - what's special about it? Any specific products you want to highlight? Any promotional offers?"

User: "It's a collection of beach-inspired jewelry using sea glass and pearls. We're offering 15% off for the launch week."

AI: [Updates configuration files and deploys changes]
"I've updated your hero section to showcase your beach-inspired summer collection with sea glass and pearls, including the 15% launch discount. The changes are now live on your site!"
```

### Integration Setup Conversation

```
User: "I need to set up payment processing for my site."

AI: "I'd be happy to help you set up payment processing. I see your business is located in Brazil, so I'll focus on the best options for that region.

For Brazilian e-commerce, I recommend implementing:
1. Pix - For instant, low-fee payments
2. Boleto Bancário - For customers who prefer traditional payment methods
3. Brazilian credit cards with installment options (parcelamento)

Would you like to proceed with this setup?"

User: "Yes, that sounds good. How do I get started?"

AI: "Great! To set up these payment methods, we'll need to:

1. Create accounts with payment processors
2. Configure the Deno API services
3. Update your site configuration
4. Test the payment flow

Let's start with creating the necessary accounts. Do you already have a relationship with a payment processor in Brazil?"

[Conversation continues with step-by-step guidance]
```

## Implementation Details

### Configuration File Structure

AI agents primarily interact with configuration files rather than modifying code directly. The main configuration files include:

1. **Site Configuration** (`config/site.json`):
   ```json
   {
     "name": "Business Name",
     "description": "Business description",
     "logo": "/images/logo.png",
     "contact": {
       "email": "contact@example.com",
       "phone": "+1234567890",
       "address": "123 Main St."
     },
     "social": {
       "instagram": "username",
       "facebook": "pagename"
     }
   }
   ```

2. **Theme Configuration** (`config/theme.json`):
   ```json
   {
     "colors": {
       "primary": "#4a6cf7",
       "secondary": "#f9656b",
       "background": "#ffffff",
       "text": "#333333"
     },
     "fonts": {
       "heading": "Montserrat",
       "body": "Open Sans"
     }
   }
   ```

3. **Page Content** (`config/content/[page-name].json`):
   ```json
   {
     "hero": {
       "title": "Welcome to our store",
       "subtitle": "Find the perfect items for your lifestyle",
       "image": "/images/hero.jpg",
       "cta": {
         "text": "Shop Now",
         "link": "/products"
       }
     },
     "sections": [
       {
         "type": "featured-products",
         "title": "Featured Products",
         "products": ["prod-1", "prod-2", "prod-3"]
       }
     ]
   }
   ```

4. **Navigation** (`config/navigation.json`):
   ```json
   {
     "main": [
       {
         "label": "Home",
         "url": "/"
       },
       {
         "label": "Products",
         "url": "/products"
       },
       {
         "label": "About",
         "url": "/about"
       },
       {
         "label": "Contact",
         "url": "/contact"
       }
     ]
   }
   ```

5. **Products/Services** (`config/products.json`):
   ```json
   [
     {
       "id": "prod-1",
       "name": "Product Name",
       "description": "Product description",
       "price": 99.99,
       "images": ["/images/products/prod-1-1.jpg", "/images/products/prod-1-2.jpg"],
       "categories": ["category-1", "category-2"]
     }
   ]
   ```

### GitHub API Interactions

AI agents use GitHub APIs to perform various operations:

1. **Creating Repositories**:
   ```javascript
   // Create a new repository from template
   createRepository({
     name: "business-website",
     templateOwner: "marquita",
     templateRepo: "ecommerce-template",
     private: false
   });
   ```

2. **Updating Files**:
   ```javascript
   // Update a configuration file
   updateFile({
     repo: "business-website",
     path: "config/site.json",
     content: JSON.stringify(siteConfig, null, 2),
     message: "Update site configuration"
   });
   ```

3. **Triggering Workflows**:
   ```javascript
   // Trigger a deployment workflow
   triggerWorkflow({
     repo: "business-website",
     workflow: "deploy.yml",
     ref: "main"
   });
   ```

### Deno Deploy API Configuration

AI agents help set up and configure the Deno Deploy APIs:

1. **Creating Deno Deploy Projects**:
   ```javascript
   // Create a new Deno Deploy project linked to GitHub repo
   createDenoProject({
     name: "business-api",
     githubRepo: "business-website",
     entrypoint: "api/index.ts"
   });
   ```

2. **Setting Environment Variables**:
   ```javascript
   // Set environment variables for API keys (securely)
   setDenoEnvVars({
     project: "business-api",
     vars: {
       "PAYMENT_API_KEY": "sk_live_...",
       "REGION": "brazil",
       "WEBHOOK_SECRET": "whsec_..."
     }
   });
   ```

## Best Practices for AI Agents

When working with Marquita websites, AI agents should follow these guidelines:

1. **Preserve Structure**: Don't fundamentally alter the template structure
2. **Use Configuration First**: Always prefer updating config files over modifying code
3. **Clear Explanations**: Explain what you're doing in non-technical terms
4. **Step-by-Step Guidance**: Break complex tasks into manageable steps
5. **Visual Preview**: When possible, describe how changes will look
6. **Validate Changes**: Confirm changes are working as expected
7. **Document Actions**: Keep a record of significant changes made
8. **Security Focus**: Never expose API keys or sensitive credentials
9. **Performance Awareness**: Consider the performance impact of changes
10. **Mobile Consideration**: Ensure all changes work well on mobile devices

## Example Templates for AI Assistance

### Site Creation Template

```
To create a new [Business Type] website with Marquita:

1. I'll help you set up a GitHub repository using our [Template Type] template.
2. We'll customize the basic information:
   - Business name and description
   - Contact information
   - Brand colors and logo
3. We'll set up essential pages:
   - Homepage
   - About page
   - Products/Services
   - Contact page
4. We'll configure any needed integrations:
   - Payment processing for [Region]
   - Contact forms
   - Social media connections
5. We'll deploy your site to GitHub Pages
6. We'll set up your custom domain (optional)

Let's start! What's the name of your business?
```

### Content Update Template

```
To update the content on your [Page Type] page:

1. I'll help you identify what specific content needs changing
2. We'll draft the new content together
3. I'll update the appropriate configuration files
4. We'll preview how the changes will look
5. Once approved, I'll deploy the changes to your live site

What specific content would you like to update on your [Page Type] page?
```

### Integration Setup Template

```
To set up [Integration Type] for your site:

1. I'll explain the requirements and expected outcomes
2. We'll gather necessary information:
   - API keys or credentials
   - Configuration preferences
   - Regional requirements
3. I'll walk you through setting up accounts if needed
4. I'll update your site's configuration files
5. We'll configure the Deno API services to handle backend functions
6. We'll test the integration to ensure it's working properly
7. I'll provide documentation on how to manage this integration

Are you ready to set up [Integration Type] for your site?
```

## Troubleshooting Guidance

Common issues AI agents should be prepared to handle:

1. **Deployment Failures**:
   - Check GitHub Actions logs
   - Verify configuration file syntax
   - Review recent changes

2. **Integration Issues**:
   - Validate API keys and credentials
   - Check API endpoint configurations
   - Verify webhook URLs

3. **Content Display Problems**:
   - Inspect configuration files for errors
   - Check image paths and formats
   - Verify responsive design configurations

4. **Performance Concerns**:
   - Optimize image sizes
   - Review third-party script usage
   - Check for redundant content

## Continuous Improvement

AI agents should also:

1. **Gather Feedback**: Ask users about their experience
2. **Suggest Improvements**: Proactively recommend enhancements
3. **Learn Patterns**: Recognize common requests and optimize responses
4. **Maintain Knowledge**: Stay updated on template changes and new features