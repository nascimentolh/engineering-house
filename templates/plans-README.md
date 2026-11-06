<!-- ESQUELETO — vira `plans/README.md`. Substitua tudo entre < > e apague este comentário. -->

# plans/ — planos e processo (NÃO é documentação do sistema)

Working area de **planos, blueprints e levantamentos "a executar"**. Separado de
[`docs/`](../docs/README.md).

- **`docs/`** = só documentação do que **já está pronto** (como o sistema É).
- **`plans/`** = o que **vamos fazer** (processo, faseamento, decisões a bater).

> ⚠️ **Hierarquia de fontes: código > `docs/` > `plans/`.** Um plano informa **intenção**, nunca estado
> atual — ele pode ter sido executado, abandonado ou contradito pelo código sem que o arquivo tenha sido
> atualizado. Em qualquer conflito, valem o **código** e o **`docs/`**.

## Ciclo de vida de um plano

Virou realidade → a doc estável migra para `docs/` → **o plano é apagado** (está no git;
`git log --diff-filter=D -- plans/` acha). Se o valor que sobra não é o estado mas o **porquê** da decisão,
ele não vira `docs/`: vira **ADR** em `docs/adr/`, e aí o plano morre.

**Plano que fica para sempre é a terceira fonte da verdade que ninguém consultou.** Apagar é parte do
ciclo, não faxina.

## Índice

<Comece pelo roadmap, se houver.>

| Arquivo | O que é |
|---|---|
| <[plano.md](./plano.md)> | <o que é, e o estado conferido contra o código: entregue / parcial / nada executado> |

### Frentes em execução

| Arquivo | O que é |
|---|---|
| <...> | <...> |

### Regras permanentes (o plano é o critério, não some quando "termina")

<Plano que fica porque é a leitura obrigatória de uma skill ou o critério permanente de um assunto.
Diga por que ele não morre.>

### Ainda não começados

| Arquivo | O que é |
|---|---|
| <...> | <...> |

### Bootstrap de repositório novo (gerados pela skill `engineering-house:new-project`)

Prompt pronto para colar no Claude do repositório alvo. **Não é doc deste projeto** — quando o repositório
estiver de pé e validado, o arquivo morre.

| Arquivo | O que é |
|---|---|
| <[bootstrap-x.md](./bootstrap-x.md)> | <o repositório alvo e o estado dele> |
