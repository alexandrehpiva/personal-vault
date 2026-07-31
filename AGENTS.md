# AGENTS.md

Instruções para qualquer agente de IA (Claude ou outro) que for ler, organizar ou adicionar conteúdo neste repositório.

## O que é este repositório

Cofre pessoal e privado do Alexandre para guardar ideias, notas, projetos e referências. Não é um repositório de código — é uma base de conhecimento pessoal em Markdown.

## Idioma

- Todo o conteúdo (arquivos `.md`, nomes de pasta, títulos) é em **português**.
- Nomes de arquivo e pasta: minúsculas, sem acento, palavras separadas por hífen (`ex: apostas-de-viagem.md`).
- Arquivos de estrutura do repositório (este `AGENTS.md`, eventual `CLAUDE.md`) podem ficar em português também, mas priorize clareza sobre convenção.

## Estrutura de pastas

| Pasta | Conteúdo |
|---|---|
| `ideias/` | Ideias soltas, insights, brainstorms — sem compromisso de virarem algo. |
| `projetos/` | Ideias maduras, com escopo e próximos passos. Uma subpasta por projeto: `projetos/nome-do-projeto/README.md`. |
| `notas/` | Anotações do dia a dia: aprendizados, reflexões, resumos de conversas/cursos/livros. |
| `referencias/` | Links, artigos, livros, vídeos — organizados por tema (`referencias/investimentos.md`). |
| `recursos/` | Templates, checklists e materiais reutilizáveis. |

Cada pasta tem seu próprio `README.md` explicando o propósito. Leia-o antes de adicionar conteúdo novo naquela pasta.

## Como decidir onde colocar um conteúdo novo

1. É um pensamento rápido, sem desenvolvimento? → `ideias/`
2. Já tem escopo, próximos passos, é algo que pode virar realidade? → `projetos/`
3. É aprendizado, reflexão ou registro do dia a dia? → `notas/`
4. É um link/material externo para consultar depois? → `referencias/`
5. É um template ou checklist reutilizável? → `recursos/`

Na dúvida entre `ideias/` e `projetos/`: comece em `ideias/`. Só mova para `projetos/` quando o usuário confirmar que quer desenvolver aquilo.

## Convenção de arquivos em `ideias/`, `notas/` e `referencias/` por data

Nome do arquivo: `AAAA-MM-DD-titulo-curto.md`

Cabeçalho padrão dentro do arquivo:

```markdown
# Título da ideia/nota

**Data:** AAAA-MM-DD
**Tags:** #tag1 #tag2

Conteúdo...
```

## Regras de edição

- **Nunca apague ou reescreva conteúdo existente do usuário** sem confirmação explícita. Isso é um cofre pessoal — perder uma ideia registrada é o pior cenário possível.
- Ao adicionar informação relacionada a uma nota já existente, prefira **complementar o arquivo existente** (nova seção, ou apêndice com data) em vez de criar duplicata, a menos que seja claramente um assunto novo.
- Ao criar arquivo novo, sempre usar a convenção de nome com data quando fizer sentido cronológico (ideias, notas, referências pontuais). Arquivos de projeto e recursos não precisam de data no nome.
- Não crie pastas novas na raiz sem necessidade clara — use as cinco categorias existentes. Se surgir uma categoria genuinamente nova e recorrente, pergunte ao usuário antes de criar.

## Git

- Repositório de uso pessoal, sem colaboradores — pode commitar direto na branch `main`, sem PR.
- Mensagens de commit curtas e descritivas, em português ou inglês, descrevendo o que foi adicionado/alterado (ex: `Adiciona ideia sobre app de hábitos`).
- Nunca force-push nem reescreva histórico sem pedido explícito do usuário.
