---
title: Retrofit
feed: show
date: 18-06-2024
---

A [Retrofit](https://square.github.io/retrofit/) library segítségével annotációk segítségével tudunk HTTP kéréseket kezelni. Gyakorlatilag egy interface-t kell megírnunk, amiben definiáljuk a végpontokat, ahhoz hasonlóan, mintha a saját REST Controllerünket hoznánk létre. A Retrofit a háttérben (I guess reflection-nel) létrehozza belőle az implementációt.

Előnyök:
- Nem kell kézzel kliens kódot írni, az adott apikat sima metódusként tudjuk hívni
- Többféle parser támogatott (Gson, Moshi, stb.)
- Többféle klienst támogat (OkHttp, stb.)
- Könnyen kiterjeszthető, pl. interceptorokkal

Hátrány:
- Reflection-ök használata
- Mivel annotációkat használunk, így nem tudjuk minden részét kivezetni az application.yml-be, mivel azokból nem lesz fordítás idejű konstans. Lsd: [[Kotlin - Static konstansok]]

A példa az [OkHttp](https://square.github.io/okhttp/) klienst használja.

## Dependencies


```kotlin
	implementation("com.squareup.retrofit2:retrofit:2.11.0")
	implementation("com.squareup.retrofit2:converter-jackson:2.11.0")
```

Suspend és egyéb coroutine fgv-ek használatához:

```kotlin
	implementation("org.jetbrains.kotlinx:kotlinx-coroutines-core:1.8.0")
```

## Használat

Az API leírásához csak a következő szükséges:

```kotlin
interface CoffeeApiClient {
	@POST("coffee")
	suspend fun updateCoffee(@Body request: CoffeeRequest, @Query("coffeeId") coffeeId: Int): Response<Unit>
}

```

Ezt nyilván szét lehet még customizálni egyéb header-ök, query paraméterek, stb. segítségével.


Ha ez megvan, akkor létre kell hozni egy Retrofit példányt:

```kotlin
class CoffeeApiClientFactory(
	private val jsonMapper: ObjectMapper,
	private val config: Config,
	private val tokenAuthenticator: TokenAuthenticator,
	private val defaultHeadersInterceptor: DefaultHeadersInterceptor,
	private val defaultParamsInterceptor: DefaultParamsInterceptor,
	private val tokenInterceptor: TokenInterceptor,
): ClientFactory<CoffeeApiClient> {

	override fun createClient(): CoffeeApiClient {

		val client = OkHttpClient.Builder()
			.addInterceptor(tokenInterceptor)
			.authenticator(tokenAuthenticator)
			.addInterceptor(defaultHeadersInterceptor)
			.addInterceptor(defaultParamsInterceptor)
			.build()

		val retrofit = Retrofit.Builder()
			.client(client)
			.baseUrl(config.baseUrl!!)
			.addConverterFactory(JacksonConverterFactory.create(jsonMapper))
			.build()

		val service: CoffeeApiClient = retrofit.create(CoffeeApiClient::class.java)
		return service
	}

}
```

Ezt követően már hívhatóak is az interface-ben definiált végpontok. Ez a CoffeeApiClientFactory már nem egy minimalista megvalósítás, de megmutatja, hogy hogy lehet egyedi interceptorokat, authentikációt, stb. beállítani. Ezek nyilván elhagyhatóak.

## Példa interceptorok

```kotlin
class TokenInterceptor(
	private val tokenRepository: TokenRepository
) : Interceptor {

	override fun intercept(chain: Interceptor.Chain): Response {
		val accessToken: String? = tokenRepository.getAccessToken()
		val request: Request = newRequestWithAccessToken(chain.request(), accessToken)
		val response: Response = chain.proceed(request)

		return response
	}

	private fun newRequestWithAccessToken(request: Request, accessToken: String?): Request {
		if(accessToken == null) return request
		return request.newBuilder()
			.header("Authorization", "Bearer $accessToken")
			.build()
	}
}
```

Ez az interceptor lekéri a tárolt tokent, majd beállítja a request Authorization header-jébe. Ha nincs token, vagy lejárt, akkor a requestet változatlanul továbbítja. Ekkor a kérésünk valószínűleg egy 403-mas hibát fog kapni. 

Ha 403-mas hibát kapunk, akkor a beállított Authenticator fogja kezelni a hibát.

```kotlin
class TokenAuthenticator(
	private val tokenRepository: TokenRepository
) : Authenticator {

	// AtomicBoolean in order to avoid race condition
	private var tokenRefreshInProgress: AtomicBoolean = AtomicBoolean(false)
	private var request: Request? = null

	override fun authenticate(route: Route?, response: Response): Request? {
		return runBlocking {
			request = null

			// Checking if a token refresh call is already in progress or not
			// The first request will enter the if block
			// Later requests will enter the else block
			if (!tokenRefreshInProgress.get()) {
				tokenRefreshInProgress.set(true)
				// Refreshing token
				refreshToken()
				request = buildRequest(response.request.newBuilder())
				tokenRefreshInProgress.set(false)
			} else {
				// Waiting for the ongoing request to finish
				// So that we don't refresh our token multiple times
				waitForRefresh(response)
			}

			// return null to stop retrying once responseCount returns 3 or above.
			if (responseCount(response) >= 3) {
				null
			} else request
		}
	}

	// Refresh your token here and save them.
	private suspend fun refreshToken() {
		// Simulating a token refresh request
		delay(200)
		tokenRepository.refreshToken()
		delay(200)
	}

	// Queuing the requests with delay
	private suspend fun waitForRefresh(response: Response) {
		while (tokenRefreshInProgress.get()) {
			delay(100)
		}
		request = buildRequest(response.request.newBuilder())
	}

	private fun responseCount(response: Response?): Int {
		var result = 1
		while (response?.priorResponse != null && result <= 3) {
			result++
		}
		return result
	}

	// Build a new request with new access token
	private fun buildRequest(requestBuilder: Request.Builder): Request {
		val token = tokenRepository.getAccessToken()
		return requestBuilder
			.header(HEADER_CONTENT_TYPE, HEADER_CONTENT_TYPE_VALUE)
			.header(HEADER_AUTHORIZATION, HEADER_AUTHORIZATION_TYPE + token)
			.build()
	}

	companion object {
		const val HEADER_AUTHORIZATION = "Authorization"
		const val HEADER_CONTENT_TYPE = "Content-Type"
		const val HEADER_CONTENT_TYPE_VALUE = "application/json"
		const val HEADER_AUTHORIZATION_TYPE = "Bearer "
	}
}
```

```kotlin
class DefaultHeadersInterceptor() : Interceptor {
	override fun intercept(chain: Interceptor.Chain): Response {
		val originalRequest = chain.request()

		val modifiedRequest = originalRequest.newBuilder()
			.addHeader("CustomHeaderParam", "42")
			.build()
		return chain.proceed(modifiedRequest)
	}
}

class DefaultParamsInterceptor() : Interceptor {
	override fun intercept(chain: Interceptor.Chain): Response {
		val originalRequest = chain.request()

		val modifiedRequest = originalRequest.newBuilder()
			.url(originalRequest.url.newBuilder()
				.addQueryParameter("queryParam", "someValue")
				.build())
			.build()
		return chain.proceed(modifiedRequest)
	}
}
```

> Megj.: JWT autentikációnál, ha szintén Retrofittel akarjuk megoldani a JWT lekérést, refresh-t, stb, akkor azt egy külön Client-ben kell megoldanunk, amin nincs AuthInterceptor, Auth, stb.