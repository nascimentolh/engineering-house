---
name: handoff
description: Salva o estado da sessão em HANDOFF.md para continuar em uma sessão nova com contexto limpo
disable-model-invocation: true
---

Grave em `HANDOFF.md` na raiz do projeto (sobrescreva se existir):

1. Tarefa em andamento e objetivo original
2. O que já foi FEITO (com a lista de arquivos alterados)
3. O que FALTA fazer
4. Decisões tomadas e por quê
5. **Comandos de verificação deste projeto** — os de build e teste da seção "Stack e comandos" do
   `CLAUDE.md` — e o **último resultado de cada um**, com o texto da falha quando falhou
6. Armadilhas descobertas (o que foi tentado e não funcionou)

Ao terminar, avise: "Handoff pronto — rode `/clear` e cole: leia `HANDOFF.md` e continue a tarefa."
