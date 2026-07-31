# Smart Grana (nome de trabalho)

**Data:** 2026-07-31
**Tags:** #financas-pessoais #aws #personal-infra-sre #audienceforge-pattern

## Decisão — nome (2026-07-31)

Nome de trabalho fechado: **"Smart Grana"** (não "Grana Certa" —
`granacerta.com.br` já está registrado por terceiro; "Smart Grana" não teve
nenhum conflito de marca encontrado). Não precisa de domínio próprio agora,
já que o produto vive sob `agentstrail.dev`. Pode mudar no futuro sem custo
de refatoração: repositórios, classes, variáveis e identificadores técnicos
usam nomes **genéricos**, desacoplados da marca (ver seção "Convenção de
nomenclatura técnica" abaixo).

## Decisão — convenção de nomenclatura técnica (2026-07-31)

Prefixo técnico: **`personal-finance`**, em inglês (mesmo padrão dos
projetos Volpi: identificadores técnicos em inglês, nome comercial só na
marca/frontend). Pasta-mãe `personal-finance/`, repos
`personal-finance-frontend`, `personal-finance-backend`,
`personal-finance-infra` etc. Sobrevive a qualquer troca futura do nome
comercial sem custo de refatoração.

## Ideia

Substituir a planilha Excel atual de gerenciamento de contas pessoais (contas
a pagar, despesas mensais e outras informações financeiras) por um sistema
próprio, com frontend e backend, hospedado na infra AWS pessoal (via skill
`personal-infra-sre`) — privado, só para uso do Alexandre.

## Visão de produto (ampliada em 2026-07-31)

Não é só substituir a planilha: é o embrião de uma **plataforma agêntica de
IA para gerenciamento financeiro completa**. Nasce como subsistema sob a
marca guarda-chuva **AgentsTrail** (`agentstrail.dev`, o mesmo domínio que
hospeda o Recepta) — sem lançamento público por enquanto, mas desenhado
desde já pensando nisso (multi-tenant real, não só uso pessoal).

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
- **Planilha4**: lista simples de gastos fixos, mais antiga/redundante com
  a aba "Custo Fixo" — pode servir de referência histórica, sem estrutura
  nova a modelar.
- **Planilha3**: descartada — Alexandre confirmou que não representa nada
  relevante (números soltos sem cabeçalho).

## Planilha de cálculo de pró-labore — o que foi mapeado (2026-07-31)

Arquivo separado `Cálculo de imposto.xlsx`, recebido depois da planilha
principal. Calcula, a partir do faturamento mensal da empresa (regime
Simples Nacional): INSS, Imposto do Simples Nacional, ISS Município, IRPF,
pró-labore bruto/líquido, total de descontos, valor de contabilidade — e
divide o resultado entre os dois sócios (Alexandre e a esposa), com IRRF,
GPS e retirada calculados por sócio, limite de retirada e controle de
retiradas já efetuadas vs. saldo em conta. Confirma a necessidade de um
módulo de pró-labore/PJ com lógica tributária própria, não só lançamento
manual de valores.

## Decisão — dados bancários (CC e pix)

Alexandre confirmou que o sistema deve, no futuro, importar dados
bancários automaticamente via CLI/API do banco (não digitação manual) —
mas isso fica para depois do sistema já estar funcional (não é escopo do
MVP). Implicação de arquitetura: guardar essa decisão como item de
segurança a resolver antes de qualquer integração bancária real (chaves
de API, escopo de acesso, criptografia em repouso).

## Arquitetura pré-anotada (não fechada — Tech Lead confirma depois)

- Multi-tenant SaaS, AWS Lambda, MongoDB Atlas (cloud NoSQL, mirando free
  tier). Referências de código a estudar como base: `pipefy-middleware`
  (estrutura de MCP, sem relação de negócio com este projeto) e exemplos
  de Lambda + Python + FastAPI nas pastas de projetos Volpi.
- Auth: Google OAuth SSO (app no GCP do Alexandre) + cadastro/convite de
  usuários — multi-tenant real desde o início.
- MCP obrigatório no MVP — Alexandre precisa conseguir interagir com a
  plataforma via agente de IA (Claude), não só pela UI.
- **Permissões (2026-07-31):** 1 tenant = 1 família; todo usuário
  convidado tem acesso total e igual ao tenant no MVP (sem papéis/roles).
  Restrição arquitetural: o modelo de dados/autorização não pode ser
  desenhado de um jeito que torne difícil acrescentar níveis de permissão
  depois — não é RBAC completo agora, mas precisa deixar a porta aberta.
- **Empréstimos a terceiros (2026-07-31):** a pessoa que deve dinheiro
  (irmão, pai, mãe etc.) é só um **contato/registro dentro do tenant**, sem
  login próprio no MVP — só o(s) usuário(s) do tenant veem/atualizam. Login
  para o devedor consultar o próprio saldo fica para fase 2.

## Épicos candidatos ampliados (pós-discussão de 2026-07-31)

Além dos épicos originais (lançamentos mensais, custos fixos/parcelamentos,
investimentos, cartões, dashboard), dois blocos novos de escopo:

- **Gestão de empréstimos pessoais**: Alexandre (e a esposa) empresta
  parcelas de cartão para familiares, sem juros; precisa de tela de
  acompanhamento por pessoa/dívida e fluxo via agente de IA ("fulano me
  pagou este mês" → agente confere o que é devido, tira dúvida, registra
  o pagamento). Recurso genérico, para qualquer usuário da plataforma.
- **Gestão completa de pró-labore/PJ**: conta empresarial, recebimentos e
  retiradas, lógica tributária (ver planilha de cálculo de imposto acima).
  **Decisão (2026-07-31): integração com Contabilizei/Banco BS2 fica FORA
  do MVP** — nenhuma das duas costuma ter API pública documentada para
  PJ pequena, viraria investigação própria (scraping/engenharia reversa)
  e atrasaria o resto. No MVP, o módulo funciona com lançamento manual
  (como a planilha de cálculo de imposto faz hoje). Registrado como
  possível epic de fase 2.

Import histórico da planilha (desde 2023) entra no MVP, mas como última
etapa, depois do sistema já em produção.

## Decisões de produto — regras de negócio (2026-07-31)

- **Contas fixas**: lançamento **automático mês a mês** a partir do
  catálogo de custos fixos (não manual). Sistema também emite
  **relatórios de previsão** e visualização em dashboard a partir desses
  lançamentos recorrentes.
- **Empréstimos a terceiros**: ao registrar um empréstimo parcelado,
  sistema **gera cronograma automático** das parcelas futuras a receber.
  Precisa suportar **pular parcela** (pessoa não paga naquele mês por
  dificuldade financeira) com **reajuste automático**, empurrando as
  parcelas restantes para os meses seguintes.
- **Prioridade de entrega do MVP**: todos os épicos do MVP têm prioridade
  igual — entrega é uma release única quando tudo estiver pronto, com o
  import histórico da planilha (via agente de IA) rodando por último,
  depois do sistema em produção. Ordem de **desenvolvimento** interna
  (qual épico o Tech Lead ataca primeiro) fica a critério do Tech Lead na
  fase técnica — não é decisão de produto.

## Status

Ainda em `ideias/` — não amadureceu para `projetos/` até a planilha ser
estudada e a funcionalidade discutida.
