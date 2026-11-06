# Receita — Python (pip / poetry / uv)

Some ao [`_base-git.md`](./_base-git.md).

```json
{
  "permissions": {
    "deny": [
      "Read(.pypirc)",
      "Read(*.env)",
      "Read(secrets.py)",
      "Read(local_settings.py)",
      "Bash(twine upload:*)",
      "Bash(poetry publish:*)",
      "Bash(uv publish:*)",
      "Bash(rm *migrations*)",
      "Bash(mv *migrations*)",
      "Bash(rm *alembic/versions*)",
      "Bash(rm *poetry.lock*)",
      "Bash(rm *uv.lock*)",
      "Bash(rm *requirements*.txt*)",
      "Bash(python manage.py flush:*)",
      "Bash(python manage.py reset_db:*)",
      "Bash(alembic downgrade base:*)",
      "Bash(pip install --break-system-packages:*)"
    ],
    "ask": [
      "Bash(pip install:*)",
      "Bash(poetry add:*)",
      "Bash(uv add:*)",
      "Bash(python manage.py migrate:*)",
      "Bash(alembic upgrade:*)",
      "Bash(alembic downgrade:*)"
    ],
    "allow": [
      "Bash(python -m pytest:*)",
      "Bash(pytest:*)",
      "Bash(python -m mypy:*)",
      "Bash(ruff:*)",
      "Bash(black:*)",
      "Bash(python -m build:*)",
      "Bash(pip install -r requirements.txt:*)",
      "Bash(poetry install:*)",
      "Bash(uv sync:*)",
      "Bash(python manage.py makemigrations:*)",
      "Bash(python manage.py showmigrations:*)",
      "Bash(python manage.py runserver:*)",
      "Bash(alembic revision:*)",
      "Bash(alembic current:*)",
      "Bash(alembic history:*)"
    ]
  }
}
```

## Os pares de isca e controle

| Entrada | Isca (tem de bloquear) | Por que é inócua | Controle (tem de passar) |
|---|---|---|---|
| `Read(.pypirc)` | criar `.pypirc` com `password = MARCADOR` e tentar lê-lo | arquivo-isca | ler `pyproject.toml` |
| `Read(*.env)` | criar `isca.env` com marcador e tentar lê-lo | arquivo-isca | ler `isca.envx` |
| `Read(secrets.py)` | criar `secrets.py` com `ISCA = "marcador"` e tentar lê-lo | arquivo-isca | ler `settings.py` |
| `Read(local_settings.py)` | criar `local_settings.py` com marcador e tentar lê-lo | arquivo-isca | ler `local_settings.example.py` |
| `Bash(twine upload:*)` | `twine upload --repository isca-inexistente dist/*` | repositório inexistente; nada sobe | `twine check dist/* \|\| true` |
| `Bash(poetry publish:*)` | `poetry publish --dry-run` | dry-run | `poetry check` |
| `Bash(uv publish:*)` | `uv publish --help` | só ajuda | `uv --version` |
| `Bash(rm *migrations*)` | `rm app/migrations/isca_inexistente.py` | alvo inexistente | `rm isca-temp.txt` (criado para isto) |
| `Bash(mv *migrations*)` | `mv app/migrations/isca_inexistente.py /tmp/x` | alvo inexistente | `mv isca-temp.txt isca-temp2.txt` |
| `Bash(rm *alembic/versions*)` | `rm alembic/versions/isca_inexistente.py` | alvo inexistente | `rm isca-temp.txt` |
| `Bash(rm *poetry.lock*)` | `rm poetry.lock.isca-inexistente` | alvo inexistente | `rm isca-temp.txt` |
| `Bash(rm *uv.lock*)` | `rm uv.lock.isca-inexistente` | alvo inexistente | `rm isca-temp.txt` |
| `Bash(rm *requirements*.txt*)` | `rm requirements-isca-inexistente.txt` | alvo inexistente | `rm isca-temp.txt` |
| `Bash(python manage.py flush:*)` | `python manage.py flush --help` | só ajuda, nenhum banco é tocado | `python manage.py showmigrations` |
| `Bash(python manage.py reset_db:*)` | `python manage.py reset_db --help` | só ajuda | `python manage.py check` |
| `Bash(alembic downgrade base:*)` | `alembic downgrade base --sql` | gera SQL na tela, não executa | `alembic current` |
| `Bash(pip install --break-system-packages:*)` | `pip install --break-system-packages isca-pacote-9z` | pacote inexistente | `pip install -r requirements.txt --dry-run` |
| `Bash(pip install:*)` (`ask`) | `pip install isca-pacote-que-nao-existe-9z` — tem de **pedir** | pacote inexistente | `pytest --version` passa direto |
| `Bash(alembic upgrade:*)` (`ask`) | `alembic upgrade head --sql` | gera SQL, não executa | `alembic history` passa direto |

> ⚠️ **`migrations` é o `Migrations_*` desta stack:** é o histórico versionado do schema. Apagada, o banco
> de quem já aplicou fica órfão. O `deny` cobre `rm` e `mv` — **ele não cobre `find -delete`, um script, nem
> o editor**, e é por isso que a regra escrita no `CLAUDE.md` tem de dizer que **o proibido é o efeito, não
> a ferramenta**.

**A prova é a mensagem de negação da permissão, não o código de saída.** Apague os arquivos-isca ao
terminar e confirme que sumiram.
