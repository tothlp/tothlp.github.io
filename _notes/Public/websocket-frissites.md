---
title: Angular + Spring - Websocket használata frissítéshez
feed: show
date: 23-02-2024
---

Az interneten kismillió tutorial van a Websocket használatára, de mindegyik chatelésre, vagy todo listre van kihegyezve. Az alábbiakban egy olyan megoldás látható, ami használható akkor, ha pl. egy listát automatikusan akarunk  frissíteni egy Create operáció után.

## Backend

```kotlin
implementation("org.springframework.boot:spring-boot-starter-websocket")
```

```kotlin
data class WebsocketNotificationResponse(
    val fire: Boolean
)
```

```kotlin
import org.springframework.context.annotation.Configuration
import org.springframework.messaging.simp.config.MessageBrokerRegistry
import org.springframework.web.socket.config.annotation.EnableWebSocketMessageBroker
import org.springframework.web.socket.config.annotation.StompEndpointRegistry
import org.springframework.web.socket.config.annotation.WebSocketMessageBrokerConfigurer

@Configuration
@EnableWebSocketMessageBroker
class WebSocketConfig : WebSocketMessageBrokerConfigurer {
    override fun configureMessageBroker(registry: MessageBrokerRegistry) {

        // Set prefix for the endpoint that the client listens for our messages from
        registry.enableSimpleBroker("/topic")

        // Set prefix for endpoints the client will send messages to. Not used.
        registry.setApplicationDestinationPrefixes("/app")
    }

    override fun registerStompEndpoints(registry: StompEndpointRegistry) {

        // Registers the endpoint where the connection will take place
        registry.addEndpoint("/stomp") // Allow the origin http://localhost:63343 to send messages to us. (Base URL of the client)
            .setAllowedOriginPatterns("*")
            .withSockJS()
    }
}
```

```kotlin
@PostMapping("orders/create")
    fun createOrder(@RequestBody request: OrderCreateRequest) =
        orderService.createOrder(request).apply {
            simpTemplate.convertAndSend("/topic/check", WebsocketNotificationResponse(true))
        }
```

Itt igazából a simpTemplate.convertAndSend a lényeg, hogy melyik endpointon küldjön milyen adatot. Az, hogy a metódusod amúgy mit ad vissza, teljesen mindegy. Itt is igazából simán a REST API-ba lett “interceptolva”.

## Frontend

Deps:

```json
"sockjs-client": "^1.6.1",
"@types/sockjs-client": "^1.5.4"
```

index.html-ben a  global variable létrehozás miatt be kell szúrni az alábbi scriptet:

> Figyelem! Ha a spring szolgálja ki az `index.html`-t, akkor ott is át kell vezetni!
> 

```html
<script type="application/javascript">
    var global = window;
</script>
```

WebSocketService:

```tsx
import { Injectable } from '@angular/core';
import * as Stomp from '@stomp/stompjs';
import * as SockJS from 'sockjs-client';
 
@Injectable()
export class WebSocketService {
  connect() {
    let socket = new SockJS(`/ws`);
    let stompClient = Stomp.Stomp.over(socket);
    return stompClient;
  }
}
```

```tsx
let stompClient = this.webSocketService.connect();
    stompClient.connect({}, (_: any) => {
      stompClient.subscribe('/topic/check', (_: any) => {
        this.getCardBoardList();
      })
    });
```

Nyilván, ez egy nagyon leegyszerűsített szenárió, bármeddig lehet bonyolítani. Itt igazából annyi a lényeg, hogy ha lefut a create operáció, akkor websocket-en csak egy jelzést küldünk (mint egy push noti), bármi adat nélkül. A lista erre van feliratkozva, és tudja, hogy frissülnie kell. Simán lehet, hogy a szűrőfeltételek miatt nem fog a listában  látszódni az új elem, de legalább fixen tudjuk, mikor kell frissíteni.