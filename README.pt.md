# Projeto Marquita

> Kit de inicialização de negócios de código aberto com gestão assistida por IA, implantação no GitHub Pages e integrações de terceiros para empreendedores não técnicos.

## Visão Geral

O Projeto Marquita é um conjunto de ferramentas projetadas para capacitar empreendedores não técnicos a estabelecer e manter sua presença comercial online sem exigir conhecimentos técnicos especializados ou intermediários caros. O projeto combina o poder da hospedagem do GitHub Pages com o desenvolvimento assistido por IA para criar uma solução de custo zero a baixo para proprietários de pequenas empresas em todo o mundo.

## Proposta de Valor Principal

- **Implantação de Custo Zero a Baixo**: Aproveita a hospedagem gratuita do GitHub Pages com suporte a domínio personalizado e SSL
- **Gestão Assistida por IA**: Permite que usuários não técnicos construam e atualizem sua presença online através de conversas com assistentes de IA (OpenAI, Claude)
- **CI/CD Automatizado**: Fornece um pipeline totalmente automatizado para construção, teste e implantação de alterações no site
- **Ferramentas de Marketing Integradas**: Inclui recursos para marketing em redes sociais para direcionar tráfego
- **Integrações Modulares com Terceiros**: Suporta processadores de pagamento, funcionalidade de e-commerce e hospedagem de mídia através de arquitetura de plugins

## Documentação

### Arquitetura e Design
- [Visão Geral da Arquitetura](docs/architecture.md) - Arquitetura técnica central
- [Sistema de Templates](docs/template-system.md) - Design do sistema de templates
- [Desenvolvimento Assistido por IA](docs/ai-assisted-development.md) - Como agentes de IA gerenciam a criação e atualizações do site

### Integrações de Pagamento
- [Visão Geral das Integrações de Pagamento](docs/payment-integrations/index.md) - Abordagem geral de integração de pagamentos
- [Argentina](docs/payment-integrations/argentina.md) - Mercado Pago, Rapipago/Pago Fácil e cartões com pagamento parcelado
- [Brasil](docs/payment-integrations/brazil.md) - Pix, Boleto e métodos de pagamento brasileiros
- [Chile](docs/payment-integrations/chile.md) - Webpay Plus, Khipu, Multicaja e MACH
- [Colômbia](docs/payment-integrations/colombia.md) - Integração com PSE e DaviPlata
- [Costa Rica](docs/payment-integrations/costa-rica.md) - SINPE Móvil, transferências SINPE e cartões locais
- [Equador](docs/payment-integrations/ecuador.md) - Cartões de crédito/débito, Payphone e redes de pagamento em dinheiro
- [Guatemala](docs/payment-integrations/guatemala.md) - Cartões de crédito/débito, transferências bancárias e carteiras móveis
- [México](docs/payment-integrations/mexico.md) - SPEI, Oxxo Pay e pagamentos com cartão parcelado
- [Panamá](docs/payment-integrations/panama.md) - Cartões de crédito/débito, transferências ACH, Yappy e Nequi
- [Peru](docs/payment-integrations/peru.md) - PagoEfectivo, Yape, transferências CCI e SafetyPay
- [EUA](docs/payment-integrations/usa.md) - Cartão de crédito, ACH e métodos de pagamento alternativos

### Guias
- [Primeiros Passos](docs/getting-started.md) - Guia de início rápido para novos usuários
- [Roteiro do Projeto](docs/roadmap.md) - Plano de desenvolvimento e marcos

## Componentes do Projeto

O Marquita consiste em vários repositórios interconectados:

1. **Framework Principal** (este repositório): Documentação, guias e coordenação do projeto
2. **Template Base**: [marquita-base-template](https://github.com/iksnae/marquita-base-template) - Fundação para todos os templates de sites de negócios
3. **Templates Específicos para Negócios** (em breve):
   - Template de E-commerce
   - Template de Negócio de Serviços
   - Template de Portfólio
   - Template de Página de Destino
4. **Serviços de API Deno** (em breve): Serviços de backend para lidar com integrações e operações seguras
5. **Automação de Marketing** (em breve): Ferramentas para distribuição de conteúdo em plataformas

## Primeiros Passos

Para empreendedores que desejam criar um site com o Marquita:

1. Revise o guia de [Primeiros Passos](docs/getting-started.md)
2. Escolha um template que corresponda às necessidades do seu negócio
3. Siga o processo de configuração guiado com assistência de IA
4. Configure seu site com as informações do seu negócio
5. Implante no GitHub Pages e conecte seu domínio

Para desenvolvedores que desejam contribuir:

1. Revise a documentação do [Sistema de Templates](docs/template-system.md)
2. Explore a [Visão Geral da Arquitetura](docs/architecture.md)
3. Verifique o [Roteiro do Projeto](docs/roadmap.md) para áreas de foco atuais
4. Siga nossas [diretrizes de contribuição](CONTRIBUTING.md)

## Templates

Marquita oferece vários templates especializados para diferentes necessidades de negócios:

- **Template de E-commerce**: Para negócios baseados em produtos, com catálogos de produtos, carrinhos de compras e fluxos de checkout
- **Template de Negócio de Serviços**: Para provedores de serviços, com sistemas de agendamento e showcases de serviços
- **Template de Portfólio**: Para profissionais criativos, com galerias de projetos e exibição de estudos de caso
- **Template de Página de Destino**: Para campanhas de marketing, com layouts otimizados para conversão

## Suporte Regional

O Marquita é projetado para funcionar globalmente, com otimizações especiais para:

- **Argentina**: Integração com Mercado Pago, Rapipago/Pago Fácil e pagamentos parcelados
- **Brasil**: Integração com Pix, Boleto e pagamentos parcelados
- **Chile**: Suporte para Webpay Plus, Khipu, Multicaja e MACH
- **Colômbia**: Integração com sistemas de pagamento PSE e DaviPlata
- **Costa Rica**: Suporte para SINPE Móvil, transferências SINPE e cartões locais
- **Equador**: Suporte para cartões de crédito/débito, Payphone e redes de pagamento em dinheiro
- **Guatemala**: Integração com cartões locais, transferências bancárias e carteiras móveis
- **México**: Suporte para SPEI, Oxxo Pay e cartões com pagamento parcelado
- **Panamá**: Suporte para cartões, transferências ACH, Yappy e Nequi
- **Peru**: Suporte para PagoEfectivo, Yape, transferências CCI e SafetyPay
- **EUA**: Suporte para ACH, cartões de crédito e carteiras digitais

Regiões adicionais serão adicionadas com base em contribuições da comunidade.

## Stack Tecnológico

- **Frontend**: SvelteKit 2+ / Svelte 5 para geração de site estático
- **Estilização**: Tailwind CSS para design responsivo
- **Implantação**: GitHub Pages para hospedagem gratuita
- **CI/CD**: GitHub Actions para implantação automatizada
- **APIs Backend**: Deno Deploy para funções serverless
- **Internacionalização**: Suporte i18n incorporado para múltiplos idiomas

## Contribuindo

Recebemos contribuições de desenvolvedores, designers e empreendedores. Confira nossas [diretrizes de contribuição](CONTRIBUTING.md) para começar.

## Licença

Este projeto está licenciado sob a Licença MIT - veja o arquivo [LICENSE](LICENSE) para detalhes.