# Receita — Docker / compose

Some ao [`_base-git.md`](./_base-git.md) e à receita da stack da aplicação.

```json
{
  "permissions": {
    "deny": [
      "Bash(docker volume rm:*)",
      "Bash(docker volume prune:*)",
      "Bash(docker system prune:*)",
      "Bash(docker compose down -v:*)",
      "Bash(docker compose down --volumes:*)",
      "Bash(docker rm -f:*)",
      "Bash(docker push:*)",
      "Bash(docker login:*)",
      "Bash(kubectl delete:*)",
      "Bash(kubectl apply -f * --context prod*)",
      "Bash(helm uninstall:*)"
    ],
    "ask": [
      "Bash(docker build:*)",
      "Bash(docker compose down:*)",
      "Bash(docker image prune:*)",
      "Bash(kubectl apply:*)"
    ],
    "allow": [
      "Bash(docker compose up:*)",
      "Bash(docker compose ps:*)",
      "Bash(docker compose logs:*)",
      "Bash(docker compose exec:*)",
      "Bash(docker ps:*)",
      "Bash(docker logs:*)",
      "Bash(docker inspect:*)",
      "Bash(docker volume ls:*)",
      "Bash(kubectl get:*)",
      "Bash(kubectl describe:*)",
      "Bash(kubectl logs:*)"
    ]
  }
}
```

## Os pares de isca e controle

| Entrada | Isca (tem de bloquear) | Por que é inócua | Controle (tem de passar) |
|---|---|---|---|
| `Bash(docker volume rm:*)` | `docker volume rm isca-volume-inexistente` | volume inexistente; o docker responde "No such volume" | `docker volume ls` |
| `Bash(docker volume prune:*)` | `docker volume prune --filter label=isca-inexistente -f` | o filtro não casa com nada | `docker volume ls --filter dangling=true` |
| `Bash(docker system prune:*)` | `docker system prune --filter label=isca-inexistente -f` | filtro que não casa | `docker system df` |
| `Bash(docker compose down -v:*)` | `docker compose -f isca-inexistente.yml down -v` | arquivo de compose inexistente | `docker compose ps` |
| `Bash(docker compose down --volumes:*)` | `docker compose -f isca-inexistente.yml down --volumes` | arquivo inexistente | `docker compose config --quiet` |
| `Bash(docker rm -f:*)` | `docker rm -f isca-container-inexistente` | container inexistente | `docker ps -a --format "{{.Names}}"` |
| `Bash(docker push:*)` | `docker push isca.invalid/isca:inexistente` | registry inexistente; nada sobe | `docker image ls` |
| `Bash(docker login:*)` | `docker login isca.invalid` | registry inexistente | `docker info` |
| `Bash(kubectl delete:*)` | `kubectl delete pod isca-pod-inexistente --dry-run=client` | dry-run client-side: nada sai da máquina | `kubectl get pods --dry-run=client \|\| kubectl version --client` |
| `Bash(kubectl apply -f * --context prod*)` | `kubectl apply -f isca-inexistente.yml --context prod-isca-inexistente` | arquivo e contexto inexistentes | `kubectl apply -f isca-inexistente.yml --context dev-isca-inexistente` ⚠️ ver nota |
| `Bash(helm uninstall:*)` | `helm uninstall isca-release-inexistente --dry-run` | dry-run | `helm list` |
| `Bash(docker compose down:*)` (`ask`) | `docker compose -f isca-inexistente.yml down` — tem de **pedir** | arquivo inexistente | `docker compose ps` passa direto |
| `Bash(docker build:*)` (`ask`) | `docker build -f isca-Dockerfile-inexistente .` | Dockerfile inexistente | `docker image ls` passa direto |
| `Bash(kubectl apply:*)` (`ask`) | `kubectl apply -f isca-inexistente.yml --dry-run=client` | dry-run client-side | `kubectl get nodes` passa direto |

> ⚠️ **O controle de `kubectl apply -f * --context prod*` é a linha mais importante da tabela**, e é a que
> costuma falhar: o padrão tem de pegar **só** o contexto de produção e deixar passar o de
> desenvolvimento. Se o controle for bloqueado, o `deny` está largo demais e você acabou de inutilizar o
> `kubectl` do dia a dia — estreite o padrão e **refaça o par**.
>
> ⚠️ **`-v` / `--volumes` é a diferença entre "para os containers" e "apaga o banco".** `docker compose
> down` sem a flag é reversível e por isso é `ask`; com a flag é perda de dado e por isso é `deny`. Duas
> entradas, porque são duas coisas.
>
> ⚠️ **`prune` sem filtro atinge o que você não está olhando** — imagem, cache e volume de outro projeto na
> mesma máquina. É por isso que a isca usa um filtro que não casa com nada: ela mede o bloqueio sem apagar
> nada de ninguém.

**A prova é a mensagem de negação da permissão, não o código de saída.**
