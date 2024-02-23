---
layout: default
title: Reactive Reactor
description: Provides an overview of Project Reactor, an implementation of the Reactive Stream Specification.
parent: Java
nav_order: 10
---

Reactor has supported the Reactive Streams standard from version 2.5 milestone (February 2016). Release was marked as version 3.0 in August 2016.

Around the same time, RxJava 2.0, supporting the Reactive Streams standard, was also released. The significant difference lies in the fact that RxJava supports JDK 6 onwards, including Android, while Reactor requires JDK 8 or later.

# Resources
* [Build Reactive MicroServices using Spring WebFlux/SpringBoot
](https://github.com/dilipsundarraj1/reactive-spring-webflux/tree/final)

* [Reactor 3 Reference Guide](https://projectreactor.io/docs/core/release/reference)




# 튜토리얼
[Reactor 의 탄생, 생성과 구독 (역사, Publisher 구현체 Flux와 Mono, defer, Subscriber 직접구현)](https://sjh836.tistory.com/185)

https://www.baeldung.com/reactor-core

https://www.baeldung.com/java-reactor-flux-vs-mono


 * https://github.com/eugenp/tutorials/tree/master/reactor-core
   + => 코드에서 다양한 리액터 관련한 글을 볼 수 있다.

 * https://www.baeldung.com/java-reactive-systems
   + => 리액티브 하게 MSA를 구성하는 내용. 트랜잭션 관리에 대한 내용도 있음!!
   + => 리액티브 시스템을 구현하는 것에 대해서 고민해야 되는 것들(디스커버리, 오토스케일링 등)에 대해서도 언급함...



# Publisher interface 의 구현체
[Flux](https://projectreactor.io/docs/core/release/reference/#flux) and [Mono](https://projectreactor.io/docs/core/release/reference/#mono) implemented Publisher interface. 이 둘은 대략적인 카디널리티 정보를 담는 식으로 타입을 구분한다.
If 아이템이 0개 아니면 1개라면 Mono can be used.
Mono의 연산자들은 버퍼 중복, 값비싼 동기화 작업 등이 생략해서 성능적인 이점이 있다.
But, Flux와 Mono는 서로 쉽게 변환할 수 있다, also.

```
Flux<T>.collectList() = Mono<List<T>>
Mono<T>.flux() = Flux<T>
```

publisher 는 구독자가 없으면 가만히 있기 때문에 메모리 부족을 야기하지 않고도 무한대의 리액티브 스트림을 만들 수 있습니다. subscribe를 통해 구독자를 추가하면 데이터가 전달됩니다.

### just VS fromSupplier VS defer
Flux와 Mono는 팩토리 메소드를 제공하기 때문에 햇갈려서 정리한다.

* just는 즉시 시퀀스를 만든다.
* fromSupplier과 defer는 구독시점에 시퀀스를 생성한다. (lazy 처리)
* fromSupplier 는 Supplier<? extends T> supplier 를 인자로 받는다. Mono가 아닌 값에 사용하면 된다.
* defer 는 Supplier<? extends Mono<? extends T>> supplier 를 인자로 받는다. 이미 Mono로 반환되는 메소드에 사용하는 게 좋다.

즉, 아래 경우에는 fromSupplier 가 더 적절하고 깔끔해보이는 상황이다.

```java
@Test
public void defer_테스트() throws Exception {
    long start = System.currentTimeMillis();

    // just
    Mono<Long> clock1 = Mono.just(System.currentTimeMillis());

    Thread.sleep(5000);
    long result1 = clock1.block() - start;
    log.info("흐른 시간 = {}", result1); // 5초가 지났으나, 위에서 즉발실행되어서 0 출력


    // fromSupplier
    Mono<Long> clock2 = Mono.fromSupplier(System::currentTimeMillis);

    Thread.sleep(5000);
    long result2 = clock2.block() - start;
    log.info("흐른 시간 = {}", result2); // 10초 지남


    // defer
    Mono<Long> clock3 = Mono.defer(() -> Mono.just(System.currentTimeMillis()));

    Thread.sleep(5000);
    long result3 = clock3.block() - start;
    log.info("흐른 시간 = {}", result3); // 15초 지남
}
```

```
// 실행결과
23:41:03.091 [main] INFO com.devljh.reactive.practice.ReactorTest - 흐른 시간 = 0
23:41:08.106 [main] INFO com.devljh.reactive.practice.ReactorTest - 흐른 시간 = 10108
23:41:13.128 [main] INFO com.devljh.reactive.practice.ReactorTest - 흐른 시간 = 15123
```

# Subscriber interface의 구현체

Subscriber가 구독을 신청하게 되면 Publisher로부터 이벤트를 수신받을 수 있습니다.

이 이벤트들은 Subscriber 인터페이스의 메서드를 통해 전송됩니다.

* onSubscribe, 구독시 최초에 한번만 호출
* onNext, 구독자가 요구하는 데이터의 수 만큼 호출 (최대 java.lang.Long.MAX_VALUE)
* onError, 에러 발생 또는 더이상 처리할 수 없는 경우
* onComplete, Publisher가 데이터 통지를 완료했음을 알릴 때

Subscriber가 수신할 첫 번째 이벤트는 onSubscribe 메서드의 호출을 통해 이루어집니다.
Publisher가 onSubscribe 메서드를 호출할 때 이 메서드의 인자로 Subscription 객체를 Subscriber에 전달합니다.

Subscriber의 구현

```java
public class MyCustomSubscriberSafety extends BaseSubscriber<String>
{
    @Override
    public void hookOnSubscribe(Subscription subscription) {
        System.out.println("onSubscribe, subscription " + subscription.hashCode());
        request(1); // 구독 후, 최초 요청
    }

    @Override
    public void hookOnNext(String s) {
        System.out.println("onNext = {}" + s);
        request(1); // 데이터 수신 후, 추가 요청
    }

    @Override
    public void hookOnComplete() {
        System.out.println("onComplete");
    }
}
```

리액터 프레임워크에서 미리 만들어둔, BaseSubscriber 추상클래스를 상속해서 구현합니다.

이는 Subscriber의 onSubscribe, onNext, onError, onComplete 는 모두 직접 재정의할 수 없게 final 로 선언되어 있습니다.

대신 우리는 제공하는 hook 을 통해서 상황에 따라 조정할 수 있도록 해 놓았다.

일반적인 Reactive Streams 에서의 Publisher 는 subscribe 를 실행할 때 subscriber 를 등록해줍니다.

```java
    @Test
    void subscriber()
    {
        Flux<String> flux = Flux.just("A", "B");

        flux.subscribe(new MyCustomSubscriber()); // 커스텀으로 구현한 subscriber 사용
    }
```
하지만 Flux 에서 제공하는 팩토리 메서드는 위와 같이 subscriber 를 등록해주는 메서드도 있는 반면에 subscriber 를 매개변수로 등록하지 않는 함수도 존재합니다.

subscriber 가 필요없어서가 아니라, 내부 로직에서 자동으로 subscriber 를 만들어 주기 때문입니다..(new LambdaSubscriber<>(...) 를 통해 Subscriber를 생성)

이때 publisher에서 전달받을 개수를 지정하는 s.request는 디폴트로 Long.MAX_VALUE가 세팅된다.

```java
    @Test
    void nonSubscriber()
    {
        List<String> list = new ArrayList<>();

        Flux<String> flux = Flux.just("A", "B");
        flux.subscribe(list::add); // 내부적으로 subscriber를 구현해줌

        Assertions.assertEquals(2, list.size());
    }
```


# Subscription interface의 구현체
Subscriber는 Subscription 객체를 통해서 구독을 관리할 수 있다.
Subscription 인터페이스는 Subscriber가 구독한 데이터의 개수를 요청하거나 데이터 요청의 취소, 즉 구독을 해지하는 역할을 한다.

Subscription 인터페이스는 request 메서드와 cancel 메서드를 가지고 있다.

Publisher에서 Subscriber의 onSubscribe 메서드를 호출하며 매개변수로 subscription을 전달한다.

Subscriber는 Subscription의 request()를 호출하여 데이터를 요청하거나, cancel()을 호출하여 데이터를 더 이상 수신하지 않거나 구독을 취소할 수 있다.

request()를 호출할 때 Subscriber는 받고자 하는 데이터 항목 수를 나타내는 long 타입 값을 인자로 전달하는데 이것이 백프레셔이며, Subscriber가 처리할 수 있는 것보다 더 많은 데이터를 전송하는 것을 막아준다.

Subscriber의 데이터 요청이 완료되면 데이터가 스트림을 통해 전달되기 시작합니다. 이 때 onNext() 메서드가 호출되어 Publisher가 전송하는 데이터가 Subscriber에게 전달되며, 에러 발생시 onError()가 호출된다.

그리고 Publisher에서 전송할 데이터가 없고 더 이상 데이터를 생성하지 않는다면 Publisher가 onComplete()를 호출하여 작업이 끝났다고 Subscriber에게 알려준다.

전반적인 코드는 위의 MyCustomSubscriber 클래스를 확인한다.


# Processor interface의 구현체


# 전체적은 흐름
![](../images/java/all-reactive-streams-components.png)

1. Subscriber 가 subscribe 메소드를 통해 Publisher 에게 구독을 요청
2. Publisher 는 onSubscribe 메소드로 Subscriber 에게 Subscription 를 전달
3. Subscriber 는 Subscription.request 을 통해, 자신에게 데이터를 흘려줄 것을 요구
4. Publisher 는 Subscription 를 통해 Subscriber.onNext로 데이터를 전달한다.
   1. Subscriber 는 내부에 Subscription를 set하였기 때문 (2번)
5. 전달이 잘 끝났으면, onComplete, 오류났다면 onError 로 끝낸다.


# Processor의 흐름(Publisher와 Subscriber를 혼합)
![](../images/java/reactive-sptream-components-processor.png)

Publisher 와 Subscriber 를 혼합한 Processor 라는 것도 있다.
```java
  public interface Processor<T, R> extends Subscriber<T>, Publisher<R> {
  }
```

이 둘 사이에서 몇 가지 처리 단계를 유연하게 추가할 수 있다.

하나의 subscriber 의 결과물을 다른 subscriber 에 그대로 전달하거나, 변형할 때도 사용할 수있다. 마치 새로운 Publisher 처럼 행동하는 것이다.

* ex1) 구독자 중 기준에 일치하는 구독자들에게만 전송한다던지..
* ex2) 멀티캐스팅


# jdk9 Flow
리액티브 스트림은 jdk9 에서도 Flow API 로 반영이 되었다.

리액티브 스트림 타입들을 Flow 로도 쉽게 변환이 가능하다. (ex. FlowAdapters)

