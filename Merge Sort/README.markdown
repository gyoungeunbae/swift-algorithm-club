# 병합정렬

목표: 오름차순 또는 내림차순으로 배열을 정렬하는 것.

 1945년  John von Neumann이 개발했다. 병합정렬은 최고, 최악 그리고, 평균**O(n log n)**의 시간 복잡도를 가진 효율적인 알고리즘이다.

병합정렬 알고리즘은 큰문제를 작은문제로 분할하여 해결하는 분할 정복 알고리즘을 사용한다. 즉, 병합정렬 알고리즘은 **분할**한 후 **병합**하는 것이다. 

n개의 숫자의 배열을 순서대로 정렬 한다고 가정하자. 
병합정렬 알고리즘은 아래와 같이 동작한다:

- 정렬되지 않은 더미에 숫자들을 입력한다.
- 더미를 2개로 나눈다.  ( **2개의 정렬되지 않은 숫자 더미**가 있다.)
- 더이상 나눌 수 없을 때까지 더미를 계속 나눈다. 마지막에는 각 더미에 숫자 한개만 남게 된다.
- 순서대로 짝을 지어 더미들을 결합하여 **병합**한다.  병합할 때 마다, 정렬 순서에 따라 숫자들을 넣는다. 이 방법은 각각의 더미들이 이미 정렬 되어 있기 때문에 쉽게 이용할 수 있는 방법이다.

## 예제

### 분할

 *n* 개의 숫자를 가진 배열 `[2, 1, 5, 4, 9]`이 있다고 하자. 이 배열은 정렬되지 않은 배열이다. 더미를 더 이상 나눌 수  없을 때까지 더미를  계속 나눈다.

먼저, 배열을 2개의 배열로 나눈다: `[2, 1]`  그리고  `[5, 4, 9]`.

좌측 배열 `[2, 1]`을  `[2] `와`[1]`로 나눈다.

우측 배열  `[5, 4, 9]` 을 `[5]` 그리고  `[4, 9]`로 나눈다. 배열 `[5]`는 더 이상 나눌 수 없는 배열이다. 하지만,  배열`[4, 9]` 는  `[4]`그리고 `[9]`로 한번 더 나눌 수 있다.

분할프로세스가 끝나면 아래와 같은 더미들이 남는다: `[2]` `[1]` `[5]` `[4]` `[9]`. 각 더미는 하나의 원소로 구성된다.

### 병합

이제 앞서서 분할한 배열을 갖고서 각각의 더미들을 결합하여 병합할 수 있다. 여기서 기억해야 할 것은 병합정렬은 큰 문제 하나가 아니라 여러개의 작은 문제들을 해결해 나가는 전략이라는 것이다. 병합을 반복할 때 마다 중요한 것은 하나의 더미를 다른 더미에 병합한다라는 개념이다.



여기  `[2]` `[1]` `[5]` `[4]` `[9]` 가 있다. 1단계로  `[2]`과 `[1]`을 비교 병합 `[5]` 와`[4]` 을 비교 병합한다. 그 결과  `[1, 2]` 과 `[4, 5]`그리고 `[9]`가 된다.   `[9]` 는 하나만 남았기 때문에 , 1단계에서는 어떤 더미와도 병합할 수 없다.

다음 단계로 `[1, 2]`과 `[4, 5]` 을 병합한다. 그 결과 `[1, 2, 4, 5]` , 또다시`[9]` 만 남았다.



 이제 2개의 더미만 남았고(`[1, 2, 4, 5]`그리고`[9]` ),  합병을 하면  결과 배열로`[1, 2, 4, 5, 9]`라는 정렬된 배열이 된다.

## Top-down 구현

Swift로 구현한 병합정렬:

```swift
func mergeSort(_ array: [Int]) -> [Int] {
  guard array.count > 1 else { return array }    // 1

  let middleIndex = array.count / 2              // 2

  let leftArray = mergeSort(Array(array[0..<middleIndex]))             // 3

  let rightArray = mergeSort(Array(array[middleIndex..<array.count]))  // 4

  return merge(leftPile: leftArray, rightPile: rightArray)             // 5
}
```

