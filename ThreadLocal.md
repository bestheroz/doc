# Thread Local

ThreadLocal 은 각 스레드마다 독립적인 변수를 가질 수 있게 해주는 Java의 클래스입니다. 
하나의 변수를 여러 스레드가 동시에 접근할 때 발생할 수 있는 동시성 문제를 해결하는 데 도움이 됩니다.

주요 특징:
- 각 스레드마다 독립적인 변수 값을 가짐
- 스레드 안전성 보장
- 전역 변수처럼 사용 가능하면서도 각 스레드별로 격리된 값 유지


ThreadLocal 의 주요 사용 사례:
- SecurityContextHolder
- RequestContextHolder

주의사항:
- 사용이 끝난 후에는 반드시 clear() 메서드로 값을 제거해야 메모리 누수를 방지할 수 있습니다.
- 웹 애플리케이션에서는 요청 처리가 끝날 때 ThreadLocal 값을 정리하는 것이 좋습니다.

ThreadLocal 자체는 스레드 세이프하지만, ThreadLocal을 사용한다고 해서 모든 코드가 자동으로 스레드 세이프해지는 것은 아닙니다.

ThreadLocal 사용 시 주의할 점:

1. ThreadLocal 변수 자체의 get/set 작업은 스레드 세이프하지만:
- 저장된 객체를 수정하는 작업은 스레드 세이프하지 않을 수 있음
- ThreadLocal 외부의 공유 자원 접근은 여전히 동기화 필요

2. 잘못된 사용 예:
```Java
public class WrongExample {
    private static ThreadLocal<List<String>> listHolder = new ThreadLocal<>();
    
    public void addItem(String item) {
        List<String> list = listHolder.get();
        if (list == null) {
            list = new ArrayList<>();
            listHolder.set(list);
        }
        list.add(item); // 여러 스레드가 같은 List 객체를 참조하면 문제 발생
    }
} 
```

3. 올바른 사용 예:
```Java
public class CorrectExample {
    private static ThreadLocal<List<String>> listHolder = ThreadLocal.withInitial(ArrayList::new);
    
    public void addItem(String item) {
        List<String> list = listHolder.get(); // 각 스레드마다 새로운 ArrayList 생성
        list.add(item); // 각 스레드가 자신만의 List를 가지므로 안전
    }
} 
```

결론적으로:
- ThreadLocal 은 각 스레드마다 독립적인 변수를 가질 수 있게 해주는 도구입니다
- 하지만 ThreadLocal 을 사용한다고 해서 모든 코드가 자동으로 스레드 세이프해지는 것은 아닙니다
- 공유 자원에 대한 접근이나 객체의 상태 변경은 여전히 적절한 동기화가 필요할 수 있습니다