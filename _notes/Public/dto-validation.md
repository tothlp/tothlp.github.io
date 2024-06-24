---
title: Kotlin - DTO validálás
feed: show
date: 23-02-2024
---

Ez egy igen régi probléma. Ez most a legutóbbi működő verzió, Jakartával működik már a javax package-ek helyett, szóval eléggé up-to-date.

Ami kell hozzá:

```kotlin
implementation("org.springframework.boot:spring-boot-starter-validation")
```

Nem muszáj, de esetleg kellhet:

```kotlin
implementation("jakarta.validation:jakarta.validation-api:3.0.2")
```

A DTO-ra kell controllerből a `@Valid`

```kotlin
fun createUser(@RequestBody @Valid request: UserCreateRequest) = 
    userService.createUser(request)
```

DTO szinten a validációs annotációk pedig a getterre kellenek:

```kotlin
data class UserCreateRequest(
    val userCode: String,

    val fullName: String,

    @Optional
    @get:Email(regexp = Constants.EMAIL_REGEX)
    val email1: String?,

    @Optional
    @get:Email(regexp = Constants.EMAIL_REGEX)
    val email2: String?,

    @get:JsonFormat(shape = JsonFormat.Shape.STRING, pattern = Constants.DATE_TIME_FORMAT_PATTERN)
    val validityStart: LocalDateTime,

    @Optional
    @get:JsonFormat(shape = JsonFormat.Shape.STRING, pattern = Constants.DATE_TIME_FORMAT_PATTERN)
    val validityEnd: LocalDateTime?,

    val password: String,

    val passwordAgain: String?
)
```

Email validációhoz: Működik a sima is, de érdemes saját regexet megadni, mert az eredeti megengedi a laci@hello jellegű címeket is…

```kotlin
/**
         * See: https://emailregex.com/. The email validation of spring simply accepts user@domain type strings, without country code.
         */
        const val EMAIL_REGEX =
            "(?:[a-z0-9!#\$%&'*+/=?^_`{|}~-]+(?:\\.[a-z0-9!#\$%&'*+/=?^_`{|}~-]+)*|\"(?:[\\x01-\\x08\\x0b\\x0c\\x0e-\\x1f\\x21\\x23-\\x5b\\x5d-\\x7f]|\\\\[\\x01-\\x09\\x0b\\x0c\\x0e-\\x7f])*\")@(?:(?:[a-z0-9](?:[a-z0-9-]*[a-z0-9])?\\.)+[a-z0-9](?:[a-z0-9-]*[a-z0-9])?|\\[(?:(?:25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\\.){3}(?:25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?|[a-z0-9-]*[a-z0-9]:(?:[\\x01-\\x08\\x0b\\x0c\\x0e-\\x1f\\x21-\\x5a\\x53-\\x7f]|\\\\[\\x01-\\x09\\x0b\\x0c\\x0e-\\x7f])+)\\])"
    }
}
```

Ha összetett object-et kell validálni, akkor nem elég a Controllerben feltenni a `@Valid` annotációt, hanem a child objectekre is kell. Pl:

```kotlin
import javax.validation.Valid
import javax.validation.constraints.NotBlank

data class DocumentRequest(
	@field:NotBlank var ucr: String,
	@field:Valid var okmany: Document
)

data class Document(
	@field:NotBlank var fajlnev: String,
	var tartalom: String
)
```

**2024.03.28**: Java 17-tel legújabb tapasztalat, hogy a sima classoknál simán kellenek a validációs annotációk, a data classoknál pedig a field-ekre kellenek. Jakartával működik, a jakarta-beli annotációkkal használjuk.

Ha össze akarjuk szedni a hibákat, le lehet egy globális error handlerrel kezelni kifejezetten a validációs hibákat:

```kotlin
@RestControllerAdvice(annotations = [RestController::class, Controller::class])

class ValidationErrorHandler {
  @ResponseStatus(HttpStatus.BAD_REQUEST)
  @ExceptionHandler(MethodArgumentNotValidException::class)
  fun handleValidationExceptions(
    ex: MethodArgumentNotValidException
  ): Map<String, String?> {
    val errors: MutableMap<String, String?> = HashMap()
    ex.bindingResult.allErrors.forEach(Consumer { error: ObjectError ->
      val fieldName = (error as FieldError).field
      val errorMessage = error.code
      errors[fieldName] = errorMessage
    })
    return errors
  }
}
```

Itt nyilván tök mindegy, hogy milyen formában adjuk vissza, a lényeg, hogy kinyerhetőek az üzenetek, a hibás property nevek, a hibás adatok, minden.

Ha saját, egyedi formátumban akarjuk visszaadni a hibaüzeneteket, pl. ugyanaz a dto való a sima response-ra és a hibára, csak pl. egy error property-be akarjuk beírni, akkor nyilván nem lesz jó az automatikus validálás a `@Valid`-dal. Ilyenkor kézzel kell meghívni a validálást, a következő módon:

```kotlin
import jakarta.validation.Validator 

// Very simplified example
class TestService(private val validator: Validator) { 
	fun validateTest(test: Test) = validator.validate(test)
}
```

**2024.06.17:**
Bővebb infók: [https://jakarta.ee/learn/docs/jakartaee-tutorial/current/beanvalidation/bean-validation/bean-validation.html](https://jakarta.ee/learn/docs/jakartaee-tutorial/current/beanvalidation/bean-validation/bean-validation.html)