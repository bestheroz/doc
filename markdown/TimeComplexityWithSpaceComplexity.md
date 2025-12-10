# 알고리즘 복잡도(Time Complexity, Space Complexity)

## 1. 시간복잡도 (Time Complexity)

**정의**: 입력 크기 N에 따라 알고리즘이 수행하는 연산 횟수의 증가율

### Big-O 성능 순서 (빠름 → 느림)
```
O(1) < O(log N) < O(N) < O(N log N) < O(N²) < O(2^N) < O(N!)
```

### 복잡도별 특성

| 복잡도 | 이름 | N=1,000 | N=1,000,000 | 대표 알고리즘 |
|--------|------|---------|-------------|---------------|
| O(1) | 상수 | 1 | 1 | 배열 인덱스 접근, HashMap.get() |
| O(log N) | 로그 | 10 | 20 | 이진 탐색, 균형 트리 |
| O(N) | 선형 | 1,000 | 1,000,000 | 단일 순회, 선형 탐색 |
| O(N log N) | 선형로그 | 10,000 | 20,000,000 | 퀵정렬, 병합정렬 |
| O(N²) | 이차 | 1,000,000 | 10¹² | 버블정렬, 중첩 루프 |
| O(2^N) | 지수 | 10³⁰⁰ | 불가능 | 부분집합 탐색 |

### 코드 예시

```java
// O(1) - 상수 시간: 입력 크기와 무관
public int getFirst(int[] arr) {
    return arr[0];
}

// O(N) - 선형 시간: N번 순회
public int sum(int[] arr) {
    int total = 0;
    for (int num : arr) {
        total += num;
    }
    return total;
}

// O(N²) - 이차 시간: N × N 중첩 루프
public void printPairs(int[] arr) {
    for (int i = 0; i < arr.length; i++) {
        for (int j = 0; j < arr.length; j++) {
            System.out.println(arr[i] + ", " + arr[j]);
        }
    }
}

// O(log N) - 로그 시간: 매번 절반씩 탐색 범위 축소
public int binarySearch(int[] sorted, int target) {
    int left = 0, right = sorted.length - 1;
    while (left <= right) {
        int mid = (left + right) / 2;
        if (sorted[mid] == target) return mid;
        else if (sorted[mid] < target) left = mid + 1;
        else right = mid - 1;
    }
    return -1;
}
```

---

## 2. 공간복잡도 (Space Complexity)

**정의**: 입력 크기 N에 따라 알고리즘이 사용하는 메모리의 증가율

### 분석 대상
- **고정 공간**: 변수, 상수, 프로그램 코드 (분석에서 제외)
- **가변 공간**: 입력 크기에 비례하는 메모리 (핵심 분석 대상)

### 코드 예시

```java
// O(1) - 상수 공간: 변수 1개만 사용
public int findMax(int[] arr) {
    int max = arr[0];
    for (int num : arr) {
        if (num > max) max = num;
    }
    return max;
}

// O(N) - 선형 공간: 입력과 동일한 크기의 배열 생성
public int[] copy(int[] arr) {
    int[] result = new int[arr.length];
    for (int i = 0; i < arr.length; i++) {
        result[i] = arr[i];
    }
    return result;
}

// O(N) - HashSet 사용: 최악의 경우 N개 저장
public boolean hasDuplicate(int[] arr) {
    Set<Integer> seen = new HashSet<>();
    for (int num : arr) {
        if (!seen.add(num)) return true;
    }
    return false;
}

// O(N²) - 2차원 배열
public int[][] createMatrix(int n) {
    return new int[n][n];
}
```

---

## 3. 시간-공간 트레이드오프

**핵심 원칙**: 메모리를 더 쓰면 시간을 줄일 수 있다 (또는 그 반대)

### 예시: 중복 찾기

```java
// 방법 1: 시간 O(N²), 공간 O(1) - 메모리 절약
public boolean hasDuplicate_slow(int[] arr) {
    for (int i = 0; i < arr.length; i++) {
        for (int j = i + 1; j < arr.length; j++) {
            if (arr[i] == arr[j]) return true;
        }
    }
    return false;
}

// 방법 2: 시간 O(N), 공간 O(N) - 속도 우선 (일반적 권장)
public boolean hasDuplicate_fast(int[] arr) {
    Set<Integer> seen = new HashSet<>();
    for (int num : arr) {
        if (!seen.add(num)) return true;
    }
    return false;
}
```

| 방법 | 시간 | 공간 | 선택 기준 |
|------|------|------|-----------|
| 방법 1 | O(N²) | O(1) | 메모리 극히 제한적일 때 |
| 방법 2 | O(N) | O(N) | 일반적으로 권장 |

---

## 4. 빠른 복잡도 판단법

### 패턴별 복잡도

| 패턴 | 복잡도 |
|------|--------|
| 단일 루프 | O(N) |
| 중첩 루프 (2중) | O(N²) |
| 중첩 루프 (3중) | O(N³) |
| 절반씩 줄어듦 | O(log N) |
| 정렬 후 탐색 | O(N log N) |
| 재귀 (2갈래 분기) | O(2^N) |

### 자료구조별 시간복잡도

| 자료구조 | 접근 | 탐색 | 삽입 | 삭제 |
|----------|------|------|------|------|
| 배열 | O(1) | O(N) | O(N) | O(N) |
| HashMap | O(1) | O(1) | O(1) | O(1) |
| TreeMap | O(log N) | O(log N) | O(log N) | O(log N) |
| LinkedList | O(N) | O(N) | O(1) | O(1) |
| PriorityQueue | - | O(N) | O(log N) | O(log N) |

---

## 5. 코딩테스트 실전 가이드

### 입력 크기별 허용 복잡도 (1초 기준)

| N 범위 | 허용 복잡도 |
|--------|-------------|
| N ≤ 10 | O(N!) |
| N ≤ 25 | O(2^N) |
| N ≤ 100 | O(N³) |
| N ≤ 1,000 | O(N²) |
| N ≤ 100,000 | O(N log N) |
| N ≤ 1,000,000 | O(N) |
| N > 10,000,000 | O(log N) 또는 O(1) |

### 복잡도 개선 전략

| 현재 | 목표 | 방법 |
|------|------|------|
| O(N²) | O(N log N) | 정렬 활용 |
| O(N²) | O(N) | HashMap/HashSet 사용 |
| O(N) | O(log N) | 이진 탐색 적용 |
| O(N) | O(1) | 수학 공식 적용 |

---

## 핵심 요약

1. **시간복잡도**: 연산 횟수의 증가율 → 알고리즘의 속도
2. **공간복잡도**: 메모리 사용량의 증가율 → 알고리즘의 메모리 효율
3. **트레이드오프**: 시간과 공간은 상충 관계, 상황에 맞게 선택
4. **실전 팁**: 입력 크기 N을 보고 허용 가능한 복잡도를 먼저 파악한 후 알고리즘 설계
