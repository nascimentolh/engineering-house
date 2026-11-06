# Receita — JVM / Gradle (e Maven)

Some ao [`_base-git.md`](./_base-git.md).

```json
{
  "permissions": {
    "deny": [
      "Read(gradle.properties)",
      "Read(*.jks)",
      "Read(*.keystore)",
      "Read(*.p12)",
      "Read(local.properties)",
      "Read(**/src/main/resources/application-prod*)",
      "Bash(rm *gradle/wrapper*)",
      "Bash(mv *gradle/wrapper*)",
      "Bash(rm *gradle.lockfile*)",
      "Bash(rm *src/main/resources/db/migration*)",
      "Bash(./gradlew publish:*)",
      "Bash(./gradlew publishToMavenLocal --refresh-dependencies:*)",
      "Bash(mvn deploy:*)",
      "Bash(./gradlew flywayClean:*)",
      "Bash(mvn flyway:clean:*)"
    ],
    "ask": [
      "Bash(./gradlew flywayMigrate:*)",
      "Bash(mvn flyway:migrate:*)",
      "Bash(./gradlew --refresh-dependencies:*)",
      "Bash(./gradlew wrapper:*)"
    ],
    "allow": [
      "Bash(./gradlew build:*)",
      "Bash(./gradlew test:*)",
      "Bash(./gradlew check:*)",
      "Bash(./gradlew assemble:*)",
      "Bash(./gradlew bootRun:*)",
      "Bash(./gradlew tasks:*)",
      "Bash(./gradlew dependencies:*)",
      "Bash(./gradlew ktlintCheck:*)",
      "Bash(./gradlew spotlessApply:*)",
      "Bash(mvn test:*)",
      "Bash(mvn verify:*)",
      "Bash(mvn compile:*)",
      "Bash(./gradlew flywayInfo:*)"
    ]
  }
}
```

## Os pares de isca e controle

| Entrada | Isca (tem de bloquear) | Por que é inócua | Controle (tem de passar) |
|---|---|---|---|
| `Read(gradle.properties)` | criar `gradle.properties` com `iscaPassword=MARCADOR` e tentar lê-lo | arquivo-isca | ler `settings.gradle.kts` |
| `Read(*.jks)` | criar `isca.jks` com conteúdo marcado e tentar lê-lo | arquivo-isca | ler `build.gradle.kts` |
| `Read(*.keystore)` | criar `isca.keystore` e tentar lê-lo | arquivo-isca | ler `isca.keystorex` |
| `Read(*.p12)` | criar `isca.p12` e tentar lê-lo | arquivo-isca | ler `README.md` |
| `Read(local.properties)` | criar `local.properties` com marcador e tentar lê-lo | arquivo-isca | ler `local.properties.example` |
| `Read(**/src/main/resources/application-prod*)` | criar `src/main/resources/application-prod.yml` com marcador e tentar lê-lo | arquivo-isca | ler `src/main/resources/application.yml` |
| `Bash(rm *gradle/wrapper*)` | `rm gradle/wrapper/isca-inexistente.jar` | alvo inexistente | `rm isca-temp.txt` (criado para isto) |
| `Bash(mv *gradle/wrapper*)` | `mv gradle/wrapper/isca-inexistente.jar /tmp/x` | alvo inexistente | `mv isca-temp.txt isca-temp2.txt` |
| `Bash(rm *gradle.lockfile*)` | `rm gradle.lockfile.isca-inexistente` | alvo inexistente | `rm isca-temp.txt` |
| `Bash(rm *src/main/resources/db/migration*)` | `rm src/main/resources/db/migration/V999__isca.sql` | alvo inexistente | `rm isca-temp.txt` |
| `Bash(./gradlew publish:*)` | `./gradlew publish --dry-run` | dry-run do Gradle: monta o grafo e não executa | `./gradlew tasks --dry-run` |
| `Bash(mvn deploy:*)` | `mvn deploy -DskipTests -o -DaltDeploymentRepository=isca::default::file:///isca-inexistente` | repositório inexistente, offline | `mvn validate -o` |
| `Bash(./gradlew flywayClean:*)` | `./gradlew flywayClean --dry-run` | dry-run; nenhum schema é apagado | `./gradlew flywayInfo --dry-run` |
| `Bash(mvn flyway:clean:*)` | `mvn flyway:clean -Dflyway.url=jdbc:postgresql://isca-host-inexistente/x` | host inexistente | `mvn flyway:info -o \|\| true` |
| `Bash(./gradlew flywayMigrate:*)` (`ask`) | `./gradlew flywayMigrate --dry-run` — tem de **pedir** | dry-run | `./gradlew flywayInfo` passa direto |
| `Bash(./gradlew wrapper:*)` (`ask`) | `./gradlew wrapper --dry-run` | dry-run | `./gradlew --version` passa direto |

> ⚠️ **O wrapper do Gradle é o que torna o build reproduzível.** Apagado, cada máquina resolve uma versão
> diferente de Gradle, e "funciona aqui" deixa de significar nada. É por isso que ele está no `deny`, e não
> no `ask`.
>
> ⚠️ **As migrations do Flyway/Liquibase são o histórico versionado do schema**, igual ao `migrations` de
> qualquer stack: apagadas, o banco de quem já aplicou fica órfão.

**A prova é a mensagem de negação da permissão, não o código de saída.** Apague os arquivos-isca ao
terminar e confirme que sumiram.
