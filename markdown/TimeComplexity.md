# 시간 복잡도(Time Complexity)

알고리즘이 입력 크기 \( N \)에 따라 얼마나 많은 시간이 소요되는지를 나타내는 척도입니다.

---

### **1. \( O(1) \) - 상수 시간 복잡도**

- **설명**: 입력 크기와 상관없이 항상 일정한 시간이 소요됩니다.
- **예제**:
    - **배열에서 특정 인덱스 접근**
      ```python
      def get_element(arr, index):
          return arr[index]
      ```
    - **스택(Stack)의 push/pop 연산**
      ```python
      stack.append(element)  # push
      stack.pop()            # pop
      ```

### **2. \( O(\log N) \) - 로그 시간 복잡도**

- **설명**: 입력 크기가 커질수록 시간 증가율이 로그 함수에 비례합니다.
- **예제**:
    - **이진 탐색(Binary Search)**
      ```python
      def binary_search(arr, target):
          left, right = 0, len(arr) - 1
          while left <= right:
              mid = (left + right) // 2
              if arr[mid] == target:
                  return mid
              elif arr[mid] < target:
                  left = mid + 1
              else:
                  right = mid - 1
          return -1
      ```
    - **균형 이진 트리에서의 삽입/삭제**

### **3. \( O(N) \) - 선형 시간 복잡도**

- **설명**: 입력 크기에 비례하여 시간이 증가합니다.
- **예제**:
    - **배열의 모든 요소 합산**
      ```python
      def sum_elements(arr):
          total = 0
          for num in arr:
              total += num
          return total
      ```
    - **리스트에서 특정 요소 찾기**
      ```python
      def find_element(arr, target):
          for index, element in enumerate(arr):
              if element == target:
                  return index
          return -1
      ```

### **4. \( O(N \log N) \) - 선형 로그 시간 복잡도**

- **설명**: 시간 복잡도가 \( N \)과 \( \log N \)의 곱에 비례합니다.
- **예제**:
    - **병합 정렬(Merge Sort)**
      ```python
      def merge_sort(arr):
          if len(arr) <= 1:
              return arr
          mid = len(arr) // 2
          left = merge_sort(arr[:mid])
          right = merge_sort(arr[mid:])
          return merge(left, right)
      
      def merge(left, right):
          result = []
          i = j = 0
          while i < len(left) and j < len(right):
              if left[i] < right[j]:
                  result.append(left[i])
                  i += 1
              else:
                  result.append(right[j])
                  j += 1
          result.extend(left[i:])
          result.extend(right[j:])
          return result
      ```
    - **힙 정렬(Heap Sort)**

### **5. \( O(N^2) \) - 이차 시간 복잡도**

- **설명**: 입력 크기의 제곱에 비례하여 시간이 증가합니다.
- **예제**:
    - **버블 정렬(Bubble Sort)**
      ```python
      def bubble_sort(arr):
          n = len(arr)
          for i in range(n):
              for j in range(0, n - i - 1):
                  if arr[j] > arr[j + 1]:
                      arr[j], arr[j + 1] = arr[j + 1], arr[j]
      ```
    - **두 배열에서 모든 쌍의 합 계산**
      ```python
      def pair_sums(arr1, arr2):
          sums = []
          for num1 in arr1:
              for num2 in arr2:
                  sums.append(num1 + num2)
          return sums
      ```

### **6. \( O(2^N) \) - 지수 시간 복잡도**

- **설명**: 입력 크기가 증가할 때마다 실행 시간이 지수적으로 증가합니다.
- **예제**:
    - **피보나치 수열의 재귀적 구현**
      ```python
      def fibonacci(n):
          if n <= 1:
              return n
          else:
              return fibonacci(n - 1) + fibonacci(n - 2)
      ```
    - **부분집합 생성**
      ```python
      def generate_subsets(arr):
          if not arr:
              return [[]]
          subsets = generate_subsets(arr[1:])
          return subsets + [[arr[0]] + subset for subset in subsets]
      ```

### **7. \( O(N!) \) - 팩토리얼 시간 복잡도**

- **설명**: 입력 크기의 팩토리얼에 비례하여 시간이 증가합니다.
- **예제**:
    - **순열 생성**
      ```python
      def permute(arr):
          if len(arr) == 0:
              return [[]]
          permutations = []
          for i in range(len(arr)):
              rest = arr[:i] + arr[i+1:]
              for p in permute(rest):
                  permutations.append([arr[i]] + p)
          return permutations
      ```

---

### **시간 복잡도 비교**

- **\( O(1) \) vs \( O(\log N) \) vs \( O(N) \) vs \( O(N \log N) \)**
    - \( O(1) \): 가장 빠름. 입력 크기에 영향 없음.
    - \( O(\log N) \): 입력 크기가 매우 커도 비교적 빠름.
    - \( O(N) \): 입력 크기에 비례하여 시간 증가.
    - \( O(N \log N) \): 정렬 알고리즘의 최선 시간 복잡도.

### **실제 적용 시 고려사항**

- 입력 데이터의 크기와 특성에 따라 적절한 알고리즘을 선택해야 합니다.
- 시간 복잡도가 낮을수록 항상 좋은 것은 아니며, 구현 복잡도와 상수 시간(constant factors)도 고려해야 합니다.
- 공간 복잡도도 중요한 요소입니다. 메모리 사용량이 제한된 환경에서는 시간 복잡도보다 공간 복잡도를 우선시할 수 있습니다.

---

이러한 시간 복잡도 개념을 이해하면 알고리즘의 효율성을 평가하고 적합한 알고리즘을 선택하는 데 큰 도움이 됩니다.