코드가 어떻게 동작하는지 단계별로 설명하겠다 :

1. 만약 배열이 비어있거나, 원소가 하나 밖에 들어 있지 않다면, 더 이상 더 작은 단위로 나눌 수 없기 때문에 반드시 배열을 return 해야 한다.
2. middle index를 찾는다.
3. 이전 단계에서의 middle index를 사용함으로서 좌측 배열(middle index 보다 작은 index를 가진)을 재귀적으로 나눌 수 있다.
4. 마찬가지로, 우측 배열(middle index 보다 같거나 큰 index를 가진)을 재귀적으로 나눌 수 있다. 
5. 마지막으로, 모든 값들을 병합하고,  모든 값이 정렬되게 한다.

병합 알고리즘:

```swift
func merge(leftPile: [Int], rightPile: [Int]) -> [Int] {
  // 1
  var leftIndex = 0
  var rightIndex = 0

  // 2 
  var orderedPile = [Int]()

  // 3
  while leftIndex < leftPile.count && rightIndex < rightPile.count {
    if leftPile[leftIndex] < rightPile[rightIndex] {
      orderedPile.append(leftPile[leftIndex])
      leftIndex += 1
    } else if leftPile[leftIndex] > rightPile[rightIndex] {
      orderedPile.append(rightPile[rightIndex])
      rightIndex += 1
    } else {
      orderedPile.append(leftPile[leftIndex])
      leftIndex += 1
      orderedPile.append(rightPile[rightIndex])
      rightIndex += 1
    }
  }

  // 4
  while leftIndex < leftPile.count {
    orderedPile.append(leftPile[leftIndex])
    leftIndex += 1
  }

  while rightIndex < rightPile.count {
    orderedPile.append(rightPile[rightIndex])
    rightIndex += 1
  }

  return orderedPile
}
```

이 방법이 복잡해보일 수 있다. 하지만 아주 단순하다:

1. 합병 할 때, 두 배열의 진행상황을 확인하기 위해서 2개의 index가 필요하다.
2. 이 변수가 합병된 배열을 나타낸다. 지금은 배열이 비어있지만, 다른 배열에 있는 원소들을 순서대로 넣어서 완성 될 것이다.
3. 이 while-loop는 좌측과 우측에 있는 배열의 원소들을 비교해 줄 것이다. 그리고 결과값이 순서대로 정렬되면`orderedPile` 배열에 넣어 줄 것이다. 
4. 만약 앞에 있는 while-loop에서 빠져 나왔다면, 이것은 `leftPile` 또는 `rightPile`의 원소들이 완전히 `orderedPile`에 병합되었다는 의미이다. 여기서 중요한 것은, 더이상 비교를 하지 않아도 된다는 것이다.  배열에 남아있는 원소들을 더 이상 넣을 수 없을 때까지 넣으면 된다.  

`merge()`의 동작 예제를 통해서,  우리는 아래와 같은 더미가 될 것이라는 것을 알 수 있다:  `leftPile = [1, 7, 8]` 와 `rightPile = [3, 6, 9]`. 각각의 더미들은 이미 정렬이 되어 있다. 이 더미들은 다음단계에서 더 큰 정렬된 더미로 합병될 것이다:

```
leftPile       rightPile       orderedPile
[ 1, 7, 8 ]    [ 3, 6, 9 ]     [ ]
  l              r
```

left index는 여기서 `l`로 표현 한다. 현재 포인트는 left pile에서 첫번째 원소에 위치해 있다. `r`로 표현하는 right index에서는  `3`을 가르키고 있다. 그 결과,  `orderedPile`에 추가 한 첫번째 원소는  `1` 이다.  그리고 left index의 `l`은 다음 원소를 가리키게 된다. 

```
leftPile       rightPile       orderedPile
[ 1, 7, 8 ]    [ 3, 6, 9 ]     [ 1 ]
  -->l           r
```



