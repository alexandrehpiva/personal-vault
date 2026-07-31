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

## Lógicas, fórmulas e cálculos extraídos (2026-07-31)

Fórmulas reais lidas célula a célula (não só estrutura). Referência para
quando o módulo de regras de negócio for desenhado.

### Rateio de custo fixo (`Custo Fixo`)

`% do total = valor do gasto / soma de todos os gastos fixos` (coluna D
sobre coluna C, `=C{n}/$C$22`). Simples — cada linha é % do bolo total de
gastos fixos.

### Saldo mensal (aba de cada mês, ex. `Ago 26`)

`Saldo (C28) = Recebimentos totais (H6+H7) − Total de contas a pagar (C27)`

### Rendimento de investimentos indexados ao CDI (aba de cada mês)

Fórmula geral aplicada a cada posição (Inter CDB, Itaú CDB etc.):

```
rendimento_mensal = saldo × ((1 + taxa_CDI_anual/100)^(1/12) − 1)
                          × (%_do_CDI_contratado/100)
                          × (1 − %_IR/100)
```

Ou seja: converte taxa anual do CDI em taxa mensal composta, aplica o
percentual do CDI contratado por produto (ex.: 120%, 100%, 110%, 140% do
CDI, varia por banco/produto) e desconta IR na fonte quando aplicável.
Tesouro Direto IPCA+ usa variação da mesma fórmula com taxa própria
(ex.: "IPCA + 7,82" hardcoded). Um investimento (`Inter tesouro`) referencia
diretamente a aba `Investimentos!F14` em vez de calcular localmente.

### Pró-labore / impostos PJ (`Cálculo de imposto.xlsx`, regime Simples Nacional)

**Alíquotas fixas (hardcoded na planilha, precisam virar parâmetros
configuráveis no sistema, não constantes de código):**

| Item | Alíquota |
|---|---|
| Pró-labore sobre faturamento | 28,2% |
| INSS | 11% |
| Imposto do Simples Nacional | 7,46% |
| ISS Município | 2% (já embutido no imposto do Simples Nacional — não soma separado no total de descontos) |

**Cadeia de cálculo:**

1. `INSS = Faturamento × 11%`
2. `Pró-labore bruto = Faturamento × 28,2%`
3. `Imposto do Simples Nacional = Faturamento × 7,46%`
4. `INSS sobre pró-labore bruto = (soma do pró-labore dos sócios) × 11%`
5. `IRPF = soma do IRRF de cada sócio` (IRRF por sócio é valor calculado à
   parte, não fórmula simples nessa planilha — provavelmente tabela
   progressiva do IR, a esclarecer)
6. `Total de descontos = INSS sobre pró-labore + Imposto Simples Nacional + IRPF`
7. `Pró-labore líquido = Pró-labore bruto − INSS sobre pró-labore − IRPF`
8. `Total a receber (empresa) = Faturamento − Total de descontos`

**Split por sócio (Alexandre e esposa, 50/50 no exemplo observado):**

- `GPS (INSS individual) = Pró-labore do sócio × 11%`
- `Retirada líquida do sócio = Pró-labore do sócio − IRRF do sócio − GPS do sócio`
- Comparação contra um valor de referência mensal fixo (ex.: R$ 2.300)
  para calcular ajuste/diferença por sócio.

**Controle de retirada:**

- `Limite de retirada = Saldo em conta − Total de descontos − Contabilidade`
- `A retirar = Limite de retirada − retiradas já feitas por cada sócio`

**Esclarecimentos do Alexandre (2026-07-31) — revisam os pontos 1 e 2
abaixo, que originalmente pareciam inconsistência de planilha e na
verdade são conceitos de negócio reais, mais ricos que o que eu tinha
capturado:**

1. **Pró-labore contábil ≠ retirada real — são dois números diferentes de
   propósito.** O "pró-labore contábil" é definido todo mês pela
   Contabilizei, otimizando para pagar menos imposto (Fator R — ver item
   2). Já o valor que cada sócio efetivamente retira em relação à
   **"conta casa"** (conta interna compartilhada do casal, hospedada num
   banco separado dos outros, no nome do Alexandre) é outro número,
   digitado manualmente, e pode divergir do pró-labore contábil sem
   problema — é assim que a família decide gerenciar. Além disso, existe
   **lucro distribuído** como uma terceira forma de saída de dinheiro da
   empresa, distinta de pró-labore. **Implicação de modelagem:** o
   sistema não deve tratar "pró-labore" como a única fonte de renda PJ —
   precisa modelar separadamente (a) pró-labore contábil/fiscal, (b)
   lucro distribuído, (c) retirada real por sócio para a conta
   compartilhada da família, como conceitos ligados mas independentes.
   Isso também não é universal: cada família/tenant pode organizar esse
   fluxo de um jeito diferente (ex.: sem "conta casa", ou com sócio que é
   CLT em vez de PJ) — o modelo de fontes de renda deve ser **flexível e
   configurável por tenant**, não hardcoded no padrão do Alexandre.
