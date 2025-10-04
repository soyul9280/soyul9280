---
title: "[Java] List, Set, Hash, Map"
datePublished: Sat Apr 05 2025 15:00:00 GMT+0000 (Coordinated Universal Time)
cuid: cm7pv7qqw000309lh0zkr78ge
slug: java-list-set-hash
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1740812981492/1465b459-5dc5-4be9-ac81-3a93284ef0b0.png
tags: java, collection-framework

---

> 목표 : 각 자료 구조 구현체마다 어떻게 구성되어있는지 확인하고 사용 방법에 대해 확인하기.

# 1️⃣ List

배열은

1. 길이를 동적으로 변경할 수 없다.
    
2. 데이터를 추가하거나 삭제할 때마다 데이터를 직접 밀어야한다.
    

이러한 단점을 해소하기 위해 동적으로 데이터를 추가할 수 있는 자료구조 List가 등장하였다.

List는

1. 순서가 있다.
    
2. 중복을 허용한다.
    
3. 크기를 동적으로 변경할 수 잇다.
    

## 👉🏻 ArrayList

데이터를 내부 배열에 보관한다. 인덱스를 이용해서 요소에 빠르게 접근이 가능하다. 정해진 크기 보다 더 많은 데이터를 추가하면 크기가 보통 50% 증가한다.

자바에서 성능을 최적화 하기 위해 데이터를 추가할때, 데이터를 직접 밀지 않고 메모리 고속 복사 기능을 사용한다. 예시에서는 데이터를 직접 이동시켰다.

```java
public class MyArrayList<E> {
    private static final int DEFAULT_CAPACITY = 10;//배열 크기
    //제네릭은 런타임 시점에 타입이 이레이저로 지워지기 때문에 생성자에는 타입 매개변수를 사용할 수 없다.
    private Object[] elementdata;//내부 데이터 관리 배열
    private int size = 0;

    public MyArrayList() {
        this.elementdata = new Object[DEFAULT_CAPACITY];
    }

    public MyArrayList(int initialCapacity) {
        this.elementdata = new Object[initialCapacity];
    }

    public int size() {
        return size;
    }

    public void add(E e) {
        if (size == elementdata.length) {
            grow();
        }
        elementdata[size]=e;
        size++;
    }

    public void add(int index, E e) {
        if (size == elementdata.length) {
            grow();
        }
        shiftRightFrom(index);
        elementdata[index]=e;
        size++;
    }

    private void shiftRightFrom(int index) {
        for (int i = size; i > index; i--) {
            elementdata[i] = elementdata[i - 1];
        }
    }

    private void grow() {
        int oldCapacity=elementdata.length;
        int newCapacity=oldCapacity*2;
        elementdata = Arrays.copyOf(elementdata, newCapacity);
    }

    public E get(int index) {
        return (E)elementdata[index];
    }

    public E set(int index, E element) {
        E oldVaue = get(index);
        elementdata[index] = element;
        return oldVaue;
    }

    public E remove(int index) {
        E oldValue = get(index);
        shiftLeftFrom(index);
        size--;
        elementdata[size] = null;
        return oldValue;
    }

    private void shiftLeftFrom(int index) {
        for (int i = index; i < size - 1; i++) {
            elementdata[i] = elementdata[i + 1];
        }
    }

    public int indexOf(E o) {
        for (int i = 0; i < size; i++) {
            if (o.equals(elementdata[i])) {
                return i;
            }
        }
        return -1;
    }

    @Override
    public String toString() {
        return "MyArrayList{" +
                "elementdata=" + Arrays.toString(elementdata) +
                ", size=" + size +
                '}';
    }
}
```

데이터 추가 혹은 삭제시,

1. 마지막 : O(1)
    
2. 앞 혹은 중간 : O(n)
    

인덱스 조회 O(1)

데이터 검색 : O(n)

## 🙋🏻‍♀️ i &gt;= index가 아닌 이유

i&gt;index까지 데이터를 이동시키면 elementdata = \[A, B, C, C, D, E\]이 된다. 따라서 elementdata\[index\]에 원하는 값을 넣으면 원하는 위치에 값을 넣는 과정을 완성할 수 있다.

