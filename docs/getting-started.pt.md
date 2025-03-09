# Primeiros passos com o Projeto Marquita

## Pré-requisitos

Antes de começar, certifique-se de ter o seguinte:

- Uma conta no GitHub
- Conhecimento básico de tecnologias web (útil, mas não obrigatório)
- Uma ideia de negócio ou negócio existente para colocar online

## Início rápido

### 1. Escolha um template

Marquita oferece vários templates pré-construídos projetados para diferentes tipos de negócios:

- **Loja virtual**: Para negócios baseados em produtos
- **Serviços**: Para prestadores de serviços (consultores, agências, etc.)
- **Portfólio**: Para profissionais criativos
- **Página de destino**: Para campanhas de marketing ou negócios de produto único

### 2. Faça um fork do repositório do template

1. Navegue até o repositório do template de sua escolha
2. Clique no botão "Fork"
3. Nomeie seu repositório (isso fará parte da URL do seu site)

### 3. Configure seu site

1. Edite o arquivo `config.json` para personalizar:
   - Nome do negócio
   - Esquema de cores
   - Logo e imagens
   - Informações de contato
   - Produtos/serviços

### 4. Implante com GitHub Pages

1. No seu repositório, vá para Configurações > Pages
2. Selecione a branch principal como fonte
3. Clique em Salvar
4. Seu site estará ao vivo em `https://[nome-usuário].github.io/[nome-repositório]/`

### 5. Configure um domínio personalizado (Opcional)

1. Compre um domínio de qualquer registrador de domínios
2. No seu repositório, vá para Configurações > Pages
3. Insira seu domínio personalizado
4. Atualize as configurações de DNS do seu domínio conforme instruído

## Usando assistência de IA

Marquita é projetado para trabalhar com assistentes de IA para ajudar a gerenciar e atualizar seu site:

### Criação de conteúdo

Peça a um assistente de IA (como Claude):

```
Crie descrições de produtos para minha loja online com base nestas características principais: [lista de características]
```

### Atualizações do site

Peça a um assistente de IA:

```
Atualize meu arquivo config.json para alterar o horário da minha loja para: Segunda a Sexta, das 9h às 17h
```

### Configuração de integração

Peça a um assistente de IA:

```
Ajude-me a configurar a integração de pagamentos da Colômbia para meu negócio. Preciso das opções PSE e DaviPlata.
```

## Próximos passos

- [Configurar processamento de pagamentos](./payment-integrations)
- [Configurar automação de marketing](./marketing)
- [Personalizar seu design](./design-customization)