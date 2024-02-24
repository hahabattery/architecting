---
layout: default
title: Reactor Operator
description: 
grand_parent: Java
parent: Reactive Streams
nav_order: 3
---

# Reactor Operator
{: .no_toc}

# Table of contents
{: .no_toc .text-delta }

1. TOC 
{:toc}

# Flux


### flatMap()
* https://projectreactor.io/docs/core/release/api/reactor/core/publisher/Flux.html#flatMap-java.util.function.Function-
### concatMap()  
* https://projectreactor.io/docs/core/release/api/reactor/core/publisher/Flux.html#concatMap-java.util.function.Function-

### flatMapSequential
* https://projectreactor.io/docs/core/release/api/reactor/core/publisher/Flux.html#flatMapSequential-java.util.function.Function-

### Tips for flatMap, concatMap, flatMapSequential

use flatMap for fast parallel calls (max 256 limit — default concurrency), when ordering of elements is not required.

use concatMap for sequential calls when strict ordering of elements is required.

use flatMapSequential for parallel calls with strict ordering of elements.

### share()
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



# Mono

### cache()

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
