## EDA

**E**vent **D**riven **A**rchitecture

분산된 시스템에서 이벤트를 생성(발행)하고 발행된 이벤트를 수신자에게 전송하는 구조로 수신자는 그 이벤트를 처리하는 방식의 아키텍처

분산 아키텍처 환경에서 상호 간 결합도를 낮추기 위해 **비동기 방식으로 메시지를 전달하는 패턴**으로 주로 Message Broker(`Kafka`, `RabbitMQ`)와 결합하여 구성

![eda 4](https://akasai.space/static/7ba919f10f436c2f2d6684ad2226252f/c1b63/eda_4.png)

##### 장점

1. Loosely Coupling

분산 시스템간 느슨한 결합도를 제공

2. 분산된 시스템간 의존성 배제

약속된 Message를 통해 통신하기 때문에 다른 시스템의 정보를 알 필요가 없으므로 시스템 간 의존성이 배제

3. 확장성, 탄력성 향상

##### 단점

1. Broker Dependency

시스템 간 의존도는 낮아지지만 메시지브로커에 대한 의존성이 발생

만약 메시지 브로커의 장애가 발생하면 큰 장애로 확산될 가능성이 있음

2. Transaction 단위 분리

장애나 이슈발생시 Retry/Rollback에 대한 고려가 필요

3. 시스템 Flow파악이 어려움

4. 디버깅이 어려움

##### EDA의 구성요소

1. **Event Generator (Publisher, Producer, Creater)**

표준화된 형식의 이벤트를 생성(발행)

생성된 이벤트는 Event Channel로 전송

2. **Event Channel (Bus)**

Event Generator에서 Event Processing Engine으로 수집된 데이터를 전파하는 메커니즘

즉, 이벤트를 필요로 하는 시스템까지 발송하는 역할

3. **Event Processing Engine (Consumer, Processor)**

수신한 이벤트를 식별/처리하는 역할

처리 결과에 따라 새로운 이벤트를 생성할 수 있음

Consumer는 이벤트의 송신자에 대한 정보를 알 필요가 없음

![eda 2](https://akasai.space/static/c281ca3843c645c19e735dd11a992999/c0b7e/eda_2.webp)

##### EDA의 동작 방식

1. **Message 생성 (Publish/Subscribe)**

   **이벤트**가 생성되면 `Subscriber(수신자)`에게 전달

   이벤트는 반복되어 전달되지 않으며, 수신자는 송신자의 정보를 알 필요가 없음

2. **Event Source**

   `Event Processor`에게 이벤트를 전달하는 역할을 함

   `Event Source`는 1개 이상일 수 있으며, 1개 이상의 `Event Processor`에게 전달

3. **Event Processor**

   수신된 이벤트에 대한 여러 `Action`을 수행하는 역할

   단일 이벤트에 대하여 타임스템프를 추가한다거나, 파생 이벤트를 만드는 등의 작업을 수행

4. **Event Consumer**

   이벤트에 대한 처리를 함

   실질적인 **Biz Logic**을 수행