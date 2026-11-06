# Receita — Android

Some ao [`_base-git.md`](./_base-git.md) **e** ao [`jvm-gradle.md`](./jvm-gradle.md) — um app Android é um
projeto Gradle, então as entradas de lá (wrapper, `*.jks`, `local.properties`) valem aqui e não se repetem.

```json
{
  "permissions": {
    "deny": [
      "Read(keystore.properties)",
      "Read(**/google-services.json)",
      "Read(*.aab)",
      "Bash(rm *app/schemas*)",
      "Bash(mv *app/schemas*)",
      "Bash(adb shell pm clear:*)",
      "Bash(adb uninstall:*)",
      "Bash(adb shell rm:*)",
      "Bash(adb root:*)",
      "Bash(fastlane deploy:*)",
      "Bash(fastlane release:*)",
      "Bash(./gradlew publishReleaseBundle:*)"
    ],
    "ask": [
      "Bash(./gradlew bundleRelease:*)",
      "Bash(./gradlew assembleRelease:*)",
      "Bash(adb install:*)",
      "Bash(fastlane:*)"
    ],
    "allow": [
      "Bash(./gradlew assembleDebug:*)",
      "Bash(./gradlew installDebug:*)",
      "Bash(./gradlew testDebugUnitTest:*)",
      "Bash(./gradlew connectedDebugAndroidTest:*)",
      "Bash(./gradlew lintDebug:*)",
      "Bash(adb devices:*)",
      "Bash(adb logcat:*)",
      "Bash(adb shell pm list packages:*)",
      "Bash(adb shell dumpsys:*)"
    ]
  }
}
```

## Os pares de isca e controle

| Entrada | Isca (tem de bloquear) | Por que é inócua | Controle (tem de passar) |
|---|---|---|---|
| `Read(keystore.properties)` | criar `keystore.properties` com `storePassword=MARCADOR` e tentar lê-lo | arquivo-isca | ler `keystore.properties.example` |
| `Read(**/google-services.json)` | criar `app/google-services.json` com marcador e tentar lê-lo | arquivo-isca | ler `app/build.gradle.kts` |
| `Read(*.aab)` | criar `isca.aab` com conteúdo marcado e tentar lê-lo | arquivo-isca | ler `isca.aabx` |
| `Bash(rm *app/schemas*)` | `rm app/schemas/isca-inexistente.json` | alvo inexistente | `rm isca-temp.txt` (criado para isto) |
| `Bash(mv *app/schemas*)` | `mv app/schemas/isca-inexistente.json /tmp/x` | alvo inexistente | `mv isca-temp.txt isca-temp2.txt` |
| `Bash(adb shell pm clear:*)` | `adb shell pm clear com.isca.pacote.inexistente` | pacote inexistente; o adb responde "Failure" | `adb shell pm list packages` |
| `Bash(adb uninstall:*)` | `adb uninstall com.isca.pacote.inexistente` | pacote inexistente | `adb shell pm path android` |
| `Bash(adb shell rm:*)` | `adb shell rm /sdcard/isca-inexistente.txt` | alvo inexistente no aparelho | `adb shell ls /sdcard` |
| `Bash(adb root:*)` | `adb root` num emulador sem root, ou sem aparelho conectado | o adb recusa | `adb devices` |
| `Bash(fastlane deploy:*)` | `fastlane deploy --help` | só imprime ajuda; nada sobe para a loja | `fastlane lanes` |
| `Bash(fastlane release:*)` | `fastlane release --help` | só ajuda | `fastlane --version` |
| `Bash(./gradlew publishReleaseBundle:*)` | `./gradlew publishReleaseBundle --dry-run` | dry-run | `./gradlew tasks --dry-run` |
| `Bash(./gradlew bundleRelease:*)` (`ask`) | `./gradlew bundleRelease --dry-run` — tem de **pedir** | dry-run | `./gradlew assembleDebug --dry-run` passa direto |
| `Bash(adb install:*)` (`ask`) | `adb install isca-inexistente.apk` | arquivo inexistente | `adb devices` passa direto |

> ⚠️ **Os schemas exportados do Room (`app/schemas/`) são o `Migrations_*` desta plataforma:** é o histórico
> versionado do banco **no aparelho**. Apagado, quem já tem o app instalado fica órfão na próxima migração —
> e o dado dessa pessoa não está em servidor nenhum para ser recuperado.
>
> ⚠️ **`adb shell pm clear` e `adb uninstall` apagam o dado do app no aparelho**, e não há undo. Num
> aparelho de teste isso é rotina; num aparelho que alguém usa, é perda. O `deny` não distingue os dois, e é
> por isso que ele é `deny` e não `ask`.
>
> ⚠️ **Material de assinatura** (`*.jks`, `keystore.properties`) está no `deny` com um motivo específico:
> perder ou vazar a chave de upload significa **nunca mais atualizar o app** para quem já instalou.

**A prova é a mensagem de negação da permissão, não o código de saída.** Apague os arquivos-isca ao
terminar e confirme que sumiram.
