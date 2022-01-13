# Transactional

## Proxy 패턴을 이용한 Transaction 부가기능 분리

```java
// Proxy 생성을 위한 인터페이스화
public interface UserService {
  // 기능들 정의
}

// 실제 비즈니스 로직이 담긴 클래스
public class UserServiceImpl implements UserService{
    UserDao userDao;
    MailSender mailSender;
    UserLevelUpgradePolicy policy;
    // 실제 비즈니스 로직 정의
}

// 트랜잭션 기능만을 다루는 클래스
@Setter
public class UserServiceTx implements UserService {
    private UserService userService; // 실제 비즈니스 로직이 담긴 오브젝트를 주입
    private PlatformTransactionManager transactionManager;

    // 트랜잭션 처리 기능을 위한 proxy method
    @Override
    public void upgradeLevels() {
        TransactionStatus status =
                this.transactionManager.getTransaction(new DefaultTransactionDefinition());

        try {
            userService.upgradeLevels();
        } catch (RuntimeException e) {
            this.transactionManager.rollback(status);
            throw e;
        }
    }
}
```
- `UserService` 인터페이스화를 통해 Proxy를 생성
- 클라이언트는 UserServiceTx를 사용함
- UserServiceTx 내부에서 실제 UserService 비즈니스 로직 실행

### 단점
- Proxy를 서비스 오브젝트마다 설정해줘야 함
- 해당 서비스 오브젝트 내에서 Transaction 설정 대상 메소드마다 Transaction 처리 로직 구현해야 함
- 상당히 번거로움
