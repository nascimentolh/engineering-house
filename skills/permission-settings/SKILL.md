---
name: permission-settings
description: Use ao criar ou alterar qualquer entrada de permissão no `.claude/settings.json` (deny/ask/allow), ao montar o `settings.json` de um projeto cuja stack NÃO tem receita pronta em templates/settings/, ao investigar por que uma regra de permissão não pegou, quando a sessão parece não estar respeitando o settings do projeto, ou quando uma escrita foi recusada (hook, classificador ou ferramenta de arquivo).
---

# Entrada de permissão só existe depois de testada

Uma entrada de `deny` que você escreveu e não testou **não é proteção — é a sensação dela**, que é pior,
porque faz confiar. Toda entrada nova ou alterada passa pelo par **isca + controle** antes de a entrega
fechar. O registro do que já foi provado mora no `PERMISSIONS.md` da raiz deste projeto (o esqueleto está
em `${CLAUDE_PLUGIN_ROOT}/templates/PERMISSIONS.md`).

## Os dois modos de falha (nenhum aparece sem rodar o comando)

1. **Padrão inválido não dá erro — ele simplesmente não pega.** Não existe validação de sintaxe, nem aviso
   no boot, nem log. Um `deny` com glob errado, prefixo trocado ou `:*` faltando fica lá, parecendo
   proteção, e nunca é consultado.
2. **O settings do projeto pode não estar carregado na sessão.** O arquivo pode estar certo e valendo nada
   naquela sessão específica.

É por isso que o `deny` é **a rede, não a regra**: a regra escrita continua valendo mesmo quando a rede tem
furo, e é ela que segura quando o `deny` não segura.

## O par

- **Isca** — o comando que a entrada tem de **bloquear**.
- **Controle** — um comando legítimo **parecido**, que tem de **passar**.

Sem o controle, `deny` largo demais passa no teste de isca e inutiliza a stack: bloquear tudo "funciona"
para a isca e quebra o dia a dia. O controle é o que distingue bloqueio **seletivo** de bloqueio burro.

**Execute os dois de verdade.** Ler o padrão e concluir que ele casa é exatamente o raciocínio que falha —
se ele bastasse, os dois modos de falha acima nunca teriam existido.

## A isca tem de ser INÓCUA SE O DENY FALHAR

**Nunca use o comando real contra o alvo real.** O teste pressupõe que o `deny` pode não funcionar — é
literalmente o que ele está medindo. Testar um bloqueio destrutivo deixando-o destruir não é teste, é
acidente.

Quatro formas de tornar a isca inócua, todas preservando o sinal:

| Forma | Exemplo | Se o deny falhar |
|---|---|---|
| **Alvo inexistente** | `<comando> <alvo-que-não-existe>` | a ferramenta responde "não encontrado"; nada acontece |
| **Remote/ref falso** | `git push --force <remote-inexistente> main` · `git reset --hard <ref-inexistente>` | o git erra sem tocar em nada |
| **Dry-run** | `git clean -nd` · a flag de dry-run da ferramenta da stack | imprime o que faria |
| **Arquivo-isca** | criar `isca.<ext>` com conteúdo marcado e tentar lê-lo ou apagá-lo | você lê ou apaga o seu próprio marcador |

**A prova é a mensagem de negação da permissão, não o código de saída.** Um comando que falhou por "alvo
não encontrado" e um que foi bloqueado ambos "não fizeram nada" — só a mensagem diz qual foi. **Leia a
mensagem.**

Terminou o teste com arquivo-isca? **Apague o arquivo** e confirme que sumiu.

## Quando a isca passa

O padrão está inválido ou o settings não está carregado. **Conserte e refaça o par** — entrada corrigida
sem re-teste volta à estaca zero. Se o padrão estiver certo e ainda assim passar, o problema é
carregamento: reabra a sessão e refaça.

## Ao terminar

Registre no `PERMISSIONS.md`: entrada, isca, resultado, controle e a data. É o registro que evita re-testar
o que já foi provado e que **denuncia o que nunca foi**.

## Escolher a caixa certa

| Caixa | Quando |
|---|---|
| `deny` | destrutivo e irreversível. **Não tem exceção** — se precisa de "bloqueia, mas quando for X pode", não é `deny` |
| `ask` | irreversível, mas legítimo no dia a dia |
| `allow` | rotina; tirar do caminho o que só gera confirmação inútil |

`deny` cobre **comando**, não intenção: ele não sabe se houve ordem explícita e não cobre toda forma de
produzir o mesmo efeito. Por isso ele acompanha a regra escrita, nunca a substitui.

## Escrita recusada — são três mecanismos, cada um com uma saída

**Leia o texto da recusa**, diga qual dos três é, e **entregue a linha pronta** para o dono rodar. Não
tente a mesma coisa duas vezes esperando outro resultado, e não fique inventando caminho alternativo.

| Texto da recusa | Quem é | Saída |
|---|---|---|
| uma mensagem de um **hook** do projeto (o texto é o que o script imprime) | **hook** | só editando o hook — `allow` **NÃO** vence hook |
| `Blocked by classifier` | **classificador** do modo auto | uma regra de `allow` no `settings.local.json` |
| `The user doesn't want to proceed` **sem diálogo na tela** | **travamento** da ferramenta de arquivo | escreva num diretório temporário do projeto e peça um `cp` |

