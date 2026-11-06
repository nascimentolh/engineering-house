<!--
  ESQUELETO DO ARCH — renomeie o arquivo PELA PLATAFORMA-ALVO, nunca pela linguagem:
  ANDROID-ARCH.md, IOS-ARCH.md, WEB-ARCH.md, GAMESERVER-ARCH.md, CLI-ARCH.md.
  A mesma linguagem serve plataformas com convenções diferentes — é a plataforma que decide o padrão.
  Substitua tudo entre < > e apague este comentário.
-->

# <PLATAFORMA>-ARCH.md — padrão de engenharia

## 1. Objetivo e escopo

Este documento define o **padrão arquitetural obrigatório** para <a plataforma / os projetos desta
plataforma>. Ele estabelece como organizamos código, como separamos responsabilidades, como acessamos dados
e como versionamos a plataforma.

## 2. Regras invioláveis

As regras abaixo **não admitem exceção**. Qualquer desvio precisa de um **ADR aprovado antes do merge**
(`docs/adr/NNNN-*`).

### 2.1 Os cinco princípios

1. **SEM LEGADO.** Não existe "deprecated", fallback, caminho duplo ou código morto. Mudou o desenho?
   Reescreve em cima ou deleta e refaz — **por inteiro, na mesma entrega**, migrando todos os consumidores.
   Rastro de transição = entrega não pronta. Vale também para o texto: escreva no presente, como a única
   forma que a coisa já teve; o motivo fica, a cronologia sai.

2. **Doc-as-you-go.** A documentação de uma unidade de trabalho faz parte da entrega dela, nunca de uma fase
   posterior. Se fica para "quando terminar 100%", ela não acontece.

3. **i18n nasce localizada.** Nenhuma mensagem ao usuário — erro ou sucesso — é string literal no código,
   nem como fallback. Código estável e machine-readable no código; texto no catálogo de recursos, resolvido
   na borda conforme o idioma pedido, com fallback declarado. <Nesta plataforma, o catálogo é
   `<arquivo/mecanismo>`, e os idiomas são `<lista>`.> Texto retrofitado nunca fica completo.

4. **Identificadores em inglês.** Conversa em <idioma>, código em inglês, e a tradução se faz sozinha ao
   materializar. Rótulo visível pode ser <idioma> (é recurso de i18n); nome de classe, função, propriedade,
   arquivo ou pasta, nunca. O identificador também é neutro de país.

5. **Terminou = implementar + testar + documentar**, na mesma entrega.

### 2.2 Regras desta plataforma

<As regras invioláveis que saem da pesquisa da Fase 1: direção de dependência, organização do código,
limites de módulo, estratégia de acesso a dados, política de versão da plataforma. Uma linha por regra, com
o motivo.>

## 3. <Estrutura de pastas>

<A estrutura canônica, à risca, vinda da pesquisa. Diga o que vai onde e o que NÃO pode estar em cada
lugar.>

## 4. <Organização do código>

<Como o código se organiza: por feature, por camada, por tela — o que a convenção viva da plataforma manda,
com a fonte e a data da pesquisa.>

## 5. <Acesso a dados / persistência>

<O que usa o quê, e onde cada coisa é proibida.>

## 6. <Testes>

<Famílias de teste, o que é obrigatório, o que é declarado como ausente de propósito, e o comando que roda
cada coisa. O comando também está no `CLAUDE.md`: aqui mora a estratégia, lá mora o atalho.>

## 7. <Versões>

<A política de versão da plataforma e das dependências principais, e onde ela está fixada no repositório.>

## 8. Resumo: Faça / Não faça

| ✅ Faça | ❌ Não faça |
|---|---|
| <...> | <...> |

## 9. Governança e exceções

Toda exceção a este padrão exige um **ADR aprovado** antes do merge, registrando contexto, decisão e
consequências. PR que viole as regras invioláveis da Seção 2 sem ADR é reprovado no code review.

## 10. Procedência dos cinco princípios

> A fonte canônica dos cinco princípios da Seção 2.1 é o `<ARQUIVO>` do `<REPOSITÓRIO>`. Eles estão
> re-enunciados aqui para que este repositório se sustente sozinho — se um princípio mudar na fonte, os
> re-enunciados acompanham.

<!--
  Preencha `<ARQUIVO>` e `<REPOSITÓRIO>` com a fonte canônica real ao materializar este ARCH. O ponteiro
  tem de ser nominal: "um repositório privado da casa" não diz a ninguém para onde olhar quando um
  princípio mudar. Este template não nomeia a fonte porque ele mora num repositório público.
-->

Mudar o texto da Seção 2.1 **aqui** não muda a fonte: a mudança de verdade é um commit **no repositório da
fonte**, e quem a faz é o dono dele. O procedimento está na skill `engineering-house:new-rule`, §3.
