# 퀵정렬

목표 : 한 배열을 오름차순 (또는 내림차순) 으로 정렬.

퀵정렬은 역사적으로 가장 유명한 알고리즘 중 하나이다. 퀵정렬은 '재귀' 개념이 불투명하던 시기인 1959년, Tony Hoare에 의해 발명됐다.

아래는 이해하기 쉽도록 구현한 Swift 코드이다 :

```swift
func quicksort<T: Comparable>(_ a: [T]) -> [T] {
  guard a.count > 1 else { return a }

  let pivot = a[a.count/2]
  let less = a.filter { $0 < pivot }
  let equal = a.filter { $0 == pivot }
  let greater = a.filter { $0 > pivot }

  return quicksort(less) + equal + quicksort(greater)
}
```

아래 코드를 main 메소드에서 테스트한다 :

```swift
let list = [ 10, 0, 3, 9, 2, 14, 8, 27, 1, 5, 8, -1, 26 ]
quicksort(list)
```

다음은 작동 방식이다. 배열이 주어지면, `quicksort()`는 "pivot" 변수에 따라 배열은 세 부분으로 나눈다. 여기서, 배열의 가운데 요소를 pivot으로 사용한다. ( 피벗을 선택하는 다른 방법은 나중에 살펴보겠다. )

pivot보다 작은 모든 원소들은 새로운 배열 `less`로 이동시킨다. pivot과 같은 모든 원소들은 `equal`배열에 넣는다. 눈치 챘을지 모르지만, pivot 보다 큰 원소들은 세번째 배열 `greater`에 넣는다.  참고로 Java는 연산자 오버로딩을 지원하지 않는다. 객체를 정렬하는 방법은 나중에 살펴본다. 

일단 세가지 배열을 구하고 나면, quicksort()는 재귀적으로 `less`배열과 `greater`배열을 정렬한다. 그리고나서, 정렬된 두 하위 배열(sub-array)을 `equal`배열과 붙여 최종결과를 얻어낸다.

## 예제

예제를 살펴보겠다. 배열은 다음과 같이 초기화 하였다 :

```swift
[ 10, 0, 3, 9, 2, 14, 8, 27, 1, 5, 8, -1, 26 ]
```

먼저, `8`을 pivot을 선택한다. 배열의 중간에 있는 값이 `8`이기 때문. 다음에는 배열을 less, equal, greater 로 나누겠다 :

```swift
less:    [ 0, 3, 2, 1, 5, -1 ]
equal:   [ 8, 8 ]
greater: [ 10, 9, 14, 27, 26 ]
```

`less`와 `greater`이 동일한 수의 원소를 갖고 있으므로, 잘 나누어졌다. 또한 배열을 반으로 가르는 좋은 pivot을 골랐다.

`less`와 `greater`는 아직 정렬되지 않았다. 그래서 `quicksort()`를 다시 호출하여 두 하위배열(subarray)을 정렬하겠다. 이번에 호출된 quicksort()도 앞에서 한 것과 똑같은 일을 한다 : pivot을 고르고 하위 배열을 다시 세 개의 더 작은 배열로 나눈다.

`less` 배열로 이 과정을 살펴보겠다:

```swift
[ 0, 3, 2, 1, 5, -1 ]
```

여기서 pivot은 중간에 있는 값 중 `1`로 선택하겠다( `2`로 해도 상관없다. )  다시 pivot 주변에서 3개의 하위배열을 만들어낸다 :

```swift
less:    [ 0, -1 ]
equal:   [ 1 ]
greater: [ 3, 2, 5 ]
```

아직 끝나지 않았다. 재귀적으로 `less`와 `greater` 배열에서 `quicksort()`가 다시 호출된다.  `less`를 다시 살펴보겠다 :

```swift
[ 0, -1 ]
```

pivot으로 `-1`을 선택하겠다. 이제 하위 배열(subarray)은 아래와 같다 :

```swift
less:    [ ]
equal:   [ -1 ]
greater: [ 0 ]
```

