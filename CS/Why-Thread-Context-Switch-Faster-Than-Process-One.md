## 스레드의 컨텍스트 스위치가 프로세스보다 더 빠른 이유?
### 컨텍스트 스위칭이란?
- CPU나 코어에서 실행 중이던 프로세스나 스레드가 다른 프로세스나 스레드로 교체되는 과정
- 멀티태스킹 시스템에서 여러 작업을 효율적으로 관리하기 위한 메커니즘

### 컨텍스트 스위칭의 구체적인 과정
- **프로세스 컨텍스트 스위칭 (다른 프로세스에 속한 스레드 간의 컨텍스트 스위칭)**
  - 현재 프로세스의 레지스터 상태와 메모리 정보 저장
  - 스케줄러가 선택한 다음 프로세스의 상태 로드
  - 프로세스 전환
  - 추가 메모리 주소 관련 작업 처리
- **스레드 컨텍스트 스위칭 (같은 프로세스에 속한 스레드 간의 컨텍스트 스위칭)**
  - 현재 스레드의 레지스터 상태 저장
  - 동일한 프로세스 내에서 실행할 다음 스레드의 상태 저장
  - 스레드 전환
  - 메모리 처리 생략

### 프로세스와 스레드의 컨텍스트 스위칭 차이점
- 같은 프로세스에 속한 스레드 간 컨텍스트 스위칭은 같은 프로세스에 속하기 때문에 **메모리 영역을 공유**한다.
- 이로 인해 스위칭이 발생해도 메모리와 관련된 추가 작업은 발생하지 않는다.
- 다른 프로세스에 속한 스레드 간 컨텍스트 스위칭의 경우 메모리 프로세스간 메모리 주소 체계가 달라서 메모리 주소 관련 처리를 추가적으로 해야 한다.

### 스레드의 컨텍스트 스위칭이 프로세스보다 빠른 이유
- 스레드의 컨텍스트 스위칭의 경우 프로세스 컨텍스트 스위칭과 달리 메모리 주소 관련 작업을 하지 않고 CPU 상태 정보만 바꿔주면 되기 때문
  - MMU (Memory Manage Unit)의 새로운 주소 체계를 바라보도록 수정하고 TLB (Translation Lookaside Buffer) 라는 가상 메모리 주소와 실제 메모리에 저장된 캐시를 비우는 등 메모리 주소 관련 작업

