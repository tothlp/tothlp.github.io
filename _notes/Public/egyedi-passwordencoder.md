---
title: Egyedi passwordEncoder
feed: show
date: 10-06-2023
---

Ha kézzel akarjuk mappelni, hogy melyik típusú jelszót mivel encode-oljuk, akkor hasznos lehet a következő. Akkor is jól jöhet, ha teljesen egyedi passwordEncoder-re van szükségünk, pl SHA1-re.

```kotlin
@Bean
fun delegatingPasswordEncoder(): PasswordEncoder {
    val defaultEncoder: PasswordEncoder = SHA1PasswordEncoder()
    val encoders: MutableMap<String, PasswordEncoder> = HashMap()
    encoders["bcrypt"] = BCryptPasswordEncoder()
    encoders["sha1"] = SHA1PasswordEncoder()
    val passwordEncoder = DelegatingPasswordEncoder(
        "sha1", encoders
    )
    passwordEncoder.setDefaultPasswordEncoderForMatches(defaultEncoder)
    return passwordEncoder
}

class SHA1PasswordEncoder : PasswordEncoder {

    companion object {
        private val INSTANCE: PasswordEncoder = SHA1PasswordEncoder()
        fun getInstance() = INSTANCE
    }

    override fun encode(rawPassword: CharSequence?): String =
        sha1Encode(rawPassword.toString()).apply {
            println("Source: $rawPassword")
            println("Encoded: $this")
        }

    override fun matches(rawPassword: CharSequence?, encodedPassword: String?): Boolean =
        encode(rawPassword) == encodedPassword

    private fun sha1Encode(data: String?): String {
        return DigestUtils.sha1Hex(data)
    }

}
```

Ennek használatához a SecurityConfig-ban kell definiálnunk egy DaoAuthenticationProvider beant, aminek meg lehet adni a passwordEncodert és a userDetailsService-t.

Ezt követően  egy AuthenticationManager bean-t kell létreozni, ami pedig ezt felszedi és megeteti vele a security configunkat.

```kotlin
@Bean
fun authManager(http: HttpSecurity): AuthenticationManager {
    val authenticationManagerBuilder = http.getSharedObject(
        AuthenticationManagerBuilder::class.java
    )
    authenticationManagerBuilder.authenticationProvider(daoAuthenticationProvider())
    return authenticationManagerBuilder.build()
}

@Bean
fun daoAuthenticationProvider(): DaoAuthenticationProvider {
    val daoAuthenticationProvider = DaoAuthenticationProvider()
    daoAuthenticationProvider.setPasswordEncoder(delegatingPasswordEncoder())
    daoAuthenticationProvider.setUserDetailsService(userDetailsService)
    return daoAuthenticationProvider
}
```