`-1`보다 작은 값이 없기 때문에 `less`배열은 비었다. 다른 배열들은 하나씩 값이 들어있다. 이것은 재귀가 이번 단계에서 끝났고, 이전의 `greater`배열을 정렬하기 위해 다시 되돌아간다는 것을 의미한다.

`greater`배열이다 :

```swift
[ 3, 2, 5 ]
```

이번에도 전과 같이 작동한다 : 중간값인 `2` 를 피벗으로 선택하고 `subarray`배열을  채워 넣는다.

```swift
less:    [ ]
equal:   [ 2 ]
greater: [ 3, 5 ]
```

여기서 하나만 짚고 넘어가겠다. 사실, 피벗으로 `3`을 선택하는 것이 더 낫다. 더 빨리 끝날테니. 그러나 지금은 `greater`배열에서 재귀를 다시 돌아야 한다. 확실히 정렬되도록 해야하기 때문이다. 여기서 좋은 피벗을 선택하는 것이 왜 중요한지 알 수 있다. 실제로 "나쁜" 피벗을 너무 많이 선택하면, 퀵정렬은 매우 느려진다. 

`greater` subarray를 나누면 아래와 같은 결과를 얻을 수 있다 :

```swift
less:    [ 3 ]
equal:   [ 5 ]
greater: [ ]
```

그러면 지금 레벨에서 재귀는 끝난다. 왜냐면 배열을 더이상 쪼갤 수 없기 때문이다.

이러한 처리과정을 모든 subarray들이 정렬될때까지 반복하면 된다. 그림에서 :

![Example](Images/Example.png)

왼쪽에서 오른쪽 방향으로 색칠된 박스만 보면, 정렬이 완료된 배열을 읽어낼 수 있을 것이다.

```swift
[ -1, 0, 1, 2, 3, 5, 8, 8, 9, 10, 14, 26, 27 ]
```
여기에선 `8`이 좋은 초기 피벗임을 알 수 있는데, 그 이유는 정렬이 완료된 배열의 중간값 또한 `8`이기 때문이다.

지금까지의 설명들이 퀵정렬의 기본원리를 이해하는데 많은 도움이 됐기를 바란다. 그러나, 지금 버전의 퀵정렬이 그렇게 빠르지는 않다. 왜냐하면, 같은 배열에서 `filter()`를 세번이나 사용하고 있기 때문. 더 효율적으로 배열을 쪼개는 방법이 있다.

## 분할하기

피벗 주변의 배열을 쪼개는 것을 분할(partitioning)이라고 부른다. 분할 방법은 여러가지이지만, 몇 가지만 소개하겠다.

배열을 아래처럼 가정하겠다 :

```swift
[ 10, 0, 3, 9, 2, 14, 8, 27, 1, 5, 8, -1, 26 ]
```

그리고 중간값 `8`을 피벗으로 고르겠다. 이 상태에서 분할을 하고 나면 배열은 아래처럼 될 것이다 :

```swift
[ 0, 3, 2, 1, 5, -1, 8, 8, 10, 9, 14, 27, 26 ]
  -----------------        -----------------
  all elements < 8         all elements > 8
```

여기서 알 수 있는 중요한 사실은 분할 후에 피벗 원소가 이미 정렬이 완료된 위치에 놓인다는 것이다. 나머지 원소들은 아직 정렬되지 않은 채, 단순히 피벗 원소를 기준으로 나뉘었다. 퀵정렬은 모든 값들이 제 자리를 찾을 때까지 여러번 반복해서 배열을 분할한다. 

분할된 공간안에서, 원소들 간의 상대적인 순서가 달라질 수도 있다. 예를들어, `8`을 피벗으로 선택해 분할한 후 다음과 같은 배열은 아래와 같을 수 있다는 것이다 :

