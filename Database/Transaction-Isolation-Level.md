## 트랜잭션의 격리 수준
* 트랜잭션의 격리 수준이란 **여러 트랜잭션이 동시에 처리될 때, 특정 트랜잭션이 다른 트랜잭션에서 변경하거나 조회하는 데이터를 볼 수 있게 허용할지 여부를 결정하는 것**이다.

### 트랜잭션 격리 수준 목록
**참고: 아래 예제들은 모두 자동 커밋 (AUTO COMMIT) 이 false 인 상태에서 발생한다.**

1. **READ UNCOMMITTED**
   * **커밋하지 않은 데이터**까지도 접근할 수 있는 격리 수준
   * 다른 트랜잭션의 작업이 **커밋 또는 롤백되지 않아도 즉시 보인다.**
     * 예) A의 트랜잭션에서 INSERT를 통해 데이터 추가 → 아직 커밋 또는 롤백되지 않음 → **변경된 데이터에 접근 가능**
     * 어떤 트랜잭션 작업이 완료되지 않았는데 다른 트랜잭션에서 볼 수 있는 부정합 문제: **Dirty Read** (오손 읽기)
2. **READ COMMITTED**
   * **커밋된 데이터만** 조회할 수 있는 격리 수준
   * **Non-Repeatable Read** (반복 읽기 불가능) 문제가 발생할 수 있다.
     * 예) B 트랜잭션에서 `name = "John"`인 레코드를 조회 → 해당 조건을 만족하는 레코드 아직 존재하지 않음 → A 트랜잭션에서 UPDATE를 통해 `name = "John"` 조건을 만족하는 레코드 생성 및 커밋 완료 → B 트랜잭션에서 다시 같은 조건으로 조회 → 조건을 만족하는 결과 조회됨
     * 즉 반복 읽기르 수행하면 다른 트랜잭션의 커밋 여부에 따라 **조회 결과가 달라질 수 있다.**
     * 일반적인 경우 크게 문제되지 않지만, 하나의 트랜잭션에서 동일한 데이터를 여러 번 읽고 변경하는 작업이 금전적인 처리와 연결되어 있다면 문제가 될 수 있다.
3. **REPEATABLE READ**
   * 일반적인 RDBMS는 변경 전의 레코드를 Undo 공간에 백업해둔다. 그러면 변경 전/후 데이터가 모두 존재하므로 동일한 레코드에 대해 여러 버전의 데이터가 존재한다고 하여 이를 **MVCC** (다중 버전 동시성 제어) 라고 한다.
   * REPEATABLE READ는 MVCC를 통해 한 트랜잭션 내에서 동일한 결과를 보장하지만, **새로운 레코드가 추가되는 경우 부정합**이 생길 수 있다.
     * 트랜잭션이 끝나기 전에 다른 트랜잭션에 의해 추가된 레코드가 발견되는 문제: **Phantom Read** (유령 읽기)
     * MVCC 덕분에 일반적인 조회에서 유령 읽기는 발생하지 않는다. → 나중에 실행된 트랜잭션이 추가한 레코드는 무시하면 되기 때문
     * **락을 사용하는 경우** 유령 읽기가 발생할 수 있다.
       * 예) B 트랜잭션에서 `id >= 50`인 레코드를 SELECT FOR UPDATE 로 쓰기 락을 걸어 조회 → A 트랜잭션에서 `id = 50`인 레코드 생성 → B 트랜잭션에서 한번 더 같은 조건의 레코드 조회 시 추가된 레코드도 조회됨
     * MVCC를 통해 해결이 안되는 이유는 락이 있는 읽기는 Undo 로그가 아닌 **테이블에서 수행되기 때문**이다.
     * 하지만 **MySQL**에서는 갭 락이 존재하여 위의 문제가 발생하지 않음
       * B 트랜잭션에서 `id >= 50`인 범위에 갭 락으로 넥스트 키 락을 검 → A 트랜잭션에서 `id = 50`인 레코드 INSERT 시도 → B 트랜잭션이 종료될 때까지 기다렸다 수행
     * 따라서 일반적으로 **MySQL의 REPEATABLE READ에서는 유령 읽기 문제가 발생하지 않는다.**
4. **SERIALIZABLE**
    * 가장 엄격한 격리 수준으로, 이름 그대로 트랜잭션을 **순차적**으로 진행시킨다.
    * **여러 트랜잭션이 동일한 레코드에 동시에 접근할 수 없으므로**, 어떤 부정합 문제도 발생하지 않는다.
    * 하지만 트랜잭션이 순차적으로 처리되어야 하기 때문에 **동시 처리 성능이 매우 떨어진다.**
    * SERIALIZABLE 격리 수준에서는 순수한 SELECT 작업도 대상 레코드에 넥스트 키 락을 읽기 락으로 건다.
    * 가장 안전하지만 가장 성능이 떨어지므로, 극단적으로 안전한 작업이 필요한 경우가 아니라면 사용하지 않는다.

