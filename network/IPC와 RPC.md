# IPC(Inter Process Communication)

IPC란 각 프로세스들이 통신하는 모든 형태를 일컫는 용어이다.
다양한 형태의 메시지 전달 방식, 공유 자원에 접근했을 때 안전을 보장하는 오브젝트 동기화(Mutex)와 리소스 공유 방식이 포함된다.

### IPC의 종류

- Shared Memory
    - 프로세스 간 공유되는 메모리 영역을 만들어 사용하는 방법
    - 프로세스들은 읽기/쓰기를 통해 공유 메모리 영역을 수정할 수 있다.
- Message Passing
    - 다른 프로세스에 메시지를 보내 정보를 교환하는 방법
    - 주로 작은 데이터를 교환하며, 구현이 쉽다.
    - system call이 잦아 자칫 느려질 수 있다.
- Sockets
    - Network Communication의 Endpoint간의 통신
    - IP주소와 포트를 이용해 직접 통신
- Pipe
    - 양방향 간의 버퍼를 통해서 데이터를 읽거나 쓰는 구조이다
    - 연속적인 byte stream을 교환할 때 많이 사용

# RPC(Remote Procedure Call, 원격 프로시저 호출)

IPC의 방식 중 한 가지로, 메서드 호출 내부 통신을 숨겨주며, 별도의 원격 제어를 위한 코딩 없이 다른 주소 공간에서 함수나 프로시저를 실행할 수 있게 하는 프로세스 간 통신 기술이다.

클라이언트는 다른 머신에 존재할 수 있는 원격 서버와 IPC를 처리하는 로컬 메소드를 호출한다.

### RPC의 종류

- RMI(Remote Method Invocation)
    - RPC를 객체 지향의 원칙을 기반으로 구현한 호출
    - 서버와 클라이언트 모두 helper가 필요하다. (서버: Skeleton, 클라이언트: Stub)
- Stub
    - 원격지에 위치한 프로그램을 대리하는 작은 루틴이다.
    - RPC를 사용하는 프로그램이 컴파일되면 요청된 절차를 제공하는 프로그램의 대역을 한다.
    - 클라이언트에서 요청하는 데이터를 Marchaling하고 작업이 완료된 데이터를 다시 UnMarshaling하는 역할
- Skeleton
    - Stub과 비슷한 역할로 서버의 보조 객체이다.
    - 클라이언트의 Stub에서 데이터가 Marshaling되어 전송되면 Skeleton에서 다시 UnMarshaling하여 원래의 형태로 복원한다.
- Marshaling/UnMarshaling
    - Marshaling은 데이터를 바이트로 쪼개서 TCP/IP와 같은 통신 채널을 통해 전송될 수 있는 형태로 바꿔주는 과정
    - UnMarshaling은 반대로 전송 받은 바이트를 원래의 데이터 형태로 복원하는 과정
