---
title: "[ Java ] Thread"
datePublished: Sat Apr 19 2025 15:00:00 GMT+0000 (Coordinated Universal Time)
cuid: cm7r8a14r000007jg7hrzczrg
slug: java-thread
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1740884573780/f5978180-62d5-49ec-b6f7-3b1b6f54dfdc.png
tags: java, thread

---

# 1️⃣ 용어

시분할 기법 : CPU가 빠르게 두 프로그램을 교차하면서 수행 → 두 프로그램이 동시에 실행되는 것처럼 보임

멀티태스킹 : 1개 CPU코어가 동시에 여러작업 수행 능력

스케줄링 : 어떤 프로그램이 얼마만큼 실행될지 운영체제가 결정

멀티프로세싱 : 2개이상의 CPU코어(프로세서) 사용하여 여러 작업 동시에 처리

프로세스 : 실행 중인 프로그램 , 서로 별도의 메모리 공간을 갖고 있어 독립적, 1개이상 스레드 보유

스레드 : 프로세스 메모리 공간 공유, 생성 관리 단순, 가벼움, 코드를 실행하면서 내려가는 것, 실행되는 작업의 단위

* 멀티스레드 : 하나의 프로세스에 여러 개의 스레드 존재
    

<div data-node-type="callout">
<div data-node-type="callout-emoji">💡</div>
<div data-node-type="callout-text">프로그램이 실행된다 = 프로그램을 메모리에 불러오면서 프로세스 만듦→ 프로세스 안 코드가 한 줄씩 실행</div>
</div>

```java
public static void main(String[] args){
        Integer[] arr = {3, 2, 1};
        Arrays.sort(arr,new AscComparator());
        Arrays.sort(arr,new DescComparator());
    }
```

main부터 시작해서 하나씩 실행

---

# 2️⃣ 스레드와 스케줄링

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1740890345166/0266817d-9b0a-414e-be9d-944a4bb34d01.png align="center")

스레드1을 CPU코어에서 작업하다가 일정 시간이 지나면 수행을 멈추고 다시 스케줄링 큐에 집어넣는다. 그 후 스레드2를 작업하고 해당 과정을 반복한다.

스레드1을 실행하다가 멈추면 그동안 진행했던 과정들을 저장해놔야 나중에 다시 실행할때 계속 이어서 작업할 수 있다. 이러한 과정을 컨텍스트 스위칭이라 한다. 이때 비용이 발생하기 때문에 멀티스레드가 항상 효율적인 것은 아니다.

---

# 3️⃣ 스레드

## 👉🏻 생성1 : Thread상속

```java
 public static void main(String[] args) {//main스레드 생성
        HelloThread helloThread = new HelloThread();
        helloThread.start(); // thread 실행 원할때 run이 아닌 start사용!
        //결과 : Thread-0 : run
    }
    private static class HelloThread extends Thread{
        @Override
        public void run() {
            System.out.println(Thread.currentThread().getName()+": run");//현재 스레드 이름 확인
        }
    }
```

main스레드가 start로 Thread-0에게 일하라고 지시, Thread-0가 run실행

start가 아닌 run을 하면 main스레드가 run하기 때문에 별도의 스레드에서 정의한 run 실행 x

스레드는 동시에 실행돼서 순서 보장x

## 데몬 스레드

보조 작업 수행, 모든 사용자 스레드가 종료되면 자동 종료, main(사용자 스레드)가 종료되면 helloThread가 작업을 완료하지 않아도 프로그램 자동 종료됨.

```java
public static void main(String[] args) throws IOException {
        HelloThread helloThread = new HelloThread();
        helloThread.setDaemon(true); // 데몬스레드 설정
        helloThread.start();
    }
    private static class HelloThread extends Thread{
        @Override
        public void run() {
            System.out.println(Thread.currentThread().getName()+": run");
        }
    }
```

## 👉🏻 생성2 : Runnable 구현

```java
public static void main(String[] args){
        HelloThread runnable= new HelloThread();
        Thread thread = new Thread(runnable);
        thread.start();
    }
    private static class HelloThread implements Runnable{
        @Override
        public void run() {
            System.out.println(Thread.currentThread().getName()+": run");
        }
    }
```

Runnable방식을 주로 사용한다.

1. Thread 상속 방법을 사용할 경우 : 단일 상속만 허용하므로 다른 클래스를 상속받고 있으면 해당 클래스 상속불가
    
2. Runnable 구현 : 스레드 실행부분만 따로 분리하여 코드 가독성 높음, 여러 스레드가 Runnable 공유 가능, Runnable을 생성하고 이를 Thread에 전달하는 과정이 추가되어 코드 복잡해질 수 있음
    

