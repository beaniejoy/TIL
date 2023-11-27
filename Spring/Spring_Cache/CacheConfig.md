# Spring Boot Cache 관련 Config 내용 필기

```kotlin
// Redis
implementation("org.springframework.boot:spring-boot-starter-data-redis")
// Cache
implementation("org.springframework.boot:spring-boot-starter-cache")
```

## 기본 설정

```kotlin
@Bean
fun redisCacheManager(redisConnectionFactory: LettuceConnectionFactory): RedisCacheManager {
    val redisCacheConfiguration = RedisCacheConfiguration.defaultCacheConfig()
        .entryTtl(Duration.of(5L, ChronoUnit.MINUTES))
        .serializeValuesWith(
            RedisSerializationContext.SerializationPair
                .fromSerializer(
                    GenericJackson2JsonRedisSerializer(createRedisObjectMapper())
                )
        )
        .disableCachingNullValues() // Disable caching null values. (null value 대해서 caching X)

    return RedisCacheManager
        .builder(redisConnectionFactory)
        .cacheDefaults(redisCacheConfiguration)
        .build()
}
```

<br> 

## RedisSerializer

`GenericJackson2JsonRedisSerializer`을 통해서 json에 class type도 추가  

```kotlin
private fun createRedisObjectMapper(): ObjectMapper {
    val typeValidator = BasicPolymorphicTypeValidator.builder()
        .allowIfSubType(Any::class.java)
        .build()

    return jacksonObjectMapper().activateDefaultTyping(
        typeValidator,
        ObjectMapper.DefaultTyping.EVERYTHING,
        JsonTypeInfo.As.PROPERTY
    )
}
```
- `DefaultTyping.EVERYTHING`: allowIfSubType에 지정한 타입의 subType 모든 타입을 대상으로 하겠다.
- `JsonTypeInfo.As.PROPERTY`: json field에 `JsonTypeInfo.Id` 설정 내용대로 class 정보 입력
- `JsonTypeInfo.Id.CLASS`: `@class` field name으로 package 포함한 class 내용이 입력됨
  - 만약 대상이 되는 클래스 패키지 경로가 변경이되거나 클래스 이름이 변경되면 Deserialization에서 에러 발생할 수 있다.
- `JsonTypeInfo.Id.NAME`: `@type` field name으로 class 이름만 입력됨  
  (이부분은 어떻게 사용하는지 아직 잘 모르겠음)

<br>

## CacheErrorHandler

Spring cache의 `CacheErrorHandler` 기본설정은 `SimpleCacheErrorHandler`  
단순히 throw exception하는 handler다.  
하지만 이렇게 되면 redis cache상 문제되거나, serialization, deserialization 과정에서 예외 발생시 기존 비즈니스 로직에도 영향을 줄 수 있다.  
(사실 DB로 조회해도 무방한데)  
**이 때 따로 Handler를 설정할 수 있다.**

```kotlin
@Configuration
@EnableCaching
class CacheConfig : CachingConfigurer {
  //...

  override fun errorHandler(): CacheErrorHandler {
      return CustomCacheErrorHandler()
  }
}
```
`CacheErrorHandler`를 따로 구현해서 클래스를 bean으로 설정할 수 있고  
로깅만 할거면 `LoggingCacheErrorHandler`도 따로 있다.

- [Reference](https://hellokoding.com/spring-caching-custom-error-handler/)