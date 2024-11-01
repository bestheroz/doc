# 트랜잭션 격리 수준(Isolation Level)

격리 수준이 높아질수록 데이터 일관성은 증가하지만, 동시성 처리 성능은 떨어집니다. 따라서 애플리케이션의 요구사항에 맞는 적절한 격리 수준을 선택하는 것이 중요합니다.

1. READ UNCOMMITTED (가장 낮은 격리 수준)
   - Dirty Read 발생 가능
   - Non-Repeatable Read 발생 가능
   - Phantom Read 발생 가능
   - 성능은 가장 좋지만 데이터 일관성이 매우 낮음
2. READ COMMITTED
   - Dirty Read 방지
   - Non-Repeatable Read 발생 가능
   - Phantom Read 발생 가능
   - 대부분의 데이터베이스 시스템의 기본 격리 수준
3. REPEATABLE READ
   - Dirty Read 방지
   - Non-Repeatable Read 방지
   - Phantom Read 발생 가능 (일부 DBMS에서는 방지)
   - 동일 데이터에 대한 일관된 읽기 보장
4. SERIALIZABLE (가장 높은 격리 수준)
   - Dirty Read 방지
   - Non-Repeatable Read 방지
   - Phantom Read 방지
   - 완벽한 데이터 일관성 보장, 그러나 성능 저하가 가장 큼

## 주요 용어 설명:

- Dirty Read: 커밋되지 않은 데이터를 읽는 현상
- Non-Repeatable Read: 같은 쿼리를 실행했을 때 결과가 다르게 나오는 현상
- Phantom Read: 없던 레코드가 생기거나 있던 레코드가 사라지는 현상

## 성능차이

한 벤치마크 테스트에서 다음과 같은 결과가 나왔습니다 (상대적 성능):

- READ UNCOMMITTED: 100%
- READ COMMITTED: 98%
- REPEATABLE READ: 90%
- SERIALIZABLE: 70%

하지만 이는 특정 환경과 워크로드에 대한 결과이며, 실제 상황은 다를 수 있습니다.