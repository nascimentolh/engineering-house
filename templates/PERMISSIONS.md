# PERMISSIONS.md — o registro das permissões provadas

Toda entrada de `deny` e de `ask` do `.claude/settings.json` tem uma linha aqui, **com data**. O
procedimento está na skill `engineering-house:permission-settings`.

Este arquivo existe por dois motivos: evitar re-testar o que já foi provado, e **denunciar o que nunca
foi**. Entrada no `settings.json` sem linha aqui é entrada **não testada** — ou seja, a sensação de
proteção, não a proteção.

> **Como se prova:** a **isca** é o comando que a entrada tem de bloquear; o **controle** é um comando
> legítimo parecido que tem de passar. Os dois se **executam de verdade** — raciocinar sobre o padrão é
> exatamente o raciocínio que falha. A isca tem de ser **inócua se o deny falhar** (alvo inexistente,
> dry-run, arquivo-isca). E **a prova é a mensagem de negação da permissão, não o código de saída**: um
> comando que falhou por "alvo não encontrado" e um que foi bloqueado ambos "não fizeram nada".

## `deny`

| Entrada | Isca (tem de ser bloqueada) | Resultado | Controle (tem de passar) | Resultado | Data |
|---|---|---|---|---|---|
| `<padrão>` | `<comando inócuo>` | bloqueado / **PASSOU** ⚠️ | `<comando legítimo parecido>` | passou / **bloqueado** ⚠️ | `<AAAA-MM-DD>` |

## `ask`

| Entrada | Isca (tem de pedir confirmação) | Resultado | Controle (tem de passar direto) | Resultado | Data |
|---|---|---|---|---|---|
| `<padrão>` | `<comando inócuo>` | pediu / **PASSOU** ⚠️ | `<comando legítimo parecido>` | passou / **pediu** ⚠️ | `<AAAA-MM-DD>` |

## Pendências

<Entrada que está no `settings.json` e ainda não foi provada. Enquanto estiver aqui, ela não conta como
proteção — e a regra escrita no `CLAUDE.md` é o que segura.>
