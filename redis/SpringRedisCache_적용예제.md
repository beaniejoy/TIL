# Spring Boot에 Redis Cache 적용 예제

```gradle
// Redis
implementation 'org.springframework.boot:spring-boot-starter-data-redis'
```

## Redis Cache Config file

```java
// RedisCacheConfig.java
@Value("${spring.redis.cache.host}")
private String cacheHost;

@Value("${spring.redis.cache.port}")
private int cachePort;

@Bean
public RedisConnectionFactory redisConnectionFactory() {
    RedisStandaloneConfiguration redisStandaloneConfiguration = new RedisStandaloneConfiguration();
    redisStandaloneConfiguration.setHostName(cacheHost);
    redisStandaloneConfiguration.setPort(cachePort);
    return new LettuceConnectionFactory(redisStandaloneConfiguration);
}

@Bean(name = "cacheManager")
public RedisCacheManager cacheManager(RedisConnectionFactory connectionFactory) {
    // redis cache의 default로 설정된 내용
    RedisCacheConfiguration defaultConfiguration = RedisCacheConfiguration.defaultCacheConfig()
            .disableCachingNullValues()
            .entryTtl(Duration.ofMinutes(CacheKey.DEFAULT_EXPIRE_MIN))
            .computePrefixWith(CacheKeyPrefix.simple())
            .serializeKeysWith(RedisSerializationContext.SerializationPair.fromSerializer(new StringRedisSerializer()))
            .serializeValuesWith(RedisSerializationContext.SerializationPair.fromSerializer(new GenericJackson2JsonRedisSerializer()));

    Map<String, RedisCacheConfiguration> cacheConfigurations = new HashMap<>();
    // POINT를 위한 cache 임의 설정
    // default 설정에 overriding하는 방식으로 설정
    cacheConfigurations.put(CacheKey.POINT, defaultConfiguration
            .entryTtl(Duration.ofMinutes(CacheKey.POINT_EXPIRE_MIN)));

    return RedisCacheManager.builder(connectionFactory).cacheDefaults(defaultConfiguration)
            .withInitialCacheConfigurations(cacheConfigurations).build();
}
```
- `LettuceConnectionFactory`로 redis connection 관리
- `RedisCacheManager`를 통해 cache를 위한 manager 객체 생성

```java
public class CacheKey {
    public static final String POINT = "point";
    public static final long DEFAULT_EXPIRE_MIN = 30L;
    public static final long POINT_EXPIRE_MIN = 10L;
}
```
- cache 관련 key를 관리하는 클래스

## cache 적용

### @Cacheable
```java
@Cacheable(cacheNames = CacheKey.POINT, key = "#userId")
@Transactional(readOnly = true)
public PointTotalResponseDto findTotalPoint(UUID userId) {

    PointTotal pointTotal = pointTotalRepository.findByUserId(userId)
            .orElseThrow(() -> new PointTotalNotFoundException(userId));

    return pointTotal.toResponse();
}
```
- `@Cacheable`: [cacheNames]:[#userId] 해당 key값에 데이터가 없는 경우 DB조회해서 메모리 캐시에 저장
- 기존에 데이터가 있는 경우 캐시화된 데이터를 조회(DB 조회 X)

### @CacheEvict
```java
@CacheEvict(cacheNames = CacheKey.POINT, key = "#userId")
@Transactional
public void updateActivePoints(UUID userId) {
    Integer currentUserPoint = pointRemainRepository.sumPointByUserId(userId);
    log.info("갱신된 user[" + userId + "]의 유효포인트 총점: " + currentUserPoint);

    Optional<PointTotal> selectedPointTotal = pointTotalRepository.findByUserId(userId);
    selectedPointTotal.ifPresentOrElse(
            pointTotal -> pointTotal.updateTotalPoint(currentUserPoint),
            () -> pointTotalRepository.save(PointTotal.builder()
                    .totalRemainPoint(currentUserPoint)
                    .userId(userId)
                    .build())
    );
}
```
- `@CacheEvict`: 해당 key에 대한 redis cache 데이터 삭제