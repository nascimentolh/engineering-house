# engineering-house

Um plugin de Claude Code que instala uma **forma de trabalho** em qualquer projeto, de qualquer stack.

Três coisas, e só três:

- **[`PRINCIPLES.md`](./PRINCIPLES.md)** — o kernel: cinco princípios de engenharia que não dependem de
  stack, a regra que governa as outras, a hierarquia de fontes, e o critério de permissão.
- **`skills/`** — os workflows que fazem o kernel acontecer.
- **[`templates/`](./templates/)** — as receitas para materializar o kernel num projeto concreto.

**O que ele não é:** não é framework, não é gerador de código de produto, não é opinião sobre linguagem. Se
um artefato só faz sentido numa stack, ele não entra aqui.

---

> Nos comandos abaixo, `<CAMINHO-DO-CLONE>` é a pasta onde você clonou este repositório — por exemplo
> `~/engineering-house` ou `C:/Users/<você>/engineering-house`. Barras normais funcionam nos dois sistemas.

## Pré-requisitos

- **Claude Code CLI ≥ 2.1.319.** Confira com `claude --version`.
- **git.** O plugin é um repositório local; não há publicação nem remote.

## Instalar

O repositório é, ao mesmo tempo, o plugin e o marketplace que o serve — então são dois comandos:

```bash
claude plugin marketplace add <CAMINHO-DO-CLONE>
claude plugin install engineering-house@engineering-house
```

`claude plugin install` aceita `--scope user` (padrão, vale para você em toda máquina), `--scope project`
(vai para o `.claude/settings.json` do projeto, e vale para quem clonar) ou `--scope local`.

**Antes de instalar em qualquer lugar, valide:**

```bash
claude plugin validate <CAMINHO-DO-CLONE>
```

Exit 0 = passou (com ou sem warning). Exit 1 = falhou. **Manifesto inválido não dá erro útil:** o plugin
simplesmente não carrega, e tudo o que vem depois parece funcionar sem funcionar.

## Confirmar que carregou

Três provas, e a terceira é a que importa:

```bash
claude plugin list          # o engineering-house aparece, com a versão
```

Numa sessão:

1. `/plugin list` — o `engineering-house` está na aba de instalados.
2. Digite `/` — os seis workflows aparecem como `/engineering-house:<nome>`.
3. **A prova negativa:** desinstale (`claude plugin uninstall engineering-house@engineering-house`) e confirme que
   eles **desaparecem**. Sem essa metade você não sabe se o que viu veio do plugin ou do contexto da
   sessão.

## Os workflows

Skills de plugin se invocam pelo nome completo — **`/spec` não existe, `/engineering-house:spec` existe.**

| Workflow | O que faz | Quem invoca |
|---|---|---|
| **`/engineering-house:spec`** | Pedido informal → prompt oficial executável: decompõe antes de ler o repositório, acha o dono de cada informação, mapeia quem age e o caminho da pessoa, escreve **critério de aceite nas duas metades** (o que a máquina confere **e** o que quem pediu vê na tela), lista o fora de escopo com motivo e gatilho de volta, e **para** para aprovação | **só você** (`disable-model-invocation`) |
| **`/engineering-house:handoff`** | Salva o estado da sessão em `HANDOFF.md` para continuar com contexto limpo | **só você** |
| **`/engineering-house:new-rule`** | Classifica onde uma regra nova mora (`CLAUDE.md` · skill · `settings.json` · ARCH · `docs/` · `plans/`), só escreve depois de aprovada, e pergunta se ela vale no repositório irmão | **só você** |
| **`/engineering-house:sibling-spec`** | Monta o prompt pronto para o Claude de um repositório irmão, no padrão **dele**, sem nunca editá-lo | **só você** |
| **`/engineering-house:new-project`** | Gera o prompt de bootstrap de um repositório novo: instala este plugin lá e materializa as receitas, com validação por isca e controle | **só você** |
| **`/engineering-house:permission-settings`** | O procedimento de **isca e controle**, e o diagnóstico de "escrita recusada" | você **ou** o modelo, quando o assunto aparece |

**Por que cinco são `disable-model-invocation`:** elas são de quem pede, não do modelo. Uma skill que o
modelo pode chamar é uma skill que ele chama em vez de parar e perguntar — e o ponto da `spec` é justamente
a parada.

## Usar as receitas num projeto

