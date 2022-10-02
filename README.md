# NetworkProgramming

## 4주차
  - Blocking(Two cases)

      - blocking on I/O
    
        데이터를 받아들여야 하는데 그 속도가 느려서 CPU 컨트롤을 포기, 다른 준비된 스레드가 사용할 수도 있도록

      - blocking on lock

        락이 걸린 오브젝트나 Block을 만났을때 정지

        Lock은 token이다.

        동시에 T1 -> T2 락 기다리고, T2 -> T1 락 기다리면
        **DeadLock**이 발생 가능하다.

  - Yield

  
    스레드가 CPU 컨트롤을 포기한다.

    같은 우선순위의 스레드가 CPU의 제어를 받을 수 있도록 함. 양보의 느낌

    하지만 가지고 있던 Lock은 풀리지 않고 유지된다.

  - Sleep

      Yield 과의 차이점은 다른 스레드가 준비가 되어있건 말건간에 중지한다.(Yield는 다른 스레드가 준비되어 있지않으면 포기가 되지 않음)

      Yield와 공통점은 Lock은 풀리지 않는다.( 그 부분은 다른 스레드가 사용하지 못한다.)


      다른 스레드 interrupt를 통해깨울 수 있고, VM이 다른 일을 하느라 바빠서 항상 보장되는 것은 아니다.

  - Join

    계속해서, 또는 주어진 시간동안 어떤 스레드를 기다리는 함수
    <img src="https://1.bp.blogspot.com/-OFxQ16S1GNs/X7POQ7dOd8I/AAAAAAAAkdM/m_4o4lB09tIK9DRybAvl7HThv6-lXxTLgCLcBGAsYHQ/w400-h278/Thread%2BJoin%2Bmethod%2Bexample.png">

  - Wait

    똑같이 기다리지만 Join과 차이점은 Join은 스레드가 끝날때까지 기다리는 것이고, wait은 object나 resource가 특정 상태가 될 때까지 기다리는 것이다.(**스레드에 대해서가 아님**)

  - Notify

    Lock을 풀어줌

    예를 들어 m이라는 object에서 m.wait()을 하면 m이 가지고 있는 Lock을 해제함.

    오브젝트위에 wait 중인 스레드 중에서 랜덤으로 하나를 골라서 깨운다.
    <img src="https://t1.daumcdn.net/cfile/tistory/160FE1444F69AF4D0E">

  - Producer&Consumer 문제

    ```
    package networkprogramming2;

    class Producer extends Thread {
      private Buffer blank;

      public Producer(Buffer blank) {
        this.blank = blank;
      }

      public void run() {
        for (int i = 0; i < 10; i++) {
          blank.put(i);

          System.out.println("Producer: Produced" + i);
          try { // 생산 한번 할때마다 출력하고 sleep
            sleep((int) (Math.random() * 100));
          } catch (InterruptedException e) {

          }
        }
      }
    }

    class Consumer extends Thread {
      private Buffer blank;

      public Consumer(Buffer blank) {
        this.blank = blank;
      }

      public void run() {
        int value = 0;

        for (int i = 0; i < 10; i++) {
          value = blank.get(); // 버퍼에있는 값 소비

          System.out.println("Consumer: Consumed" + value);
        }
      }
    }

    class Buffer {
      private int contents;
      private boolean available = false;

      public synchronized int get() {
        while (available == false) {
          try {
            wait(); // 생산될때까지 대기
          } catch (InterruptedException e) {
          }
        }
        System.out.println("소비자: " + contents);
        notify(); // 스레드 깨우기
        available = false;
        return contents;
      }

      public synchronized void put(int value) {
        while (available == true) {
          try {
            wait();
          } catch (InterruptedException e) {
          }
        }
        contents = value;
        System.out.println("생산자: " + contents);
        notify(); // 데이터 생산후 대기 스레드 깨움
        available = true;
      }
    }

    public class ProducerConsumer {

      public static void main(String[] args) {
        Buffer b = new Buffer(); // 공유버퍼
        Producer p1 = new Producer(b);
        Consumer c1 = new Consumer(b);

        p1.start();
        c1.start();

      }

    }

    ```



    




    





      
        

          
  