---
layout: default
title: Overview
nav_order: 3
grand_parent: Architecture
parent: MircoService
---

---
# Resources
마이크로 서비스는 러닝커브가 상당한 것 같다. 너무 고민하지 말고 친절하게 알려주는 글로 시작하자.

* [실패하지 않는 MSA 전환, 두 가지만 기억하세요! 1편](https://www.lgcns.com/blog/cns-tech/36171/)
* ["우아한형제들" 회원시스템 이벤트기반 아키텍처 구축하기](https://techblog.woowahan.com/7835/)
* [LINE 광고 플랫폼의 MSA 환경에서 Zipkin을 활용해 로그 트레이싱하기](https://engineering.linecorp.com/ko/blog/line-ads-msa-opentracing-zipkin/)
* [Microservices With Spring Boot And Spring Cloud](https://github.com/piomin/course-spring-microservices)

---
# MSA 설계팁
* msa에서는 코어모듈을 이용한 모놀리식 아키텍쳐에 비해서 네트워크 트래픽에 영향을 많이 받아서 grpc같은 기술을 이용해야한다.
* BFF이용시에 앱의 메모리 사용량과 렌더링 로딩시간이 감소한다.

* 마이크로서비스 개발시의 최대 덕목은 "분리하기" (예 spring-boot-starter@bemily)
  + spring-boot-starter라는 프로젝트하나에 성격이 다른 부분들을 관리하였다.
    springboot관련 기본적인 종속성
    공통라이브러리
    설정정보

    나중에 필요에 의해서 이것들을 분리해서 관리하려고 하는 도중에 빈번하게 수정되어서 분리하는 작업이 지체되었다.
    만약에 처음부터 성격이 다른 부분들을 나눠서 관리하려고 했다면 어땠을까?

* 마이크로 서비스는 모듈별로 분리해서 빠른 변화에 용이하도록 시스템을 구축하는데, 최근에는 기준처럼 보여지기도 한다. 하지만, 단순히 분리하는데만 치중하면 겪지 않아도 될 문제에 봉착할 수도 있다. 형식상 분리하는데 그치는게 아니라 더 전문적인 능력이 필요한 거 같다. 인증이면, 인증에 고도화 되어서 전문적으로 처리할 수 있어야 할 것이고, 해당 영역에서 다른 서비스들이 해당 기능을 위임할 수 있을 정도로 높은 **응집성**을 가져야 한다.

* 아키텍쳐 설계시에 실제 비즈니스를 이해하지 않고 개발하는 경우가 있다. 최근 유행하고 있는 MSA 아키텍쳐인 경우는 특히 이런 경향이 있는데, 운용관점에서 서비스를 분리하기 때문에, 많은 서비스가 만들어지게 된다. 실제 비즈니스와 개발되는 서비스가 파편화되는 경우에는 구현되는 서비스가 일관성이 떨어지게 되는거 같다. 기준이 명확하지 않은 상태에서 개발되기 때문에, 운영되는 서비스들 또한, 작업자의 판단에 의해서 제각기 구현되게 된다. 보다 개선된 아키텍쳐 설계를 위해서는 위의 리소스들을 참조하자.


---
# API 설계 (Microservice 에서)
* API간에 통신시에 결정되야 하는 것들
  + Data format
  + Error patterns
* 그외에
  + Payload size
  + Latency
  + Scaliability
  + Load Balancing
  + Languages Interop (서로 다른 프로그래밍 언어간)
  + Auth, Monitoring, Logging


---
# 관련 강의
 * MSA관련 아래 강좌는 이미 구입했는데, 들어보자
   * https://www.udemy.com/course/microservices-with-spring-boot-and-spring-cloud

* https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%81%B4%EB%9D%BC%EC%9A%B0%EB%93%9C-%EB%A7%88%EC%9D%B4%ED%81%AC%EB%A1%9C%EC%84%9C%EB%B9%84%EC%8A%A4#curriculum
  + https://github.com/joneconsulting/msa_with_spring_cloud/tree/main/pdf
  + 마이크로서비스 관련해서 진행할 때 참고해보자

* https://www.baeldung.com/java-reactive-systems
  + 리액티브 하게 MSA를 구성하는 내용. 트랜잭션 관리에 대한 내용도 있음!!

---
# MonolithFirst

 * https://martinfowler.com/bliki/MonolithFirst.html
 * 결국 MSA로 시작하는 것은 실패의 지름길이라는 교훈?

### 모놀리스의 장단점
 * coremodule기반 모놀리스 아키텍쳐
   + 모듈을 기반으로 신속한 개발을 할 수 있고,
   + 하지만, 배포시에 불합리하고, 영향도가 커짐.

---
# SOA vs microservice
검색 키워드