```swift
[ 3, 0, 5, 2, -1, 1, 8, 8, 14, 26, 10, 27, 9 ]
```
분할(partioning)이 보장하는 건, 피벗 왼쪽의 원소들은 모두 피벗보다 작다는 것과 피벗 오른쪽의 원소들은 모두 피벗보다 크다는 것 뿐이다. 왜냐하면 분할하기가 원소들의 원래 순서를 바꿀수 있고, 퀵정렬 자체가 [merge sort](../Merge%20Sort/)처럼 "안정적인" 정렬방식을 사용하고 있지 않기 때문이다. 대부분의 경우 이것이 큰 문제가 되진 않는다.

# Lomuto의 분할 알고리즘

퀵정렬의 첫번째 예제는, 분할이 Swift의 filter() 함수를 세번 호출해서 완료됨을 보였다. 이 방법은 그리 효율적이진 않다. 그래서 지금부터는 조금 더 똑똑한 분할알고리즘에 대해 살펴보겠다. 원래 배열을 조금 변경한 예제로 살펴보자.

Lomuto의 분할 알고리즘을 Swift로 구현한 예제 :

```swift
func partitionLomuto<T: Comparable>(_ a: inout [T], low: Int, high: Int) -> Int {
  let pivot = a[high]

  var i = low
  for j in low..<high {
    if a[j] <= pivot {
      (a[i], a[j]) = (a[j], a[i])
      i += 1
    }
  }

  (a[i], a[high]) = (a[high], a[i])
  return i
}
```

playground에서 테스트 해보려면 :

```swift
var list = [ 10, 0, 3, 9, 2, 14, 26, 27, 1, 5, 8, -1, 8 ]
let p = partitionLomuto(&list, low: 0, high: list.count - 1)
list  // show the results
```

하나만 짚고 넘어가겠다. 여기서 `list` 같은 경우는 `var`로 선언해주었는데, `partitionLomuto()`가 배열의 내용물을 `input`파라미터로 전달받아 곧바로 변경할 수 있도록 하기 위함이다. 이 방법이 새로 배열객체를 할당하는 것보다 더 효율적인 방법이다.

다음으로, `low`와 `high`파라미터가 필요하다. 왜냐하면, 이것을 퀵정렬 내부에서 적절히 사용하면, 전체 배열을 (재)분할할 필요가 없기 때문이다. 이 파라미터들로 (재)분할의 범위를 제한하고, 점점 더 좁아지게 한다.

이전에 우리는 피벗을 배열의 중간값으로 선택했지만, Lomuto 알고리즘에서는 항상 마지막 원소 즉, `a[high]`를 피벗으로 사용한다는 것을 먼저 알아두자. 왜냐면 우리가 `8`근처를 피벗으로 선택하는 것에 익숙해져 있기 때문이다. 제가 이번 예제에서는 일부러 `8`의 위치를 배열 마지막 원소인 `26`과 바꾸었습니다.  `8`을 배열의 마지막에 위치시켜서 이번에도 `8`을 피벗으로 사용하기 위함이다.

분할이 끝나고 나면, 배열은 아마 이런 모습일 것이다 :

```swift
[ 0, 3, 2, 1, 5, 8, -1, 8, 9, 10, 14, 26, 27 ]
                        *
```

변수 `p`에는 `partitionLomuto()`가 반환한 값인 7이 담겨있을 것이다. 7은 피벗원소의 배열 인덱스이다. ( *로 표시된 것 )

왼쪽 파티션은 0부터 `p-1`까지인 `[ 0, 3, 2, 1, 5, 8, -1 ]` 입니다. 오른쪽 파티션은 `p+1`부터 끝까지고, `[ 9, 10, 14, 26, 27 ]. ( 오른쪽 파티션이 이미 정렬되어있는건 우연이다. )

여기서 흥미로운 부분 있다.. 배열에서 `8`이 한번 이상 보이는 것입니다. 이러한 `8`들 중 결국 하나는 깔끔하게 중간으로 가진 않겠지만, 왼쪽 파티션 어딘가에 위치하게 된다. 이러한 점 때문에 Lumoto 알고리즘은, 원소들이 많이 중복되면 퀵정렬 속도가 느려지는 약점이 있다.

그러면 Lumoto 알고리즘은 어떻게 작동하는 것일까? `for`루프에서 마법같은 일이 벌어진다. 이 루프는 배열을 4부분으로 나눈다.

1. pivot 이하의 모든 원소를 담고 있는`a[low...i]` 
2. pivot 초과의 모든 원소를 담고있는 `a[i+1...j-1]`
3. 우리가 아직 보지 못한 값들인 `a[j...high-1]`
4. 피벗 값인 `a[high]`

배열의 나누어짐을 ASCII art로 표현해보았다 :

```swift
[ values <= pivot | values > pivot | not looked at yet | pivot ]
  low           i   i+1        j-1   j          high-1   high
