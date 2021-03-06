## 스트림 병렬화는 주의해서 적용하라.
---

#### 병렬 스트림을 하면 안되는 스트림 조건
 - limit()을 사용하는 경우
 - 데이터 소스가 Stream.iterate()를 사용한 경우

#### 병렬 효과가 좋은 스트림 데이터 구조
 - ArrayList, HashMap, HashSet, ConcurrentHashMap, 배열

> 위 자료구조들의 공통점은 *참조 지역성이 뛰어나다는 것이다. 참조들이 가르키는 실제 객체가 메모리에서 서로 떨어져 있을 수 있는데, 그러면 참조 지역성이 나쁘다는 것이다. 참조 지역성이 낮으면 스레드는 데이터가 주 메모리에서 캐시 메모리로 전송되어 오기를 기다리며 대부분 시간을 멍하니 보내게 된다.

*참조지역성
> 이웃한 원소의 참조들이 메모리에 연속해서 저장되어 있다는 뜻

*배열
> 기본 타입 배열에서는 (참조가아닌) 데이터 자체가 메모리에 연속해서 저장되기 때문에 참조 지역성이 가장 뛰어나다고 말할 수 있다.

#### 병렬 스트림과 종단 연산
종단 연산에서 수행하는 작업량이 파이프라인 전체 작업에서 상당 비중을 차지하면서 순차적인 연산이라면 파이프라인 병렬 수행의 효과는 제한될수밖에 없다.

*종단 연산으로 가장 적합한 작업은 축소(reduction)이다.
> 축소 메서드 : reduce , min, max, count, sum, anyMatch, allMatch, nonMatch

*종단 연산으로 적합하지 않은 작업은 가변 축소(mutable reduction)이다.
> 가변 축소 메서드 : collect(컬렉션들을 합치는 부담이 크다)


#### 병렬 효과가 좋은 데이터 사이즈
 - int 범위, long 범위 
 
#### 주의할 점
 - 커스텀 스레드풀을 사용하지 않을시, 병렬 스트림 파이프라인도 기본 포크-조인풀에서 수행되므로 다른 시스템의 부분에 성능에 악영향을 줄수 있다.
 - reduce 연산에 사용되는 함수는 결합법칙을 만족하고, 간섭받지 않고, 상태를 갖지 않아야 한다.
 - 랜덤 스트림 연산시 [SplittableRandom](https://docs.oracle.com/javase/8/docs/api/java/util/SplittableRandom.html)을 사용하여 성능을 높일수 있다.
  
---
### 요약
`
계산도 올바로 수행하고 성능도 빨라질 거라는 확신 없이는 스트림 파이프라인 병렬화는 시도하지 말자.
오히려 오동작하게 하거나 성능을 떨어 트릴수도 있다. 병렬화가 낫다고 믿더라도 운영 환경과 유사한 조건에서
성능지표를 테스트해보아야 한다. 계산도 정확하고 성능도 좋아지는 게 확실해 졌을 경우, 운영코드에 반영하라.
`