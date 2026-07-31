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

1. ~~Alexandre vai subir a planilha Excel atual para estudo~~ — feito em
   2026-07-31, via Magic Wormhole (`wormhole-share`).
2. Discutir junto a funcionalidade do sistema (o que ele deve fazer, telas,
   fluxos) antes de escrever qualquer documentação de produto ou desenhar
   arquitetura em detalhe. **Em andamento.**
3. Só depois da funcionalidade estar clara, avançar para documentação de
   produto (via `dev-squad`, papel Tech Lead/PO-proxy) e refletir no
   espaço pessoal já existente no ClickUp.

## Planilha atual — o que foi mapeado (2026-07-31)

Arquivo `Contas 2026.xlsx`, em uso desde set/2023. Estrutura:

- **Uma aba por mês** (bimestral até 2024, mensal a partir de 2025), com 4
  blocos: contas a pagar (tipo/conta/valor/vencimento/parcela),
  recebimentos (pró-labore de Alexandre e da esposa + lucro), caixa
  (rateio por categoria + saldo devedor de cartão), poupança/investimentos
  por banco e cartões de crédito (dia de fechamento/melhor dia de compra).
  Também há bloco de dados da empresa (DARF, SIMPLES, contador).
- **Custo Fixo**: catálogo de gastos fixos com % do total, controle de
  empréstimos internos entre o casal, e parcelamentos de compras grandes.
- **Investimentos**: carteira de FIIs (ticker, quantidade, valor da cota,
  rendimento, total investido, dia de pagamento).
- **CC e pix**: dados bancários e chaves PIX — dado sensível.
- **Planilha2**: composição do salário (bruto/INSS/IRRF/líquido).
- **gasto carro**: seguro, gasolina, licenciamento, IPVA.
- **Planilha4** e **Planilha3**: parecem parcialmente duplicadas/rascunho,
  a confirmar com Alexandre se ainda são usadas.

## Decisão — dados bancários (CC e pix)

Alexandre confirmou que o sistema deve, no futuro, importar dados
bancários automaticamente via CLI/API do banco (não digitação manual) —
mas isso fica para depois do sistema já estar funcional (não é escopo do
MVP). Implicação de arquitetura: guardar essa decisão como item de
segurança a resolver antes de qualquer integração bancária real (chaves
de API, escopo de acesso, criptografia em repouso).

## Status

Ainda em `ideias/` — não amadureceu para `projetos/` até a planilha ser
estudada e a funcionalidade discutida.
