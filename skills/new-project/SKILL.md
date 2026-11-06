---
name: new-project
description: Gera o prompt de bootstrap de um repositório novo a partir da stack e do propósito — transplanta o kernel do engineering-house, adapta o settings.json aos riscos daquela stack, e impõe pesquisa, proposta, aprovação, geração e validação com isca e controle
disable-model-invocation: true
---

Quem pediu vai abrir (ou já abriu, e quer dar forma a) um repositório novo: $ARGUMENTS

**Seu produto final é um prompt, nunca arquivos no repositório alvo.** Mesmo contrato da skill
`sibling-spec`: você não edita o outro repositório, não propõe editá-lo por ele, e não "adianta" um pedaço.
Quem executa lá é o Claude de lá, com o prompt que você escrever.

## 1. O que você precisa saber antes de escrever

Stack e **plataforma-alvo**, propósito do repositório, caminho dele no disco, e se ele é **cliente de algum
serviço já existente deste grupo**. Se algo essencial faltar, use `AskUserQuestion` — só o essencial.

Não pergunte nome em inglês de nada (a tradução se faz sozinha), nem se o repositório está vazio: **isso
quem descobre é a Fase 0, lá dentro.** Você não sabe o estado do repositório alvo e não deve fingir que
sabe.

## 2. O que se transplanta: o plugin, não as cópias

O ganho de o `engineering-house` ser um plugin é que **as skills não se copiam para dentro de cada repositório**.
Então o prompt gerado manda **instalar o plugin** — uma linha — e materializa apenas as **receitas**.

O prompt gerado leva, nesta ordem:

```
claude plugin marketplace add <caminho do engineering-house>
claude plugin install engineering-house@engineering-house
```

E depois, dos `${CLAUDE_PLUGIN_ROOT}/templates/`, materializa no repositório alvo:

| Template | Vira |
|---|---|
| `templates/CLAUDE.md` | o `CLAUDE.md` do projeto, com a **stack e os comandos reais** dele |
| `templates/PLATFORM-ARCH.md` | `<PLATAFORMA>-ARCH.md` — nomeado **pela plataforma-alvo**, nunca pela linguagem |
| `templates/settings/<stack>.md` | `.claude/settings.json`, somado ao `_base-git.md` |
| `templates/PERMISSIONS.md` | `PERMISSIONS.md` — o registro de isca e controle |
| `templates/docs-README.md` · `templates/plans-README.md` | `docs/README.md` e `plans/README.md` |

**O ARCH re-enuncia os cinco princípios como texto próprio, e isso não é redundância:** o repositório tem
de se sustentar **sem o plugin instalado**, para quem clona e lê. Leve também a cláusula de procedência do
`${CLAUDE_PLUGIN_ROOT}/PRINCIPLES.md`, apontando para a **fonte canônica** — não para este plugin, que
também é um re-enunciado.

**O `CLAUDE.md` gerado cita os workflows pelos nomes namespaced** — `/engineering-house:spec`,
`/engineering-house:handoff`, `/engineering-house:new-rule`, `/engineering-house:sibling-spec`,
`/engineering-house:permission-settings` — porque é assim que uma skill de plugin se invoca. `/spec` não existe.

**Regra que não cabe em ~3 linhas no `CLAUDE.md` é skill**, e o prompt gerado diz isso: é o sinal de
classificação errada.

## 3. Se o alvo é cliente de um serviço existente, levante o contrato LENDO

Nunca de memória, nunca "mais ou menos". Leia a fonte: o código do serviço e a documentação versionada
dele, anotando **a versão e a data** do que você leu.

- Transcreva os **invariantes** (transporte, autenticação, headers obrigatórios, gramática de rota, gate de
  versão) **inline** no prompt — são curtos e estáveis.
- Para a **superfície** (rotas, payloads, campo a campo), o prompt manda rodar `/add-dir <caminho do
  serviço>` e **ler a doc versionada na fonte**.
- **Copiar a doc de contrato para o repositório alvo está proibido** — duplicata apodrece, e contrato
  apodrecido é pior que contrato ausente.
- **Diga o que ainda NÃO existe.** Um contrato levantado só com o que está pronto faz o outro lado planejar
  em cima de um vazio. Se a doc marca algo como pendente, isso vai no prompt com essa palavra.

## 4. Monte o prompt — seis fases, nesta ordem

### Fase 0 — Inventário, antes de qualquer proposta

Ler o que já existe: arquivos de build, estrutura, versões já fixadas, ferramental, `.gitignore`, CI. Dois
ramos, e o prompt escreve os dois:

