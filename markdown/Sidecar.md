## 사이드카(Sidecar) 패턴

![img](https://image.samsungsds.com/kr/insights/mesh_img03.jpg?queryString=20220820100250)

- 모든 응용 프로그램 컨테이너에 사이드카 컨테이너를 추가하여 배포
- 서비스에 들어오거나 나가는 모든 네트워크 트래픽을 처리
- 가장 큰 특징은 비즈니스 로직이 포함된 실제 서비스와 사이드카가 병렬로 배치되기 때문에 서비스가 타 서비스를 직접 호출하는 것이 아니라 프록시(proxy)를 통해서 호출한다는 점
- 따라서 대규모의 마이크로서비스 환경이더라도 별도 작업 없이 서비스 연결뿐만 아니라 로깅, 모니터링, 보안, 트래픽 제어가 가능

![img](https://www.redhat.com/cms/managed-files/service-mesh-1680.png)

