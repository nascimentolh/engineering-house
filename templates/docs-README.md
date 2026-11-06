<!-- ESQUELETO — vira `docs/README.md`. Substitua tudo entre < > e apague este comentário. -->

# Documentação — <Nome do projeto>

Documentação **viva** do projeto: registra o que **já existe** e como está. À medida que o projeto cresce,
esta pasta cresce junto — um doc por módulo/assunto, sempre atualizado.

> A doc aqui é **complementar** ao [`<PLATAFORMA>-ARCH.md`](../<PLATAFORMA>-ARCH.md) (o padrão de
> engenharia) e ao [`README.md`](../README.md) (setup e operação). Aqui se documenta **o estado real** do
> código: o que está implementado, como funciona e onde encontrar.

## `docs/` é o que JÁ É

Só entra aqui o que **existe e passou**. Intenção futura, faseamento e decisão a bater moram em
[`plans/`](../plans/README.md).

**Hierarquia de fontes: código > `docs/` > `plans/`.** Em qualquer conflito entre esta pasta e o código,
**o código vence** — e a divergência se reporta, não se ignora.

## Índice

| Pasta / Doc | Assunto |
|---|---|
| [adr/](./adr/) | **ADRs** — log imutável e numerado de decisões de arquitetura |
| <[area/](./area/)> | <o que é> |

> **Organização:** a doc é separada por **pasta/assunto** — nunca arquivos `.md` soltos na raiz. Cada pasta
> tem um `README.md` de índice. **ADRs são exceção**: ficam no log `docs/adr/NNNN-*` (imutável, numerado) e
> são **linkadas** pelos índices, nunca movidas.

## Doc-as-you-go

A documentação de uma unidade de trabalho faz parte da entrega dela. O ciclo é:

1. Implementa.
2. **Testa** — `<comando de teste>`, tudo verde.
3. **Documenta aqui** o que ficou pronto.
4. Commita as três coisas juntas (ou em commits encadeados na mesma entrega).

**O que documentar:** o contrato, o fluxo, as decisões não óbvias e os pontos de i18n. E **os índices se
atualizam junto** — doc que existe e não está no índice é doc que ninguém acha.

**Mudança em algo já documentado não se reescreve em silêncio.** <Se este projeto entrega contrato para um
consumidor externo, descreva aqui a convenção de versionamento da doc dele: cabeçalho de versão + data,
tabela de status por feature, e sub-doc de mudança registrando o delta. Se não entrega, apague este
parágrafo.>

## <Convenção de descoberta>

<O índice que impede recriar o que já existe: como se marca um conceito reutilizável, onde ele se registra,
e o comando exato que o varre. Se este projeto não tem um, escreva "não há" — a `engineering-house:spec` lê
isto, e "não há" é informação; omissão faz a skill supor que procurou.>