## ArrayList 생성

```java
//1. 기본 생성자
List<String> list = new ArrayList<>();
//2. 초기 크기를 인수로 한 생성자 : 내부 배열의 크기 미리 지정해서 요소 추가 시 불필요한 계산 방지
List<String> list = new ArrayList<>(20);
//3. 컬렉션을 인수로 한 만들어지는 생성자
Collection<Integer> numbers = IntStream.range(0, 10).boxed().collect(toSet());
List<Integer> list = new ArrayList<>(numbers);
```

## ArrayList 연산(참고)

Sequenced Collection을 사용하여 연산 가능. 이컬렉션은 요소들이 명확한 순서를 가지도록 정의되어 있음.

<table><tbody><tr><td colspan="1" rowspan="1"><p>get</p></td><td colspan="1" rowspan="1"><p>getFirst(), getLast()</p></td></tr><tr><td colspan="1" rowspan="1"><p>add</p></td><td colspan="1" rowspan="1"><p>addFirst(), addLast()</p></td></tr><tr><td colspan="1" rowspan="1"><p>remove</p></td><td colspan="1" rowspan="1"><p>removeFirst(), removeLast()</p></td></tr></tbody></table>

## 👉🏻 LinkedList

데이터를 리스트로 저장하며 모든 요소는 이전과 다음 요소로 연결되어있다. List 및 Deque 인터페이스를 구현한 이중 연결 리스트 구조, null을 포함하여 모든 리스트 연산 지원, Deque연산 사용가능

효율적인 삽입혹은 deque연산의 이점(예외를 던지지 않거나 직관적인 기능)을 얻고 싶을때 사용

특징

1. 특정 인덱스로 접근하는 연산은 지정된 인덱스에서 가까운 쪽부터 끝까지 리스트를 순회한다.
    
2. 동기화 되지 않는다. 멀티스레드 환경에서 직접 사용하면 안전하지 않다. 하지만 Collections.synchronizedList메서드를 이용하여 동기화 버전을 얻을 수 있다.
    
    <div data-node-type="callout">
    <div data-node-type="callout-emoji">🐳</div>
    <div data-node-type="callout-text">List&lt;String&gt; synchronizedList = Collections.synchronizedList(new LinkedList&lt;&gt;());</div>
    </div>
    
3. Iterator을 생성한 후에 리스트가 수정되면 ConcurrentModificationException이 발생한다.
    
4. 각 요소는 노드이고 이전과 다음을 가르키는 참조로 구성되어있다.
    
5. 삽입순서를 유지한다.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1740813831145/29b9698b-538f-47aa-9a34-e2e5ea652039.png align="center")

다음과 같이 노드 인스턴스를 생성하고 안에 item, next를 넣어둔다. next를 통해 다음 노드 인스턴스를 가르킬 수 있다. 이렇게 첫번째 노드와 두번째 노드가 서로 연결(Link)된다.

```java
public class Node<E> {
    E item;
    Node<E> next;

    public Node(E item) {
        this.item = item;
    }

    @Override
    public String toString() {

        return "Node{" +
                "item=" + item +
                ", next=" + next +
                '}';
    }
}
```

