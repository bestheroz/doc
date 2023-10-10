## 병렬성과 동시성

CPU가 쉴 틈 없이 한 번에 주어진 테스크들을 빠르게 처리되다 보니 컴퓨터 사용자는 사실상 모든 프로세스의 명령이 "동시에" 처리된다고 느끼게 됨, 이것을 동시성(Concurrency)이라고 함

- 동시성이라는 개념은 물리적으로 CPU 1개의 코어에서만 동작하는 개념은 아님

- 제한된 자원에서 여러 작업을 한번에 실행시키려는 논리적인 개념

### 동시성(Concurrency)

![img](https://velog.velcdn.com/images%2Fcha-suyeon%2Fpost%2Fe13b6da0-c211-44d6-a8bf-a7dee3d539b3%2Fimage.png)

### 병렬성(Parallelism)

![img](https://velog.velcdn.com/images%2Fcha-suyeon%2Fpost%2Fd7ddc0d2-23b6-41fe-b406-3c1284634e22%2Fimage.png)

- **동시성**은 실제로는 하나의 명령을 빠르게 수행하지만 처리속도가 매우 빨라 여러 작업이 동시에 진행되는 것처럼 "느껴지게" 해줌

- **병렬성**은 "실제로" 여러 개의 명령어를 동시에 실행하는 것

### 병렬 동시 실행

![멀티태스킹(1) - 동시성, 병렬성(Concurrency, Parallelism)](https://velog.velcdn.com/images%2Fcha-suyeon%2Fpost%2F16ef40eb-e9b8-45cb-9a7e-00a08d946907%2Fimage.png)