A `/engineering-house:new-project` faz isso por você, gerando o prompt de bootstrap. À mão, de
[`templates/`](./templates/):

| Template | Vira |
|---|---|
| [`CLAUDE.md`](./templates/CLAUDE.md) | o `CLAUDE.md` do projeto — **a seção "Stack e comandos" é lida pelas skills** |
| [`PLATFORM-ARCH.md`](./templates/PLATFORM-ARCH.md) | `<PLATAFORMA>-ARCH.md`, nomeado pela **plataforma-alvo**, nunca pela linguagem |
| [`settings/`](./templates/settings/) | `.claude/settings.json` — `_base-git.md` + o arquivo da stack |
| [`PERMISSIONS.md`](./templates/PERMISSIONS.md) | `PERMISSIONS.md` — o registro das permissões provadas |
| [`docs-README.md`](./templates/docs-README.md) · [`plans-README.md`](./templates/plans-README.md) | `docs/README.md` e `plans/README.md` |

**O ARCH re-enuncia os cinco princípios como texto próprio**, e isso não é redundância: o projeto tem de se
sustentar para quem clona e lê **sem este plugin instalado**.

### As receitas de `settings.json`

Uma por família de risco, e **cada entrada de `deny` traz o par de isca e controle ao lado dela** — sem o
par, a receita é palpite.

| Receita | Cobre |
|---|---|
| [`_base-git.md`](./templates/settings/_base-git.md) | git e segredos em arquivo. **Some a todas as outras** |
| [`node.md`](./templates/settings/node.md) | npm/pnpm/yarn, lockfile, publish, Prisma/Drizzle |
| [`python.md`](./templates/settings/python.md) | pip/poetry/uv, migrations de Django e Alembic, twine |
| [`jvm-gradle.md`](./templates/settings/jvm-gradle.md) | Gradle e Maven, wrapper, Flyway, material de assinatura |
| [`android.md`](./templates/settings/android.md) | adb, schemas do Room, keystore, Play Store. Some ao `jvm-gradle` |
| [`dotnet.md`](./templates/settings/dotnet.md) | `dotnet ef`, Migrations, NuGet, user-secrets |
| [`docker.md`](./templates/settings/docker.md) | volumes, prune, push, kubectl e helm |

**Elas são `.md` e não `.json` de propósito:** o par de isca e controle tem de viver ao lado da entrada que
ele prova, e JSON não tem comentário. Cole o bloco `json` no `settings.json` do projeto e mantenha a tabela
no `PERMISSIONS.md` dele.

⚠️ **Receita não testada neste plugin não quebra um projeto — quebra todos os que a instalarem.** As
tabelas trazem o par já escrito; **executá-lo é parte de adotar a receita**, e a skill
`engineering-house:permission-settings` conduz isso.

### Sua stack não está na lista?

A skill `engineering-house:permission-settings` monta a receita a partir dos riscos reais dela, executa os
pares, e **oferece devolvê-la para cá por pull request** — sem isso, o próximo projeto da mesma stack paga
o mesmo trabalho. **Toda receita entra por PR, inclusive as do dono**, porque uma receita errada aqui não
quebra um projeto: quebra todos os que a instalarem. A régua da revisão e o que o PR tem de trazer estão em
**[CONTRIBUTING.md](./CONTRIBUTING.md)**.

Nenhuma sessão de Claude dá `push` nem abre o PR por você: ela monta o branch e devolve o comando.

## Atualizar

Um plugin que vem de um marketplace de **caminho local** é lido **no lugar**. Então:

```
# editou um arquivo do plugin
/reload-plugins        # numa sessão aberta
```

Não é preciso subir `version` no `plugin.json`, e não é preciso reinstalar. Numa sessão nova, a edição já
vale. Suba a `version` quando quiser marcar um ponto no tempo — não para fazer uma mudança valer.

Para desenvolver sem instalar nada:

```bash
claude --plugin-dir <CAMINHO-DO-CLONE>
```

## Desinstalar

```bash
claude plugin uninstall engineering-house@engineering-house
claude plugin marketplace remove engineering-house      # também desinstala os plugins dele
```

---

## Procedência

A fonte canônica dos cinco princípios é o padrão de engenharia .NET de um repositório privado da casa.
Este repositório os **re-enuncia** stack-agnósticos, e os ARCHs que ele gera apontam para a fonte
canônica — não para cá. Promover este plugin a fonte canônica é mudança de regra **no repositório da
fonte**, num commit do dono dele.
