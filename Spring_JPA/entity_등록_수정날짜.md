# Time 관련 Entity 자동 등록

```sql
CREATE TABLE `point_history` (
    `history_id` binary(16) NOT NULL,
    `point_type` varchar(20) NOT NULL,
    `point` tinyint NOT NULL,
    `user_id` binary(16) NOT NULL,
    `place_id` binary(16) NOT NULL,
    `registered_date` datetime,
    `updated_date` datetime,
    PRIMARY KEY (`history_id`),
INDEX `idx_user_id` (`user_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```

```java
@Getter
@MappedSuperclass
@EntityListeners(AuditingEntityListener.class)
public abstract class BaseTimeEntity {
    @CreatedDate
    private LocalDateTime createdAt;

    @CreatedBy
    private String createdBy;

    @LastModifiedDate
    private LocalDateTime updatedAt;

    @LastModifiedBy
    private String updatedBy;
}
```
create, update 날짜 및 기록한 주체에 대한 자동 기록 가능

```java
@Configuration
@EnableJpaAuditing
public class JpaConfig {

}
```
- config 설정  
- Spring Data JPA에서 `@CreatedDate`같은 annotation auditing을 가능하게 해줌

```java
@Component
public class AllEntityAuditorAware implements AuditorAware<String> {
    @Override
    public Optional<String> getCurrentAuditor() {
        return Optional.of("Administrator");
    }
}
```
`@CreatedBy`에 적용