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


## 5주차(대면)

- Polling


  <img src="https://ifh.cc/g/azfJhQ.png">

  <img src="https://ifh.cc/g/prmLgA.png">



- Race condition

  <img src="https://ifh.cc/g/bky1X2.jpg">



  Main은 For loop 안에서 실행 한 순서대로 Thread가 종료할 때 까지 대기.
  각 Thread는 Digest를 생성하고 출력 및 종료
  Main에서 대기 중인 Thread가 종료되면 Digest를 출력하고,
  다음 Thread가 종료 될 때 까지 대기.
  ＂각 Thread의 출력은 순서 없음(OS의 Scheduling에 의함),
  Main Thread는 생성한 순서대로 출력됨”
  * Thread-{n} 은 run() method안의 출력
  * Main은 main() method안의 출력


- Sort Without Compare

  <img src="https://ifh.cc/g/w3kZ7p.jpg">

  <img src="https://ifh.cc/g/cYZClD.png">

## 6주차

- IP Addresses

  인터넷 안의 호스트의 Identifier

  (네트워크 인터페이스의 ID)

  IPv4(표준) : 4bytes : 0.0.0.0 ~ 255.255.255.255
  
  IPv6 : 16 bytes : 더 김

- Hostname

  www.konkuk.ac.kr = 203.30.38.108

  - DNS 

    DNS 서버가 Hostname을 IP 주소로 translation 해준다.

    하나의 호스트 네임이 여러가지의 IP 주소 가질 수 있음(load balancing)

    - 계층

      <img src="https://ifh.cc/g/PRO1Xf.jpg">

    


- InetAddress Class

    public 타입을 가지지 않아서 static으로 객체 생성후 객체.method() 방식으로 이용 가능 
  
  - Static methods to connect to DNS to resolve hostname

    - public static InetAddress getByName(String host) throws UnkownHostException

      DNS 서버에 접근해서 IP 주소 알아내 그 정보를 가지고 InetAddress 객체 만듬

    - public static InetAddress[] getAllByName(String host) throws UnkownHostException  

    
    하나가 아닌 여러개의 IP 주소 맵핑 가능

    그 주소들을 가져와서 각각의 IP 주소에 대해서 InetAddress 객체 만듬

    - DNS records

      - DNS : RR(distributed database storing resource records)

        RR foramt : name, value, type ,ttl

      - type = A

        name : 호스트 네임
        value : IP 주소

      - type = CNAME

        name : 껍데기 이름(www.ibm.com)

        value : 실제 이름(serverseat.backup2.ibm.com)


  - getter Methods

    - public String getHostName() : InetAddress객체.getHostName() => IP 주소가 알려진 상황에서 reverse Lookup을 통해 HostName을 읽어옴(DNS 접근 해야함)

    - public String getCanonicalHostName() : 역시 DNS 접근, 정식 HostName 받아옴

    - public String getHostAddress() 
    : IP 주소 받아옴

     - public static InetAddress getLocalHost() throws UnknownHostException : lookback address
  
        내 컴퓨터안의 웹 서버를 만들어 놓고 컴퓨터내에서의 직접 접속을 하려 할때 사용함(외부 접속이 필요하지 않음)

- NetWork Interface

    - getter Methods

      - public Enumeration getInetAddress() : 네트워크 인터페이스에 해당하는 모든 address 가져옴

      - public String getName() : 네트워크 인터페이스의 오브젝트 네임을 가져옴


    - One More Getter

      - public byte[] getHardwareAddress() : 인터페이스의 맥 주소(물리주소) 반환

      

  
    




    





      
        

          
  