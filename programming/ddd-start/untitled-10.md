# 11. CQRS

## 단일 모델의 단점 <a id="2eb643e2-d27c-486a-ba3c-b8588a2b0a6d"></a>

조회 화면의 특성상 조회 속도가 빠를수록 좋은데 **여러 애그리거트에서 데이터를 가져와야 할 경우** 구현 방법을 고민해야 한다.

* ID를 이용하여 애그리거트를 참조하는 방식을 사용하면 `즉시 로딩(eager loading)` 방식과 같은 JPA 쿼리 관련 최적화 기능을 사용할 수 없다.
* 직접 애그리거트를 참조하는 방식을 해도 조회 화면의 특성에 따라 같은 연관도 즉시 로딩이나 `지연 로딩(lazy loading)`으로 처리해야 한다.

이런 고민이 발생하는 이유는 시스템의 상태를 변경할 때와 조회할 때 `단일 도메인 모델`을 사용하기 때문이다. 이런 구현 복잡도를 낮추기 위해서는 상태 변경을 위한 모델과 조회를 위한 모델을 분

## CQRS <a id="3560453e-04ac-486d-b235-eae3c4a9f5ee"></a>

`CQRS (Comand Query Responsibility Segregation)`는 상태를 변경하는 **명령\(Command\)**을 위한 모델과 상태를 제공하는 **조회\(Query\)**를 위한 모델을 분리하는 패턴이다.

* **CQRS는 복잡한 도메인에 적합하다.** 도메인이 복잡할수록 명령 기능과 조회 기능이 다루는 범위에 차이가 발생하는데, 이 두 기능을 단일 모델로 처리하면 조회 기능의 로딩 속도를 위해 모델
* **CQRS를 사용하면 각 모델에 맞는 구현 기술을 선택할 수 있다.** 예를 들어, 명령 모델은 객체 지향에 기반해서 도메인 모델을 구현하기에 적당한 JPA를 사용해서 구현하고, 조회 모델은 DB 테이블에서 SQL로 데이터를 조회할 때 좋은 MyBatis를 사용해서 구현할 수 있다.
* **명령 모델과 조회 모델이 서로 다른 데이터 저장소를 사용할 수도 있다.** 명령 모델은 트랜잭션을 지원해주는 RDBMS를 사용하고, 조회 모델은 조회 성능이 좋은 메모리 기반 NoSQL을 사용할 수 있다. 두 데이터 저장소 간의 `데이터 동기화`는 이벤트를 활용해서 처리한다. 명령 모델에서 데이터가 바뀌자마자 변경 내역을 바로 조회 모델에 반영해야 한다면 `동기 이벤트`와 `글로벌 트랜잭션`을 사용해서 실시간으로 동기화 할 수 있다\(성능 감소\). 서로 다른 저장소의 데이터를 특정 시간 안에만 동기화해도 된다면 `비동기`로 데이터를 전송해도 된다.

### 조회 모델 <a id="1bb1feb6-ca0b-411a-a2d6-cec05d815b1a"></a>

조회 모델 → 서비스 인터페이스 → 조회 모델을 이용해서 표현 계층에 데이터 출력 → UI

### 명령 모델 <a id="a15c1ce4-cf43-494c-bf2f-bee0429c1cf9"></a>

명령 모델 \(도메인 로직 실행\) ← 서비스 인터페이스 ← 애플리케이션이 변경 요청을 명령 모델로 처리 ← UI

### 웹과 CQRS <a id="3286c64c-1898-4997-87a4-c975e50fa725"></a>

대규모 트래픽이 발생하는 웹 서비스는 알게 모르게 CQRS를 적용하게 된다. 단지, 명식적으로 명령 모델과 조회 모델을 구분하지 않을 뿐이다.

조회 속도를 높이기 위해 별도 처리를 하고 있다면 **명시적으로 명령 모델과 조회 모델을 구분하자.** 이를 통해 조회 기능 때문에 명령 모델이 복잡해지는 것을 방지할 수 있고 명령 모델에 관계 없이 조회 기능에 특화된 구현 기법을 쉽게 적용할 수 있다.

### CQRS 장단점 <a id="944cf1bc-a6ed-44b2-be3d-bfd0fe5d8592"></a>

**장점**

* `명령 모델을 구현할 때 도메인 자체에 집중할 수 있다.` 복잡한 도메인은 주로 상태 변경 로직이 복합한데 두 모델을 구부하면 조회 성능을 위한 코드가 명령 모델에 없으므로 도메인 로직을 구현하는데 집중할 수 있다. 또한 명령 모델에서 조회 관련 로직이 사라져 복잡도를 낮춰준다.
* `조회 성능을 향상시키는 데 유리하다.` 조회 단위로 캐시 기술을 적용할 수 있고, 조회에 특화된 쿼리를 마음대로 사용할 수도 있다. 캐시뿐만 아니라 조회 전용 저장솔르 사용하면 조회 처리량을 대폭 늘릴 수 있다.

**단점**

* `구현해야 할 코드가 많아진다.` 도메인이 복잡하거나 대규모 트래픽이 발생하는 서비스라면 조회 전용 모델을 만드는 것이 유지보수에 유리하다. 반면 도메인이 단순하거나 트래픽이 많지 않은 서비스라면 조회 전용 모델을 만들 필요가 없다.
* `더 많은 구현 기술이 필요하다.` 명령 모델과 조회 모델을 다른 구현 기술을 사용해서 구현하기도 하고 경우에 따라 다른 저장소를 사용하기도 한다. 또한, 데이터 동기화를 위해 메시징 시스템을 도입해야 할 수도 있다.

