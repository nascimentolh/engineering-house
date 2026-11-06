# Receita — Node / npm / pnpm / yarn

Some ao [`_base-git.md`](./_base-git.md).

```json
{
  "permissions": {
    "deny": [
      "Read(.npmrc)",
      "Read(.env.local)",
      "Read(.env.production*)",
      "Bash(npm publish:*)",
      "Bash(pnpm publish:*)",
      "Bash(yarn publish:*)",
      "Bash(npm config set //*)",
      "Bash(npm token:*)",
      "Bash(rm *node_modules*)",
      "Bash(rm *package-lock.json*)",
      "Bash(rm *pnpm-lock.yaml*)",
      "Bash(rm *yarn.lock*)",
      "Bash(npm cache clean:*)",
      "Bash(npx prisma migrate reset:*)",
      "Bash(npx prisma db push --force-reset:*)",
      "Bash(npx drizzle-kit drop:*)"
    ],
    "ask": [
      "Bash(npm install:*)",
      "Bash(pnpm add:*)",
      "Bash(yarn add:*)",
      "Bash(npx prisma migrate deploy:*)",
      "Bash(npx prisma migrate dev:*)"
    ],
    "allow": [
      "Bash(npm run:*)",
      "Bash(pnpm run:*)",
      "Bash(yarn run:*)",
      "Bash(npm test:*)",
      "Bash(npm ci:*)",
      "Bash(pnpm install --frozen-lockfile:*)",
      "Bash(npx tsc:*)",
      "Bash(npx eslint:*)",
      "Bash(npx prettier:*)",
      "Bash(npx vitest:*)",
      "Bash(npx jest:*)",
      "Bash(node:*)",
      "Bash(npx prisma generate:*)",
      "Bash(npx prisma migrate status:*)"
    ]
  }
}
```

## Os pares de isca e controle

| Entrada | Isca (tem de bloquear) | Por que é inócua | Controle (tem de passar) |
|---|---|---|---|
| `Read(.npmrc)` | criar `.npmrc` com `//isca:_authToken=MARCADOR` e tentar lê-lo | token falso, arquivo-isca | ler `package.json` |
| `Read(.env.local)` | criar `.env.local` com `ISCA=marcador` e tentar lê-lo | arquivo-isca | ler `.env.example` |
| `Read(.env.production*)` | criar `.env.production` com marcador e tentar lê-lo | arquivo-isca | ler `.env.development` |
| `Bash(npm publish:*)` | `npm publish --dry-run` | dry-run: não sobe nada | `npm pack --dry-run` |
| `Bash(npm config set //*)` | `npm config set //isca.invalid/:_authToken=X` | registry inexistente | `npm config get registry` |
| `Bash(npm token:*)` | `npm token list` | só lista; o deny é o que se mede | `npm whoami \|\| true` |
| `Bash(rm *node_modules*)` | `rm -rf node_modules-isca-inexistente` | alvo inexistente | `rm` num arquivo temporário que você criou |
| `Bash(rm *package-lock.json*)` | `rm package-lock.json.isca-inexistente` | alvo inexistente | `rm isca-temp.txt` (criado para isto) |
| `Bash(rm *pnpm-lock.yaml*)` | `rm pnpm-lock.yaml.isca-inexistente` | alvo inexistente | `rm isca-temp.txt` |
| `Bash(rm *yarn.lock*)` | `rm yarn.lock.isca-inexistente` | alvo inexistente | `rm isca-temp.txt` |
| `Bash(npm cache clean:*)` | `npm cache clean --dry-run` | dry-run | `npm cache ls \|\| npm cache verify` |
| `Bash(npx prisma migrate reset:*)` | `npx prisma migrate reset --help` | só imprime ajuda, nenhum banco é tocado | `npx prisma migrate status` |
| `Bash(npx prisma db push --force-reset:*)` | `npx prisma db push --force-reset --help` | só ajuda | `npx prisma generate --help` |
| `Bash(npx drizzle-kit drop:*)` | `npx drizzle-kit drop --help` | só ajuda | `npx drizzle-kit --help` |
| `Bash(npm install:*)` (`ask`) | `npm install isca-pacote-que-nao-existe-9z` — tem de **pedir** | pacote inexistente; o registry recusa | `npm ci --dry-run` tem de passar direto |
| `Bash(npx prisma migrate dev:*)` (`ask`) | `npx prisma migrate dev --help` | só ajuda | `npx prisma migrate status` passa direto |

> ⚠️ **`Bash(rm *package-lock.json*)` é a entrada mais fácil de errar:** o `*` no começo e no fim é o que
> pega `rm ./package-lock.json` e `rm -rf dir/package-lock.json`. Se a isca passar, é quase sempre o `*`
> faltando de um lado. **O lockfile é o histórico versionado da resolução de dependência deste projeto** —
> apagado "para regerar limpo", ele traz versões que ninguém revisou.

**A prova é a mensagem de negação da permissão, não o código de saída.** Apague os arquivos-isca ao
terminar e confirme que sumiram.