```java
public class MyLinkedList<E> {
    private Node<E> first;
    private int size = 0;

    public void add(E e) {
        Node<E> newNode = new Node<>(e);
        if (first == null) {
            first = newNode;
        } else {
            Node<E> lastNode = getLastNode();
            lastNode.next = newNode;
        }
        size++;
    }

    public void add(int index, E e) {
        Node<E> newNode = new Node<>(e);
        if (index == 0) {
            newNode.next=first;
            first=newNode;
        }else {
            Node<E> prev = getNode(index - 1);
            newNode.next=prev.next;
            prev.next=newNode;
        }
        size++;
    }

    private Node<E> getLastNode() {
        Node<E> x = first;
        while (x.next != null) {
            x = x.next;
        }
        return x;
    }

    public E set(int index, E element) {
        Node<E> x = getNode(index);
        E oldValue = x.item;
        x.item = element;
        return oldValue;
    }

    public E remove(int index) {
        Node<E> removeNode = getNode(index);
        E removedItem=removeNode.item;
        if (index == 0) {
            first = removeNode.next;
        }else {
            Node<E> prev = getNode(index - 1);
            prev.next = removeNode.next;
        }
        removeNode.item = null;
        removeNode.next = null;
        size--;
        return removedItem;
    }

    public E get(int index) {
        Node<E> node = getNode(index);
        return node.item;
    }

    private Node<E> getNode(int index) {
        Node<E> x=first;
        for (int i = 0; i < index; i++) {
            x = x.next;
        }
        return x;
    }

    public int indexOf(E o) {
        int index=0;
        for (Node<E> x = first; x != null; x = x.next) {
            if (o.equals(x.item)) {
                return index;
            }
            index++;
        }
        return -1;
    }

    public int size() {
        return size;
    }

    @Override
    public String toString() {
        return "MyLinkedList{" +
                "first=" + first +
                ", size=" + size +
                '}';
    }
}
```

데이터 조회 :O(n)

데이터 삽입 혹은 삭제 : ArrayList보다 빠르다. 임의 위치에 요소를 추가할때 배열의 크기를 조정하거나 인덱스를 어벧이트할 필요가 없고 주변 참조만 변경하면 되기 때문.

1. 앞 : O(1)
    
2. 중간 혹은 마지막 : O(n)
    

자바에서는 마지막 노드를 찾는 성능을 최적화하기 위해 이중 연결리스트를 제공한다. 따라서 뒤에 추가하거나 삭제하는 경우에도 O(1)성능을 제공한다.

## LinkedList 생성

생성자를 이용하여 새로운 인스턴스를 초기화하는데 두가지 방법이 있다.

1. 기본 생성자
    

<div data-node-type="callout">
<div data-node-type="callout-emoji">💡</div>
<div data-node-type="callout-text">LinkedList&lt;T&gt; list = new LinkedList&lt;&gt;();</div>
</div>

2. 컬렉션 요소 포함하여 생성: LinkedList(Collection&lt;? extends E&gt; c)
    
    주어진 컬렉션의 반복자가 반환하는 순서대로 요소를 추가한다. LinkedList의 요소들은 컬렉션 요소 타입과 동일하다.
    

<div data-node-type="callout">
<div data-node-type="callout-emoji">💡</div>
<div data-node-type="callout-text">ArrayList&lt;Integer&gt; arrayList=<strong>new</strong> <strong>ArrayList</strong>&lt;Integer&gt;(3); LinkedList&lt;Integer&gt; linkedList=<strong>new</strong> <strong>LinkedList</strong>&lt;Integer&gt;(arrayList);</div>
</div>

## LinkedList 연산(참고)

| **<mark>기본 연산</mark>** |  |
| --- | --- |
| add | add(), addAll(), addFirst(), addLast() |
| Remove | removeFirst(),removeLast(), `removeFirstOccurence()` : 지정된 요소 있는지 boolean값 반환 |
| **<mark>Queue 연산</mark>** |  |
| pop() | 없으면 NoSuchElementException |
| poll() | 없으면 null |

## Array → LinkedList

1. Arrays.asList 사용
    
    ```java
    String[] array = { "apple", "banana", "cherry", "date" };
    List<String> list = Arrays.asList(array);  // Convert array to List
    LinkedList<String> linkedList = new LinkedList<>(list);
    ```
    
2. Collections.addAll() 사용
    
    ```java
    LinkedList<String> linkedList = new LinkedList<>();
    Collections.addAll(linkedList, array);
    ```
    
    원래 배열을 LinkedList로 바로 만들어주는 간단한 방법이다.
    

## 🙋🏻‍♀️ 제네릭 클래스를 사용하는 이유

1. 원하는 타입으로 매개변수화 할 수 있다.
    
2. 컴파일러는 호환되지 않는 타입을 사용할 수 없도록 보장한다. (타입 불일치 오류 방지)
    
3. 요소가 반환될때 형변환(cast)할 필요가 없다.
    