```

이 루프는 `low`에서 `high-1`까지 각 원소를 순차적으로 살핀다. 만약에 현재 보고 있는 값이 피벗 이하면, swap을 사용해서 1번 영역으로 이동시킨다.

> **Note** :  `(x, y) = (y, x)`문법은 스위프트에서 `x`와 `y`의 값을 서로 교환하는 편리한 방법 중 하나이다. 꼭 이 방법만 있는건 아니고, `swap(&x, &y)`를 사용할 때도 있다.

루프가 종료되고 나서, 피벗은 여전히 배열의 마지막에 위치하고 있다. 그러니 피벗보다 큰 원소인 첫번째 원소와 피벗을 교환한다. 이제 피벗은 <= 과 > 영역 사이에 자리를 잡게 됐고, 배열도 적절히 분할됐다.

이 과정을 예제를 통해 자세히 보겠다. 아래의 배열에서부터 시작한다 :

```swift
[| 10, 0, 3, 9, 2, 14, 26, 27, 1, 5, 8, -1 | 8 ]
   low                                       high
   i
   j
```

초기화 시, "아직 보지 못함" 영역의 인덱스를 0부터 11까지로 늘려놓았다.  피벗은 인덱스 12에 위치하고 있다. "값들 <= 피벗" 과 "값들 > 피벗" 영역엔 아무것도 없다. 왜냐하면 아직 어떠한 값도 확인하지 않았기 때문이다.

첫번째 값은 `10`이다. 피벗보다 작은가? 아니다. 그러니 다음 원소로 넘어간다.

```swift
[| 10 | 0, 3, 9, 2, 14, 26, 27, 1, 5, 8, -1 | 8 ]
   low                                        high
   i
       j
```

이제 "아직 보지 못함" 영역은 index가 1부터 11까지로 바뀌었다. "값들 > 피벗" 영역은 `10`을 담고 있으며, "값들 <= 피벗"에는 아직 아무것도 없다.

두번째 값인 `0`을 보자. 피벗보다 작은가? 그렇다. 따라서 `10`과 `0`을 교환하고, `i`를 앞으로 한칸 움직인다.

```swift
[ 0 | 10 | 3, 9, 2, 14, 26, 27, 1, 5, 8, -1 | 8 ]
  low                                         high
      i
           j
```

이제 "아직 보지 못함" 영역은 index가 2부터 11까지로 바뀌었다. "값들 > 피벗" 영역은 여전히 `10`을 담고 있으며, "값들 <= 피벗"에는 `0`이 담겨있다.

세번째 값인 `3`을 보자. `3`은 피벗보다 작으니, `10`과 자리를 바꾼다.

```swift
[ 0, 3 | 10 | 9, 2, 14, 26, 27, 1, 5, 8, -1 | 8 ]
  low                                         high
         i
             j
```

"값들 <= 피벗" 영역은 이제 `[0, 3]` 이다. 한번 더 해보겠다.. `9`는 피벗보다 크니, 그냥 넘어간다 :

```swift
[ 0, 3 | 10, 9 | 2, 14, 26, 27, 1, 5, 8, -1 | 8 ]
  low                                         high
         i
                 j
```

이제 "값들 > 피벗" 영역은 `[10, 9]`를 담고 있다. 이런 식으로 계속해서 해나가면, 아래와 같은 결과를 얻게 된다 :

```swift
[ 0, 3, 2, 1, 5, 8, -1 | 27, 9, 10, 14, 26 | 8 ]
  low                                        high
                         i                   j
