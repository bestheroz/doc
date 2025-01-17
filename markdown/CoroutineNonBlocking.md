# 코루틴의 논블럭킹 동작

1. 코루틴의 논블로킹:
- await asyncio.sleep()은 제어권을 이벤트 루프에 반환합니다
- 대기 중에 다른 코루틴이 실행될 수 있습니다
- 여러 작업을 동시에 처리할 수 있습니다

2. 주요 특징:
- 코루틴은 async/await 구문을 사용합니다
- asyncio.gather()로 여러 코루틴을 동시에 실행할 수 있습니다
- 이벤트 루프가 작업들을 스케줄링합니다

코루틴은 기본적으로 싱글 스레드에서 동작합니다. 하지만 필요한 경우 멀티 스레드와도 함께 사용할 수 있습니다.

1. 기본 동작 (싱글 스레드)
- 코루틴은 기본적으로 싱글 스레드에서 실행됩니다
- asyncio.gather()로 실행되는 여러 코루틴들은 같은 스레드에서 동작합니다
- I/O 작업을 만났을 때 다른 코루틴에게 제어권을 넘깁니다
  
2. CPU 바운드 작업 처리 (멀티 스레드 활용)
- CPU 집약적인 작업은 ThreadPoolExecutor를 사용해 별도 스레드에서 실행할 수 있습니다
- loop.run_in_executor()를 통해 코루틴과 스레드 풀을 연결합니다
- 이를 통해 CPU 바운드 작업이 메인 스레드를 블록하지 않게 됩니다

3. 주의사항
- 일반적인 I/O 작업은 코루틴만으로 충분합니다
- CPU 바운드 작업의 경우 스레드 풀이나 프로세스 풀을 고려해야 합니다
- 스레드를 사용할 때는 스레드 안전성에 주의해야 합니다

이렇게 코루틴은 기본적으로는 싱글 스레드 모델이지만, 필요에 따라 멀티 스레드와 함께 사용하여 더 효율적인 프로그램을 만들 수 있습니다.