4. ArrayList&lt;&gt; ..=new ArrayList&lt;&gt;()가 아닌 이유는 유연성 때문이다. 특정 구현체에 종속되지 않을 수 있다. 예를 들어, ArrayList→LinkedList일때 특정 구현체에 종속될 경우 많은 코드를 수정해야한다.
    

## 메모리 사용

LinkedList : 모든 노드마다 previous필드, next필드를 정의해야하기 때문에 추가적인 메모리 사용이 일어나긴한다.

ArrayList : 오직 데이터와 인덱스만 저장하면 된다.

---

## 🙋🏻‍♀️다형성 제공

위 MyArrayList와 MyLinkedList는 구현만 다를 뿐이지 제공하는 기능은 같다. 실제로 , ArrayList 와 LinkedList에 들어가보면 List인터페이스를 구현하고 있다.

```java
public class ArrayList<E> extends AbstractList<E>
        implements List<E>, RandomAccess, Cloneable, java.io.Serializable
---------------------------------------------------------------------------
public class LinkedList<E>
    extends AbstractSequentialList<E>
    implements List<E>, Deque<E>, Cloneable, java.io.Serializable
```

```java
public interface List<E> extends SequencedCollection<E> {
    int size();
    E get(int index);
    E set(int index, E element);
    void add(int index, E element);
    E remove(int index);
    int indexOf(Object o);
    int lastIndexOf(Object o);
...
}
```

List 인터페이스에서 공통기능을 메서드로 제공하고 있다.

---

## 🙋🏻‍♀️실제 성능

이론적으로 LinkedList가 중간 삽입이 빠르지만 ArrayList가 메모리 상 연속적으로 위치하여 CPU캐시 효율이 좋고 메모리 접근 속도가 빠르다. 또한, 정해진 크기가 넘어서 배열을 다시 만들고 복사하는 과정은 50%씩 크기가 늘어나 가끔식 발생하여 전체 성능에 큰 영향을 주지 않는다.

즉, ArrayList가 더 나은 성능을 보여주는 경우가 많다.

---

# 2️⃣ Set

1. 중복 요소가 존재하지 않는다.
    
2. 순서를 보장하지 않는다.
    
3. 요소 유무를 빠르게 확인할 수 있다.
    

중복 허용하지 않고 요소의 유무만 중요할때 사용한다.

```java
public class MySet {
    private int[] elementData = new int[10];
    private int size=0;

    public boolean add(int value) {
        if (contains(value)) {
            return false;
        }
        elementData[size]=value;
        size++;
        return true;
    }

    public boolean contains(int value) {
        for (int data : elementData) {
            if (data == value) {
                return true;
            }
        }
        return false;
    }

    public int getSize(){
        return size;
    }

    @Override
    public String toString() {
        return "MySet{" +
                "elementData=" + Arrays.toString(elementData) +
                ", size=" + size +
                '}';
    }
}
```

add할때 중복데이터가 있는지 확인을 해야해서 O(n)으로 성능이 나쁘다.

데이터를 입력할때마다 성능이 느리면 사용하기 어렵다.

이를 개선하기 위해 Hash가 등장한다.

---

# 3️⃣ Hash

