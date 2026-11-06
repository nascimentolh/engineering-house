---
name: new-rule
description: Classifica onde uma regra, decisão ou aprendizado novo deve morar (CLAUDE.md, skill, settings.json, o ARCH do projeto, docs/ ou plans/), escreve no lugar certo depois de aprovado e pergunta se a regra vale também num repositório irmão
disable-model-invocation: true
---

Quem pediu acabou de estabelecer uma regra, tomar uma decisão ou corrigir um comportamento: $ARGUMENTS

Sua tarefa é **decidir onde isso mora** — regra guardada no lugar errado é regra que ninguém acha na hora
que precisa, ou que polui todo contexto sem nunca ser usada. **Não escreva nada antes da aprovação.**

## 1. Classifique o destino

Pergunte, **nesta ordem** (a primeira que responder "sim" ganha):

| Pergunta | Destino |
|---|---|
| Vale para **qualquer projeto desta plataforma**, não só este repositório? | **o ARCH do projeto** (`<PLATAFORMA>-ARCH.md`) — só como **proposta** (ver §3) |
| É **proibição/obrigação universal e curta** deste projeto, que vale em TODA sessão? | **`CLAUDE.md`** — uma linha, terminando em `→ skill <nome>` se houver procedimento |
| É **procedimento que só vale numa situação específica** (ao criar X, ao mexer em Y)? | **skill** — existente se couber, nova se não |
| Dá para transformar em **bloqueio mecânico determinístico** (comando, caminho de arquivo)? | **`.claude/settings.json`** — `deny` (nunca) ou `ask` (sempre confirmar) → skill `engineering-house:permission-settings` |
| Descreve **como o sistema se comporta ou o que existe hoje**? | **`docs/<área>/`** |
| É **intenção futura**, ainda não construída? | **`plans/`** |

**As caixas não são exclusivas — e é comum uma regra ocupar duas.** O padrão que funciona: proibição de uma
linha no `CLAUDE.md` + o procedimento inteiro na skill; ou regra escrita + `deny` no `settings.json` que a
torna impossível de violar por acidente. Quando for o caso, proponha as duas.

**Sinais de que você classificou errado:**

- Escreveu mais de ~3 linhas no `CLAUDE.md` → **é skill.** O `CLAUDE.md` custa contexto em toda sessão; a
  skill custa zero até ser usada.
- A skill não tem gatilho claro ("use quando…") → provavelmente é regra universal, vai para o `CLAUDE.md`.
- O `deny` precisa de exceção ("bloqueia, mas quando for X pode") → `deny` não tem exceção; use `ask`, ou
  deixe como regra escrita.
- É sobre o que o sistema **faz**, e você ia escrever no `CLAUDE.md` → é `docs/`.

## 2. Apresente e PARE

Mostre, antes de qualquer escrita:

1. **Destino** (arquivo exato) e **por quê** — citando a linha da tabela que decidiu.
2. **Redação final**, pronta para colar, no tom do arquivo de destino.
3. **O que mais muda junto**: linha nova no `CLAUDE.md` apontando a skill, ponteiro no índice de `plans/`,
   entrada no índice de descoberta, índice de `docs/<área>/README.md`.
4. **Conflito**: se a regra nova contradiz algo já escrito, diga **qual trecho de qual arquivo** ela
   revoga. Regra nova que convive em silêncio com a antiga é como um projeto acumula contradição.
5. **Validade contra o código de hoje.** Antes de escrever, confira que o que a regra afirma sobre o
   repositório **é verdade agora**: o arquivo, a função ou o comando que ela cita existem com esse nome, e
   o comportamento que ela descreve é o que o código faz. **Motivo que não descreve mais o sistema não se
   escreve** — corrija para o estado vivo ou deixe morrer, e **reporte o que estava desatualizado**, porque
   isso é achado, não faxina.

Então **PARE e espere aprovação explícita**. Não use `Edit` nem `Write` antes disso.

## 3. O ARCH do projeto — só com aprovação explícita, SEMPRE

O ARCH é o **padrão de engenharia da plataforma**, compartilhado com outros repositórios — mexer nele muda
a regra de projetos que não estão nesta sessão. Para ele, mesmo depois de a aprovação do resto ter vindo:

- Apresente a redação como **proposta de alteração**, apontando a seção que recebe ou muda.
- Diga **o que quebra nos outros repositórios** se a regra passar a valer lá.
- Espere um **"sim, altera o ARCH"** dito com todas as letras. Aprovação genérica ("aprovado", "pode
  escrever") **não** vale para este arquivo.
- Se a regra nova é uma **exceção** ao padrão e não uma evolução dele, o caminho é um **ADR** em
  `docs/adr/NNNN-*`, não editar o padrão.

**Se um princípio do ARCH é re-enunciado de uma fonte canônica externa** (é o caso dos cinco princípios que
o `engineering-house` instala), mudá-lo aqui não muda a fonte. Diga isso: a mudança de verdade é um commit **no
repositório da fonte**, e quem a faz é o dono dele.

## 4. Escreva

Depois da aprovação: escreva no destino, faça as atualizações do item 2.3, e diga em uma linha o que mudou
onde. Se a regra virou skill nova, confira que a `description` do frontmatter diz **quando usar** — é ela, e
só ela, que faz a skill carregar sozinha na hora certa.

**Escreva no presente**, como a única forma que a coisa já teve. "X deixou de existir", "passou a", "entrou
em tal data" descrevem um diff que o leitor nunca viu e sugerem uma alternativa que não está mais lá. O
motivo fica; a cronologia sai.

**Nunca gere o arquivo de regra por cima de um existente.** Acrescente ou edite o trecho. Se for gerar do
zero, o alvo tem de **não existir**, e isso se confere antes.

## 5. Propague ao repositório irmão — pergunte SEMPRE

Regra escrita aqui e ausente no irmão é como dois repositórios divergem sem que ninguém tenha decidido que
divergissem. Então, **depois de escrever** (§4), o último passo é sempre a mesma pergunta:

> "Quer que eu gere o `/engineering-house:sibling-spec` desta regra para o Claude do `<irmão>`?"

- **Os irmãos são os que o `CLAUDE.md` deste projeto declarar** (a seção "onde está o quê"). Se ele não
  declara nenhum, **pergunte se existe** em vez de concluir que não existe.
- **Pergunte sempre**, mesmo quando a resposta parecer óbvia — quem decide é quem pediu a regra, não você.
- **Se sim:** rode a `sibling-spec` e entregue o **prompt**. Quem escreve lá é o Claude daquele
  repositório, na sessão dele.
- **Você NUNCA edita o repositório irmão daqui.** Não é etiqueta, é segurança: o `settings.json` de lá —
  `deny`, `ask`, hooks — **não governa esta sessão**. Escrever naquele repositório a partir daqui é passar
  por fora dos bloqueios que ele montou para si mesmo, e um dia isso desliga algo que ninguém queria
  desligado. **O prompt atravessa a fronteira; a mão, não.**
- **O prompt tem de levar três coisas:** o texto final da regra, o **motivo** dela (senão vira ordem sem
  contexto, e regra sem porquê é a primeira a ser removida) e o destino provável **lá** — a tabela de
  destinos dele é outra, e o conteúdo também muda. Quem classifica no destino final é o Claude de lá, com
  a skill dele.
- **Se não:** siga em frente e não deixe rastro — regra recusada não vira comentário "considerada e
  descartada" no arquivo.
- **O par fecha nos dois sentidos:** o irmão tem a mesma seção, invertida. Com um lado só, a propagação
  funciona numa direção e a divergência volta pela outra.
