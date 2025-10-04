---
title: "[ Java ] 순회, 정렬, 컬렉션 유틸"
datePublished: Sat Apr 12 2025 15:00:00 GMT+0000 (Coordinated Universal Time)
cuid: cm7r1lr18000208lb41ofdecr
slug: java-collecitonutil
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1740884333912/088d8559-c828-4794-9c94-9428db9f528a.png
tags: java, collection

---

# 1️⃣ Iterable, Iterator

데이터를 차례대로 접근해서 처리하는 것

다양한 자료구조가 있고 각 자료 구조마다 데이터 접근 방법 다름.

자료구조를 동일한 방법으로 순회하기 위해 Iterable, Iterator 제공.

```java
public interface Iterable<T> {
    Iterator<T> iterator();//반복자 반환
}
```

```java
public interface Iterator<E> {
    boolean hasNext(); //다음 요소 존재 확인
    E next(); // 다음 요소 반환
}
```

Iterable을 구현하면 Iterator을 반환하기 때문에 Iterable 단독 사용x

자바 컬렉션 상위에 Iterable이 있어 모든 컬렉션을 순회할 수 있도록 하였다.

각 자료구조마다 iterator을 구현하며 이를 반환한다. 따라서 각 자료구조가 Iterable 역할을 한다.

---

# 2️⃣ Comparable, Comparator

## 👉🏻 Comparator

```java
public interface Comparator<T> {
     int compare(T o1, T o2);
}
```

1. compare은 o1이 크면 -1, 같으면 0 , 작으면 1을 반환한다.
    
2. 만약 정렬기준을 설정하고 싶을때 사용한다.
    

```java
public static void main(String[] args){
        Integer[] arr = {3, 2, 1};
        Arrays.sort(arr,new AscComparator());//123
        Arrays.sort(arr,new DescComparator());//321
    }

    static class AscComparator implements Comparator<Integer> {
        @Override
        public int compare(Integer o1, Integer o2) {
            return (o1<02)?-1: ((o1 == o2) ? 0 : 1);
        }
    }
    static class DescComparator implements Comparator<Integer> {
        @Override
        public int compare(Integer o1, Integer o2) {
            return ((o1<02)?-1: ((o1 == o2) ? 0 : 1))*-1;
        }
    }
```

정렬을 사용할때 Arrays.sort를 사용하는데 코드를 살펴보면 Comparator을 파라미터로 입력받는 것을 확인할 수 있다.

```java
public static <T> void sort(T[] a, Comparator<? super T> c) {
        if (c == null) {
            sort(a);
        } else {
            if (LegacyMergeSort.userRequested)
                legacyMergeSort(a, c);
            else
                TimSort.sort(a, 0, a.length, c, null, 0, 0);
        }
    }
```

## 👉🏻 Comparable

내가 정의한 객체를 정렬하려면 어떤 객체가 더 큰지 알려줘야한다.

```java
public class MyUser implements Comparable<MyUser> {
    private String id;
    private int age;

    public MyUser(String id, int age) {
        this.id = id;
        this.age = age;
    }

    public String getId() {
        return id;
    }
    public int getAge() {
        return age;
    }

    @Override
    public int compareTo(MyUser o) {
        return this.age<o.age?-1: (this.age == o.age ? 0 : 1);
    }
}
```

정렬 기준을 나이로 정했다. Arrays.sort(array)하면 오름차순으로 정렬되어 나타난다.

Arrays.sort(array,comparator)로 정렬방식을 지정하면 객체가 가지고 있는 Comparable(기본 정렬)을 무시하고 전달받은 기준으로 정렬한다.

트리 구조는 정렬해서 보관하기 때문에 정렬 기준을 꼭 제공해야한다.

```java
TreeSet<MyUser> treeSet1= new TreeSet<>();//나이 기준 정렬
TreeSet<MyUser> treeSet2= new TreeSet<>(new IdComparator());//id기준 정렬
```

---

# 3️⃣ 컬렉션 유틸

max, min, shuffle. sort, reverse 등 다양한 기능을 제공한다.

그중, XXX.of()는 컬렉션을 편리하게 생성할 수 있지만 <mark>불변</mark>이다. List, Set, Map모두 지원한다.

```java
public static void main(String[] args){
        List<Integer> list = List.of(1, 2, 3);
        ArrayList<Integer> mutableList = new ArrayList<>(list);
        mutableList.add(4);
        List<Integer> unmodifiableList=Collections.unmodifiableList(mutableList);
    }

static class UnmodifiableList<E> extends UnmodifiableCollection<E>
                                  implements List<E> {
        @java.io.Serial
        private static final long serialVersionUID = -283967356065247728L;

        @SuppressWarnings("serial") // Conditionally serializable
        final List<? extends E> list;

        UnmodifiableList(List<? extends E> list) {
            super(list);
            this.list = list;
        }
```

불변 리스트→ 가변 리스트 : new ArrayList를 사용한다.

가변 리스트→ 불변 리스트 : Collections.unmodifiableList()가 파라미터 리스트를 UnmodifiableList로 바꿔 return한다. UnmodifiableList는 final로 List가 불변이 되도록 한다.

cf) Arrays.asList() : 고정된 크기를 가지지만 요소들은 변경 가능