%[https://soyulia.hashnode.dev/java-hashset] 

Hash에 대한 설명은 위 글로 대체한다.

---

# 4️⃣ 다양한 Set

1. HashSet
    
    * 해시 자료 구조로 저장
        
    * 순서 보장 X
        
    * O(1)
        
2. LinkedHashSet
    
    * HashSet에 연결리스트를 추가→ 요소들의 순서 보장
        
    * 삽입 순서 유지해야할 경우 사용
        
    * O(1)
        
3. TreeSet
    
    * 삽입 순서를 유지하지 않고 정렬된 순서로 저장된다.(기본 : 오름차순)
        
    * 멀티스레드 환경에서 안전하지 않다.
        
    
    <div data-node-type="callout">
    <div data-node-type="callout-emoji">💡</div>
    <div data-node-type="callout-text">Collections.synchronizedSortedSet(new TreeSet&lt;&gt;()) 로 해결가능</div>
    </div>
    
    * 순서 기준은 기본적으로 Comparable인터페이스를 구현한 구현체 객체 기준으로 정렬 되며 Comparator로 변경 가능
        
    * 자동 균형 이진 탐색 트리 (레드 블랙 트리)를 내부적으로 사용
        
    * O(log n)
        

---

# 5️⃣ Map

1. 키 - 값 쌍을 저장하는 자료구조이다.
    
2. 키는 Set구조로 중복을 허용하지 않는다.
    
3. 값은 중복될 수 있다.
    
4. 순서를 보장하지 않는다.
    

HashMap, TreeMap, LinkedHashMap이 존재한다.

* HashMap : 순서보장x, 해시 자료구조를 사용하여 삽입 삭제 검색 작업은 O(1)이다.
    
* LinkedHashMap : 연결리스트를 이용해서 입력 순서를 유지할 수 있다. O(1)이다.
    
* TreeMap : 키는 Comparator의해 정렬된다. O(log n) 이다.
    

## 👉🏻 HashMap

HashMap은 다음과 같은 기능을 제공한다.

```java
public static void main(String[] args){
        Map<String, Integer> studentMap = new HashMap<>();
        studentMap.put("A", 90);//데이터를 집어 넣는다.
        studentMap.put("B", 70);
        studentMap.put("C", 80);
        studentMap.put("D", 100);
        Integer result = studentMap.get("D");//해당 키의 값을 가져온다.
        Set<String> keySet = studentMap.keySet(); //모든 키를 가져온다.
        for (String s : keySet) {
            Integer value = studentMap.get(s);
        }
        Set<Map.Entry<String, Integer>> entries = studentMap.entrySet();//키,값 구조를 가져온다.
        for (Map.Entry<String, Integer> entry : entries) {
            String key = entry.getKey();
            Integer value = entry.getValue();
        }
        Collection<Integer> values = studentMap.values();
        for (Integer value : values) {
            System.out.println("value = " + value);
        }
        studentMap.putIfAbsent("F",100);
    }
```

1. 키는 중복 허용하지 않기때문에 Set구조로 가져온다.
    
2. Entry로 키-값쌍으로 이루어진 객체를 가져온다.
    
3. 값은 순서도 보장 안돼고 중복도 허용하지 않기 때문에 List, Set으로 반환할 수 없다. 따라서 상위 인터페이스인 Collection으로 반환한다.
    
4. 같은 키에 다른 값을 저장하면 기존 값을 대체한다.
    
5. putIfAbsent로 키가 없는 경우에만 데이터를 저장한다.
    

Map의 Key는 hashCode, equals를 반드시 구현해야한다. 이유는 위 HashSet글을 참고하자.

---

# 1️⃣ Stack, Queue, Deque

## Stack

일단 결론적으로 Stack클래스는 사용하지 않는다. Stack내부에서 Vector클래스를 사용하는데 지금은 사용되지 않고 하위 호환을 위해 존재한다. 대신 Deque를 사용하는 것이 좋다.

후입선출 구조(LIFO)이다.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1741182585581/a11c023d-1794-4a40-ad4a-b86401f90aac.png align="center")

## Queue

선입선출 구조(FIFO)이다.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1741182673432/6d01cdbe-3d03-471c-82d0-d353653585c8.png align="center")

offer : 큐에 값 넣기

poll : 큐에서 값 꺼내기

## Dequeue

ArrayDeque가 LinkedList보다 더 빠르다. ArrayList vs LinkedList차이와 비슷하게 배열과 동적노드링크 사용차이 때문이다. ArrayDeque는 원형 큐 자료구조를 사용하는데 덕분에 앞 뒤 입력 모두 O(1)성능을 제공한다. LinkedList가 삽입삭제 . 시더 효율적일 . 수있지만 현대 메모리 접근 패턴, CPU캐시 최적화등을 고려할때 ArrayDeque가 실제 사용 환경에서 나은 성능을 보여준다. 스택과 큐 역할을 모두 수행한다.

출처 : 인프런 김영한님 강의