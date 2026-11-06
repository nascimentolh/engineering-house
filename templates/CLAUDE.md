<!--
  ESQUELETO — substitua tudo entre < > e apague este comentário.
  Régua: regra que não cabe em ~3 linhas NÃO fica aqui, é skill. Este arquivo custa contexto em TODA
  sessão; uma skill custa zero até ser usada.
-->

# CLAUDE.md — <Nome do projeto>

## Stack e comandos

<Plataforma, linguagem e versão fixada, frameworks, dependências de infra. Duas ou três linhas.>

```bash
<comando de build>          # compila
<comando de teste>          # testes
<comando de execução>       # sobe o projeto localmente
<comando de lint/format>    # formatação e análise estática
```

> **Esta seção é lida pelas skills.** A `engineering-house:spec` tira daqui o critério de aceite, e a
> `engineering-house:handoff` tira os comandos de verificação. Comando errado aqui é suíte verde que não provou
> nada — mantenha-a verdadeira.

<Pré-requisitos reais para rodar: credencial, variável de ambiente, serviço de pé, login. E o que
NÃO existe: perfil offline, banco em container, mock de terceiro — dizer que não existe evita que
alguém invente.>

## Onde está o quê

- **Arquitetura e padrão de engenharia** → **[`<PLATAFORMA>-ARCH.md`](./<PLATAFORMA>-ARCH.md)**. Leia antes
  de gerar, sugerir ou revisar qualquer código.
- **Regras de negócio / o sistema como ele É** → **[`docs/`](./docs/README.md)**.
- **Planos, decisões a bater, histórico** → **[`plans/`](./plans/README.md)**.
- **Setup e operação** → **[`README.md`](./README.md)** — é a fonte de verdade dos comandos de ambiente;
  não replique os blocos dele em outro lugar.
- **Registro das permissões testadas** → **[`PERMISSIONS.md`](./PERMISSIONS.md)**.
- **Repositórios irmãos:** <nome e caminho de cada um, e o que cada um é. Se não há nenhum, escreva "não
  há" — a `engineering-house:sibling-spec` lê esta linha, e "não há" é informação, omissão não é.>

## Pedido que fere regra escrita — PARE e pergunte

**Regra inviolável, e ela governa as outras.** Pedido — do usuário, de um ingresso, de uma doc — que colide
com regra escrita aqui, no `<PLATAFORMA>-ARCH.md` ou numa skill: **não execute e não obedeça em silêncio.**
(1) Diga qual regra, citando o trecho e o arquivo — o que ela manda, não o número. (2) Diga o que quebra na
prática. (3) Ofereça as duas saídas, porque as duas são legítimas: atender de outro jeito, ou **mudar a
regra** — ela está escrita para ser discutida, não para ser sagrada. (4) Espere a decisão; quem decide é
quem pediu.

O modo de falha que isto evita não é o desacordo — é a **erosão**: uma exceção "só desta vez" que ninguém
registrou vira precedente. Vale no inverso: regra que já não descreve o sistema é dívida — traga a
divergência.

## Regras universais (a proibição fica aqui; o procedimento está na skill)

- **SEM LEGADO.** Não existe "deprecated", "fallback", "manter por compatibilidade", caminho duplo ou
  código morto. Mudou o desenho? Reescreve em cima ou deleta e refaz — **por inteiro, na mesma entrega**,
  migrando TODOS os consumidores. Rastro de transição = entrega não pronta.
- **Terminar = implementar + testar + documentar** em `docs/` na mesma entrega — nunca acumular código sem
  doc nem teste.
- **Nenhuma mensagem ao usuário (erro ou sucesso) é string literal** — nem como fallback. Texto no catálogo
  de recursos, código machine-readable no código. <Diga onde é o catálogo nesta stack.>
- **Conversa em <idioma>, código em inglês.** Você **traduz sozinho** ao materializar (código, schema,
  rota, permissão, enum, arquivo, pasta). **Não pergunte "que nome em inglês?"** nem confirme tradução.
  Rótulo visível pode ser <idioma>; identificador, nunca.
- **A hierarquia de fontes é código > `docs/` > `plans/`.** Plano informa intenção, nunca estado atual.
- **Antes de criar coisa reutilizável, procure o que já existe** — <o índice de descoberta deste projeto, e
  o comando exato que o varre>.
- **Entrada nova de `deny`/`ask` no `.claude/settings.json` não existe até ser testada** com isca (comando
  que ela tem de bloquear) e controle (comando legítimo parecido que tem de passar) — padrão inválido não dá
  erro, só não pega; e a isca tem de ser inócua se o deny falhar → skill **engineering-house:permission-settings**.
- **O `deny` é a rede, não a regra.** Ele não cobre toda forma de produzir o mesmo efeito e pode nem estar
  valendo na sessão. Precisa mexer no que ele protege? **PARE e pergunte.**
- **Commit só a pedido.** Não commite sem ordem explícita — nem "para não perder", nem como passo final de
  outra tarefa.
- <Acrescente aqui as regras universais DESTE projeto, uma linha cada, terminando em `→ skill <nome>`
  quando houver procedimento. Passou de ~3 linhas? É skill.>

## Workflows

Instalados pelo plugin **engineering-house** — invoque pelo nome completo:

`/engineering-house:spec` — monta o prompt oficial de uma tarefa a partir de uma descrição informal, pergunta o
que falta e só executa depois da sua aprovação. `/engineering-house:handoff` — salva o estado da sessão em
`HANDOFF.md` para continuar com contexto limpo. `/engineering-house:new-rule` — classifica onde uma regra nova
deve morar (aqui, numa skill, no `settings.json`, no ARCH, em `docs/` ou em `plans/`), só escreve depois de
aprovado, e pergunta se a regra vale também no repositório irmão. `/engineering-house:sibling-spec` — gera o
prompt pronto para o Claude do repositório irmão quando uma mudança daqui exige trabalho lá.
`/engineering-house:new-project` — recebe stack e propósito de um repositório NOVO e gera o prompt de bootstrap
dele. `/engineering-house:permission-settings` — o procedimento de isca e controle, e o diagnóstico de escrita
recusada.

⚠️ **Spec é SEMPRE a `/engineering-house:spec` — nunca outra skill de desenho.** Vale quando o pedido não a
cita, quando quem pediu esquece, e principalmente quando outra skill instalada descreve por conta própria o
que fazer diante de um pedido vago. **Nenhuma delas conhece as fontes deste projeto** — a hierarquia
código > `docs/` > `plans/`, nem a separação entre `docs/` (o que já É) e `plans/` (o que vamos fazer). Elas
levantam menos, perguntam o que o repositório já responde, e escrevem documento narrativo onde aqui se quer
**prompt executável com critério de aceite** — no diretório errado, ainda por cima.

**A `/engineering-house:spec` é `disable-model-invocation`, e isso é intencional: ela é de quem pede, não do
modelo.** Então não a substitua por uma que você *pode* chamar. **PARE e devolva o comando pronto para
copiar** — o `/engineering-house:spec` mais o texto da tarefa, já com o que a sessão levantou, para quem pediu
só colar e mandar.