이제 `l`는  `7` 을 가르키고 있다. 하지만  `r` 은 아직 `3`에 위치해 있다. 값을 더할때 가장 작은 값의 원소를 orderedPile에 추가해야 하기 때문에  `3`을 추가해야 한다. 이제 각각의 배열은 아래와 같은 상태가 되었다:

```
leftPile       rightPile       orderedPile
[ 1, 7, 8 ]    [ 3, 6, 9 ]     [ 1, 3 ]
     l           -->r
```

이 과정을 반복한다. 각각의 단계에서, 가장 작은 원소를 `leftPile` 또는 `rightPile` 에서 뽑고 `orderedPile`에 원소를 추가 한다:

```
leftPile       rightPile       orderedPile
[ 1, 7, 8 ]    [ 3, 6, 9 ]     [ 1, 3, 6 ]
     l              -->r

leftPile       rightPile       orderedPile
[ 1, 7, 8 ]    [ 3, 6, 9 ]     [ 1, 3, 6, 7 ]
     -->l              r

leftPile       rightPile       orderedPile
[ 1, 7, 8 ]    [ 3, 6, 9 ]     [ 1, 3, 6, 7, 8 ]
        -->l           r
```

이제, left pilew에는 더이상 남은 원소가 없다. right pile에 남아있는 원소들을 추가하고 완성한다. 그리고 병합된 더미는  `[ 1, 3, 6, 7, 8, 9 ]`이 된다.

여기서 중요한 것은, 이 알고리즘이 아주 단순하다는 것이다: 이 알고리즘은 2개의 더미에서 좌측에서 우측으로 움직인다. 각 단계에서 가장 작은 원소를 뽑는다. 이 동작은 각 더미가 미리 정렬되어 있게 하기 위해 동작한다.

## Bottom-up 구현

 지금까지 살펴 본 병합 정렬 알고리즘 구현은  먼저 배열을 더 작은 더미로 나누고서 병합하기 때문에  하향식(top-down) 접근 이라고 불린다. 실제로 배열이 정렬될 때(linkedlist와 반대로) 쪼개는 단계를 생략하고 바로 각 배열의 원소들을 병합 할 수 있는데, 이것을 바로 상향식(bottom-up)접근 이라고 한다. 

여기 Swift로 구현 된 상향식(bottom-up)접근:

```swift
func mergeSortBottomUp<T>(_ a: [T], _ isOrderedBefore: (T, T) -> Bool) -> [T] {
  let n = a.count

  var z = [a, a]      // 1
  var d = 0
    
  var width = 1
  while width < n {   // 2
  
    var i = 0
    while i < n {     // 3

      var j = i
      var l = i
      var r = i + width
      
      let lmax = min(l + width, n)
      let rmax = min(r + width, n)
      
      while l < lmax && r < rmax {                // 4
        if isOrderedBefore(z[d][l], z[d][r]) {
          z[1 - d][j] = z[d][l]
          l += 1
        } else {
          z[1 - d][j] = z[d][r]
          r += 1
        }
        j += 1
      }
      while l < lmax {
        z[1 - d][j] = z[d][l]
        j += 1
        l += 1
      }
      while r < rmax {
        z[1 - d][j] = z[d][r]
        j += 1
        r += 1
      }

      i += width*2
    }
    
    width *= 2
    d = 1 - d      // 5
  }
  return z[d]
}
```



botton-up 버전이 top-down 버전 보다 어려워 보이지만 `merge()`에 있는  `while`루프 3개와 똑같는 것을 확인 할 수 있다.

주목할점들:

1.  병합정렬알고리즘은 임시 작업 배열(*temporary working array*) 없이는 좌측과 우측 더미들을 병합 할 수 없고, 동시에 좌측과 우측 더미의 원소들을 덮어 쓸 수도 없기 때문에 임시로 작업할 수 있는 배열(*temporary working array*) 이 필요하다. 이는 병합을 할때마다 새로운 배열을 할당하는 것은 낭비이기 때문에, `d`의 값을 0 또는 1로 값을 넣음으로서  2개의 임시 작업 배열을 전환해 가며 이용한다. `z[d]`는 읽을 때 사용되고,  `z[1 - d]` 는 쓸 때(*writing*) 사용된다. 이것을 *double-buffering* 이라고 한다.  

   ​