## 👉🏻 생명주기

New : 스레드 생성 상태 (start호출 안된 상태)

Runnable : 스레드 실행 중 or 실행 준비 된 상태

Suspended States

* Blocked : 동기화 락 기다리는 상태
    
* Waiting : 무기한으로 다른 스레드 작업 기다리는 상태
    
* Time Waiting : 일정 시간 동안 다른 스레드 작업 기다리는 상태
    

Terminated : 종료 상태

## 👉🏻 Sleep

```java
public static void main(String[] args) throws IOException {
        HelloThread runnable= new HelloThread();
        Thread thread = new Thread(runnable);
        thread.start();
    }
    private static class HelloThread implements Runnable{
        @Override
        public void run() {
            try {
                Thread.sleep(3000);
            } catch (InterruptedException e) {
                throw new RuntimeException(e);
            }
        }
}
```

3초동안 대기하는 스레드를 만들었다.

## 👉🏻 Join

해당 스레드가 종료될때 까지 대기, 사용하지 않으면 main스레드가 계속 실행되기 때문에 result값들은 0으로 표시되고 결과적으로 sumAll도 0이 나온다.

```java
public static void main(String[] args) throws InterruptedException {
        SumTask task1 = new SumTask(1, 50);
        SumTask task2 = new SumTask(51, 100);
        Thread thread1 = new Thread(task1, "Thread-1");
        Thread thread2 = new Thread(task2, "Thread-2");

        thread1.start();
        thread2.start();
        
        thread1.join();//메인스레드가 thread1종료될때까지 대기
        thread2.join();//메인스레드가 thread2종료될때까지 대기
        //메인 스레드 대기 완료
        System.out.println("task1 = " + task1.result); //thread1결과
        System.out.println("task2 = " + task2.result); //thread2결과
        int sumAll= task1.result+ task2.result;

    }
    private static class SumTask implements Runnable{
        int start;
        int end;
        int result=0;

        public SumTask(int start, int end) {
            this.start = start;
            this.end = end;
        }

        @Override
        public void run() {
           sleep(2000);
           int sum=0;
            for (int i = start; i <= end; i++) {
                sum +=i;
            }
            result=sum;
        }
    }

    static void sleep(long millis) {
        try {
            Thread.sleep(millis);
        } catch (InterruptedException e) {
            throw new RuntimeException(e);
        }
    }
```

단점 : 대상 스레드가 완료될때 까지 무기한 대기해야한다. 특정시간 동안만 대기하고 싶을 경우 join(millis)를 사용하면 된다.

## 👉🏻 동기화

공유 자원 : 여러 스레드가 접근하는 자원

ex ) 스레드1이 출금하고 있는 와중에(1000-200) 스레드2가 출금하려고 시도

임계 영역 : 여러 스레드가 동시에 접근하면 위험한 중요한 코드 부분, 하나의 스레드만 접근할 수 있도록 보호

ex ) 출금

```java
public class BankAccount{
        private int balance;

        public BankAccount(int initalBalance) {
            this.balance = initalBalance;
        }

        public synchronized boolean withdraw(int amount) {
            if (balance < amount) {
                return false;
            }
            sleep(1000);
            balance=balance-amount;
            return true;
        }
        public synchronized int getBalance() {
            return balance;
        }
    }
```

```java
public static void main(String[] args) throws IOException, InterruptedException {
        BankAccount bankAccount = new BankAccount(1000);
        Thread thread1 = new Thread(new WithdrawTask(bankAccount, 800));
        Thread thread2 = new Thread(new WithdrawTask(bankAccount, 800));
        thread1.start();//200
        thread2.start();//검증 실패
        sleep(500);
        thread1.join();
        thread2.join();
        System.out.println(bankAccount.getBalance());

    }
    private static class WithdrawTask implements Runnable{
        private BankAccount account;
        private int amount;

        public WithdrawTask(BankAccount account, int amount) {
            this.account = account;
            this.amount = amount;
        }

        @Override
        public void run() {
            account.withdraw(amount);
        }
    }

    static void sleep(long millis) {
        try {
            Thread.sleep(millis);
        } catch (InterruptedException e) {
            throw new RuntimeException(e);
        }
    }
```

스레드1이 synchronized메서드에 진입하면 BankAccount의 락을 획득한다. 스레드2가 synchronized메서드에 진입하려하지만 락이 없어 대기한다. 스레드1이 검증 완료 후 출금 후 잔액을 BankAccount에 저장 후 락을 반납한다. 스레드2가 락을 획득후 검증을 시도한다. 검증 실패 후 false를 반환한다.