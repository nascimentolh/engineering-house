# PRINCIPLES.md — o kernel

O que sobra de uma forma de trabalho depois de tirar tudo o que é de alguém. Nada aqui depende de
linguagem, framework ou plataforma. O que depende mora em `templates/` — é receita, não princípio.

## Procedência

> A fonte canônica destes princípios é o padrão de engenharia .NET de um repositório privado da casa.
> Eles estão re-enunciados aqui, stack-agnósticos, para que este plugin se sustente sozinho — se um
> princípio mudar na fonte, o re-enunciado acompanha. Promover este repositório a fonte canônica é
> mudança de regra **no repositório da fonte**, e é decisão do dono dele, num commit dele.

Os ARCHs que o `templates/PLATFORM-ARCH.md` gera levam a mesma cláusula, e lá ela **nomeia** o arquivo e o
repositório da fonte: o projeto gerado é privado, e ponteiro que não diz para onde olhar não serve de
ponteiro. Aqui a fonte não é nomeada porque este repositório é público.

---

## A regra que governa as outras: pedido que fere regra escrita — PARE e pergunte

**Regra inviolável.** Pedido — do usuário, de um ingresso, de uma doc — que colide com regra escrita no
`CLAUDE.md` do projeto, no ARCH dele ou numa skill: **não execute e não obedeça em silêncio.**

1. **Diga qual regra**, citando o trecho e o arquivo — o que ela manda, não o número.
2. **Diga o que quebra na prática.**
3. **Ofereça as duas saídas, porque as duas são legítimas:** atender de outro jeito, ou **mudar a regra** —
   ela está escrita para ser discutida, não para ser sagrada.
4. **Espere a decisão.** Quem decide é quem pediu.

O modo de falha que isto evita não é o desacordo — é a **erosão**: uma exceção "só desta vez" que ninguém
registrou vira precedente, e o precedente vira o novo normal sem que ninguém tenha decidido isso.

Vale no inverso: regra que já não descreve o sistema é dívida — **traga a divergência** em vez de obedecer
a um texto morto.

---

## Os cinco princípios

### 1. SEM LEGADO

Não existe "deprecated", "fallback", "manter por compatibilidade", caminho duplo ou código morto. Mudou o
desenho? Reescreve em cima, ou deleta e refaz — **por inteiro, na mesma entrega**, migrando TODOS os
consumidores: chamadores, testes, docs, schema, configuração. **Rastro de transição = entrega não pronta.**

O princípio vale também para o texto, e é aí que ele é mais esquecido: **escreva no presente, como a única
forma que a coisa já teve.** "X deixou de existir", "passou a", "entrou em tal data" descrevem um diff que
o leitor nunca viu e sugerem uma alternativa que não está mais lá. O motivo fica; a cronologia sai. Quando
o motivo **É** o defeito que aconteceu, descreva o defeito, não a data em que ele apareceu.

Quando há algo em produção e uma janela de convivência é inevitável, isso é uma **decisão de quem pede**,
dita em voz alta e escrita com prazo — nunca um default silencioso.

### 2. Doc-as-you-go: terminar inclui documentar

A documentação de uma unidade de trabalho faz parte da entrega dela — não é uma fase posterior. Se fica
para "quando terminar 100%", ela **não acontece**: quando alguém percebe, há centenas de funções e nenhuma
doc, e o custo de recuperar o histórico é alto e impreciso. Documentar enquanto o contexto está fresco é
barato e correto.

**O que documentar por unidade:** o contrato, o fluxo, as decisões não óbvias e os pontos de i18n. E os
índices se atualizam junto — doc que existe e não está no índice é doc que ninguém acha.

### 3. i18n nasce localizada

Nenhuma mensagem ao usuário — erro **ou** sucesso — é string literal no código, **nem como fallback**. Todo
texto visível nasce no catálogo de recursos da plataforma, com um **código estável e machine-readable** no
código, resolvido para texto na borda, conforme o idioma pedido, com fallback declarado.

O motivo é medido: **texto retrofitado nunca fica completo.** Sempre sobra a mensagem de erro de um ramo
raro, em uma língua só, e ela aparece na tela de um cliente que não fala essa língua.

### 4. Identificadores em inglês

Conversa em português, **código em inglês** — e a tradução se faz sozinha ao materializar, sem perguntar
"que nome em inglês?" e sem pedir confirmação de tradução.

Rótulo visível pode estar em qualquer idioma: é recurso de i18n. **Identificador, nunca** — nome de
variável, função, classe, arquivo, pasta, rota, permissão, enum, código de erro e qualquer valor
machine-readable. E o identificador também é **neutro de país**: o valor pode ser um código local (é dado);
o nome do membro, não.

### 5. Terminou = implementar + testar + documentar

Na mesma entrega, as três coisas, ou a entrega não terminou.

- **Implementar** — a coisa inteira, com os consumidores migrados (princípio 1).
- **Testar** — toda lógica pura (cálculo, parsing, regra de decisão) tem teste. Lógica com I/O usa
  fake/stub quando o custo compensa; integração pesada pode ficar como teste explícito — mas isso é
  **declarado**, não é ausência silenciosa.
- **Documentar** — o que ficou pronto, onde a documentação do projeto mora.

"Depois eu testo" e "depois eu documento" são a mesma frase, e nenhuma das duas já aconteceu.