2. 개념적으로 상향식( bottom-up) 버전은 하향식(top-down) 버전과 똑같이 동작한다. 첫번째 단계에서, 각각 원소 1개로 이루어진 더미들을 병합한다. 그리고서 각각 2개의 원소로 구성된 더미들을 병합한다. 그리고 각각 4개의 원소로 구성된 더미들을 병합한다. 이런식으로 여러번 반복한다. 더미의 크기는  `width`가 나타낸다. 처음에  `width`는 `1`로 초기화되지만 루프반복이 끝나는 시점에는 2배가 되어있다. 그래서 바깥 루프는 병합된 더미의 크기가 된다. 그리고 각 단계를 거쳐 병합 시 사용하는 부분배열의 크기는 점점 더 커진다.    

3. 안쪽 루프는 더미를 통해 단계별로 동작하고 각 쌍의 더미들을 하나로 병합한다. 그 결과는 `z[1 - d]`배열을 이용해 기록 한다. 

   ​

4. 이 부분은 하향식 버전과 같은 로직이다. 다른 점은 여기서 double-buffering을 사용했다는 것이다. 그래서 값이 `z[d]`에 의해 읽혀지고, `z[1 - d]`에 의해 기록된다. 그리고  `<`이 아닌 원소들을 비교하는 기능의 `isOrderedBefore`함수를 사용한다.(그래서 병합정렬 알고리즘이 제네릭 이다.) 그리고 어떤 종류의 객체라도 정렬에 사용할 수 있다.

   ​

5. 여기서, 배열 `z[d]`의  `width` 크기의 더비는 배열 `z[1 - d]`에서의  `width * 2` 사이즈의 더비로 병합된다.

   ​

이 기능은 제네릭이다. 그래서 원소들을 비교 할수 있는 제대로된`isOrderedBefore`closure만 있다면 이것을 이용하여 원하는 어떤 타입이든 정렬이 가능하다.

예제 :

```
let array = [2, 1, 5, 4, 9]
mergeSortBottomUp(array, <)   // [1, 2, 4, 5, 9]
```

## 실행

병합정렬의 속도는 정렬할 필요가 있는 배열의 크기에 의해 결정된다. 배열의 사이즈가 클수록 해야될 일이 더 많아진다. 

제일 처음 배열이 이미 정렬되어 있던 안되어 있던 병합정렬알고리즘의 속도에는 영향을 주지않는다. 왜냐하면 똑같은 양을 나눌 것이고 배열에 제일 처음순서에 상관없이 비교할 것이기 때문이다.

그 결과, 최고, 최악, 평균 시간복잡도는 항상 **O(n log n)**이 된다.

병합정령 알고리증의 단점은 정렬되고 있는 배열과 같은 사이즈의 일시적으로 작업할 배열이 필요한 것이다. 이것은 [퀵정렬](https://github.com/gyoungeunbae/swift-algorithm-club-kor/tree/master/QuickSort) 예제와 달리 **제자리**정렬이 아니다.



병합정렬알고리즘에 대한 대부분의구현은 **안정적인**정렬로 만들어진다. 이것은 동일한 정렬키들을 가진 배열요소들이 각각 같은 순서에서 정렬이 될때까지 기다리게 된다는 의미이다.

이것은 단순한 값(숫자 나 문자열과 같은)에서는 중요한 문제가 아니지만 더 복잡한 객체들을 정렬할 때 문제가 될 수도 있다.

## 추가

[병합정렬 on Wikipedia](https://ko.wikipedia.org/wiki/%ED%95%A9%EB%B3%91_%EC%A0%95%EB%A0%AC)

*Written by Kelvin Lau. Additions by Matthijs Hollemans.*

*Translated from English into Korean by GyoungEun Bae.*