**Como distinguir sem chutar — meça, com isca e controle:** tente **três linhas inócuas** no caminho
recusado (se cair igual, **o conteúdo não é o gatilho**) e a **mesma escrita num caminho diferente** do
projeto (se passa, o problema é o prefixo, não você). É a diferença entre "o classificador não gosta do que
escrevi" e "este caminho está barrado".

**Os comandos, para o dono colar com `!` na frente** (roda na conta dele, não na da sessão):

```
python3 -c "import json,os;p='.claude/settings.local.json';d=json.load(open(p)) if os.path.exists(p) else {};a=d.setdefault('permissions',{}).setdefault('allow',[]);[a.append(r) for r in ['Write(.claude/**)','Edit(.claude/**)','Write(CLAUDE.md)','Edit(CLAUDE.md)'] if r not in a];json.dump(d,open(p,'w'),indent=2)"
```
↑ **classificador**: libera escrita em `.claude/**` e no `CLAUDE.md`. Acrescente `'Bash(cp *)'` e
`'Bash(cat *)'` à lista se o shell também estiver sendo barrado.

```
cp <temp>/<arquivo> <destino>
```
↑ **travamento da ferramenta**: a sessão escreve o conteúdo pronto num diretório temporário (que sempre
passa) e o `cp` do dono o põe no lugar.

**Três armadilhas medidas:**

1. **O menu de permissões pode não persistir** — ele aceita a entrada e o `settings.local.json` continua
   sem ela. **Confira o arquivo** antes de tentar escrever de novo.
2. **`git checkout` para descartar trabalho reverte o hook junto** — ele é arquivo rastreado, e a trava que
   o dono tinha tirado volta. Depois de um descarte, **assuma que o hook voltou ao original**.
3. **Pular o prompt de permissão não serve** — a flag que ignora confirmação não vence hook nem
   classificador.

## Stack sem receita: monte, prove, e devolva para a casa

**Primeiro confira se já existe.** Liste `${CLAUDE_PLUGIN_ROOT}/templates/settings/` e procure a família de
risco desta stack. `_base-git.md` soma a todas; algumas somam entre si (`android.md` soma ao
`jvm-gradle.md`). Reusar uma receita provada é sempre melhor que escrever outra.

**Não achou?** Então monte a partir dos **riscos reais desta stack** — não copie os padrões de outra, porque
eles nunca vão casar com nada aqui, e **deny que não casa é deny que não existe**. Três perguntas dão a
lista inteira:

- **O que, nesta stack, é destrutivo e irreversível?** Normalmente: o histórico versionado do schema (ele
  tem um nome diferente em cada stack e é sempre a coisa mais fácil de apagar "para regerar limpo"), o
  material de assinatura e os segredos em arquivo, o lockfile, o que publica para fora, e o que apaga dado
  que não está em git nenhum — volume, banco local, dado no aparelho. → `deny`
- **O que é irreversível mas legítimo no dia a dia?** → `ask`
- **O que só gera confirmação inútil?** → `allow`

**Cada entrada nasce com o par escrito ao lado dela, e o par se EXECUTA.** Até isso acontecer você tem um
rascunho, não uma receita. A isca tem de ser inócua se o `deny` falhar, e a prova é a mensagem de negação.

### Então ofereça o PR — e a oferta é obrigatória

Depois que os pares passaram, **diga a quem pediu, em uma linha, que esta receita pode voltar para o
`engineering-house`** e pergunte se ele quer. Duas razões, e as duas são dele:

- A receita custou trabalho real. Sem ela no plugin, **o próximo projeto desta stack paga o mesmo preço** —
  e quem paga é ele.
- Receita volta **por PR, nunca por commit direto**, inclusive para o dono do repositório. Uma receita
  errada no plugin não quebra um projeto: quebra **todos** os que a instalarem. O PR é onde isso é revisado
  antes de virar verdade para outras pessoas. A régua da revisão está em
  `${CLAUDE_PLUGIN_ROOT}/CONTRIBUTING.md`.

**Se ele disser sim, você monta e PARA.** Monte o arquivo `templates/settings/<stack>.md` no formato das
outras (bloco `json` + tabela de pares) e entregue **os comandos prontos para ele rodar com `!`**:

```
gh repo view --json nameWithOwner,viewerPermission -q '.nameWithOwner, .viewerPermission'
```
↑ rode isto **dentro** de `${CLAUDE_PLUGIN_ROOT}` primeiro: ele diz se quem está rodando pode abrir branch
direto ou precisa de fork. Se `${CLAUDE_PLUGIN_ROOT}` não for um clone git (o plugin pode estar num cache),
o caminho é clonar o repositório à parte e montar o branch lá.

```
git -C <clone> switch -c receita-<stack> && git -C <clone> add templates/settings/<stack>.md && git -C <clone> commit
gh pr create --repo <dono>/engineering-house --fill
```

⚠️ **Você NUNCA dá `push` nem abre o PR.** É o mesmo contrato da `sibling-spec`, pela mesma razão: publicar
é decisão de quem responde pela conta, e o repositório de destino não é governado por esta sessão. **O
prompt atravessa a fronteira; a mão, não.**

⚠️ **E o corpo do PR leva a prova, não a promessa:** para cada par, o resultado e **a mensagem de negação**.
Sem isso o revisor não tem o que revisar, e um PR não revisável é pior que receita ausente — ele parece
coberto.

**Se ele disser não:** siga em frente e não deixe rastro. A receita fica no `settings.json` e no
`PERMISSIONS.md` daquele projeto, que é onde ela já serve.
