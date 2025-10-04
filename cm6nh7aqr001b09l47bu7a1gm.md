---
title: "[JAVA] HashSet"
datePublished: Sat Feb 08 2025 15:00:00 GMT+0000 (Coordinated Universal Time)
cuid: cm6nh7aqr001b09l47bu7a1gm
slug: java-hashset
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1738491905624/9799daba-638c-4a06-94e3-76595159b6f9.png
tags: hashset

---

> 목표 : **HashSet의 내부 동작 방식과 중복 제거 메커니즘, HashSet이 효율적인 중복 체크를 할 수 있는 이유 확인하기.**
> 
> 사진 출처 : 김영한의 JAVA 중급 과정

# 1️⃣HashSet ?

Set은 중복을 허용하지 않는다.

Hash는 (인덱스값=값) 인덱스만 찾으면 값을 찾을 수 있기 때문에 O(1)성능을 가진다. 하지만 9999숫자 데이터가 들어오면 9999개의 인덱스가 필요하다.메모리 낭비가 심하게 발생한다. 이를 해결하기 위해 HashIndex가 등장한다. 나머지 연산을 활용하여 인덱스값=나머지값 으로 설정한다. 이때 CAPACITY=10으로 설정하고 99,9가 들어오면 **<mark>해시 충돌</mark>**이 일어나는데 배열 안에 배열을 만들면 해결된다. 조회할때도 인덱스 안 배열만 비교하면 된다.(평균 O(1) 최악 O(n))

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1738392800545/d9be72b3-5521-4557-baf2-ef425c47595b.png align="center")

---

# 2️⃣ 중복 제거/체크 메커니즘

해시 코드는 해시 함수를 통해서 데이터를 대표하는 값을 나타낸다. (예 : A→65 ) 해시 인덱스는 해시 코드를 이용해서 데이터의 저장 위치를 결정한다. 따라서, 우리가 만든 객체 또한 해시 코드로 나타내어 해시 인덱스를 구할 수 있다.

```java
public class Object{
    public int hashCode();
}
```

이 메서드는 객체의 참조값을 기반으로 해시 코드를 생성하기 때문에 인스턴스가 다르면 해시 코드도 다르다. 이를 재정의해서 사용하는데 equals()도 같이 재정의 해줘야한다. 해시 인덱스가 같아도 실제 저장된 데이터는 다를 수 있기 때문에 equals()로 확인하는 것이다.

```java
public static void Main(String[] args){
    MyHashSet set=new MyHashSet(10);
    Member m1 = new Member("A");
    Member m2 = new Member("A");
    set.add(m1);
    set.add(m2);
}
```

1. hashCode, equals 둘 다 재정의 안할 경우 : 객체 참조값을 기반(기본)으로 해시 코드를 생성하기 때문에 해시 코드가 실행마다 값이 달라진다.
    
2. hashCode 재정의, equals 재정의 안할 경우 : hashCode를 재정의 했기 때문에 같은 해시 코드를 사용한다. 따라서 해시 인덱스가 같다. equals는 재정의 하지 않았기 때문에 Object의 equals를 사용한다. 이때는 인스턴스의 참조값을 비교한다.따라서 “A” 비교에 실패하여 같은 인덱스에 데이터가 중복으로 저장된다.
    
3. hashCode, equals 모두 재정의 할 경우 : hashCode를 재정의 했기 때문에 같은 해시 코드를 사용한다. 따라서 해시 인덱스가 같다. equals 또한 재정의 했기 때문에 “A” 중복을 확인한다. 중복 데이터가 저장되지 않는다.
    

## ⚡ 정리

**HashSet**  
해시 인덱스를 활용한 중복 데이터가 없는 Set

**중복 제거/체크 메커니즘**  
hashCode(), equals()함수를 재정의하여 중복 제거, 체크