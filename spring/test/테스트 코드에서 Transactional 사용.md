# 테스트 코드에서 @Transactional

- 서비스 코드에 대한 테스트 코드 작성시 `@Transactional` 를 사용할 수 있다.
- 하지만 실제 비즈니스 코드에는 트랜잭션이 걸려있지 않고, 테스트 코드에만 트랜잭션이 걸리는 경우도 있다.
- 이러한 경우에 테스트 코드는 정상동작하지만, 실제 비즈니스 로직에는 문제가 있을 수 있다.
- 따라서 이를 인지하고 사용하는 것이 중요하다.