2. **A alíquota de 28%/28,2% é uma estimativa para o Fator R, não um
   valor incerto ou inconsistente.** O Fator R é a razão entre
   folha+pró-labore acumulados em 12 meses e a receita bruta acumulada no
   mesmo período — para empresas de serviço/software, se essa razão for
   ≥ 28%, a empresa se enquadra no Anexo III do Simples Nacional (imposto
   menor) em vez do Anexo V (mais caro). Alexandre usa 28% e 28,2% como
   **estimativa de quanto precisa retirar de pró-labore total** (soma dos
   sócios) para se manter acima desse limiar. O valor real de pró-labore
   total é sempre a soma do que for lançado por sócio — a % é só uma meta
   de planejamento, não uma fonte de verdade concorrente. **Implicação de
   produto:** vale a pena um recurso dedicado de "calculadora/estimador de
   Fator R" no módulo de PJ, não só um campo solto — mas a confirmar como
   epic de fase 2 ou MVP quando chegarmos na fase técnica.

**Ainda em aberto (item 3):** a fórmula de "limite de retirada" subtrai o
valor de Contabilidade duas vezes (confirmado célula a célula, não é
suposição — ver explicação acima do chat). Alexandre não tinha percebido;
ainda não confirmado se é intencional (margem de segurança) ou erro de
arrasto de fórmula. Verificar antes de implementar essa regra.

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

## Recursos e telas previstas (visão consolidada, 2026-07-31)

Síntese de tudo decidido acima, organizada por tela/módulo — referência
rápida para a escrita da documentação de produto.

1. **Dashboard** — visão geral consolidada: saldo, investimentos, cartões,
   empréstimos a receber, relatórios de previsão de contas fixas. **Aberto:**
   métricas/widgets exatos ainda não detalhados — refinar durante a escrita
   da doc, não é bloqueante.
2. **Lançamentos mensais** — contas a pagar/recebimentos/caixa; contas
   fixas lançadas **automaticamente** todo mês a partir do catálogo, com
   confirmação; relatórios de previsão.
3. **Custos fixos** — catálogo de gastos recorrentes com % de rateio sobre
   o total; serve de template para os lançamentos automáticos.
4. **Investimentos** — carteira (FIIs, CDB, Tesouro Direto, poupança);
   cálculo automático de rendimento (fórmula indexada ao CDI/IPCA
   extraída da planilha). **Aberto:** Alexandre pediu "algo bem legal"
   para contas pessoais e investimentos, sem detalhar o quê — aspiração
   registrada, mas sem escopo concreto ainda; explorar com ele durante a
   escrita ou numa rodada futura de grill-me.
5. **Cartões de crédito** — cadastro de cartões, dia de fechamento/melhor
   dia de compra, saldo devedor.
6. **Empréstimos a terceiros** — devedor é contato sem login; cronograma
   automático de parcelas; suporte a pular parcela com reajuste automático
   das seguintes; fluxo via agente de IA para conferir e registrar
   pagamento. Recurso genérico para qualquer usuário do tenant.
7. **Pró-labore / PJ** — conta empresarial, recebimentos e retiradas;
   cálculo tributário completo (Simples Nacional) por sócio, com limite
   de retirada; manual no MVP, sem integração Contabilizei/BS2 (fase 2).
   Modelo de renda PJ precisa separar **pró-labore contábil** (definido
   pela contabilidade, otimizado por Fator R), **lucro distribuído** e
   **retirada real por sócio** para a conta compartilhada da família —
   três conceitos ligados mas independentes, configuráveis por tenant
   (nem toda família organiza do mesmo jeito; pode ter sócio CLT em vez
   de PJ). Possível recurso dedicado: calculadora/estimador de Fator R.
8. **Import histórico** — importação assistida por agente de IA da
   planilha (2023+), como última etapa do MVP, depois do sistema em
   produção.
9. **Multi-tenant / Auth** — Google OAuth SSO (GCP do Alexandre), cadastro
   e convite de usuários; 1 tenant = 1 família; permissões iguais entre
   membros no MVP, mas modelo de dados aberto para RBAC futuro.
10. **MCP / Agente de IA** — interface conversacional cobrindo os módulos
    acima (registrar pagamento de empréstimo, consultar saldo etc.),
    obrigatória desde o MVP — não é feature isolada, é forma de acesso
    paralela à UI em todo o sistema.

## Status

Ainda em `ideias/` — não amadureceu para `projetos/` até a planilha ser
estudada e a funcionalidade discutida.
