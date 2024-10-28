## CQS

**C**ommand and **Q**uery **S**eparation. 

CQS는 명령과 조회를 연산 수준에서 분리

## CQRS패턴

**C**ommand **Q**uery **R**esponsibility **S**egregation, 명령 조회 책임 분리 패턴(읽기와 쓰기 분리)

- CQRS는 CQS 원리에 기원
- 사실 CQRS는 처음엔 CQS의 확장으로 얘기되었음
- CQS는 명령과 조회를 연산 수준에서 분리하는 반면 CQRS는 개체(object)나 시스템(혹은 하위 시스템) 수준에서 분리

![쓰기와 읽기의 분리 과정](https://engineering-skcc.github.io/assets/images/msa/MSA3.14.png)

- 윗 그림에서 Write Data Store 과 Read Data Store 은 결과적 일관성(Eventual Consistency)추구