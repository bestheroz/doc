## 소프트웨어 아키텍처란

소프트웨어의 전체적인 구조를 잡아주는 설계도

예) 레이어드 아키텍처, MVC 패턴 등

##### 아키텍처가 있다면

- 소프트웨어의 큰 그림을 이해하기 쉬움
  - 코드를 다 확인하지 않아도, 일관된 코드 구조로 흐름을 쉽게 유추할 수 있음
    - 개발의 생산성 향상
    - 개발자의 커뮤니케이션 비용을 줄일 수 있음
- 트레이드 오프
  - 코드를 작성하거나 읽어야 할 때 알아야 될 규칙이 늘어남
    - 팀원 전체가 프로젝트의 아키텍처 패턴에 익숙해야 함
    - 전반적으로 작성해야하는 코드의 양이 늘어남

##### 아키텍처가 없다면

- 코드를 어디에 두어야 할지 모름
  - 또한 내가 생각한 기준과 팀원이 생각한 기준이 다를 수 있기에 output 이 일관성이 없음
- 팀에 새로운 사람이 들어와 프로젝트 코드를 보면, 어디서부터 어떻게 봐야 할지 감이 오지 않음

##### 아키텍처를 처음에 쉽게 이해하려면

- **의존성** 관계에 집중
  - 컴포넌트별로 역할을 명확하게 나누고 의존 관계를 분명히 하는 것이 핵심

- 본인이 자주 사용하는 언어 프레임워크와 아키텍처 패턴을 구글링해서 다른 사람들은 어떻게 구현하는지 알아보고 적용

**아키텍처에 정답은 없음**

- 아무리 유명한 아키텍처라고 하더라도, 당장 상황에 맞지 않은 아키텍처는 좋은 아키텍처가 아님
- 완벽한 아키텍처는 존재하지 않음

- 상황에 따라 레이어 개수나 레이어별 의미가 달라질 수 있음
- 중요한 것은 레이어를 잘 나눌 수 있도록 경계를 설정할 수 있어야 함
  - 개발자가 어떤 모듈을 어디에 두어야 할지에 대한 고민을 줄여줘야 함
- 반드시 의존 흐름을 바깥에서 안쪽으로 가져가야 함

### 레이어드 아키텍처(Layered architecture)

- 레이어를 분리하여 레이어 마다 해야 할 역할을 정의해놓은 구조(**의존성 방향**)

