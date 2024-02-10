---
layout: default
title: Properties
grand_parent: Springs
parent: Spring Boot
nav_order: 4
---

properties 적용 우선 순위
```
1. 유저 홈 디렉토리에 있는 spring-boot-dev-tools.properties
2. 테스트에 있는 @TestPropertySource
3. @SpringBootTest 애노테이션의 properties 애트리뷰트
4. 커맨드 라인 아규먼트
5. SPRING_APPLICATION_JSON 내부의 프로퍼티
6. ServletConfig 파라미터
7. ServletContext 파라미터
8. java:comp/env JNDI 애트리뷰트
9. System.getProperties() 자바 시스템 프로퍼티
10. OS 환경 변수
11. RandomValuePropertySource
12. JAR 밖에 있는 특정 프로파일용 application properties
13. JAR 안에 있는 특정 프로파일용 application properties
14. JAR 밖에 있는 application properties
15. JAR 안에 있는 application properties
16. @PropertySource
17. 기본 프로퍼티
```

### 속성별로 파일 분리하기
단순히 profile별로 분리하는 방법도 있지만, 속성별로 분리해서 관리할 수도 있다.

spring.profiles.include= 속성을 사용하면, 프로퍼티 파일을 원하는 만큼 분리하고 한 번에 사용할 수 있다.

숨기고 싶은 Security 관련 설정을 위해 application-security.properties 파일을 만들자. 

application.properties 안에 spring.profiles.include=security

---
# 테스트시의 properties 파일 사용법(@TestPropertySource)
/src/test/resources/ 경로에 application.properties 파일을 생성하면, 테스트에 알아서 인식된다.

테스트에 profile을 지정하는 경우는 여러가지 방법으로 테스트해보았는데, 확실히 정리되지 않았다.

application-test2.properties 이런식으로 테스트 클래스를 생성하는 방법이 괜찮은 거 같다.

```
@TestPropertySource(locations = "/application-data.properties")
@SpringBootTest
public class SpecialServiceTest {
  // ... 생략
}
```

---
# 테스트시의 properties 파일 사용법(@ActiveProfiles("test")) <= overriding 가능!
@ActiveProfiles를 이용하면 src/main/resources파일에서 properties 파일들을 사용하는 방식과 동일하게 이용이 가능하다!

```
/src/test/main/resources/application.properties
/src/test/main/resources/application-test.properties // <=이 설정이 application.properties파일을 overriding한다.

@SpringBootTest
@ActiveProfiles("test")
public class ProfilePropertySourceResolverIntegrationTest {

    @Autowired private PropertySourceResolver propertySourceResolver;

    @Test
    public void shouldProfiledProperty_overridePropertyValues() {
        String firstProperty = propertySourceResolver.getFirstProperty();
        String secondProperty = propertySourceResolver.getSecondProperty();

        assertEquals("profile", firstProperty);
        assertEquals("defaultSecond", secondProperty);
    }
}
```

---
# 테스트시의 properties 파일 사용법(@SpringBootTest(properties =... )) <= overriding 가능!

```
@SpringBootTest(properties = { "example.firstProperty=annotation" })
public class SpringBootPropertySourceResolverIntegrationTest {

    @Autowired private PropertySourceResolver propertySourceResolver;

    @Test
    public void shouldSpringBootTestAnnotation_overridePropertyValues() {
        String firstProperty = propertySourceResolver.getFirstProperty();
        String secondProperty = propertySourceResolver.getSecondProperty();

        Assert.assertEquals("annotation", firstProperty);
        Assert.assertEquals("defaultSecond", secondProperty);
    }
}
```


---
# 테스트시의 properties 파일 사용법(TestPropertySourceUtils)
ApplicationContextInitializer 를 오버라이드해서 initialize() 메소드에서 TestPropertySourceUtils 를 이용한다.
```
public class PropertyOverrideContextInitializer
  implements ApplicationContextInitializer<ConfigurableApplicationContext> {

    static final String PROPERTY_FIRST_VALUE = "contextClass";

    @Override
    public void initialize(ConfigurableApplicationContext configurableApplicationContext) {
        TestPropertySourceUtils.addInlinedPropertiesToEnvironment(
          configurableApplicationContext, "example.firstProperty=" + PROPERTY_FIRST_VALUE);

        TestPropertySourceUtils.addPropertiesFilesToEnvironment(
          configurableApplicationContext, "context-override-application.properties");
    }
}
```

이렇게 하면 src/test/resources/conteext-override-application.properties 의 아래 설정을 읽어들이게 된다.
```
example.secondProperty=contextFile
```

그리고 나서 위에서 설정한 내용을 이용해서 아래처럼 테스트코드를 작성할 수 있게 된다.
```
@SpringBootTest
@ContextConfiguration(
  initializers = PropertyOverrideContextInitializer.class,
  classes = Application.class)
public class ContextPropertySourceResolverIntegrationTest {

    @Autowired private PropertySourceResolver propertySourceResolver;

    @Test
    public void shouldContext_overridePropertyValues() {
        final String firstProperty = propertySourceResolver.getFirstProperty();
        final String secondProperty = propertySourceResolver.getSecondProperty();

        assertEquals(PropertyOverrideContextInitializer.PROPERTY_FIRST_VALUE, firstProperty);
        assertEquals("contextFile", secondProperty);
    }
}
```



---
# IDE 에 따라서 application.properties 파일이 중복될 때 발생하는 문제

1. src/test/resources/application-test.properties 파일을 만들고 여기에 test 관련 설정(src/test/resource/application.properties)을 넣어준다.
2. 테스트에 @ActiveProfiles("test")를 적용

이렇게 하면 application-test.properties 파일을 높은 우선순위로 인식한다.

만약 이 설정이 적용되지 않은다면 src/test 대신에 src/main 위치에 해당 파일을 만든다.

src/main/resources/application-test.properties

=> IDE에 따라서도 여러가지 신경써줘야 하는게 많은거 같다.

---
# IDE에서 application.properties 파일이 인식 안될 때의 Tip
https://www.inflearn.com/questions/812559/src-test-의-application-properties-문제

예상되는 원인은 vscode에서 자바 프로젝트를 테스트하기 위해 사용되는 "Test Runner for Java" 확장 프로그램입니다.

intelliJ에서는 내부적으로 테스트를 gradle에게 위임하여 테스트를 수행한다고 합니다.(default)

gradle은 테스트를 수행할 때, src/test/resources/application.properties를 사용하기 때문에, intelliJ에서는 아무런 문제가 없었던 것 같습니다.

vscode에도 "gradle for Java" 라는 확장 프로그램이 제공이 되서, 이 확장 프로그램을 이용하여 gradle에서 직접 test를 실행했더니, 강의 내용을 그대로 따라가도 문제가 발생하지 않았습니다.

이클립스에서도 동일한 문제가 발생하지만, Delegate to Gradle라는 feature 를 적용하면, 문제 없이 테스트가 수행된다고 하네요.






