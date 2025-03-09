# Integrações de Pagamento da Colômbia

## Visão Geral

Este documento descreve a integração de métodos de pagamento específicos da Colômbia na plataforma Marquita. A Colômbia possui requisitos e preferências de pagamento únicos que diferem dos padrões internacionais, tornando a integração local essencial para empresas que operam neste mercado.

## Métodos de Pagamento Suportados

### PSE (Pago Seguro en Línea)

**Descrição**: Uma solução de pagamento por transferência bancária amplamente utilizada na Colômbia para transações online.

**Benefícios**:
- Sem taxas para os consumidores
- As taxas de transação para empresas variam entre 1% e 2,5%
- Pagamentos locais, eliminando taxas internacionais
- Sistema seguro e regulamentado pelo governo

**Implementação**:
- Os usuários serão redirecionados para a página segura do seu banco onde podem autorizar o pagamento
- Após a conclusão, os usuários retornarão à plataforma de e-commerce com uma confirmação de pagamento

### DaviPlata

**Descrição**: Uma solução de pagamento mobile-first do Bancolombia, utilizada principalmente para pagamentos móveis e transferências bancárias.

**Benefícios**:
- Sem taxas para transferências domésticas entre contas Bancolombia
- Experiência otimizada para dispositivos móveis
- Transferência fácil de fundos
- Sistema de pagamento local sem taxas internacionais

**Implementação**:
- Os usuários selecionam DaviPlata como método de pagamento
- O pagamento é concluído através do aplicativo DaviPlata em dispositivos móveis
- A confirmação da transação é exibida imediatamente após a conclusão

## Arquitetura de Integração

### Implementação da API Deno Deploy

```typescript
// Estrutura de implementação de exemplo para métodos de pagamento da Colômbia
import { serve } from "https://deno.land/std/http/server.ts";
import { PSEGateway } from "./gateways/pse.ts";
import { DaviPlataGateway } from "./gateways/daviplata.ts";
import { config } from "../config.ts";

serve(async (req) => {
  const url = new URL(req.url);
  const path = url.pathname;
  
  // Endpoint de integração PSE
  if (path === "/api/payments/pse") {
    const pse = new PSEGateway(config.pse);
    return await pse.processPayment(req);
  }
  
  // Endpoint de integração DaviPlata
  if (path === "/api/payments/daviplata") {
    const daviplata = new DaviPlataGateway(config.daviplata);
    return await daviplata.processPayment(req);
  }
  
  // Endpoint de status de pagamento
  if (path === "/api/payments/status") {
    // Lógica de verificação de status de pagamento
  }
  
  return new Response("Não encontrado", { status: 404 });
});
```

## Jornada do Usuário

1. **Início do Checkout**:
   - O usuário navega para a página de checkout
   - Seleciona o método de pagamento (PSE ou DaviPlata)

2. **Fluxo do PSE**:
   - O usuário seleciona o banco no menu suspenso
   - É redirecionado para a página de login segura do banco
   - Completa a autenticação e confirma o pagamento
   - Retorna ao site do comerciante com a confirmação

3. **Fluxo do DaviPlata**:
   - O usuário recebe instruções de pagamento
   - Completa o pagamento no aplicativo DaviPlata
   - A API do Deno Deploy confirma o status do pagamento
   - O usuário vê a confirmação no site do comerciante

## Configuração

Os gateways de pagamento são configurados através de variáveis de ambiente ou arquivos de configuração:

```json
{
  "colombia": {
    "pse": {
      "merchantId": "SEU_ID_DE_COMERCIANTE",
      "apiKey": "SUA_CHAVE_API",
      "redirectUrl": "https://seudominio.com/checkout/complete",
      "notificationUrl": "https://api.seudominio.com/payments/pse/webhook"
    },
    "daviplata": {
      "merchantId": "SEU_ID_DAVIPLATA",
      "apiKey": "SUA_CHAVE_API_DAVIPLATA",
      "callbackUrl": "https://api.seudominio.com/payments/daviplata/callback"
    }
  }
}
```

## Considerações de Segurança

- Chaves de API armazenadas com segurança em variáveis de ambiente do Deno Deploy
- HTTPS obrigatório para todas as transações
- Nenhum dado de pagamento sensível armazenado no código frontend
- Validação de webhook para prevenir notificações fraudulentas

## Testes

Contas de teste estão disponíveis para ambos os métodos de pagamento:

- Ambiente de teste do PSE: [Link para o portal do desenvolvedor]
- Sandbox do DaviPlata: [Link para o portal do desenvolvedor]

## Cronograma de Implementação

1. **Fase 1**: Integração básica do PSE (2 semanas)
2. **Fase 2**: Integração básica do DaviPlata (2 semanas)
3. **Fase 3**: Recursos avançados e tratamento de erros (1 semana)
4. **Fase 4**: Testes e otimização (1 semana)

## Referências

- [Documentação da API do PSE](https://pseapi.com/docs)
- [Guia de Integração do DaviPlata](https://daviplata.com/developers)
- [Portal de Desenvolvedores do Bancolombia](https://developer.bancolombia.com)