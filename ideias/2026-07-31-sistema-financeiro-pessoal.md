# Sistema de gerenciamento financeiro pessoal

**Data:** 2026-07-31
**Tags:** #financas-pessoais #aws #personal-infra-sre #audienceforge-pattern

## Ideia

Substituir a planilha Excel atual de gerenciamento de contas pessoais (contas
a pagar, despesas mensais e outras informações financeiras) por um sistema
próprio, com frontend e backend, hospedado na infra AWS pessoal (via skill
`personal-infra-sre`) — privado, só para uso do Alexandre.

## Arquitetura pretendida

Seguir o padrão usado no AudienceForge (`~/Projects/audienceforge.dev`):
uma pasta-mãe contendo vários repositórios git independentes (frontend,
backend, infra etc.), cada um com seu próprio histórico, em vez de um único
repositório monolítico. Estudar a estrutura local do AudienceForge como
referência antes de desenhar o novo projeto.

## Próximos passos

1. Alexandre vai subir a planilha Excel atual para estudo (contas a pagar,
   despesas mensais, estrutura de dados já em uso).
2. Discutir junto a funcionalidade do sistema (o que ele deve fazer, telas,
   fluxos) antes de escrever qualquer documentação de produto ou desenhar
   arquitetura em detalhe.
3. Só depois da funcionalidade estar clara, avançar para documentação de
   produto (via `dev-squad`, papel Tech Lead/PO-proxy) e refletir no
   espaço pessoal já existente no ClickUp.

## Status

Ainda em `ideias/` — não amadureceu para `projetos/` até a planilha ser
estudada e a funcionalidade discutida.
