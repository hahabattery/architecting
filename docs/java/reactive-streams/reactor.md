---
layout: default
title: Reactive Reactor Overview
description: Provides an overview of Project Reactor, an implementation of the Reactive Stream Specification.
grand_parent: Java
parent: Reactive Streams
nav_order: 10
---

Reactor has supported the Reactive Streams standard from version 2.5 milestone (February 2016). Release was marked as version 3.0 in August 2016.

Around the same time, RxJava 2.0, supporting the Reactive Streams standard, was also released. The significant difference lies in the fact that RxJava supports JDK 6 onwards, including Android, while Reactor requires JDK 8 or later.

# Resources
* [Build Reactive MicroServices using Spring WebFlux/SpringBoot
](https://github.com/dilipsundarraj1/reactive-spring-webflux/tree/final)
* [Reactor 3 Reference Guide](https://projectreactor.io/docs/core/release/reference)
* [Hot Versus Cold](https://projectreactor.io/docs/core/snapshot/reference/#reactor.hotCold)
* [Threading and Schedulers](https://projectreactor.io/docs/core/release/reference/#schedulers)
* [Activating Debug Mode - aka tracebacks](https://projectreactor.io/docs/core/snapshot/reference/#debug-activate)



# 튜토리얼
[Reactor 의 탄생, 생성과 구독 (역사, Publisher 구현체 Flux와 Mono, defer, Subscriber 직접구현)](https://sjh836.tistory.com/185)

https://www.baeldung.com/reactor-core

https://www.baeldung.com/java-reactor-flux-vs-mono


 * https://github.com/eugenp/tutorials/tree/master/reactor-core
   + => 코드에서 다양한 리액터 관련한 글을 볼 수 있다.

 * https://www.baeldung.com/java-reactive-systems
   + => 리액티브 하게 MSA를 구성하는 내용. 트랜잭션 관리에 대한 내용도 있음!!
   + => 리액티브 시스템을 구현하는 것에 대해서 고민해야 되는 것들(디스커버리, 오토스케일링 등)에 대해서도 언급함...

 * https://tech.kakao.com/2018/05/29/reactor-programming/
   * 간단한 예제로 flux병령처리 내용도 포함하고 있다.

 * https://velog.io/@suhongkim98/%EB%A6%AC%EC%95%A1%ED%8B%B0%EB%B8%8C-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D-%EC%8B%9C%EC%9E%91%ED%95%98%EA%B8%B0-Reactor%ED%8E%B8-2
   * 내용이 상당히 많다. 추가 정리 필요.
  
 * 

# Publisher interface 의 구현체
[Flux](https://projectreactor.io/docs/core/release/reference/#flux) and [Mono](https://projectreactor.io/docs/core/release/reference/#mono) implemented Publisher interface. 이 둘은 대략적인 카디널리티 정보를 담는 식으로 타입을 구분한다.
If 아이템이 0개 아니면 1개라면 Mono can be used.
Mono의 연산자들은 버퍼 중복, 값비싼 동기화 작업 등이 생략해서 성능적인 이점이 있다.
But, Flux와 Mono는 서로 쉽게 변환할 수 있다, also.

```
Flux<T>.collectList() = Mono<List<T>>
Mono<T>.flux() = Flux<T>
```

publisher 는 구독자가 없으면 가만히 있기 때문에 메모리 부족을 야기하지 않고도 무한대의 리액티브 스트림을 만들 수 있습니다. publisher 인터페이스는 subscribe 라는 추상 메서드를 가집니다. Publisher.subscribe(Subscriber)의 형식으로 data 제공자와 구독자가 연결된다.



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

Subscriber가 구독을 신청하게 되면 Publisher로부터 이벤트를 수신받는다.

이 이벤트들은 Subscriber 인터페이스의 메서드를 통해 전송된다.

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

리액터 프레임워크에서 미리 만들어둔, BaseSubscriber 추상클래스를 상속해서 구현한다.

이는 Subscriber의 onSubscribe, onNext, onError, onComplete 는 모두 직접 재정의할 수 없게 final 로 선언되어 있다.

대신 우리는 제공하는 hook 을 통해서 상황에 따라 조정할 수 있도록 해 놓았다.

일반적인 Reactive Streams 에서의 Publisher 는 subscribe 를 실행할 때 subscriber 를 등록해준다.

```java
    @Test
    void subscriber()
    {
        Flux<String> flux = Flux.just("A", "B");

        flux.subscribe(new MyCustomSubscriber()); // 커스텀으로 구현한 subscriber 사용
    }
```
하지만 Flux 에서 제공하는 팩토리 메서드는 위와 같이 subscriber 를 등록해주는 메서드도 있는 반면에 subscriber 를 매개변수로 등록하지 않는 함수도 존재한다.

subscriber 가 필요없어서가 아니라, 내부 로직에서 자동으로 subscriber 를 만들어 주기 때문이다.(new LambdaSubscriber<>(...) 를 통해 Subscriber를 생성)

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
Subscriber는 Subscription 객체를 통해서 구독을 관리한다.
Subscription 인터페이스는 Subscriber가 구독한 데이터의 개수를 요청하거나 데이터 요청의 취소, 즉 구독을 해지하는 역할을 한다.

Subscription 인터페이스는 request 메서드와 cancel 메서드를 가지고 있다.

Publisher에서 Subscriber의 onSubscribe 메서드를 호출하며 매개변수로 subscription을 전달한다.

Subscriber는 Subscription의 request()를 호출하여 데이터를 요청하거나, cancel()을 호출하여 데이터를 더 이상 수신하지 않거나 구독을 취소할 수 있다.

request()를 호출할 때 Subscriber는 받고자 하는 데이터 항목 수를 나타내는 long 타입 값을 인자로 전달하는데 이것이 백프레셔이며, Subscriber가 처리할 수 있는 것보다 더 많은 데이터를 전송하는 것을 막아준다.

Subscriber의 데이터 요청이 완료되면 데이터가 스트림을 통해 전달되기 시작합니다. 이 때 onNext() 메서드가 호출되어 Publisher가 전송하는 데이터가 Subscriber에게 전달되며, 에러 발생시 onError()가 호출된다.

그리고 Publisher에서 전송할 데이터가 없고 더 이상 데이터를 생성하지 않는다면 Publisher가 onComplete()를 호출하여 작업이 끝났다고 Subscriber에게 알려준다.

전반적인 코드는 위의 MyCustomSubscriber 클래스를 확인한다.


# Cold Sequence / Hot Sequence
Cold와 Hot의 차이는 간단하게 Cold는 새로 시작한다, Hot은 새로 시작하지 않는다로 이해할 수 있다.

우리가 월간 잡지를 보내주는 서비스를 5월에 구독한다고 했을 때, 1월부터 5월까지 모든 잡지를 배달받고 이후로도 월마다 잡지를 배달받는 구조를 Cold, 5월 이후의 잡지만 배달받는 형태를 Hot이라고 보면 된다.

Cold Sequnece 흐름으로 동작하는 Publisher를 Cold Publisher라고 하고,
Publisher가 데이터를 emit하는 과정이 한 번만 일어나도 Subscriber가 각각의 구독시점 이후에 emit된 데이터만 전달받는 데이터의 흐름을 Hot Sequence라고 한다.

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


# share()
share() 오퍼레이터는 원본 Flux를 멀티캐스트하는 새로운 Flux를 리턴하는 오퍼레이터이다.

즉 여러 Subscriber가 하나의 Flux를 공유하도록 합니다. 하나의 원본 Flux를 공유하여 다 같이 사용하기 때문에 어떤 Subscriber가 이 원본 Flux를 먼저 구독해버리면 데이터 emit을 시작하게 되고, 이후에 다른 Subscriber가 구독하는 시점에는 이미 emit된 데이터는 전달받을 수 없게된다.

우리는 share() 오퍼레이터를 통해 Cold Sequence를 Hot Sequence로 동작하게 할 수 있다.

```java
    @Test
    @DisplayName("share() Operator를 이용해 Cold Sequence를 Hot Sequence로 동작하게 할 수 있다")
    void testHotSequence() throws InterruptedException {
        String[] singers = {"로이킴", "뉴진스", "트와이스", "테일러 스위프트"};

        // 4명의 가수는 2초 간격으로 무대에 나와 노래를 부를 예정입니다.
        Flux<String> concertFlux = Flux.fromArray(singers)
                .delayElements(Duration.ofSeconds(2))
                .share();

		// 첫 관객 관람(구독)을 하여 공연이 시작됐습니다.
        concertFlux.subscribe(singer -> System.out.printf("[%s] 관객 A가 %s의 무대를 보았습니다.%n", Thread.currentThread(), singer));

        Thread.sleep(2500); //공연 시작 2.5초 뒤.. 관객 B가 관람(구독) 시작
        concertFlux.subscribe(singer -> System.out.printf("[%s] 관객 B가 %s의 무대를 보았습니다.%n", Thread.currentThread(), singer));

        Thread.sleep(6000);
    }
```

```
[Thread[parallel-1,5,main]] 관객 A가 로이킴의 무대를 보았습니다.
[Thread[parallel-2,5,main]] 관객 A가 뉴진스의 무대를 보았습니다.
[Thread[parallel-2,5,main]] 관객 B가 뉴진스의 무대를 보았습니다.
[Thread[parallel-3,5,main]] 관객 A가 트와이스의 무대를 보았습니다.
[Thread[parallel-3,5,main]] 관객 B가 트와이스의 무대를 보았습니다.
[Thread[parallel-4,5,main]] 관객 A가 테일러 스위프트의 무대를 보았습니다.
[Thread[parallel-4,5,main]] 관객 B가 테일러 스위프트의 무대를 보았습니다.
```
다음 테스트코드를 통해 관객 B는 뉴진스의 공연부터 보는걸 볼 수 있습니다.

# cache()
cache() 오퍼레이터는 Cold Sequence로 동작하는 Mono를 Hot Sequence로 변경해주고, emit된 데이터를 캐시한 뒤 구독이 발생할 때마다 캐시된 데이터를 전달해준다.

```java
@Test
    void testCacheHotSequence() throws InterruptedException {
        var mono = Mono.fromCallable(() -> {
                    System.out.println("Go!");
                    return 5;
                })
                .map(i -> {
                    System.out.println("Double!");
                    return i * 2;
                });

        var cached = mono.cache();

        System.out.println("Using cached"); // Hot Sequence로 동작한다.
        System.out.println("1. " + cached.block()); // 최초 한 번 연산
        System.out.println("2. " + cached.block()); // 캐싱 반환
        System.out.println("3. " + cached.block()); // 캐싱 반환

        System.out.println("Using NOT cached"); // Cold Sequence로 동작한다.
        System.out.println("1. " + mono.block()); // 처음부터 다시 동작
        System.out.println("2. " + mono.block()); // 처음부터 다시 동작
        System.out.println("3. " + mono.block()); // 처음부터 다시 동작
    }
```

```
Using cached
Go!
Double!
1. 10
2. 10
3. 10
Using NOT cached
Go!
Double!
1. 10
Go!
Double!
2. 10
Go!
Double!
3. 10
```

테스트 결과를 보면 cache()사용 시 Hot Sequence로 동작하여 호출마다 처음부터 돌지 않고, 2번째 호출부터는 캐시된 값을 반환한다.