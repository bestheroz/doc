# Stream Operations

스트림(Stream) API의 중간 연산자와 최종 연산자에 대해 설명해 드리겠습니다.

1. 중간 연산자 (Intermediate Operations):

   - 정의: 스트림을 변환하거나 필터링하는 연산자로, 새로운 스트림을 반환합니다.
   - 특징: 지연 평가(lazy evaluation)를 사용하며, 최종 연산자가 호출될 때까지 실행되지 않습니다.
   - 주요 중간 연산자:
     - filter(): 조건에 맞는 요소만 선택
     - map(): 각 요소를 변환
     - flatMap(): 각 요소를 스트림으로 변환 후 하나의 스트림으로 평탄화
     - distinct(): 중복 제거
     - sorted(): 정렬
     - limit(): 요소 수 제한
     - skip(): 처음 n개 요소 건너뛰기

2. 최종 연산자 (Terminal Operations):

   - 정의: 스트림 처리를 마무리하고 결과를 반환하는 연산자입니다.
   - 특징: 스트림을 소비하며, 이후 스트림을 재사용할 수 없습니다.
   - 주요 최종 연산자:
     - forEach(): 각 요소에 대해 작업 수행
     - collect(): 요소를 컬렉션으로 변환
     - reduce(): 요소를 하나의 결과로 줄임
     - count(): 요소 개수 반환
     - anyMatch(), allMatch(), noneMatch(): 조건 충족 여부 확인
     - findFirst(), findAny(): 요소 찾기
     - min(), max(): 최소값, 최대값 찾기

3. 사용 예시:

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);

int sumOfSquaresOfEvenNumbers = numbers.stream()
    .filter(n -> n % 2 == 0)   // 중간 연산자: 짝수만 필터링
    .map(n -> n * n)           // 중간 연산자: 제곱으로 변환
    .reduce(0, Integer::sum);  // 최종 연산자: 합계 계산

System.out.println(sumOfSquaresOfEvenNumbers);  // 출력: 220
```

이 예시에서 `filter()`와 `map()`은 중간 연산자이고, `reduce()`는 최종 연산자입니다.

중간 연산자와 최종 연산자를 적절히 조합하면 복잡한 데이터 처리 작업을 간결하고 효율적으로 수행할 수 있습니다.