# SpringMockK

[![CircleCI](https://circleci.com/gh/Ninja-Squad/springmockk.svg?style=svg)](https://circleci.com/gh/Ninja-Squad/springmockk)

Support for Spring Boot integration tests written in Kotlin using [MockK](https://mockk.io/) instead of Mockito.
 
Spring Boot provides `@MockBean` and `@SpyBean` annotations for integration tests, which create mock/spy beans using Mockito.

This project provides equivalent annotations `MockkBean` and `SpykBean` to do the exact same thing with MockK.

## Principle

All the Mockito-specific classes of the spring-boot-test library, including the automated tests, have been cloned, translated to Kotlin, and adapted to MockK.

This library thus provides the same functionality as the standard Mockito-based Spring Boot mock beans.

For example (using JUnit 5, but you can of course also use JUnit 4):

```kotlin
@ExtendWith(SpringExtension::class)
@WebMvcTest
class GreetingControllerTest {
    @MockkBean
    private lateinit var greetingService: GreetingService
    
    @Autowired
    private lateinit var controller: GreetingController
    
    @Test
    fun `should greet by delegating to the greeting service`() {
        every { greetingService.greet("John") } returns "Hi John"
        
        assertThat(controller.greet("John")).isEqualTo("Hi John")
        verify { greetingService.greet("John") }
    }
}
```

## Usage

### Gradle (Kotlin DSL)

Add this to your dependencies:
```kotlin
testImplementation("com.ninja-squad:springmockk:3.1.1")
```

If you want to make sure Mockito (and the standard `MockBean` and `SpyBean` annotations) is not used, you can also exclude the mockito dependency:
```kotlin
testImplementation("org.springframework.boot:spring-boot-starter-test") {
    exclude(module = "mockito-core")
}
```

### Maven

Add this to your dependencies:
```xml
<dependency>
  <groupId>com.ninja-squad</groupId>
  <artifactId>springmockk</artifactId>
  <version>3.1.1</version>
  <scope>test</scope>
</dependency>
```

## Differences with Mockito

 - the MockK defaults are used, which means that mocks created by the annotations are strict (i.e. not relaxed) by default. But [you can configure MockK](https://mockk.io/#settings-file) to use different defaults globally, or you can use `@MockkBean(relaxed = true)` or `@MockkBean(relaxUnitFun = true)`. 
 - the created mocks can't be serializable as they can be with Mockito (AFAIK, MockK doesn't support that feature)


## Limitations
 - the [issue 5837](https://github.com/spring-projects/spring-boot/issues/5837), which has been fixed for Mockito spies using Mockito-specific features, also exists with MockK, and hasn't been fixed yet. 
   If you have a good idea, please tell!
 - [this is not an official Spring Boot project](https://github.com/spring-projects/spring-boot/issues/15749), so it might not work out of the box for newest versions if backwards incompatible changes are introduced in Spring Boot. 
 Please file issues if you find problems.
 - annotations are looked up on fields, and not on properties (for now). 
   This doesn't matter much until you use a custom qualifier annotation.
   In that case, make sure that it targets fields and not properties, or use `@field:YourQualifier` to apply it on your beans.

## Versions compatibility

 - Version 3.x of SpringMockK: compatible with Spring Boot 2.4.x, 2.5.x and 2.6.x
 - Version 2.x of SpringMockK: compatible with Spring Boot 2.2.x and 2.3.x
 - Version 1.x of SpringMockK: compatible with Spring Boot 2.1.x 
 
## How to build

```
  ./gradlew build
```
