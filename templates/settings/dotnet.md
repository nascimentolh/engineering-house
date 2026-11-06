# Receita — .NET

Some ao [`_base-git.md`](./_base-git.md).

```json
{
  "permissions": {
    "deny": [
      "Read(*.pfx)",
      "Read(*.p12)",
      "Read(secrets.json)",
      "Read(appsettings.Production.json)",
      "Bash(dotnet ef database drop:*)",
      "Bash(dotnet ef migrations remove:*)",
      "Bash(rm *Migrations*)",
      "Bash(mv *Migrations*)",
      "Bash(git checkout *Migrations*)",
      "Bash(git restore *Migrations*)",
      "Bash(dotnet nuget push:*)",
      "Bash(dotnet user-secrets clear:*)"
    ],
    "ask": [
      "Bash(dotnet ef database update:*)",
      "Bash(dotnet ef migrations add:*)",
      "Bash(dotnet add package:*)",
      "Edit(**/Migrations/**)",
      "Write(**/Migrations/**)"
    ],
    "allow": [
      "Bash(dotnet build:*)",
      "Bash(dotnet test:*)",
      "Bash(dotnet run:*)",
      "Bash(dotnet format:*)",
      "Bash(dotnet restore:*)",
      "Bash(dotnet publish:*)",
      "Bash(dotnet ef migrations list:*)",
      "Bash(dotnet ef dbcontext info:*)",
      "Bash(dotnet dev-certs https:*)",
      "Bash(dotnet user-secrets list:*)"
    ]
  }
}
```

## Os pares de isca e controle

| Entrada | Isca (tem de bloquear) | Por que é inócua | Controle (tem de passar) |
|---|---|---|---|
| `Read(*.pfx)` | criar `isca.pfx` com conteúdo marcado e tentar lê-lo | arquivo-isca | ler `isca.pfxx` |
| `Read(*.p12)` | criar `isca.p12` e tentar lê-lo | arquivo-isca | ler `README.md` |
| `Read(secrets.json)` | criar `secrets.json` com `{"isca":"MARCADOR"}` e tentar lê-lo | arquivo-isca | ler `appsettings.json` |
| `Read(appsettings.Production.json)` | criar `appsettings.Production.json` com marcador e tentar lê-lo | arquivo-isca | ler `appsettings.Development.json` |
| `Bash(dotnet ef database drop:*)` | `dotnet ef database drop --dry-run` | dry-run: imprime o que faria | `dotnet ef dbcontext info` |
| `Bash(dotnet ef migrations remove:*)` | `dotnet ef migrations remove --dry-run` | dry-run | `dotnet ef migrations list` |
| `Bash(rm *Migrations*)` | `rm Infra/Migrations/Isca_Inexistente.cs` | alvo inexistente | `rm isca-temp.txt` (criado para isto) |
| `Bash(mv *Migrations*)` | `mv Infra/Migrations/Isca_Inexistente.cs /tmp/x` | alvo inexistente | `mv isca-temp.txt isca-temp2.txt` |
| `Bash(git checkout *Migrations*)` | `git checkout -- Infra/Migrations/Isca_Inexistente.cs` | caminho inexistente; o git erra sem descartar nada | `git checkout -- isca-temp.txt` (rastreado, criado para isto) |
| `Bash(git restore *Migrations*)` | `git restore Infra/Migrations/Isca_Inexistente.cs` | caminho inexistente | `git restore isca-temp.txt` |
| `Bash(dotnet nuget push:*)` | `dotnet nuget push isca-inexistente.nupkg -s https://isca.invalid` | pacote e servidor inexistentes | `dotnet nuget list source` |
| `Bash(dotnet user-secrets clear:*)` | `dotnet user-secrets clear --help` | só imprime ajuda | `dotnet user-secrets list` |
| `Bash(dotnet ef database update:*)` (`ask`) | `dotnet ef database update --dry-run` — tem de **pedir** | dry-run | `dotnet ef migrations list` passa direto |
| `Bash(dotnet add package:*)` (`ask`) | `dotnet add package Isca.Pacote.Inexistente.9z` | pacote inexistente | `dotnet restore` passa direto |
| `Edit(**/Migrations/**)` (`ask`) | editar um arquivo existente em `Migrations/` — tem de **pedir** | é um `ask`, não um `deny`: você confirma e desfaz | editar um arquivo fora de `Migrations/` passa direto |

> ⚠️ **`git checkout` e `git restore` entram no `deny` por um motivo que o `rm` não cobre:** eles são a
> forma mais comum de **descartar** uma migration recém-gerada sem perceber, e não parecem destrutivos.
>
> ⚠️ **Migration gerada não se edita à mão** — o arquivo que o `dotnet ef migrations add` produziu é o que o
> EF sabe reproduzir. Por isso `Migrations/**` é `ask` na escrita e não `allow`: linha escrita por cima dele
> é código que ninguém gerou e ninguém revisou. Precisa de uma a mais? Diga o motivo e espere autorização —
> a resposta pode ser "regera e aceita o resultado do scaffold".
>
> ⚠️ **O padrão `*Migrations*` é largo de propósito**, porque nomes de pasta variam entre projetos. Se o
> controle for bloqueado, estreite-o para o caminho real deste repositório e **refaça o par**.

**A prova é a mensagem de negação da permissão, não o código de saída.** Apague os arquivos-isca ao
terminar e confirme que sumiram.