![Layered Architecture](https://images.velog.io/images/hanblueblue/post/c2555af2-4fc5-4e64-9a3a-2314b2239941/layered-architecture-overview.png)

- Presentation Layer
  - 인터페이스와 애플리케이션이 연결되는 곳 (예: controller, router)
- Application Layer
  - 소프트웨어가 제공하는 주요 기능을 구현하는 코드가 모이는 곳
- Domain Layer
  - 도메인 모델, 도메인 서비스 등 도메인 문제를 코드로 풀어내는 일을 담당
- Infrastructure Layer
  - DB와의 연결, ORM 객체, 메시지 큐 등 애플리케이션 외적인 인프라들과의 어댑터 역할

##### 장점

- 정해진 역할이 분명하여 유지보수 및 코드 관리가 용이
  - 의존성 방향이 동일하여 테스트 코드 짜기도 수월
  - 새로운 기능을 개발할 때 통일된 흐름에 맞게 빠르게 개발 가능

##### 단점

- 소프트웨어가 최종적으로 Infrastructere Layer 에 의존성을 갖음
  - 소프트웨어에서 가장 중요한 부분은 비지니스 로직을 처리하는 ""Application Layer"와 "Domain Layer"
  - Domain Layer 가 Infrastructure Layer, 특히 DB에 의존하게 됨
  - **소프트웨어의 모든 구조가 DB 중심의 설계가 됨**
    - **소프트웨어는 그저 DB에 데이터를 옮겨주는 역할이 됨**
  - **애플리케이션 설계에 앞서 데이터베이스를 먼저 설계하게 됨**
  - 객체지향에서 추구하는 "Action" 중심이 아니라 "State" 중심으로 설계하게 됨
    - 점점 객체지향에서 벗어나는 코드를 작성하게 됨

##### 소프트웨어 아키텍처의 지향점

- 소프트웨어의 중심은 DB와 같은 외부 시스템이 아니라 애플리케이션이 중심

- 애플리케이션을 중심으로 보고 DB, 웹 프레임워크 등은 모두 애플리케이션이 사용하는 부품으로 봐야함

### 헥사고날 아키텍처(Hexagonal architecture)

애플리키에션과 바깥 모듈을 자유롭게 탈부착

- 예: 게임기 본체 (엑스박스, 플레이스테이션, 닌텐도) 와 주변기기 (입력패드, 모니터, 주변기기)
  - 핵심은 게임기 본체 => 애플리케이션
  - 주변기기는 취향에 따라 변경 => 바깥 모듈
  - **포트 앤 어댑터 패턴**(Ports and Adapters)

**의존성 흐름**

**Adapter -> Application -> Domain**

![헥사고날(Hexagonal) 아키텍처 in 메쉬코리아:: MESH KOREA | VROONG 테크 블로그](https://mesh.dev/static/5218ecc01e2106b794a5831fda5baca3/2b72d/03.png)

- Domain
  - 소프트웨어의 핵심이 되는 도메인
  - Layered architecture의 Domain Layer와 같음
- Application
  - Domain을 이용하여 비지니스를 제공
  - 관용적으로 Service 라고 표현
  - 포트를 가지고 있음
    - 포트는 외부의 어댑터를 끼울 수 있는 인터페이스
- Adapter
  - Application 의 포트 규격에 맞게 구현한 구현체
    - Application 의 포트를 나타내는 인터페이스를 상속받아 구현
  - DB는 Out bound adapter
  - Browser, App 은 In bound adapter

##### 코드 변화

AS-IS

```java
public class AdminService {
  private final AdminRepository adminRepository;

  public AdminDTO remove(final Long id) {
    return this.adminRepository
        .findById(id)
        .map(admin -> new AdminDTO(admin.remove()))
        .orElseThrow(() -> new BusinessException(ExceptionCode.FAIL_NO_DATA_SUCCESS));
  }
}
```

TO-BE

```java
public class AdminService {
  public AdminDTO remove(final Long id, final AdminRepository adminRepository) {
    return adminRepository
        .findById(id)
        .map(admin -> new AdminDTO(admin.remove()))
        .orElseThrow(() -> new BusinessException(ExceptionCode.FAIL_NO_DATA_SUCCESS));
  }
}
```

##### 트레이드오프

어댑터, 포트 등 알아야 할 개념과 보일러 플레이트 코드가 늘어날 수 있음

### 클린 아키텍처

당연한 아키텍처 설계

- 세부 사항(DB, 프레임워크)가 정책(업무 규칙)에 영향을 주면 안 됨
- 계층별로 관심사를 명확하게 분리하여 변경이 필요할 때 영향을 주는 부분을 최소화
- 내부 계층이 외부 계층을 의존하지 않아야 함
- **의존 방향 규칙** (저수준 -> 고수준)

![Clean Architecture는 모바일 개발을 어떻게 도와주는가? - (1) 경계선: 계층 나누기 | by Saryong Kang  | Medium](https://miro.medium.com/max/1400/1*_HxTmyiDQNCfACBZle67rw.jpeg)

### 모놀리스 아키텍처

일반적이고 우리 상식에 들어맞는 아키텍처 패턴

##### 장점

- 디버깅이나 비교적 장애 대응이 비교적 쉬움

##### 단점

- 최신 기술 스택이 나와도 쉽게 도입하기 어려움

### 마이크로서비스 아키텍처

소프트웨어를 구성하는 각각의 컴포넌트들을 독립적인 프로젝트들로 분리하는 것

일반적으로 서비스가 성장하고 프로젝트 규모가 커질 때 모놀리식에서 마이크로서비스로 전환함

![img](https://www.redhat.com/cms/managed-files/microservices-1680.png)

![마이크로 서비스 아키텍처 & 모놀리틱 아키텍처](https://images.velog.io/images/dsunni/post/b61ec810-1347-4022-91e6-2b8c139d3d06/assets_-LE8_fwLnI2gUuguYTDU_-LH6l3KoYH7hthyWqBvj_-LH6l4H0EWQvNC1mq4_P_monolithic-vs-microservice.png)

![마이크로서비스란? SOA와 차이점 | SK(주) C&C's TECH BLOG](https://engineering-skcc.github.io/assets/images/MSA1.6.png)

- 서로의 데이터처리가 필요한 경우 서로간의 HTTP 통신

##### 장점

- 서비스 개발과 배포를 빠르게 진행
- 마이크로서비스 마다 프로그래밍 언어나 데이터베이스에 대한 기술 스택을 더 자유롭게 가져갈 수 있음
- 하나의 마이크로서비스에서 장애가 발생하더라도 다른 마이크로서비스는 정상 동작 가능

##### 단점

- 테스트 난이도가 굉장히 높음
- DDD, EDA 의 개념을 이해하는 난이도가 굉장히 높음
- 처음에 어떤 기준으로 마이크로서비스를 나눌지 결정이 어려움

##### 구축의 난이도, 복잡성

- 고도의 디커플링을 요구하며 재사용은 커플링을 유발하기 때문에 재사용보다 중복을 우선시 함
- 잦은 네트워크 호출이 필요하므로 성능은 다소 부정적이며 보안 처리, 분산트랜잭션 등 난이도와 복잡도가 높아짐
- 저장소 (데이터)의 완벽한 격리 필요하며, 통합 데이터 모델링 사상과 상충되며 데이터 중복, 복제가 고려되어야 함
- 데이터 일관성 처리를 위한 결과적 일관성 매커니즘 필요(복잡도 증가)

![img](https://static.wixstatic.com/media/73a3c2_b1064b21b626431292a1eaa827aa7c5d~mv2.png/v1/fill/w_1104,h_444,al_c,q_90,usm_0.66_1.00_0.01,enc_auto/73a3c2_b1064b21b626431292a1eaa827aa7c5d~mv2.png)

##### 성공을 위한 조건들

- **조직**: 특정 도메인에 자율적인 권한과 책임을 지는 DevOps 조직(개발 + 운영)

- **인프라**: 언제나 쉽게 확장 가능한 유연한 인프라 구성 (온 디맨드 확장성, 탄력성)

- **자동화**: 개발지원 도구 자동화, DevOps Infra(CI/CD)
- **개발 프로세스**: 피드백 기반의 개발 프로세스, 반복/점진적인 애자일 프로세스
- **개발 문화**: 공유/협업하고 학습하여 계속 진화하는 개발 문화(공유, 협업, 자율, 책임, 학습)
- **설계 방식**: 데이터 중복, 결과적 일관성 추구



##### 마이크로 서비스 성숙도

![img](https://cdn.inflearn.com/public/files/courses/328412/2184ab1c-795c-4298-9ad7-447e89fb512f/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA%202022-05-13%20%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE%2011.13.12.png)

## 