```

마지막으로 할 일은, `a[i]`와 `a[high]`를 교환하여 피벗을 제 위치에 가져다 놓는것이다 :

```swift
[ 0, 3, 2, 1, 5, 8, -1 | 8 | 9, 10, 14, 26, 27 ]
  low                                       high
                         i                  j
```

그리고 피벗의 인덱스인 `i`를 리턴하는 것이다.

> **Note** : 만약 아직 알고리즘이 전체적으로 어떻게 동작하는지 이해가 잘 안된다면, 이걸 playground에서 동작시켜보고, 루프가 4가지 영역을 어떻게 만들어내는지 눈으로 확인하는 것이 도움될 것이다.

퀵정렬을 만들기 위해 이 분할 알고리즘을 사용해보자 :

```swift
func quicksortLomuto<T: Comparable>(_ a: inout [T], low: Int, high: Int) {
  if low < high {
    let p = partitionLomuto(&a, low: low, high: high)
    quicksortLomuto(&a, low: low, high: p - 1)
    quicksortLomuto(&a, low: p + 1, high: high)
  }
}
```

정말 간단하다. 제일 처음에 `partitionLomuto()`를 호출해서 피벗을 기준으로 배열을 재정렬한다.(피벗은 항상 배열의 가장 마지막 원소) 그런 다음 `quicksortLomuto()`를 재귀적으로 호출해서 왼쪽과 오른쪽 파티션을 정렬한다.

실습 :

```swift
var list = [ 10, 0, 3, 9, 2, 14, 26, 27, 1, 5, 8, -1, 8 ]
quicksortLomuto(&list, low: 0, high: list.count - 1)
```

Lomuto의 것이 유일한 분할방법은 아니지만, 가장 이해하기 쉬울 것이다. Lomuto 보다 효율적인 Hoare의 알고리즘은, swap 연산을 더 적게 사용한다.

## Hoare의 분할 알고리즘

이 알고리즘은 퀵정렬의 발명가인 Hoare의 분할 방법이다.

코드는 이렇다 :

```Swift
func partitionHoare<T: Comparable>(_ a: inout [T], low: Int, high: Int) -> Int {
  let pivot = a[low]
  var i = low - 1
  var j = high + 1

  while true {
    repeat { j -= 1 } while a[j] > pivot
    repeat { i += 1 } while a[i] < pivot

    if i < j {
      swap(&a[i], &a[j])
    } else {
      return j
    }
  }
}
```

playground에서 아래 코드를 테스트 해보자 :

```swift
var list = [ 8, 0, 3, 9, 2, 14, 10, 27, 1, 5, 8, -1, 26 ]
let p = partitionHoare(&list, low: 0, high: list.count - 1)
list  // show the results
```

Hoare의 알고리즘에 관해 한가지 짚고 넘어가자면, 피벗은 항상 배열의 첫번째 원소 즉, `a[low]`이길 기대한다는 것이다. 그러면, 우리도 이것을 피벗으로 사용하겠다.

결과 :

```swift
[ -1, 0, 3, 8, 2, 5, 1, 27, 10, 14, 9, 8, 26 ]
```
이번에는 피벗이 전혀 중간에 있지 않다는 것에 유의해야 한다. Lomuto의 알고리즘과 달리, 반환 값이 반드시 새로운 배열에 있는 피벗의 인덱스일 필요는 없다.

대신에, 배열은 `[low...p]` 와 `[p+1...high]`영역으로 나뉜다. 여기서, 반환 값 `p`는 6이다, 그래서 두 파티션은 `[ -1, 0, 3, 8, 2, 5, 1 ]` 와 `[ 27, 10, 14, 9, 8, 26 ]`이 된다.

피벗은 두 파티션 중 하나의 어딘가에 위치하게 된다. 그러나 알고리즘 자체는 둘 중 어디에 피벗이 있는 지 얘기해주지 않는다. 만약 피벗값이 한번 이상 발생한다면, 몇몇 값들이 왼쪽 파티션에 나타날 것이고 나머지들이 오른쪽파티션에 나타날 것이다.

이러한 차이점 때문에 Hoare의 퀵정렬 구현도 약간 달라진다 :

```swift
func quicksortHoare<T: Comparable>(_ a: inout [T], low: Int, high: Int) {
  if low < high {
    let p = partitionHoare(&a, low: low, high: high)
    quicksortHoare(&a, low: low, high: p)
    quicksortHoare(&a, low: p + 1, high: high)
  }
}
```

독자가 Hoare의 분할 알고리즘의 동작방식을 정확히 이해하도록 연습문제로 남겨놓겠다. :-)

# 좋은 피벗을 선택하기

Lomuto의 분할 알고리즘은 항상 배열의 마지막 원소를 피벗으로 선택했다. Hoare의 알고리즘은 첫번째 원소를 사용한다. 그러나 이런 방법들은 좋은 피벗을 선택하는 방법과는 거리가 멀다.

나쁜 피벗을 선택하였을 때 발생하는 일을 살펴보겠다. 아래의 배열을 생각해보자 :

```swift
[ 7, 6, 5, 4, 3, 2, 1 ]
```

그리고 우리는 Lomuto의 알고리즘을 사용한다. 피벗은 마지막 원소인 `1`이다. 피벗을 고르는 일(pivoting)을 마치고 나면, 우리는 다음과 같은 배열을 얻게 된다 :

```swift
피벗보다 작음: [ ]
피벗과 같음  : [ 1 ]
피벗보다 큼  : [ 7, 6, 5, 4, 3, 2 ]
```

이제 재귀적으로 "피벗보다 큰" subarray를 분할한다 :

```swift
피벗보다 작음: [ ]
피벗과 같음  : [ 2 ]
피벗보다 큼  : [ 7, 6, 5, 4, 3 ]
```

그리고 다시하면 :

```swift
피벗보다 작음: [ ]
피벗과 같음  : [ 3 ]
피벗보다 큼  : [ 7, 6, 5, 4 ]
```

그리고 나머지...

이건 좋지 않다. 왜냐하면 이것이 퀵정렬의 성능을 대폭 낮추어 삽입 정렬보다도 더 느리기 때문이다. 퀵정렬이 더 효율적이기 위해선, 두 배열을 반으로 쪼개는 것이 필요하다.

이 예제에서 최적의 피벗은 `4`이므로, 이것을 택했다면 아래처럼 얻게 된다 :

```swift
피벗보다 작음: [ 3, 2, 1 ]
피벗과 같음  : [ 4 ]
피벗보다 큼  : [ 7, 6, 5 ]
```

You might think this means we should always choose the middle element rather than the first or the last, but imagine what happens in the following situation:

```swift
[ 7, 6, 5, 1, 4, 3, 2 ]
```

Now the middle element is `1` and that gives the same lousy results as in the previous example.

Ideally, the pivot is the *median* element of the array that you're partitioning, i.e. the element that sits in the middle of the sorted array. Of course, you won't know what the median is until after you've sorted the array, so this is a bit of a chicken-and-egg problem. However, there are some tricks to choose good, if not ideal, pivots.

One trick is "median-of-three", where you find the median of the first, middle, and last element in this subarray. In theory that often gives a good approximation of the true median.

Another common solution is to choose the pivot randomly. Sometimes this may result in choosing a suboptimal pivot but on average this gives very good results.

Here is how you can do quicksort with a randomly chosen pivot:

```swift
func quicksortRandom<T: Comparable>(_ a: inout [T], low: Int, high: Int) {
  if low < high {
    let pivotIndex = random(min: low, max: high)         // 1

    (a[pivotIndex], a[high]) = (a[high], a[pivotIndex])  // 2

    let p = partitionLomuto(&a, low: low, high: high)
    quicksortRandom(&a, low: low, high: p - 1)
    quicksortRandom(&a, low: p + 1, high: high)
  }
}
```

There are two important differences with before:

1. The `random(min:max:)` function returns an integer in the range `min...max`, inclusive. This is our pivot index.

2. Because the Lomuto scheme expects `a[high]` to be the pivot entry, we swap `a[pivotIndex]` with `a[high]` to put the pivot element at the end before calling `partitionLomuto()`.

It may seem strange to use random numbers in something like a sorting function, but it is necessary to make quicksort behave efficiently under all circumstances. With bad pivots, the performance of quicksort can be quite horrible, **O(n^2)**. But if you choose good pivots on average, for example by using a random number generator, the expected running time becomes **O(n log n)**, which is as good as sorting algorithms get.

## Dutch national flag partitioning

But there are more improvements to make! In the first example of quicksort I showed you, we ended up with an array that was partitioned like this:

	[ values < pivot | values equal to pivot | values > pivot ]

But as you've seen with the Lomuto partitioning scheme, if the pivot occurs more than once the duplicates end up in the left half. And with Hoare's scheme the pivot can be all over the place. The solution to this is "Dutch national flag" partitioning, named after the fact that the [Dutch flag](https://en.wikipedia.org/wiki/Flag_of_the_Netherlands) has three bands just like we want to have three partitions.

The code for this scheme is:

```swift
func partitionDutchFlag<T: Comparable>(_ a: inout [T], low: Int, high: Int, pivotIndex: Int) -> (Int, Int) {
  let pivot = a[pivotIndex]

  var smaller = low
  var equal = low
  var larger = high

  while equal <= larger {
    if a[equal] < pivot {
      swap(&a, smaller, equal)
      smaller += 1
      equal += 1
    } else if a[equal] == pivot {
      equal += 1
    } else {
      swap(&a, equal, larger)
      larger -= 1
    }
  }
  return (smaller, larger)
}
```

This works very similarly to the Lomuto scheme, except that the loop partitions the array into four (possibly empty) regions:

- `[low ... smaller-1]` contains all values < pivot
- `[smaller ... equal-1]` contains all values == pivot
- `[equal ... larger]` contains all values > pivot
- `[larger ... high]` are values we haven't looked at yet

Note that this doesn't assume the pivot is in `a[high]`. Instead, you have to pass in the index of the element you wish to use as a pivot.

An example of how to use it:

```swift
var list = [ 10, 0, 3, 9, 2, 14, 8, 27, 1, 5, 8, -1, 26 ]
partitionDutchFlag(&list, low: 0, high: list.count - 1, pivotIndex: 10)
list  // show the results
```

Just for fun, we're giving it the index of the other `8` this time. The result is:

	[ -1, 0, 3, 2, 5, 1, 8, 8, 27, 14, 9, 26, 10 ]

Notice how the two `8`s are in the middle now. The return value from `partitionDutchFlag()` is a tuple, `(6, 7)`. This is the range that contains the pivot value(s).

Here is how you would use it in quicksort:

```swift
func quicksortDutchFlag<T: Comparable>(_ a: inout [T], low: Int, high: Int) {
  if low < high {
    let pivotIndex = random(min: low, max: high)
    let (p, q) = partitionDutchFlag(&a, low: low, high: high, pivotIndex: pivotIndex)
    quicksortDutchFlag(&a, low: low, high: p - 1)
    quicksortDutchFlag(&a, low: q + 1, high: high)
  }
}
```

Using Dutch flag partitioning makes for a more efficient quicksort if the array contains many duplicate elements. (And I'm not just saying that because I'm Dutch!)

> **Note:** The above implementation of `partitionDutchFlag()` uses a custom `swap()` routine for swapping the contents of two array elements. Unlike Swift's own `swap()`, this doesn't give an error when the two indices refer to the same array element. See [Quicksort.swift](Quicksort.swift) for the code.

## See also

[Quicksort on Wikipedia](https://en.wikipedia.org/wiki/Quicksort)

*Written for Swift Algorithm Club by Matthijs Hollemans*
