# Receita base — git (vale em toda stack)

Some esta receita à da stack. Ela cobre só o que é git: o resto vem do arquivo da stack.

> **Por que a receita é `.md` e não `.json`:** cada entrada de `deny` tem de trazer o par de isca e
> controle ao lado dela, e JSON não tem comentário. Cole o bloco abaixo no `.claude/settings.json` do
> projeto e mantenha a tabela aqui, no `PERMISSIONS.md` dele.

```json
{
  "$schema": "https://json.schemastore.org/claude-code-settings.json",
  "permissions": {
    "deny": [
      "Read(.env)",
      "Read(.env.*)",
      "Read(*.pem)",
      "Read(*.key)",
      "Read(id_rsa)",
      "Read(id_ed25519)",
      "Bash(git push --force:*)",
      "Bash(git push -f:*)",
      "Bash(git push * --force*)",
      "Bash(git push * -f *)",
      "Bash(git reset --hard:*)",
      "Bash(git clean *)",
      "Bash(git filter-branch:*)",
      "Bash(git reflog expire:*)"
    ],
    "ask": [
      "Bash(git push:*)",
      "Bash(git rebase:*)",
      "Bash(git tag -d:*)",
      "Bash(git branch -D:*)"
    ],
    "allow": [
      "Bash(git status:*)",
      "Bash(git diff:*)",
      "Bash(git log:*)",
      "Bash(git show:*)",
      "Bash(git add:*)",
      "Bash(git commit:*)",
      "Bash(git branch:*)",
      "Bash(git switch:*)",
      "Bash(git checkout:*)",
      "Bash(git stash:*)",
      "Bash(git fetch:*)",
      "Bash(git merge --no-ff:*)"
    ]
  }
}
```

## Os pares de isca e controle

| Entrada | Isca (tem de bloquear) | Por que é inócua | Controle (tem de passar) |
|---|---|---|---|
| `Read(.env)` | criar `.env` com `ISCA=marcador` e tentar lê-lo | você lê o seu próprio marcador | ler `.env.example` |
| `Read(*.pem)` | criar `isca.pem` com conteúdo marcado e tentar lê-lo | arquivo-isca | ler `README.md` |
| `Read(*.key)` | criar `isca.key` e tentar lê-lo | arquivo-isca | ler `isca.keyx` |
| `Read(id_rsa)` | criar `id_rsa` com conteúdo marcado e tentar lê-lo | arquivo-isca | ler `id_rsa.pub` |
| `Bash(git push --force:*)` | `git push --force isca-remote-inexistente main` | o git erra sem contactar nada | `git fetch --dry-run` |
| `Bash(git push -f:*)` | `git push -f isca-remote-inexistente main` | remote falso | `git log --oneline -1` |
| `Bash(git push * --force*)` | `git push isca-remote-inexistente main --force` | remote falso | `git push isca-remote-inexistente main --dry-run` ⚠️ ver nota |
| `Bash(git reset --hard:*)` | `git reset --hard isca-ref-inexistente` | ref falsa, nada é descartado | `git reset isca-ref-inexistente` |
| `Bash(git clean *)` | `git clean -nd` | dry-run do próprio git | `git status --porcelain` |
| `Bash(git filter-branch:*)` | `git filter-branch --help` | só imprime ajuda | `git log --format=%H -1` |
| `Bash(git reflog expire:*)` | `git reflog expire --dry-run --all` | dry-run | `git reflog --oneline -1` |
| `Bash(git push:*)` (`ask`) | `git push isca-remote-inexistente main` — tem de **pedir confirmação** | remote falso | `git fetch isca-remote-inexistente` tem de passar direto |
| `Bash(git rebase:*)` (`ask`) | `git rebase isca-ref-inexistente` | ref falsa | `git merge-base HEAD HEAD` |
| `Bash(git branch -D:*)` (`ask`) | `git branch -D isca-branch-inexistente` | branch falsa | `git branch --list` |

> ⚠️ **O controle da linha `git push * --force*` é o que mais costuma falhar**, e é o ponto do teste: o
> padrão é largo e pode engolir um `push` legítimo com flags depois do remote. Se o controle for bloqueado,
> o `deny` está largo demais — estreite o padrão e **refaça o par**.

**Depois de cada arquivo-isca:** apague e confirme que sumiu.

**A prova é a mensagem de negação da permissão, não o código de saída.**
