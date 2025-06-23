---
title: Spring WebFlux - APIs Reativas para Alta Performance
date: 2017-09-15 00:00:00 +0000
categories: [Java, Spring, Web Development]
tags: [java, spring, webflux, reactive, api, performance, microservices]
---

## Introdução ao Spring WebFlux

Com o lançamento do Spring 5, temos uma nova forma de construir APIs web: o **Spring WebFlux**. Este framework reativo promete revolucionar como criamos aplicações web em Java, oferecendo uma alternativa não-bloqueante ao tradicional Spring MVC.

### O que é Programação Reativa?

A programação reativa é um paradigma que foca em streams de dados assíncronos e na propagação de mudanças. Em vez de bloquear threads aguardando respostas, o código reativo processa eventos conforme eles chegam.

### Principais Vantagens do WebFlux

#### 1. **Alto Throughput com Menos Recursos**

```java
@RestController
public class UserController {
    
    @Autowired
    private UserService userService;
    
    @GetMapping("/users")
    public Flux<User> getAllUsers() {
        return userService.findAll()
            .delayElements(Duration.ofMillis(100))
            .doOnNext(user -> log.info("Processing user: {}", user.getName()));
    }
    
    @GetMapping("/users/{id}")
    public Mono<User> getUser(@PathVariable String id) {
        return userService.findById(id)
            .switchIfEmpty(Mono.error(new UserNotFoundException(id)));
    }
}
```

#### 2. **Melhor Utilização de Threads**

O WebFlux utiliza o **Event Loop**, similar ao Node.js, permitindo que uma única thread processe milhares de requisições concorrentes sem bloqueio.

#### 3. **Composabilidade e Operadores Funcionais**

```java
@Service
public class OrderService {
    
    public Mono<OrderResponse> processOrder(OrderRequest request) {
        return validateOrder(request)
            .flatMap(this::checkInventory)
            .flatMap(this::calculatePrice)
            .flatMap(this::processPayment)
            .map(this::createResponse)
            .onErrorMap(PaymentException.class, 
                ex -> new OrderProcessingException("Payment failed", ex));
    }
    
    private Mono<Order> validateOrder(OrderRequest request) {
        return Mono.fromCallable(() -> {
            // Validação assíncrona
            return new Order(request);
        }).subscribeOn(Schedulers.boundedElastic());
    }
}
```

#### 4. **Integração com Bancos de Dados Reativos**

```java
@Repository
public class ReactiveUserRepository {
    
    private final R2dbcEntityTemplate template;
    
    public Flux<User> findByAge(int minAge) {
        return template.select(User.class)
            .matching(Query.query(where("age").greaterThanOrEquals(minAge)))
            .all();
    }
    
    public Mono<User> save(User user) {
        return template.insert(User.class)
            .using(user);
    }
}
```

### Quando Usar WebFlux vs Spring MVC

#### Use WebFlux quando:
- **Alta concorrência**: Muitas requisições simultâneas
- **I/O intensivo**: Chamadas para APIs externas, banco de dados
- **Streaming**: Dados em tempo real
- **Microserviços**: Comunicação não-bloqueante entre serviços

#### Use Spring MVC quando:
- **Aplicações CRUD simples**: Operações síncronas básicas
- **Equipe experiente**: Time já familiarizado com o modelo tradicional
- **Bibliotecas bloqueantes**: Dependências que não suportam reatividade

### Exemplo: API de Streaming de Dados

```java
@RestController
public class StreamingController {
    
    @GetMapping(value = "/events", produces = MediaType.TEXT_EVENT_STREAM_VALUE)
    public Flux<ServerSentEvent<String>> streamEvents() {
        return Flux.interval(Duration.ofSeconds(1))
            .map(sequence -> ServerSentEvent.<String>builder()
                .id(String.valueOf(sequence))
                .event("periodic-event")
                .data("Event " + sequence + " at " + LocalTime.now())
                .build());
    }
    
    @GetMapping("/news")
    public Flux<News> getLatestNews() {
        return newsService.getStreamingNews()
            .take(Duration.ofMinutes(5))
            .filter(news -> news.getCategory().equals("TECH"));
    }
}
```

### Testando APIs Reativas

```java
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
class UserControllerTest {
    
    @Autowired
    private WebTestClient webTestClient;
    
    @Test
    void shouldReturnAllUsers() {
        webTestClient.get()
            .uri("/users")
            .exchange()
            .expectStatus().isOk()
            .expectBodyList(User.class)
            .hasSize(3)
            .consumeWith(response -> {
                List<User> users = response.getResponseBody();
                assertThat(users).extracting(User::getName)
                    .containsExactly("Alice", "Bob", "Charlie");
            });
    }
}
```

### Desafios e Considerações

1. **Curva de Aprendizado**: Paradigma diferente do desenvolvimento tradicional
2. **Debugging**: Stack traces podem ser mais complexos
3. **Ecossistema**: Nem todas as bibliotecas suportam reatividade
4. **Context Switching**: Cuidado com blocking calls em código reativo

### Conclusão

O Spring WebFlux representa uma evolução natural para aplicações que precisam de alta performance e escalabilidade. Embora tenha uma curva de aprendizado, os benefícios em termos de throughput e utilização de recursos fazem dele uma excelente escolha para APIs modernas.

Para sistemas que lidam com alta concorrência, streaming de dados ou integração com múltiplos serviços, o WebFlux oferece uma solução elegante e performática que pode transformar significativamente a arquitetura de suas aplicações.