- **Repositório vazio** → propõe do zero.
- **Repositório com scaffold** (wizard de IDE, template, commit inicial) → **o scaffold é o estado real.** A
  proposta é *fill por cima*, integrando sem destruir. **Deletar ou regerar o que o wizard gerou exige ordem
  explícita** — o proibido é o **efeito**, não a ferramenta.

O inventário é **reportado antes** da proposta. E o prompt manda confirmar a versão do `claude` CLI e se há
plugins/marketplaces já configurados na máquina.

### Fase 1 — Pesquisa (proibido propor de memória)

Convenções modernas da stack (estrutura, build, DI, testes, lint/format), de **fontes oficiais atuais, com
as fontes e as datas na resposta**. Propor de memória está proibido: convenção de stack viva gira mais
rápido que qualquer conhecimento congelado.

Confrontar com a Fase 0 — se o scaffold segue convenção antiga, **dizer qual**, e **não "consertar" sem
ordem**.

### Fase 2 — Proposta (nada de escrever ainda)

A estrutura, o build e os testes vindos da Fase 1, mais os artefatos da casa do item 2.

O **`.claude/settings.json` sai da receita da stack** (`templates/settings/`), confirmada contra o que as
Fases 0 e 1 mostraram. **Copiar os padrões de outra stack é erro:** eles nunca vão casar com nada lá, e
**deny que não casa é deny que não existe**. Se a stack não tem receita em `templates/settings/`, o prompt
manda invocar a skill `engineering-house:permission-settings` e seguir a seção **"Stack sem receita"**: ela
monta a partir dos riscos reais daquela stack, cada entrada nasce com o par de isca e controle ao lado, os
pares se executam, e no fim **ela oferece devolver a receita ao plugin por PR** — porque sem isso o próximo
projeto da mesma stack paga o trabalho de novo. O prompt gerado diz isso explicitamente, e diz também que
**quem abre o PR é o dono, nunca a sessão**.

### Fase 3 — Aprovação. PARA.

Nenhum `Write` antes de um "aprovado" dito com todas as letras.

### Fase 4 — Geração

Só o que foi aprovado, sem acrescentar nada que não estava lá.

### Fase 5 — Validação, em sessão limpa (`/clear` antes)

**5a · O plugin carregou.** `/plugin list` mostra o `engineering-house`, e os `/engineering-house:*` aparecem na
listagem de `/`. Uma skill carrega quando invocada.

**5b · O `settings.json` — a primária, obrigatória.** Para **cada** entrada de `deny`, um par: **isca** =
comando que ela tem de bloquear; **controle** = comando legítimo parecido que tem de passar. **Rodar os
dois de verdade** — raciocinar sobre o padrão não vale, e é exatamente o que falha. Isca que passa = padrão
inválido ou settings não carregado: conserta e **refaz o par**.

A isca tem de ser **inócua se o deny falhar** (alvo inexistente, dry-run, arquivo-isca), e **a prova é a
mensagem de negação, não o código de saída**.

O porquê vai **escrito no prompt gerado**, porque sem ele ninguém re-roda o teste: *padrão de permissão
inválido não dá erro, só não pega; e settings de projeto pode não estar carregado na sessão.* O par de
controle também não é firula: um `deny` largo demais bloqueia **tudo**, passa no teste de isca e inutiliza
a stack.

**5c · Comportamental.** Um pedido que **fere regra escrita** → o Claude de lá tem de **parar e citar o
trecho + o arquivo**, e oferecer as duas saídas. Um pedido **legítimo e parecido** → executa normal. Uma
regra que barra os dois é paranoia, e vira ruído que se aprende a ignorar. Citar a regra já prova que leu.

**5d · Build e teste** da stack passam no repositório gerado.

### Fora de escopo do prompt gerado, sempre

Mexer neste repositório (achou problema aqui, **reporta**) · implementar feature de produto · regerar
scaffold · publicar nada.

### ⚠️ Todo exemplo é neutro

Antes de escrever qualquer exemplo no prompt, leia `${CLAUDE_PLUGIN_ROOT}/references/neutral-examples.md` e
**copie a regra para dentro do prompt entregue**.

## 5. Salve, aponte e PARE

Grave em **`plans/bootstrap-<repo>.md`** e ponha o ponteiro no índice de `plans/`. É um plano: quando o
repositório estiver de pé e validado, o arquivo **morre** (está no git).

Diga em uma linha o caminho do arquivo e em qual repositório colar. **Não execute o bootstrap, nem ofereça.**
