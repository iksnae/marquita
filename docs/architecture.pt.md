# Projeto Marquita - Visão Geral da Arquitetura

## Componentes Principais

### 1. Geração de Sites Estáticos

O Projeto Marquita utiliza geradores de sites estáticos modernos para criar websites seguros e de alto desempenho que podem ser hospedados no GitHub Pages. Nossas principais escolhas técnicas são:

- **SvelteKit**: Para aplicações web interativas e sites com conteúdo dinâmico
- **Astro**: Para websites focados em conteúdo com necessidades mínimas de JavaScript

Ambos os frameworks suportam a geração de sites estáticos, o que permite a implantação no GitHub Pages enquanto mantém excelentes características de desempenho e segurança.

### 2. Camada de Serviços API

Para suportar funcionalidades dinâmicas sem exigir infraestrutura de servidor, o Marquita aproveita:

- **Deno Deploy**: Para endpoints de API serverless que lidam com:
  - Integrações com terceiros
  - Operações seguras
  - Transformações de dados
  - Autenticação

O Deno Deploy fornece uma solução de baixo custo com um nível gratuito generoso (100 mil requisições/dia), tornando-o acessível para empreendedores que estão iniciando seus negócios.

### 3. Framework de Integração

Marquita fornece um sistema modular de integração para conectar com serviços de terceiros:

- **Processamento de Pagamentos**: Gateways de pagamento específicos por região (PSE/DaviPlata para Colômbia, etc.)
- **Plataformas de E-commerce**: Catálogo de produtos e gerenciamento de inventário
- **Hospedagem de Mídia**: Soluções de armazenamento de imagens, vídeos e arquivos
- **Analytics**: Rastreamento de visitantes e insights de negócios

### 4. Gestão Assistida por IA

Um diferencial chave para o Marquita é sua capacidade de gestão assistida por IA:

- Interface de linguagem natural para atualizações do site
- Geração e implantação automatizada de código
- Criação e otimização de conteúdo
- Recomendações e implementação de SEO

## Arquitetura de Implantação

```
+------------------+      +------------------+      +------------------+
|                  |      |                  |      |                  |
|  Repositório     |      |  GitHub Actions  |      |  Hospedagem      |
|  GitHub          +----->+  Pipeline CI/CD  +----->+  GitHub Pages    |
|                  |      |                  |      |                  |
+------------------+      +------------------+      +------------------+
         ^                                                  ^
         |                                                  |
         |                                                  |
         |                                                  |
+--------+---------+                              +---------v--------+
|                  |                              |                  |
|  Assistente IA   |                              |  Site Estático   |
|  (Claude/OpenAI) |                              |  (Usuário final) |
|                  |                              |                  |
+------------------+                              +------------------+
                                                          ^
                                                          |
                                                          |
                                                  +-------+--------+
                                                  |                |
                                                  |  Serviços API  |
                                                  |  Deno Deploy   |
                                                  |                |
                                                  +----------------+
```

## Personalização Regional

O Marquita é projetado para suportar negócios globalmente, com adaptações específicas para requisitos regionais:

- **Sistemas de Pagamento**: Gateways e métodos de pagamento específicos por região
- **Conformidade**: Templates para requisitos legais regionais (políticas de privacidade, termos de serviço)
- **Localização**: Suporte multi-idioma e templates de conteúdo regional

## Considerações de Segurança

A arquitetura prioriza a segurança através de:

- Geração de sites estáticos (reduzindo a superfície de ataque)
- Aplicação de HTTPS via GitHub Pages
- Gerenciamento de chaves de API através de variáveis de ambiente
- Escopos de permissão mínimos para integrações de terceiros

## Escalabilidade

A arquitetura suporta escalabilidade desde projetos pessoais até negócios em crescimento:

- GitHub Pages fornece capacidades de CDN para entrega global de conteúdo
- Deno Deploy escala automaticamente com base na demanda
- Integração com serviços profissionais conforme as necessidades do negócio crescem