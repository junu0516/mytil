# Swift로 순열과 조합 구현해보기

> 코테에서 제일 반갑지 않은 순열과 조합...
>
> 재귀함수 쓰는 것이 달갑지 않아서 그런가, 구현이 까다롭게 느껴진다.
>
> 이참에 한 번 정리해놓고 다른 문제에서 필요할 때마다 바로 떠올려 쓸 수 있도록 해야 겠다.
>
> 파이썬은 아얘 관련 패키지가 제공된다는데 이래서 파이썬 파이썬 하나보다.

## 순열

```swift
func permutation<T>(_ elements: [T], _ k: Int) -> [[T]] {
  var result = [[T]]()
  var visited = [Bool](repeating: false, count: elements.count)

  func permute(_ now: [T]) {
    if now.count == k {
      result.append(now)
      return
    }

    for i in 0..<elements.count {
      if visited[i] == true { continue }
      visited[i] = true
      permute(now + [elements[i]])
      visited[i] = false
    }
  }
  permute([])
  return result
}
```

- 파라미터 __`elements`__ 는 뽑을 대상들의 집합 배열, __`k`__ 는 __`nPr`__ 에서 __`r`__ 에 해당
- 배열 각 요소의 방문(혹은 참조) 여부를 판단할 __`visited`__ 배열 필요
- 내부 함수 __`permute(now:)`__ 에서 __`elements`__ 의 각 요소를 방문하게 됨
  - 맨 처음에는 __`now`__ 에 빈 배열을 넣고 시작
  - 배열의 처음부터 끝까지 하나씩 순회하면서 __`visited`__ 상의 해당 인덱스상의 값을 true로 변경
  - 그 상태에서 방문한 정수값을 __`now`__ 에 넣고 이를 그대로 파라미터로 넘겨 __`permute(now:)`__ 를 재귀 호출
  - 결과적으로 __`now`__ 에 들어간 요소들이 nPr을 통해 뽑힌 요소들이 됨
  - 마지막으로 재귀호출은 __`now`__ 의 길이가 뽑고자 하는 개수 __`k`__ 와 일치할 때 종료해주면 됨

​    

## 조합

> 순열과의 차이는 똑같이 n개중 r개를 뽑되, 순서를 고려하지 않는다는 점이다.

```swift
func combination<T>(_ elements: [T], _ k: Int) -> [[T]] {
  var result = [[T]]()

  func combinate(_ index: Int, _ now: [T]) {
    if now.count == k {
      result.append(now)
      return
    }
    for i in index..<elements.count {
      combinate(i + 1, now + [elements[i]])
    }
  }
  combinate(0, [])
  return result
}
```

- 파라미터 __`elements`__ 는 뽑을 대상들의 집합 배열, __`k`__ 는 __`nCr`__ 에서 __`r`__ 에 해당

- 조합은 순서를 따지기 때문에 순열과 달리 __`visited`__ 배열을 굳이 둘 필요가 없음
- __`combinate(index:, now:)`__ 를 재귀적으로 호출하되, index값을 0부터 시작해서 1씩 늘려나가는 식으로 반복함
- 동일하게 __`now`__ 에 __`k`__ 개의 정수를 삽입하되, 겹치지 않도록 0부터 1~끝, 1부터 2~끝 이런 식으로 반복문 돌면서 삽입하는 것