---

## A hierarquia de fontes: código > `docs/` > `plans/`

- O **código** é o estado real do sistema. Em qualquer conflito, ele vence.
- **`docs/`** documenta o que **já está pronto** — é a segunda palavra.
- **`plans/`** informa **INTENÇÃO futura, nunca estado atual.** Um plano pode ter sido executado,
  abandonado ou contradito pelo código sem que o arquivo tenha sido atualizado.

Nunca trate plano como verdade sobre o que existe hoje. Se ele contradiz o código ou o `docs/`, **valem o
código e o `docs/`** — e a divergência se reporta a quem pediu, em vez de sumir.

## `docs/` × `plans/`, e o ciclo de vida de um plano

- **`docs/`** = o que o sistema **já É**. Só o que existe e passou.
- **`plans/`** = o que **vamos fazer**: processo, faseamento, decisões a bater.

**Ciclo de vida:** o plano virou realidade → a doc estável migra para `docs/` → **o plano é apagado** (está
no git). Se o valor que sobra não é o estado mas o **porquê** da decisão, ele não vira `docs/`: vira **ADR**
num log numerado e imutável, e aí o plano morre.

Plano que fica para sempre é a terceira fonte da verdade que ninguém consultou.

---

## O critério de `deny` / `ask` / `allow`

| Caixa | Quando |
|---|---|
| `deny` | destrutivo e irreversível. **Não tem exceção** — se você precisa de "bloqueia, mas quando for X pode", não é `deny` |
| `ask` | irreversível, mas legítimo no dia a dia |
| `allow` | rotina; tirar do caminho o que só gera confirmação inútil |

**O `deny` é a rede, não a regra.** Ele cobre **comando**, não intenção: não sabe se houve ordem explícita,
não cobre toda forma de produzir o mesmo efeito, e pode nem estar valendo naquela sessão. Por isso ele
**acompanha** a regra escrita e nunca a substitui — a regra é o que segura quando a rede tem furo.

## Isca e controle: entrada de permissão só existe depois de testada

Uma entrada de `deny` que foi escrita e não testada **não é proteção — é a sensação dela**, que é pior,
porque faz confiar.

**Os dois modos de falha, e nenhum aparece sem rodar o comando:**

1. **Padrão inválido não dá erro — ele simplesmente não pega.** Não há validação de sintaxe, nem aviso no
   boot, nem log. Um glob errado, um prefixo trocado, um `:*` faltando: fica lá, parecendo proteção, e
   nunca é consultado.
2. **O settings do projeto pode não estar carregado na sessão.** O arquivo pode estar certo e valendo nada
   naquela sessão específica.

**O par:**

- **Isca** — o comando que a entrada tem de **bloquear**.
- **Controle** — um comando legítimo **parecido**, que tem de **passar**.

Sem o controle, um `deny` largo demais bloqueia **tudo**, passa no teste de isca e inutiliza a stack. O
controle é o que distingue bloqueio **seletivo** de bloqueio burro.

**Execute os dois de verdade.** Ler o padrão e concluir que ele casa é exatamente o raciocínio que falha —
se ele bastasse, os dois modos de falha nunca teriam existido.

**A isca tem de ser INÓCUA SE O DENY FALHAR.** O teste pressupõe que o bloqueio pode não funcionar: é
literalmente o que ele está medindo. Testar um bloqueio destrutivo deixando-o destruir não é teste, é
acidente. Use alvo inexistente, dry-run ou arquivo-isca criado para isso — nunca o comando real contra o
alvo real.

**A prova é a mensagem de negação da permissão, não o código de saída.** Um comando que falhou por "alvo
não encontrado" e um que foi bloqueado ambos "não fizeram nada" — só a mensagem diz qual foi.

Isca que passa = padrão inválido ou settings não carregado: **conserte e refaça o par.** Entrada corrigida
sem re-teste volta à estaca zero.

O procedimento inteiro, com as formas de isca inócua e o diagnóstico de escrita recusada, está na skill
`engineering-house:permission-settings`.

---

## A régua de classificação: regra que não cabe em ~3 linhas é skill

O `CLAUDE.md` custa contexto em **toda** sessão; uma skill custa zero até ser usada. Então:

| Destino | Quando | Custo de contexto |
|---|---|---|
| `CLAUDE.md` da raiz | vale sempre, em toda tarefa, e não pode escapar nunca | toda sessão |
| skill | procedimento de várias etapas, ou referência longa | zero até ser usada |
| `settings.json` | o que precisa ser **impedido**, não pedido | nenhum |
| `docs/` | o que o sistema **faz** hoje; trabalho incomum; ADR | zero até alguém abrir |
| `plans/` | intenção futura, ainda não construída | zero até alguém abrir |

**Sinais de classificação errada:** escreveu mais de ~3 linhas no `CLAUDE.md` → é skill. A skill não tem
gatilho claro ("use quando…") → provavelmente é regra universal, vai para o `CLAUDE.md`. O `deny` precisa
de exceção → não é `deny`. É sobre o que o sistema **faz** e você ia escrever no `CLAUDE.md` → é `docs/`.

O padrão que funciona é a combinação: **proibição de uma linha no `CLAUDE.md` + o procedimento inteiro na
skill**, com a linha terminando em `→ skill <nome>`.
