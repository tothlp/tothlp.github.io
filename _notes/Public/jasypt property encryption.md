---
title: Jasypt property encryption
feed: show
date: 19-03-2024
---

Property encryption-höz a JASYPT használható, legegyszerűbben a Springboot-starter segítségével. [https://github.com/ulisesbocchio/jasypt-spring-boot](https://github.com/ulisesbocchio/jasypt-spring-boot)

A springboot-starer igazából ahhoz kell, hogy az alkalmazás tudja a titkosított property-ket dekódolni. A titkosításra magára a JASYPT-et kell használni, vagy valamilyen online tool, vagy a lib segítségével.

A titkosításhoz szükséges jelszó átadható envvar-ként, program argumentként, az `application.yml`-ben, vagy beégetve az alkalmazásba.

## Minimal setup

1. Gradle dependency:

```kotlin
    implementation("com.github.ulisesbocchio:jasypt-spring-boot-starter:3.0.5")
```

2. Config létrehozása

```kotlin
@Configuration
class JasyptEncryptor {
    @Bean("jasyptStringEncryptor")
    fun stringEncryptor(): StringEncryptor =
        SimpleStringPBEConfig().apply {
            password = "superSecretLongPassword4!"
            poolSize = 1
        }
            .let { return PooledPBEStringEncryptor().apply { setConfig(it) } }
}

```

Ezt követően a titkosított property-ket a `ENC()` függvénnyel lehet megadni, pl.:

```yaml
app:
    password: ENC(encryptedPassword)
```

A starter by default egy jasyptStringEncryptor nevű bean-t vár, de ezt a `jasypt.encryptor.bean` property-vel felül lehet csapni.

Fontos: a `@Bean` legyen egy `@Configuration` osztályban, mert a spring különben nem fogja észlelni. lsd: [[Bean not found]]
