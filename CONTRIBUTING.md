# Contribuir com uma receita de `settings.json`

Este repositório aceita **receitas novas** e **correções em receitas existentes**. É a única contribuição
que ele pede ativamente, porque é a única que não dá para escrever de memória: uma receita só existe depois
de alguém ter rodado os comandos numa stack de verdade.

**Toda receita entra por pull request, inclusive as do dono.** Não é cerimônia: **uma receita errada aqui
não quebra um projeto, quebra todos os que a instalarem.** O PR é onde isso é revisado antes de ser verdade
para outras pessoas.

## O que um PR de receita tem de trazer

Sem os quatro, o PR não é revisável — e um PR não revisável é pior que receita ausente, porque parece
coberto.

1. **O arquivo** `templates/settings/<stack>.md`, no formato das receitas que já estão lá: um bloco ` ```json ` com
   `permissions.deny` / `.ask` / `.allow`, e abaixo dele a **tabela de pares**, uma linha por entrada.
2. **Uma linha de tabela por entrada de `deny` e de `ask`** — nenhuma entrada sem par. As colunas são:
   entrada · isca (o comando que ela tem de bloquear) · por que a isca é inócua · controle (o comando
   legítimo parecido que tem de passar).
3. **A prova de que os pares foram EXECUTADOS**, no corpo do PR: para cada par, o resultado, e **a mensagem
   de negação da permissão**. Não o código de saída — um comando que falhou por "alvo não encontrado" e um
   que foi bloqueado ambos "não fizeram nada", e só a mensagem diz qual foi.
4. **A stack e a versão em que você rodou.** Receita provada em Gradle 8 e colada como se valesse em
   qualquer Gradle é palpite com evidência de outra coisa.

## A régua da revisão

O que é olhado, e nessa ordem:

- **Cada `deny` é destrutivo e irreversível?** Se precisa de exceção ("bloqueia, mas quando for X pode"),
  não é `deny` — é `ask`, ou é regra escrita. `deny` não tem exceção.
- **A isca é inócua se o `deny` falhar?** Alvo inexistente, dry-run ou arquivo-isca. **Nunca o comando real
  contra o alvo real** — o teste pressupõe que o bloqueio pode não funcionar, que é literalmente o que ele
  mede. Isca destrutiva num PR é motivo de recusa, mesmo que o bloqueio tenha funcionado naquele dia.
- **O controle existe e passou?** Sem ele, um `deny` largo demais bloqueia tudo, passa no teste de isca e
  inutiliza a stack. O controle é o que distingue bloqueio **seletivo** de bloqueio burro, e é a metade que
  mais costuma ser esquecida.
- **A prova é a mensagem, não o exit code.**
- **O `allow` tira do caminho confirmação inútil, e nada mais.** `allow` não vence hook, e `allow` largo é
  como uma proteção de outra camada desaparece sem ninguém decidir isso.
- **O arquivo não vaza regra de um projeto.** Caminho de um repositório específico, nome de serviço interno,
  nome de cliente: não entram. Receita é da **família de risco** da stack, não do seu repositório. O que é
  só seu mora no `.claude/settings.json` dele.

## Como abrir

A skill `engineering-house:permission-settings` conduz isso e entrega os comandos prontos, na seção "Stack
sem receita". À mão:

```bash
gh repo fork nascimentolh/engineering-house --clone --remote   # se você não é o dono
git switch -c receita-<stack>
# escreva templates/settings/<stack>.md
claude plugin validate .
git add templates/settings/<stack>.md && git commit
gh pr create --repo nascimentolh/engineering-house --fill
```

**Nenhuma sessão de Claude faz o `push` nem abre o PR por você.** Ela monta o branch e devolve o comando —
o mesmo contrato da `sibling-spec`: o prompt atravessa a fronteira, a mão não. Publicar é decisão de quem
responde pela conta.

## O que este repositório não aceita

- **Receita sem os pares executados.** Nem "o padrão está obviamente certo", nem "testei mentalmente". Ler
  o padrão e concluir que ele casa é exatamente o raciocínio que falha: se ele bastasse, padrão inválido
  não passaria calado — e passa, porque não existe validação de sintaxe de permissão.
- **Skill nova de domínio** (dado pessoal, dinheiro, multitenancy, regra de negócio). Este repositório é a
  forma de trabalho, não um sistema.
- **Regra de uma stack no `PRINCIPLES.md`.** Na dúvida entre kernel e receita, é receita — o kernel é o que
  sobra depois de tirar tudo que é de alguém.
