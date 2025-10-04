---
title: "[ Java ] Stream Api의 map vs flatMap"
datePublished: Sat Feb 01 2025 15:00:00 GMT+0000 (Coordinated Universal Time)
cuid: cm63cplcr000a09l4gtav338p
slug: java-stream-api-map-vs-flatmap
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1737274959744/7925979b-682e-47e3-8367-780b8a42de24.png
tags: map, stream-api, flatmap

---

> 목표 : **Stream API의 map과 flatMap의 차이점과 각각의 활용 사례를 예시 코드와 함께 확인해보자.**

# 1️⃣StreamApi의 Map ?

먼저 코드를 통해 확인하자.

```java
//Map
public class Main{
    public static void main(String[] args) throws IOException{
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        List<Integer> list=Stream.of(br.readline()
                                            .split(" ")).map(Integer::parseInt)
                                            .toList();
        System.out.println(list.toString());
    }
}
```

1. Stream.of를 통해 Stream을 생성해줍니다.
    
2. map을 통해 입력받은 숫자들을 Integer.parseInt를 적용해서 int로 바꿔줍니다. 즉, map을 통해 원하는 요소를 뽑아낼 수 있습니다.
    
3. 사례:단순 데이터 변환
    

---

# 2️⃣ Stream Api 의 flatMap?

```java
public class Main {

    public static void main(String[] args) throws IOException {

        List<List<Integer>> nestedList = Arrays.asList(
          Arrays.asList(1, 2, 3),
          Arrays.asList(4, 5, 6),
          Arrays.asList(7, 8, 9)
        );
        List<Integer> listWithFlatMap = nestedList.stream()
                                                  .flatMap(List::stream)
                                                  .toList();
        System.out.println(listWithFlatMap.toString());
    }
}
```

1. nestedList.stream()을 통해 스트림을 생성해준다.
    
2. flatMap(List::stream)을 통해서 내부의 리스트를 개별 스트림으로 변환해준다. → 모든 요소들을 하나로 합친 단일 스트림으로 반환한다.
    
3. 그 후 결과는 \[1,2,3,4,5,6,7,8,9\] 가 나온다.
    
4. 사례: 중첩 컬렉션 처리, Optional체이닝, 다대다 관계 처리
    

## ⚡ 정리

**차이점**  
Map : `map()`은 스트림의 각 요소를 변환하여 새로운 요소로 매핑한다.

flatMap : 각 요소를 스트림으로 변환한 후, 모든 스트림을 하나의 스트림으로 평면화한다.

(출처 : [https://observerlife.tistory.com/98](https://observerlife.tistory.com/98), [https://s7won.tistory.com/5](https://s7won.tistory.com/5))