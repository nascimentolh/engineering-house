---
name: sibling-spec
description: Gera o prompt pronto para o Claude Code de um repositório irmão quando uma mudança deste repositório exige trabalho do outro lado, sem nunca editá-lo
disable-model-invocation: true
---

Uma mudança **deste** repositório exige trabalho de um repositório irmão: $ARGUMENTS

**Você NUNCA edita o repositório irmão.** Seu produto final é um **prompt** que quem pediu cola lá.

O motivo não é etiqueta, é segurança: o `settings.json` daquele repositório — `deny`, `ask`, hooks — e as
regras e os testes dele **não governam esta sessão**. O que sair daqui sai sem as regras que governam
aquele código e sem os guards de lá para acusar. **O prompt atravessa a fronteira; a mão, não.**

## 1. Quem é o irmão

**Os irmãos são os que o `CLAUDE.md` deste projeto declarar** — a seção "onde está o quê". Leia-a antes de
qualquer coisa.

**Se o projeto não declara irmão, ou o alvo não está entre os declarados: PARE e pergunte** qual é o
repositório alvo e onde ele está no disco. Não invente o irmão, e não suponha que ele é o único repositório
que você conhece.

## 2. Levante o que o outro lado precisa fazer

A partir da mudança daqui, responda com precisão — **lendo este repositório**, não adivinhando:

- **O que mudou no contrato**: rota, payload, header, claim, código de erro, permissão, formato de arquivo,
  nome de evento, schema de mensagem — o que for a fronteira entre os dois.
- **O que quebra se o outro lado não acompanhar**, e **quando** quebra (no deploy? na primeira chamada? só
  quando alguém abrir a tela X?).
- **Se é breaking**: pelo princípio **SEM LEGADO**, não existe janela de convivência — a entrega dos dois
  lados é a **mesma** entrega. Diga isso no prompt, explicitamente, e diga **qual lado sobe primeiro**.
- **Se a mudança altera um contrato que está escrito** (uma spec, um documento de acordo entre os dois
  repositórios), o prompt tem de mandar **atualizar esse documento junto** — ele é o acordo, e contrato
  desatualizado é pior que contrato inexistente.

## 3. Confirme que o repositório irmão está acessível — e PARE se não estiver

Você **precisa ler o `CLAUDE.md` do alvo** para escrever no padrão dele. Procure o repositório entre os
diretórios da sessão.

**Se o repositório irmão não estiver acessível, PARE aqui** e peça:

> Não consigo ler o `CLAUDE.md` do `<alvo>` — ele não está anexado nesta sessão. Rode
> `/add-dir <caminho-do-repo>` e me chame de novo. Sem ler as regras de lá, eu escreveria um prompt no
> padrão **deste** repositório, e o Claude de lá teria de adivinhar o que traduzir.

Não invente o padrão do alvo, não escreva "no formato provável dele", e não siga em frente com um prompt
genérico. **Um prompt no padrão errado custa mais caro que a espera.**

## 4. Escreva o prompt no padrão do ALVO

Depois de ler o `CLAUDE.md` do alvo (e o índice de skills dele, se houver):

- **Use o vocabulário e as regras de lá.** Se o alvo tem uma skill que cobre o assunto, **cite-a pelo
  nome** (`use a skill X`), porque é assim que ela carrega. Se o alvo tem regras de estrutura de pasta, de
  rota ou de nomenclatura, use os caminhos **dele**, não os daqui.
- **Use o critério de aceite do alvo** — o **build e o teste daquele** repositório, lidos da seção "Stack e
  comandos" do `CLAUDE.md` dele. Nunca o comando deste projeto.
- **Não copie documentação de contrato para dentro do repositório alvo.** Duplicata apodrece, e contrato
  apodrecido é pior que contrato ausente. Transcreva **inline** os invariantes (os poucos fatos curtos e
  estáveis) e, para a **superfície** (rotas, payloads, campo a campo), mande rodar
  `/add-dir <caminho deste repositório>` e ler a fonte aqui.
- **Diga o que ainda NÃO existe.** Um contrato levantado só com o que está pronto faz o outro lado planejar
  em cima de um vazio. Se algo está pendente, isso vai no prompt com essa palavra.

### ⚠️ Todo exemplo é neutro

Antes de escrever qualquer exemplo no prompt, leia `${CLAUDE_PLUGIN_ROOT}/references/neutral-examples.md` e
**copie a regra para dentro do prompt entregue**. Exemplo não morre no documento: ele vira placeholder,
chave de i18n, massa de teste e seed, e termina na tela de um cliente.

### Estrutura do prompt entregue

1. **Contexto** — o que mudou aqui e por quê (uma frase; o outro lado não tem contexto).
2. **Contrato novo, na íntegra** — a fronteira completa. Copie do código real daqui; não descreva "mais ou
   menos".
3. **O que fazer no repositório alvo**, na ordem, nos caminhos e no padrão dele.
4. **Critério de aceite verificável** no repositório alvo, com o comando dele.
5. **Fora de escopo** — inclusive "não mexa neste repositório; se achar problema aqui, reporte".
6. **Urgência** — é breaking? Então a entrega é conjunta e a ordem de deploy importa: diga qual vai
   primeiro.

## 5. Entregue para colar

Apresente o prompt em **um bloco só**, pronto para copiar, e diga em uma linha em qual repositório colar.
Não execute nada no repositório irmão, nem proponha fazê-lo por ele.
