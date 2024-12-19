# 운영체제란 무엇인가?

- 컴퓨터 하드웨어 바로 윗단에 설치되어 사용자 및 다른 모든 소프트웨어와 하드웨어를 연결하는 소프트웨어 계층이다.
- 커널은 운영체제의 핵심 부분으로 메모리에 상주하는 부분이다. (운영체제의 좁은 의미)
- 커널 뿐만 아니라 각종 주변 시스템 유틸리티를 포함한 개념 (운영체제의 넓은 의미)

## 운영체제의 구성 요소

- 커널
    - 운영 체제의 핵심 부분
        - 커널은 C언어와 같은 프로그래밍 언어로 개발된 소프트웨어다.
        - 하드웨어와 소프트웨어의 중간 역할을 수행한다.
        - 사용자와 직접 상호작용하진 않는다.
- 쉘
    - 사용자가 운영체제와 상호작용할 수 있도록 제공되는 인터페이스(CLI, GUI)
- 유틸리티 프로그램
    - 파일 관리, 네트워킹, 시스템 모니터링
- 라이브러리 및 서비스
    - 응용 프로그램이 운영체제 기능을 쉽게 사용할 수 있도록 돕는 인터페이스

> 운영체제를 자동차라고 한다면 커널은 엔진이다. 나머지 부품들은 사용자가 자동차를 쉽게 운전할 수 있도록 돕는 부품들이다.

## 운영 체제의 목적

- 가장 중요한 역할은 컴퓨터 시스템의 자원(CPU, 메모리, 입출력 장치)을 효율적으로 관리하는 것이다.
    - 사용자 간의 형평성 있는 자원 분배가 이루어지도록 한다.
    - 주어진 자원으로 최대한의 성능을 내도록 한다.
    - 하드웨어 자원(프로세서, 기억장치, 입출력 장치) 뿐만 아니라 소프트웨어 자원(프로세스, 파일, 메시지 등)을 효율적으로 관리 할 의무가 있다.
- 사용자가 컴퓨터 시스템을 편리하게 사용할 수 있는 환경을 제공한다.
    - 운영체제는 동시 사용자/프로그램들이 각각 독자적 컴퓨터에서 수행되는 것 같은 환상을 제공한다.
    - 하드웨어를 직접 다루는 복잡한 부분을 운영체제가 대행한다.

## 운영 체제의 분류

- **동시 작업 가능 여부**
    - single tasking
        - MS-DOS
    - multi tasking
        - 현대의 운영체제들은 모두 멀티 태스킹을 지원한다.
        - 동시에 두 개 이상의 작업 처리
        - 한 명령의 수행이 끝나기 전에 다른 명령이나 다른 프로그램을 실행할 수 있음
- **사용자의 수**
    - single user
    - multi user
        - 하나의 컴퓨터를 다수의 사용자가 동시에 사용할 수 있다.
- **처리 방식**
    - 일괄 처리(Batch Processing)
        - 작업 요청을 모아 한꺼번에 처리한다.
        - 작업이 완전 종료될 때까지 기다려야 한다.
    - 시분할(Time Sharing)
        - 여러 작업이 짧은 시간 단위로 CPU를 공유한다.
        - 현대의 OS가 사용하는 처리 방식이다.
    - 실시간(Realtime OS)
        - 정해진 시간 내에 작업이 완료되어야 한다. (매우 정교한 시스템이다.)
        - ex) 원자로/공장 제어, 미사일 제어, 반도체 장비, 로봇 제어
            - Hard realtime system
            - Soft realtime system

### 용어

- 컴퓨터에서 여러 작업을 동시에 수행하는 것을 뜻하는 용어들
- Multitasking
- Multiprogramming
    - 여러 프로그램이 메모리에 올라가 있음을 강조하는 개념이다.
- Time sharing
    - CPU의 시간을 분할하여 나누어 쓴다는 의미를 강조한다.
- Multiprocess(다중 CPU)
    - 하나의 컴퓨터에 CPU(processor)가 여러개 붙어있음을 의미한다.

## System Structure, Program Execution

- 컴퓨터 시스템은
    - **CPU**와 **메모리**로 구성되어있는 컴퓨터(Host)
    - **I/O device**들로 이루어져 있다.
        - disk, 키보드, 프린터, 모니터
        - 각 I/O Device 에는 입출력 장치를 관리하는 작은 CPU 가 붙어있다. (**device controller**)
        - CPU의 작업공간인 메모리가 필요하듯 각 컨트롤러도 작업 공간이 따로 필요하다. (**local buffer**)
- **Memory**는 CPU의 작업 공간이다.
- CPU 내부에는 메모리보다 더 빠르면서 정보를 저장할 수 있는 작은 공간이 있다. (**register**)
    - CPU를 실행하고 있는 것이 OS 인지, 사용자 프로그램인지 구분하게 해주는 것이 **mode bit** 이다.
    - CPU는 항상 메모리에 있는 instruction만 실행하는 운명이다.
        - CPU가 메모리에 있는 특정 작업을 수행하다가 입출력 장치로 부터 데이터를 받아와야 한다면?
        - CPU가 입출력 장치에 붙어있는 컨트롤러에게 해당 데이터를 받아와 달라고 지시한다.
        - 디스크는 CPU 보다 100만배 이상 느리기 때문에 CPU 는 device controller 가 작업을 수행하는 동안 다른 작업을 수행한다.
        - 외부 하드웨어 장치(입출력 장치)가 작업을 모두 완료하면 CPU의 주의를 끌기 위해 인터럽트 신호를 보낸다.
        - Interrupt Line 은 인터럽트 신호를 보내는 경로를 의미한다.
        - 인터럽트 된 CPU는 현재 작업을 중단하고 해당 이벤트를 처리한다.
        - 이는 특정 작업이 끝날 때까지 기다리지 않고 동시성을 구현하는 데 필수적이다.
            - 인터럽트 라인은 쉽게 말하자면 긴급 호출 벨이라고 보면 된다.
            - CPU가 기존 작업을 중단하고 더 중요한 작업을 즉시 수행할 수 있도록 만든다.
        - CPU는 하나의 Instruction을 끝내면 인터럽트 라인을 확인한다.
    - Timer 는 특정 프로그램이 CPU 를 독점하는 것을 막기 위해 존재한다.
        - 특정 프로그램이 너무 오랫동안 CPU를 점유하면 타임 인터럽트를 발생시킨다.
        - 인터럽트를 받은 CPU는 기존 작업을 중단한다.
        - CPU의 제어권이 사용자 프로그램으로부터 OS로 넘어간다.
        - OS는 기다리고 있던 다른 프로그램이 CPU를 사용할 수 있도록 CPU 제어권을 넘긴다.
    - 사용자 프로그램은 스스로 입출력 장치에 직접 접근할 수 없다.
        - 사용자 프로그램이 입출력 장치에 접근하기 위해선 반드시 OS를 거쳐야 한다.
        - 그러면 프로그램이 스스로 CPU 제어권을 OS에 넘겨주고 운영체제가 해당하는 작업을 I/O Controller에 지시한다.

### Mode bit

- CPU를 실행하고 있는 것이 OS 인지, 사용자 프로그램인지 구분하게 해주는 것이 **mode bit** 이다.
    - Mode bit 은 사용자 프로그램의 잘못된 수행으로 다른 프로그램 및 운영 체제에 피해가 가지 않도록 하기 위한 보호장치다.
    - Mode bit 을 통해 하드웨어적으로 두 가지 모드의 operation을 지원한다.
    - `1` 사용자 모드: 사용자 프로그램 수행
        - 사용자 프로그램에게 CPU를 넘기기 전에 mode bit 을 1로 셋팅한다.
        - mode bit 이 1 일때는 제한된 Instruction만 수행할 수 있다.
    - `0` 모니터 모드: OS 코드 수행
        - 보안을 해칠 수 있는 중요한 명령어는 모니터 모드에서만 수행할 수 있다.
        - Interrupt나 Exception이 발생하면 하드웨어가 mode bit 을 0 으로 바꾼다.

### device controller

- 해당 I/O 장치 유형을 관리하는 일종의 작은 CPU
- 제어 정보를 위해 control register, status register 를 가진다. (CPU의 작업 지시를 수행)
- local buffer 를 가진다 (일종의 data register)
- I/O 는 실제로 device 와 local buffer 사이에서 일어난다.
- device controller 는 I/O가 끝났을 경우 interrupt로 CPU에 그 사실을 알린다.

> device driver (장치 구동기) </br>
> OS 코드 중 각 장치별 처리 루틴을 의미한다. (software) </br>
> OS 가 각 장치를 손쉽게 다룰 수 있도록 하는 인터페이스와 같다 </br>
> CPU 가 disk 에 작업을 지시할 때 필요한 코드를 담고 있다. (인터럽트 처리 루틴도 이에 포함되어있다.)

### DMA(direct memory access) controller

- 원래는 메모리에 직접 접근할 수 있는 장치는 CPU 뿐이다.
- DMA Controller 도 메모리에 접근할 수 있게 만들었다.
    - 메모리에 CPU 와 DMA Controller 가 동시에 접근하면 문제가 발생할 수 있기 때문에 중간에 Memory Controller 를 두었다.
- I/O 장치가 너무 자주 Interrupt 를 걸면 CPU 의 작업이 너무 느려질 수 있다.
- I/O 장치의 작업이 끝나고 local buffer 에 해당 결과물이 담겨있으면 DMA Controller 가 메모리에 그 데이터를 copy 해서 전달해주는 역할을 수행한다.
- 그 이후 DMA Controller 가 CPU 에 인터럽트를 한번만 걸어서 작업 완료 보고를 한다.
    - 기존 Device controller 는 데이터를 한 바이트 또는 한 워드 단위로 전송한다.
    - 디바이스가 다음 데이터를 준비할 때마다(데이터를 전송할 때 마다) 다시 인터럽트를 발생시키기 때문에 문제가 된다.
    - 반면 CPU는 DMA 컨트롤러에게 한번에 전송해야할 데이터의 시작 주소와 크기, 전송 방향을 명령한다.
    - 이후 DMA 컨트롤러가 메모리와 디바이스 간의 데이터 전송을 직접 수행한다.
    - 전송이 완료되면 단 한번의 인터럽트를 통해 CPU에 알린다.
        - 데이터 전송 과정에서 CPU가 관여하지 않는 장점이 있다.
        - CPU는 데이터를 직접 전송하는 대신 다른 작업을 수행할 수 있다.
    - 만약 1KB 의 데이터를 전송한다고 가정해보면
        - device controller: 1 바이트당 1회 -> 1024 번 인터럽트 발생
        - DMA controller: 1회 전송 후 1회 인터럽트 -> 1 번 인터럽트
    - 대용량의 데이터를 처리하는 경우 성능에 큰 차이를 만든다.
    - 또한 Device controller 기반의 인터럽트는 CPU가 모든 데이터 전송 요청을 처리해야 하므로 컨텍스트 스위칭 비용이 많이 든다.
- DMA Controller의 핵심은 기존 CPU 가 직접 수행하던 데이터 전송 작업을 DMA Controller 가 분담하여 CPU 의 성능과 효율성을 향상시키는 것이다.
- Interrupt driven I/O 방식에서는 local buffer 에 데이터를 채우거나 비우는 과정에서 CPU 가 개입해야하기 때문에 인터럽트가 자주 발생했다.
- DMA 방식을 사용할 때라도 Device Controller 가 CPU에게 직접 인터럽트를 발생시킬 수 있다.
    - 주로 초기 요청(데이터 전송 준비 완료)시에만 인터럽트를 직접 발생시킨다.
    - 그러면 CPU가 DMA 컨트롤러에게 작업을 지시하고 이후로는 Device Controller 가 직접 CPU 에게 인터럽트를 보내는 일이 없다.
- DMA Controller 는 바이트 단위가 아니라 block 단위로 인터럽트를 발생시킨다.

### I/O의 수행

- 모든 입출력 명령은 특권 명령이다. (mode bit 0 일때만 가능)
- 사용자 프로그램은 어떻게 I/O를 하는가?
    - system call
        - 사용자 프로그램이 운영체제에게 I/O 요청을 한다.
    - trap 을 사용하여 인터럽트 벡터의 특정 위치로 이동한다.
    - 제어권이 인터럽트 벡터가 가리키는 인터럽트 서비스 루틴으로 이동한다.
    - 올바른 I/O 요청인지 확인 한 후 I/O 를 수행한다. (권한 확인)
    - I/O 완료 시 제어권을 시스템 콜 다음 명령으로 옮긴다.
    - 결국 I/O 요청에는 trap이, I/O 응답에는 interrupt 가 발생한다고 보면 된다.

### 인터럽트

- 인터럽트 당한 시점의 레지스터와 Program counter 를 save 한 후 CPU의 제어를 인터럽트 처리 루틴에 넘긴다,
- Interrupt
    - 하드웨어가 발생시킨 인터럽트
    - Device Controller 가 발생시킨 인터럽트
    - Timer Interrupt
- Trap
    - 소프트웨어가 발생시킨 인터럽트
    - Exception
    - System call
        - 프로그램이 커널 함수(OS)를 호출하는 경우
- 인터럽트 벡터
    - 해당 인터럽트의 처리 루틴 주소를 가지고 있다.
        - 키보드 인터럽트가 발생하면 어디로 가야하고
        - 모니터 인터럽트가 발생하면 어디로 가야하는지
- 인터럽트 처리 루틴
    - 해당 인터럽트를 처리하는 커널 함수
    - 인터럽트를 받았을 때 CPU 가 실제로 처리해야 하는 함수다.

### PC Register

- CPU 는 메모리의 Instruction 을 실행한다.
- 메모리의 어떤 위치에 있는 Instruction 을 실행하는가?
- register 에는 메모리 주소를 가리키고 있는 Program Counter Register 가 있다. (PC register)
- CPU 가 Instruction 을 수행하면 PC Register 는 다음 주소를 가리키게 된다.

## 동기식 입출력과 비동기식 입출력

- **synchronous I/O**
    - I/O 요청 후 입출력 작업이 완료된 후에야 CPU 제어가 사용자 프로그램에 넘어감
    - 구현 방법 1
        - I/O 가 끝날 때 까지 CPU 를 낭비시킴
        - 매 시점 하나의 I/O 만 일어날 수 있음, 다른 I/O 장치도 낭비가 된다.
    - 구현 방법 2
        - I/O 가 완료될 때 까지 해당 프로그램에게서 CPU 를 빼았는다.
        - I/O 처리를 기다리는 줄(line)에 그 프로그램을 줄 세운다.
        - 다른 프로그램에게 CPU 를 준다.
- **asynchronous I/O**
    - I/O 가 시작된 후 입출력 작업이 끝날 때 까지 기다리지 않고 제어가 **해당 사용자 프로그램에** 즉시 넘어간다.
    - 해당 프로그램은 I/O 가 진행되든 말든 다른 작업을 이어나갈 수 있다.
- 두 경우 모두 인터럽트로 I/O 가 끝났음을 알려준다.

### 서로 다른 입출력 명령어

- I/O 를 수행하는 special Instruction 에 의한 입출력 (Isolated I/O)
    - 메모리 주소 공간과 I/O 장치가 서로 별도의 주소 공간을 사용한다.
    - 특별한 I/O 명령어가 필요하다. (`IN`, `OUT` 등)
- Memory Mapped I/O 에 의한 입출력
    - 하드웨어 장치를 메모리 주소 공간에 매핑하여 CPU 가 메모리처럼 장치를 제어할 수 있도록 하는 방식이다.
    - I/O 장치와 메모리 간 구분이 없고, CPU 는 메모리 접근 명령을 사용해 장치와 상호작용 할 수 있다.
    - ex) 특정 메모리 주소를 읽는 행위가 사실은 키보드 입력을 읽는 작업이다.
    - 별도의 I/O 명령어가 필요하지 않기 때문에 설계가 간소화된다. (예)`LOAD`와 `STORE` 와 같은 일반적인 메모리 명령어로 장치와 상호작용)
    - 하지만 메모리 주소 공간이 낭비될 수 있다.
    - 주로 고성능 시스템에 사용된다.
        - GPU, 네트워크 카드, 임베디드 시스템

## 저장 장치 계층 구조

- 휘발성 메모리, primary memory(executable - CPU 가 직접 접근할 수 있음)
    - Register
    - Cache memory
    - Main memory
- 비휘발성 메모리, secondary memory
    - Magnetic disk
    - Optical disk
    - Magnetic tape

- 밑으로 갈수록 용량이 커지고 느려지고 싸진다.

> 최근에는 메인 메모리도 비휘발성 메모리로 개발되고 있다.

## 프로그램의 실행 (메모리 load)

- 프로그램을 실행시킨다.
    - 그 프로그램의 주소 공간(Virtual Memory)이 형성된다.
    - 해당 주소 공간은 code, data, stack 영역으로 구성된다.
    - 모든 데이터가 한번에 물리적인 메모리에 할당되는 것은 낭비다.
    - 따라서 실행에 필요한 일부만 Physical Memory 에 로드되고 사용하지 않는 부분은 Disk 의 Swap Area 에 보관된다.
    - Swap Area 는 Disk 의 File System 과는 다른 개념이다.
    - File System 은 전원이 나가더라도 데이터가 소실되어선 안된다.
    - 하지만 Swap Area 는 전원이 나가면 의미가 없는 데이터가 된다. (프로세스가 종료되기 때문에)
        - Swap Area 는 같은 Disk 에 존재할 수도, 별도의 디스크에 존재할 수도 있다.
        - 고성능 서버나 특정 환경에서는 Swap Area 와 File System 을 별도의 물리 디스크에 배치하기도 한다.

- 커널도 프로그램이므로 별도의 가상 메모리가 생성된다.

#### 사용자 프로그램이 사용하는 함수

- **사용자 정의 함수**
    - 자신이 프로그램에 정의한 함수
- **라이브러리 함수**
    - 자신의 프로그램에서 정의하지 않고 갖다 쓴 함수
    - 자신의 프로그램 실행 파일에 포함되어 있다.
- **커널 함수**
    - 운영 체제의 프로그램 함수
    - 커널 함수 호출 = 시스템 콜

# 프로세스

## 프로세스의 개념

- 프로세스란 실행중인 프로그램을 의미한다.
- **프로세스의 문맥**
    - CPU 수행 상태를 나타내는 하드웨어 문맥
        - Program Counter
        - 각종 register
    - 프로세스의 주소 공간
        - code, data, stack
    - 프로세스 관련 커널 자료 구조
        - PCB (Process Control Block)
            - 프로세스가 생성될 때 마다 PCB 가 할당되어 프로세스를 관리한다.
        - Kernel Stack
            - 프로세스가 작업을 수행하다 자신이 수행하지 못하는 일을 OS 에 요청하게 될 때
            - PC 가 커널 주소 공간을 가리키게 되고 커널 내부에서 함수가 호출된다.
                - 커널은 여러 프로세스가 공유하는 자원이다.
                - 하지만 커널 내부에서 함수가 호출될 때는 어떤 프로세스가 커널을 호출했는지 구분하기 위해
                - 별도의 커널 stack 을 프로세스마다 두고 있다.
                - 프로세스가 각 시스템 호출이나 인터럽트를 통해 커널에 접근할 때 커널은 각 프로세스의 작업 상태를 저장하고 복구해야 한다.
                - 이를 위해 각 프로세스는 독립적인 커널 스택을 가진다.
                - 커널 스택은 오직 커널 모드에서만 접근할 수 있다.
                - 사용자 모드 코드는 커널 스택에 직접 접근할 수 없다.
                - 커널 스택은 프로세스가 생성될 때 마다 커널 주소 공간 내부에 해당 프로세스를 위한 새로운 커널 스택을 생성한다.
- 현대의 OS 는 시분할 방식으로 작업을 처리하기 때문에 CPU 제어권이 특정 프로세스에서 다른 프로세스로 넘어갈 때 기존 프로세스의 문맥을 기억해 두어야
- CPU 제어권을 다시 받았을 때 이전 작업을 이어나갈 수 있게 된다.

## 프로세스의 상태

- **Running**
    - CPU 를 잡고 instruction 을 수행중인 상태
- **Ready**
    - CPU 를 기다리는 상태
- **Blocked**
    - CPU 를 주어도 당장 instruction 을 수행할 수 없는 상태
    - 따라서 CPU 를 사용할 수 없는 상태다.
    - 따라서 RUNNING 상태에서 BLOCKED 상태가 되고 실행 가능한 다른 프로세스가 있다면 컨텍스트 스위칭이 발생한다.
    - 프로세스 자신이 요청한 이벤트가 즉시 만족되지 않아 이를 기다리는 상태 (ex I/O 작업)
- **New**
    - 프로세스가 생성중인 상태
- **Terminated**
    - 프로세스의 수행이 끝난 상태
- **Suspended**
    - 외부적인 이유로 프로세스의 수행이 정지된 상태
    - 프로세스는 통째로 디스크에 swap out 된다.
        - Swapper 의 등장으로 새롭게 등장한 프로세스의 상태 개념이다.
        - 메모리에 너무 많은 프로세스가 올라가서 Swapper(중기 스케쥴러)로 부터 쫓겨난 상황은 Ready 도 아니고 Blocked 도 아니다.
        - Swapper 뿐만 아니라 사용자가 프로그램을 일시 정지시킨 경우에도 Suspended 상태가 된다. (외부적인 이유)
- Blocked vs Suspended
    - Blocked: 자신이 요청한 event 가 만족되면 Ready 상태로 전환된다.
        - 기본적으로 Blocked 라도 프로세스 자체가 멈춰버린것은 아니므로 Active 상태다.
    - Suspended: 외부에서 resume(메모리에 다시 올려주면) 해주면 Active
        - Suspended Blocked
            - 프로세스가 대기 조건(I/O 작업, 특정 이벤트 발생 등)을 기다리며 Blocked 된 상태였지만 메모리에서 제거된 상태다
        - Suspended Ready: 이벤트가 발생하거나 입출력 작업이 끝나면 Suspended Ready 상태로 전환된다.
            - 프로세스가 메모리에 적재되진 않았지만 대기 조건이 해소되어 실행 준비가 완료된 상태를 의미한다.
        - Suspended 상태 자체가 프로세스가 얼어붙은 inactive 상태를 뜻한다.
    - 예시
        - 프로세스 A 가 디스크에서 데이터를 읽어오는 작업을 요청한다.
        - 데이터를 받아올 때 까지 Blocked 상태가 된다.
        - 메모리에 너무 많은 프로세스가 올라가서 프로세스 A 가 Swap out 된다.
        - 메모리에서 제거되었기 때문에 Suspended Blocked 상태가 된다.
        - 디스크 I/O 작업이 끝나면, A 의 대기 조건이 해소되므로 Suspended Ready 상태로 전환된다.
        - 운영체제가 프로세스를 다시 메모리에 적재하면 A 는 Ready 상태가 되어 실행될 준비를 한다.

## PCB

- 운영체제가 각 프로세스를 관리하기 위해 프로세스 당 유지하는 정보
- 구성 요소
    - OS 가 관리상 사용하는 정보
        - process state
        - process ID
        - scheduling information
        - priority
    - CPU 수행 관련 하드웨어 값
        - program counter
        - registers
    - 메모리 관련
        - code, data, stack 의 위치 정보
    - 파일 관련
        - open file descriptors

## 컨텍스트 스위치

- CPU 를 한 프로세스에서 다른 프로세스로 넘겨주는 과정
- 컨텍스트 스위칭 간 운영체제는 다음과 같은 작업을 수행한다.
    - CPU 를 내어주는 프로세스 상태를 그 프로세스의 PCB 에 저장한다.
    - CPU 를 새롭게 얻는 프로세스의 상태를 PCB 에서 읽어온다.
- System call 이나 Interrupt 발생시 반드시 컨텍스트 스위치가 일어나는 것은 아니다.
    - 컨텍스트 스위칭은 CPU 제어권이 사용자 프로세스에서 다른 사용자 프로세스로 넘어가는 것을 의미한다.
    - System call 이나 Interrupt 는 CPU 제어권이 사용자 프로세스에서 운영체제로 넘어가게 한다.
    - 다만 System call 이후에 타이머 인터럽트가 발생하거나, I/O 요청 처럼 오래걸리는 작업을 요청하면 CPU 제어권을 다른 프로세스로 넘긴다.
        - 이 경우 컨텍스트 스위칭이 발생한다.
- 물론 첫번째 경우도 CPU 수행 정보 등 context 일부를 PCB 에 저장해야 하지만
- 컨텍스트 스위칭이 발생하는 두번째 경우보다 그 부담이 훨씬 적다.
    - 컨텍스트 스위칭이 발생하면 캐시 메모리에 저장되어있던 기존 프로세스의 정보를 모두 flush(제거) 해줘야 한다.
    - 반면 유저모드에서 커널모드로 넘어가고 다시 유저모드로 넘어오는 상황은 flush 를 하지 않아도 된다.
    - 캐시 메모리 flush 자체가 큰 오버헤드를 발생시킨다.
        - 프로세스 A 의 데이터가 캐시 메모리에 저장되어 있다가 프로세스 B 로 컨텍스트 스위칭이 발생하면
        - 기존의 프로세스 A 의 데이터는 캐시 메모리에서 플러시 된다.
            - 캐시 메모리의 데이터가 충돌함을 방지하기 위해
        - 다시 프로세스 A 로 CPU 의 제어권이 넘어왔을 때 캐시 메모리에 프로세스 A 의 데이터가 없기 때문에 캐시 미스가 자주 발생한다.
        - 이로 인해 프로세스 A 는 다시 메인 메모리에서 직접 데이터를 읽어와야 한다.
- 하지만 컨텍스트 스위칭이 발생한다고 반드시 캐시 메모리가 flush 되는 것은 아니다.
    - 캐시 태그가 가상 주소를 기반으로 하고있다면 flush 하는 것이 일반적이다.
    - 캐시 태그가 물리 주소를 기반으로 하고있다면 flush 를 하지 않아도 무방하다.
    - 캐시 메모리 전체를 flush 하는 것은 성능에 큰 영향을 미치기 때문에 현대 OS 와 CPU 는 이를 되도록 피하려 한다.
    - 일반적으로는 캐시를 비우지 않고 MESI 프로토콜 이나 캐시 라인별 무효화 방식을 사용한다.
    - 또한 캐시 flush 정책은 운영체제 정책이나, CPU 아키텍쳐에 따라 다를 수 있다.

## 프로세스를 스케쥴링하기 위한 Queue

- **Job Queue**
    - 현재 시스템 내에 있는 모든 프로세스의 집합
- **Ready Queue**
    - 현재 메모리 내에 있으면서 CPU 를 잡아서 실행되기를 기다리는 프로세스의 집합
- **Device Queue**
    - I/O device 의 처리를 기다리는 프로세스의 집합
- 프로세스들은 각 큐들을 오가며 수행된다.
- 큐에는 실제로 프로세스가 줄을 서는 것이 아니라 PCB 가 대기한다.

## 스케쥴러

- **Long-term scheduler**(job scheduler)
    - 시작 프로세스 중 어떤 것들을 ready queue 로 보낼지 결정한다.
    - 프로세스에 memory 를 주는 문제
    - degree of Multiprogramming 을 제어한다.
        - 메모리에 프로그램을 몇 개 올릴 건지 제어한다.
    - 시분할 시스템에는 보통 장기 스케쥴러가 없다.
        - 프로세스가 시작되면 바로 메모리를 주고 ready 상태로 대기한다.
        - 하지만 메모리에 올라가는 프로세스의 수를 제어하기 위해 Swapper 를 함께 사용한다.
- **Short-term scheduler**(CPU scheduler)
    - 어떤 프로세스를 다음번에 running 시킬지 결정한다.
    - 프로세스에 CPU 를 주는 문제
    - 충분히 빨라야 한다.
- **Medium-term scheduler**(Swapper)
    - 여유 공간 마련을 위해 프로세스를 통째로 메모리에서 디스크로 쫓아낸다.
    - 프로세스에게서 memory 를 뺏는 문제
    - degree of Multiprogramming 을 제어한다.

## 가상 메모리 생성 과정

- **페이지 테이블 설정**
    - 운영체제는 프로세스 생성 시 해당 프로세스의 가상 주소 공간을 관리하기 위한 페이지 테이블을 만든다.
    - 페이지 테이블은 가상 주소와 물리 주소 간의 매핑 정보를 저장한다.
    - 초기에는 가상 메모리가 프로세스에 대해 논리적으로만 생성되며, 물리 메모리와 매핑되지 않을 수 있다.
- **Address Translation**
    - CPU 는 MMU(Memory Management Unit)을 통해 가상 주소를 물리 주소로 변환한다.
    - 프로세스가 메모리 엑세스를 시도할 때, 운영체제가 페이지 테이블을 참조하여 해당 가상 주소를 물리 주소에 매핑한다.
        - 필요 시 새로운 물리 메모리를 할당하기도 한다.
- **Lazy Allocation**
    - 물리 메모리는 프로세스가 특정 주소에 실제로 접근할 때만 할당된다.
    - 초기에는 가상 주소 공간만 설정되고, 해당 주소에 데이터가 실제로 쓰일 때 메인 메모리를 할당한다.
    - 물리 메모리는 한정된 자원이기 때문에 모은 프로세스의 가상 메모리를 실제 메인 메모리로 바로 매핑할 수는 없다.
    - 이를 해결하기 위해 페이징 또는 스왑 기술을 사용한다.
- 운영 체제가 가상 메모리를 물리 메모리와 보조 기억 장치(하드디스크, SSD)를 조합하여 관리한다.
    - 제한된 물리 메모리를 보조 기억 장치와 연계를 통해 효율적으로 사용한다.
    - Swap out 은 사용되지 않는 프로세스나 메모리 페이지를 메인 메모리에서 제거하고 이를 보조 기억 장치의 스왑 공간(Swap area)에 저장하는 것을 의미한다.
    - 이 과정에서 가상 메모리의 논리적 주소 공간은 유지된다.
    - Swap out 된 프로세스는 Suspended 상태가 된다.

## Thread

- CPU 를 실행하는 단위다.
- Thread 는 프로세스 내에서 공유할 수 있는 자원은 모두 공유하지만 Thread 내부에 별도로 구성된 영역도 있다.
    - program counter
    - register set
    - stack space
    - CPU 관련 정보들만 별도로 가지고 있다.
- Thread 가 동료 Thread 와 공유하는 부분
    - code section
    - data section
    - OS resources
- 다중 스레드로 구성된 task 구조에서는 하나의 서버 스레드가 blocked 상태인 동안에도 동일한 task 내의 다른 스레드가 실행되어 빠른 처리를 할 수 있다.
- 스레드를 사용하여 병령성을 높이고 메모리 자원을 절약할 수 있다.
    - 크롬 웹 브라우저를 여러 개 띄운다고 생각해보자.
    - 각 크롬 웹 브라우저가 별도의 프로세스로 생성된다면 메모리 낭비가 심할 것이다.
- 동일한 일을 수행하는 다중 스레드가 협력하여 높은 처리율(throughput)과 성능 향상을 얻을 수 있다.

> 프로세스의 상태가 blocked 되면 모든 스레드도 blocked 상태가 될까? <br/>
> 유저 레벨 스레드는 프로세스 전체가 영향을 받을 가능성이 높다. <br/>
> 커널 레벨 스레드에서는 한 스레드만 blocked 상태가 되고 다른 스레드는 독립적으로 실행될 수 있다. <br/>
> 현대 운영 체제는 대부분 커널 레벨 스레드를 사용하므로, 스레드 간 독립성을 보장하는 경우가 일반적이다. <br/>
> 커널 레벨 스레드는 운영 체제가 각 스레드를 독립적으로 스케쥴링하고 관리하는 형태를 말한다. <br/>
> **프로세스의 상태는 프로세스 내부의 모든 스레드의 상태를 종합한 것이다.** <br/>
> 프로세스의 모든 스레드 중에 하나만 RUNNING 상태여도 해당 프로세스는 RUNNING 상태가 된다. <br/>
> 하지만 프로세스가 Swap out 되면 해당 프로세스 내의 모든 스레드가 실행되지 못한다. <br/>
> Swap out 된 프로세스는 운영 체제의 스케줄링 대상에서 제외된다.

### 스레드 사용의 장점

- 응답성이 높아진다.
    - 하나의 스레드가 blocked 되더라도 다른 스레드는 작업을 이어나갈 수 있다.
    - 비동기식 처리 방법이다.
        - 프로세스의 특정 스레드가 I/O 작업을 하도록 지시한 후 다른 스레드로 필요한 나머지 작업들을 먼저 수행하도록 할 수 있다.
        - CPU 제어권이 다시 프로세스로 넘어오므로 비동기식 입출력 방법이다.
- 자원 공유
    - 모든 스레드는 binary code, data, 프로세스의 자원을 공유할 수 있다.
- 좀 더 빠르다.
    - 프로세스 하나를 생성하는 것은 오버헤드가 크다.
    - 하지만 프로세스 내부에 스레드 하나를 생성하는 것은 더 빠르고 간단하다.
    - 프로세스 간 컨텍스트 스위칭은 오버헤드가 크다.
    - 스레드 간 컨텍스트 스위칭은 비교적 더 빠르고 간단하다.
- MultiProcessor Architecture 에서의 활용
    - 멀티 프로세서 환경이라면 각 스레드는 병렬적으로 작업을 수행할 수 있다.

## 프로세스 생성

- 부모 프로세스가 자식 프로세스를 생성한다. (복제 생성)
    - 여기서 복제란 프로세스 문맥을 그대로 복사한다는 뜻이다.
- 프로세스의 트리(계층 구조)형성
- 프로세스는 자원을 필요로 한다.
    - 운영 체제로부터 받는 자원
    - 부모와 공유하는 자원
- 자원의 공유
    - 부모와 자식이 모든 자원을 공유하는 모델
    - 일부를 공유하는 모델
    - 전혀 공유하지 않는 모델(원칙)
        - 부모 프로세스와 자식 프로세스가 독립적으로 동작하며 서로 CPU 를 차지하기 위해 경쟁한다.
        - 원칙적으론 부모 프로세스의 문맥을 자식 프로세스가 그대로 복사해서 독립적으로 운용되는 것이 맞다.
        - 하지만 메모리에 똑같은 데이터가 2개가 올라가면 비효율적이기 때문에 좀 더 유연하게 COW 방식을 따르기도 한다.
- 프로세스의 실행
    - 부모와 자식이 공존하며 수행되는 모델
    - 자식이 종료될 때 까지 기다리는(wait) 모델
- 주소 공간
    - 자식은 부모의 공간을 복사한다.(binary and OS data)
    - 자식은 그 공간에 새로운 프로그램을 올린다.
- fork system call
    - 새로운 프로세스를 생성하기 위한 시스템 콜
    - 부모 프로세스를 그대로 복사한다.
    - 주소 공간을 할당한다.
- exec system call
    - fork() 다음에 이어지는 exec() 시스템 콜을 통해 새로운 프로그램을 메모리에 올린다.

### COW(Copy-on-Write)

- Write 가 발생했을 때 그 때 Copy 하겠다는 의미다.
- 기존 내용을 변경하기 전까진 데이터를 복사하지 않고 (내용이 동일하다면) 부모 프로세스의 자원을 그대로 공유한다.

## 프로세스 종료

- 프로세스가 마지막 명령을 수행한 후 운영체제에게 이 사실을 알려준다.(exit)
    - 자식이 부모에게 output data 를 보낸다.(wait system call)
        - 반드시 자식 프로세스가 부모 프로세스보다 먼저 종료되어야 한다.
    - 프로세스의 각종 자원들이 운영체제에게 반납된다.
- 부모 프로세스가 자식의 수행을 종료시킨다. (abort)
    - 자식이 할당 자원의 한계치를 넘어섰을 때
    - 자식에게 할당된 작업이 더 이상 필요하지 않을 때
    - 부모 프로세스가 종료(exit)될 때
        - 운영 체제는 부모 프로세스가 종료되는 경우 자식이 더 이상 수행되도록 두지 않는다.

## 프로세스의 시스템 콜

### fork() 시스템 콜

- `fork()` 는 새로운 프로세스를 생성하는 OS 함수다.

```java
public static void main(String[] args) {
    int pid;
    pid = fork();
    if (pid == 0) {
        System.out.println("child");
    }
    if (pid > 0) {
        System.out.println("parent");
    }
}
```

- `fork()` 를 통해 새로운 프로세스를 생성한다.
- 그러면 아래와 같이 부모 프로세스의 복제본이 생성된다.

```java
public static void main(String[] args) {
    int pid;
    pid = fork(); // PC 가 fork() 호출 이후 코드를 가리킨다.
    if (pid == 0) {
        System.out.println("child");
    }
    if (pid > 0) {
        System.out.println("parent");
    }
}
```

- 자식 프로세스는 부모 프로세스의 문맥을 복사하기 때문에 `fork()` 호출한 이후 코드를 실행한다.
- 또한 부모 프로세스는 `fork()` 을 결과 값으로 양수를, 자식 프로세스는 0 을 반환받기 때문에 어떤 프로세스가 부모인지 자식인지 구분할 수 있다.

### exec() 시스템 콜

- 부모 프로세스의 복제본이 아닌 완전히 새로운 프로세스를 생성하는 OS 함수다.

```java
public static void main(String[] args) {
    int pid;
    pid = fork();
    if (pid == 0) {
        System.out.println("child");
        execlp("echo", "echo", "hello", ( char *)0); //fork() 이후 exec() 시스템 콜 호출
        System.out.println("invalid code"); // exec() 이후의 코드는 영원히 실행될 수 없다.
    }
    if (pid > 0) {
        System.out.println("parent");
    }
}
```

- `exec()` 시스템 콜은 반드시 `fork()` 이후에만 사용되야하는 것은 아니다.
    - `exec()` 시스템 콜만 호출할 수도 있다.
- `exec()` 이후의 코드는 영원히 실행될 수 없다. 새로운 프로그램으로 완전히 덮혀씌워지기 때문.

### wait() 시스템 콜

- 프로세스 A 가 `wait()` 시스템 콜을 호출하면
    - 커널은 child 가 종료될 때 까지 프로세스 A 를 sleep 시킨다. (block 상태)
    - child process 가 종료되면 커널은 프로세스 A 를 깨운다. (ready 상태)

### exit() 시스템 콜

- 프로세스를 자발적으로 종료할 때 사용한다.
    - `exit()` 시스템 콜을 명시적으로 적어주지 않아도 main 함수가 종료되는 시점에 컴파일러가 자동으로 `exit()` 시스템 콜을 넣어준다.
- 프로세스를 비 자발적으로 종료시키는 경우는?
    - 부모 프로세스가 자식 프로세스를 강제로 종료시킨다.
    - kill, break 커맨드를 사용
    - 부모 프로세스가 종료되는 경우

## 프로세스 간 협력

- 독립적 프로세스
    - 프로세스는 각자의 주소 공간을 가지고 수행되므로 원칙적으로 하나의 프로세스는 다른 프로세스의 수행에 영향을 미치지 못한다.
- 협력 프로세스
    - 프로세스 협력 메커니즘을 통해 하나의 프로세스가 다른 프로세스의 수행에 영향을 미칠 수 있다.
- 프로세스 간 협력 메커니즘(**IPC**: Interprocess Communication)
    - 메시지를 전달하는 방법
        - message-passing: 커널을 통해 메시지를 전달한다.
    - 주소 공간을 공유하는 방법
        - shared-memory: 서로 다른 프로세스 간에도 일부 주소 공간을 공유하게 하는 메커니즘
        - thread 는 사실상 하나의 프로세스이므로 프로세스 간 협력으로 보기는 어렵다.
        - 하지만 동일한 process 를 구성하는 thread 들 간에는 주소 공간을 공유하는 협력이 가능하다.
        - Shared Memory 방식도 첫 시작엔 커널의 도움이 필요하다.
        - Shared Memory 를 사용하겠다는 시스템 콜을 통해 각 프로세스는 공유 메모리 주소를 할당받는다.

### Message Passing 방식

- 프로세스 사이에 공유 변수를 일체 사용하지 않고 통신하는 시스템
- **Direct Communication**
    - 통신하려는 프로세스의 이름을 명시적으로 표시한다.
    - 사용자 프로세스 간의 직접적인 연결은 불가능하다. 커널을 통해 간접적으로 연결할 수 있다.
- **Indirect Communication**
    - mailbox 또는 port 를 통해 메시지를 간접적으로 전달한다.
        - 통신하려는 프로세스의 이름을 명시하지 않음, 대신 mail box 의 이름을 적어준다.

# CPU Scheduling

## CPU Scheduling 은 왜 필요한가?

- **I/O bound job**
    - CPU 를 사용하기 보다는 I/O 작업 비중이 높은 경우
    - many short CPU bursts
- **CPU bound job**
    - I/O 작업보다는 CPU 를 사용한 순수 연산의 비중이 높은 경우
    - few very long CPU bursts
- 여러 종류의 job(=process) 이 섞여있기 때문에 CPU 스케쥴링이 필요하다.
    - 사용자가 I/O 응답을 기다리고 있는데 CPU bound job 이 CPU 를 계속 독점한다면 문제가 된다.
    - 보다 interactive 한 job 에 적절히 CPU 를 분배해줘야 사용자 입장에서 답답하지 않다.

## CPU Scheduler, Dispatcher

- **CPU Scheduler**
    - Ready 상태의 프로세스 중에서 이번에 CPU 를 줄 프로세스를 고른다.
    - CPU Scheduler 는 운영체제 안에서 스케쥴링을 담당하는 코드다.
- **Dispatcher**
    - CPU 제어권을 스케쥴러에 의해 선택된 프로세스에게 넘긴다.
    - 이 과정을 context switch 라고 한다.
- 스케쥴링의 종류
    - **nonpreemptive**
        - CPU 를 강제로 빼앗지 않고 프로세스가 자진 반납한다.
            - I/O 요청 시스템 콜을 호출했을 때 (Running -> Blocked)
            - 프로세스가 Terminate 됐을 때
    - **preemptive** (선점형)
        - CPU 제어권을 강제로 빼았는다.
            - Timer interrupt (Running -> Ready)
            - I/O 완료 후 Interrupt (Blocked -> Ready)

## Scheduling Criteria

- 시스템 입장에서의 성능 척도
    - **CPU utilization**(이용률)
        - 전체 시간중에 CPU 가 놀지 않고 일한 시간
    - **Throughput**(처리량)
        - 주어진 시간동안 몇 개의 작업을 완료했는가
- 프로그램(고객) 입장에서의 성능 척도
    - **Turnaround time**(소요 시간)
        - CPU 를 쓰러 들어와서 다 쓸 때까지 걸린 시간 (대기 시간도 포함한다)
    - **Waiting time**(대기 시간)
        - CPU 를 받기위해 Ready Queue 에서 기다린 시간
    - **Response time**(응답 시간)
        - Ready Queue 에 들어와서 최초로 CPU 를 얻기까지 걸린 시간
    - Waiting vs Response
        - 선점형 스케쥴러의 경우 프로세스가 CPU 를 사용하다가도 스케쥴러에 의해 CPU 제어권을 뺏기고, 다시 CPU 제어권을 받는 일을 반복한다.
        - Waiting time 은 CPU 를 받기위해 기다린 모든 시간을 합친 것이다.
        - Response time 은 가장 처음 Ready Queue 에 들어와서 CPU 를 받기위해 기다린 시간만을 의미한다.

## Scheduling Algorithm

### FCFS (First-Come First-Serve)

- 먼저 온 프로세스가 먼저 CPU 제어권을 갖도록 한다.
- nonpreemptive 한 스케쥴링 방법이다.
    - 공정하지만 비효율적이다.
- P1, P2, P3 의 CPU burst time 을 각 24, 3, 3 이라고 가정해보자
    - Waiting time -> `P1 = 0`, `P2 = 24`, `P3 = 27`
        - `P1` 의 Turnaround time 은 24 다.
    - Average Waiting time = 17
- 프로그램의 도착 시간을 바꿔보자
    - Waiting time -> `P2 = 0`, `P3 = 3`, `P1 = 6`
    - Average Waiting time = 3
- **Convoy effect**
    - short process behinds long process

### SJF (Shortest Job First)

- 각 프로세스의 다음번 CPU burst time 을 가지고 스케쥴링에 활용한다.
- Nonpreemptive
    - 한번 CPU 를 잡으면 중간에 뺏지 않고 작업이 끝날 때 까지 대기한다.
- Preemptive
    - 작업을 수행하다가 더 짧은 CPU burst time 을 가진 프로그램이 도착하면 중간에 CPU 제어권을 뺏는다.
    - 이러한 방법을 SRJF (Shortest Remaining Job First)라고 한다.
    - SRJF 가 SJF 보다 더 효율적인 전략이다.
- Minimum average waiting time 을 보장한다.
- 하지만 SJF 엔 두 가지 문제점이 있다.
    - **Starvation**
        - CPU burst time(CPU 사용 시간)이 긴 작업의 우선 순위가 영원히 밀려서 처음 스케쥴러에 도착했어도 영영 작업을 끝내지 못할 수 있다.
    - **CPU burst time 의 정확한 예측이 불가능하다.**
        - 과거의 CPU 사용 시간을 토대로 추정은 할 수 있다.
        - 추정은 exponential averaging 공식을 사용한다.

### Priority Scheduling

- 우선순위가 가장 높은 프로세스에게 CPU 를 준다.
    - 각 프로세스에게 priority number 를 부여한다.
    - priority number 가 작을수록 priority 가 높다.
- SJF 도 일종의 Priority Scheduling 방식이다.
- Starvation 문제가 있다.
    - 이 문제를 해결하기 위해 `Aging` 기법을 사용한다.
    - 시간이 지날 수록 priority 값이 증가하도록 설계한다.

### RR (Round Robin)

- 각 프로세스는 동일한 할당 시간(time quantum)을 가진다.
- 할당 시간이 지나면 프로세스는 선점당하고 Ready Queue 의 제일 뒤에서 대기한다.
- 할당 지나치게 시간을 크게 설정하면 FCFS 방식이 되어버린다.
- 할당 지나치게 시간을 작게 설정하면 conext switc 오버헤드가 너무 커진다.
- RR 방식은 SJF 방식보다 average turnaround time 은 길지만, response time(최초로 CPU 를 얻기 위해 대기한 시간) 은 더 짧다.

### Multilevel Queue

- 프로세스를 우선순위에 따라 나누어 여러 줄을 세운다.
    - system process
    - interactive process
    - interactive editing process
    - batch process
    - student process
- 우선순위가 높은 Queue 가 비어있으면 그 하위 우선순위를 가진 Queue 의 프로세스의 차례가 된다.
- Ready Queue 를 여러 개로 분할한다.
    - foregroundQueue(interactive)
    - backgroundQueue(batch - no human interaction)
- 각 큐는 독립적인 스케쥴링 알고리즘을 가진다.
    - foreground - RR
        - RR 을 사용해서 응답 시간을 줄인다.
    - background - FCFS
        - 사람과 interaction 을 하지 않기 때문에 응답 시간이 크게 중요하지 않다.
        - 컨텍스트 스위칭 비용을 줄이기 위해 FCFS 알고리즘을 사용한다.
- 또한 Queue 에 대한 스케쥴링도 필요하다.
    - Fixed priority scheduling
        - 우선순위가 높은 Queue 가 반드시 먼저 스케쥴링된다.
        - starvation 문제 우려
    - Time slice
        - 각 Queue 에 CPU time 을 적절한 비율로 할당한다.

> Multilevel Queue 방식은 단일 프로세서에서도 사용할 수 있다. 다중 프로세서에선 병렬 처리가 가능하기 때문에 좀 더 효율적이다.

### Multilevel Feedback Queue

- 프로세스가 다른 Queue 로 이동할 수 있다. (신분 상승 가능)
- Aging 을 이와 같은 방식으로 구현할 수 있다.
- Multilevel Feedback Queue 를 구현하기 위해선 여러가지 기준이 필요하다.
    - 어떤 프로세스를 어떤 Queue 에 넣을지
    - 각 Queue 는 어떤 스케쥴링 알고리즘을 사용할 지
    - 어떤 프로세스를 어떤 기준에 따라 어떤 Queue 로 이동시킬지
- 일반적으론 다음과 같은 방법을 사용한다.
    - 우선 가장 우선순위가 높은 Queue 에 프로세스를 추가한다.
    - 프로세스는 RR 알고리즘을 사용하며 time quantum 을 짧게 설정한다.
    - time quantum 이 끝나도 작업이 끝나지 않으면 하위 Queue 로 이동시킨다.
    - 하위 Queue 는 time quantum 을 좀 더 여유롭게 설정한다.
    - 하지만 여기서도 작업이 끝나지 않으면 FCFS 를 사용하는 최하위 Queue 로 이동시킨다.

### Multiple-Processor Scheduling

- Homogeneous processor 인 경우
    - Queue 에 한 줄로 세워서 각 프로세서가 알아서 꺼내가게 할 수 있다.
- Load sharing
    - 일부 프로세서에 job 이 몰리지 않도록 부하를 적절히 공유하는 메커니즘이 필요하다.
- Symmetric Multiprocessing (SMP)
    - 각 프로세서가 각자 알아서 스케쥴링을 결정한다. (모든 프로세서가 대등한 경우)
- Asymmetric Multiprocessing
    - 하나의 프로세서가 시스템 데이터의 접근과 공유를 책임지고 나머지 프로세서가 그에 따르는 방식이다.

### Real-Time Scheduling

- Hard real-time systems
    - 정해진 시간 안에 반드시 끝내도록 스케쥴링
- soft real-time computing
    - soft real-time task 는 일반 프로세스에 비해 높은 priority 를 갖도록 스케쥴링

### Thread scheduling

- Local Scheduling
    - User level thread 의 경우 사용자 수준의 thread library 에 의해 어떤 스레드를 스케쥴링할 지 결정한다.
    - 운영체제가 스레드의 존재를 알지 못한다.
    - 사용자 프로세스 내부에서 스케쥴링이 결정된다.
- Global Scheduling
    - Kernel level thread 의 경우 일반 프로세스와 마찬가지로 커널의 단기 스케쥴러가 어떤 스레드를 스케쥴링할 지 결정한다.
    - 운영체제가 스레드의 존재를 알고 있다.
    - 운영체제가 직접 스케쥴링을 담당한다.

### Algorithm Evaluation

- 스케쥴링 알고리즘의 성능을 측정하는 방법들이 여러 개 있다.
- 공식을 통해 직접 계산하는 방법
- 실측하는 방법
- 모의 실험을 하는 방법

# Process Synchronization

## 데이터의 접근

- 컴퓨터 시스템 내부에서 데이터에 어떻게 접근하는가?
- Memory(Storage boxt) 에 있는 데이터에 연산이 필요하다면?
- 데이터를 CPU(Execution box) 로 전달해서 연산한다.
- 연산 결과를 다시 메모리로 전달한다.
- 이런 관계(S-Box, E-Box)는 CPU, Memory 간 뿐만 아니라, 컴퓨터 내부와 디스크, 프로세스와 프로세스의 주소 공간에도 해당된다.
- 만약 S-Box 를 공유하는 E-Box 가 여럿 있는 경우 Race Condition 의 가능성이 생긴다.
    - Multiprocessor System 환경에서
    - 공유 메모리를 사용하는 프로세스들 간
    - 커널 내부 데이터에 접근하는 루틴들 간에 Race condition 이 발생할 수 있다.

## OS 에서의 race condition

- kernel 수행 중 인터럽트 발생 시
    - kernel 에 있는 공유 데이터를 서로 다른 프로세스가 변경할 때
    - A 프로세서가 데이터를 변경하고 있을 땐 다른 프로세스가 interrupt 를 걸더라도 해당 작업이 끝날 때 까진 interrupt 를 disable 처리하는 방법으로 race condition 을 해결할
      수 있다.
    - 하지만 이는 비효율적인 방법이다.
- Process 가 system call 을 하여 kernel mode 로 수행중인데 context switch 가 일어나는 경우
    - 두 프로세스의 address space 간에는 data sharing 이 없다.
    - 그러나 system call 을 하는 동안에는 kernel address space 의 data 에 접근하게 된다. (공유 자원)
    - 이 작업 중간에 CPU 를 가져가버리면 race condition 이 발생한다.
    - 이러한 문제는 kernel 모드의 data 를 수정하는 동안에는 CPU 제어권을 중간에 가져가지 못하도록 설정하면 문제를 해결할 수 있다.
- Multiprocessor 에서 shared memory 내의 kernel data
    - 멀티 프로세스의 경우 인터럽트를 무효화하는 방법으론 race condition 문제를 해결할 수 없다.
    - 한번에 하나의 CPU 만 커널에 접근할 수 있게 만들거나
    - 커널 내부에 있는 각 공유 데이터에 접근할 때마다 그 데이터에 lock 을 걸어버리는 방법이 있다.

### 커널 모드에서 공유되는 자원

- PCB
    - PCB 는 프로세스의 상태, 프로그램 카운터, 레지스터 값, 스케쥴링 정보 등을 저장하는 커널 데이터 구조다.
    - 여러 프로세스가 실행되면서 컨텍스트 스위칭이 발생할 때 PCB 가 갱신된다.
    - 두 개의 프로세스가 동시에 PCB 를 갱신하려고 하면 Race condition 이 발생할 수 있다.
    - PCB 는 프로세스마다 생성되는 고유한 자료구조 아닌가? 어떻게 서로 다른 프로세스가 특정 PCB 에 동시에 접근할 수 있을까?
        - 컨텍스트 스위칭이 발생할 때 커널은 PCB 에 프로세스의 현재 상태를 저장하고 다른 프로세스의 PCB 를 불러와 실행한다.
            - 스케쥴러가 PCB 의 상태를 갱신하는 동안 다른 **커널 모듈**이 PCB 에 접근하려고 한다면 경합이 발생할 수 있다.
            - 커널 모듈은 커널의 기능을 확장하거나 특정 I/O 기능을 지원하는 독립적인 코드 단위다.
            - 커널은 모듈을 통해 동적으로 기능을 추가하거나 제거할 수 있어 유연성과 확장성을 유지한다.
            - 커널 모듈의 예
                - 디바이스 드라이버
                - 파일 시스템 모듈
                - 네트워크 프로토콜 모듈
                - 보안 및 모니터링 기능(방화벽)
        - PCB 자체는 프로세스마다 고유하지만, PCB 를 관리하는 자료구조인 PCB 리스트나 테이블은 커널에서 공유된다.
        - 멀티 코어 상황에서 서로 다른 CPU 가 동일한 프로세스의 상태를 변경하려고 하면 경합이 발생할 수 있다.
- 커널 스택
    - 각 프로세스는 커널 모드에서 사용할 커널 스택을 가진다.
    - 커널 스택에는 시스템 콜 실행 중 저장된 레지스터 값이나 호출 스택이 저장된다.
    - 커널 모드에서 다른 프로세스가 동일한 커널 스택에 접근하면 문제가 발생한다.
- 파일 시스템 테이블 및 I/O 버퍼
    - 두 프로세스가 같은 파일을 동시에 쓰려고 할 때 파일 시스템 테이블이 잘못 갱신되면 데이터 손상이 발생할 수 있다.
- 스케쥴링 큐
    - 커널은 실행 가능한 프로세스를 관리하기 위해 스케쥴링 큐를 사용한다.
    - 두 개의 프로세스가 동시에 실행 대기 큐를 수정하면 큐의 상태가 일관되지 않을 수 있다.
- 메모리 관리 데이터 구조 (페이지 테이블)
    - 커널은 메모리 관리 정보를 저장하기 위해 페이지 테이블이나 프레임 리스트와 같은 데이터 구조를 사용한다.
    - 여러 프로세스가 페이지 할당 또는 메모리 접근 권한을 갱신할 때 동일한 페이지 테이블을 수정할 수 있다.
    - 두 개의 프로세스가 동시에 페이지 테이블을 수정하면 일관되지 않은 메모리 매핑이 발생할 수 있다.
- 네트워크 소켓 테이블 및 패킷 버퍼
    - 네트워크 연결 시 커널은 소켓 테이블, 네트워크 버퍼를 통해 데이터 전송을 관리한다.
    - 여러 프로세스가 같은 네트워크 인터페이스를 통해 데이터를 보내거나 받을 때 충돌이 발생할 수 있다.
- 시스템 콜 테이블
    - 커널은 시스템 콜을 처리하기 위해 시스템 콜 테이블을 사용한다.
    - 시스템 콜 테이블이 잘못 수정되거나 동시에 접근될 경우 시스템 콜이 잘못 호출될 수 있다.

## Critical Section Problem (임계 영역 문제)

- 각 프로세스의 code segment 에는 공유 데이터를 접근하는 코드인 critical section 이 존재한다.
- 하나의 프로세스가 critical section 에 있을 때는 다른 프로세스가 critical section 에 들어갈 수 없어야 한다.

## 임계 영역 문제 해결법의 충족 조건

- **Mutual Exclusion** (상호 배제)
    - 프로세스 P1 이 임계 영역 부분을 수행중이면 다른 모든 프로세스들은 해당 임계 영역에 접근할 수 없다.
- **Progress**
    - 임계 영역에 어떠한 프로세스가 없는 상황이라면 특정 프로세스는 임계 영역에 접근할 수 있다.
- **Bounded Waiting**
    - 프로세스가 임계 영역에 들어가고자 요청을 한 이후 그 요청이 허용될 때 까지 다른 프로세스가 임계 영역에 들어가는 횟수에 한계가 있어야 한다.

## 임계 영역 문제 해결을 위한 알고리즘

### Semaphore

- 임계 영역에 아무런 프로세스가 없을 때 락을 걸고 임계 영역에 접근하는 방식을 추상화 한 자료구조다.
- 세마포어는 정수형 변수와 두 가지 연산으로 이루어져있다.
    - 세마 포어는 카운터 변수를 기반으로 동작하며 동시에 접근할 수 있는 임계 영역의 수를 제한한다.
- S 는 공유 자원의 갯수다.
- P 연산
    - 공유 자원을 획득하는 과정
    - `if S >= 0` 이면 `S--;`
    - 이후 임계 영역에 접근한다.
- V 연산
    - 공유 자원을 반납하는 과정
    - `S++;`
- 임계 영역에 접근할 수 없는 경우 while 문을 계속 돌면서 대기한다. (다른 프로세스가 V 연산을 할 때 까지)
- 이러면 대기중인 프로세스가 계속 CPU 를 잡아먹고 있는 비효율적인 상황이 발생한다. (Busy-Wait -> Spin Lock)

### Block & Wake-Up (Sleep Lock)

- 초기 Spin Lock 형태로 구현되었던 Semaphore 방식의 Busy-wait 문제를 해결하기 위해 Block & Wake-Up 방식이 도입되었다.
    - 그러나 Lock 유지 시간이 매우 짧은 경우 여전히 스핀락을 사용하기도 한다.
- Block
    - 커널은 block 을 호출한 프로세스를 suspend 시킨다.
    - 이 프로세스의 PCB 를 semaphore 에 대한 wait queue 에 넣는다.
- wakeup
    - block 된 프로세스 P 를 wakeup 시킨다.
    - 이 프로세스의 PCB 를 ready queue 로 옮긴다.

```java
// P 연산
public void p() {
    S.value--;
    if (S.value < 0) {
        block();
    }
}
```

- 우선 공유 자원의 갯수를 줄이고 임계 영역 접근을 시도한다.

```java
// V 연산
public void v() {
    S.value++;
    if (S.value <= 0) {
        wakeup(P);
    }
}
```

- 작업을 완료한 프로세스가 자원을 반납했음에도 공유 자원의 갯수가 0 이하라면 누군가 락을 얻기위해 대기하고 있다는 뜻이다.
- Block and Wake-Up 방식도 오버헤드가 존재한다.
- 프로세스의 상태를 변경해줘야 하기 때문에 이에 따른 비용이 발생한다.
- 만약 Critical Section 의 길이가 매우 짧은 경우 Block/Wakeup 오버헤드가 busy-wait 오버헤드보다 더 커질 수 있다.
- 일반적론 block/wakeup 방식이 더 좋다.

### Deadlock & Starvation

- **Deadlock**
    - 둘 이상의 프로세스가 서로 상대방에 의해 충족될 수 있는 이벤트를 무한히 기다리는 현상
- 하드디스크 A 의 내용을 하드디스크 B 로 복사하는 작업을 해야한다고 가정해보자.
    - 작업을 완료하기 위해선 하드디스크 A, B 의 세마포어가 모두 필요하다.
    - 프로세스 P1 이 CPU 를 얻어서 하드디스크 A 의 세마포어를 획득했다.
    - CPU 제어권이 프로세스 P2 로 넘어가고, P2 가 하드디스크 B 의 세마포어를 획득했다.
    - P1 은 B 의 세마포어를 얻기 위해 대기하고, P2 는 A 의 세마포어를 얻기 위해 대기한다.
    - 이러한 문제는 자원을 획득하는 순서를 정해준다면 해결된다.
    - 하드디스크 A 의 세마포어를 획득해야 다음에 하드디스크 B 의 세마포어를 획득하도록 설계해야 한다.
- **Starvation**
    - Deadlock 과 비슷한 상황이다.
    - 특정 프로세스들이 서로 Deadlock 에 걸려서 다른 프로세스는 영원히 작업을 처리할 수 없는 상황을 뜻한다.

> 세마포어를 획득한다는 것은 공유 자원에 접근할 권리를 얻는다는 뜻이다.

## Synchronization problem of critical section

### Bounded-Buffer Problem(Producer-Consumer Problem)

- 제한된 크기의 공유 버퍼에 생산자와 소비자가 공유 자원을 생산하거나 소비할 때의 상황을 다룬다.
- 생산자는 공유 버퍼에 공유 자원을 추가하는 역할을 한다.
- 소비자는 공유 버퍼에서 공유 자원을 가져다 사용하는 역할이다.
- 공유 버퍼가 가득 차버리면 생산자가 버퍼에 접근하더라도 공유 자원을 추가할 수 없다. 공유 버퍼에 자리가 생길 때 까지 기다려야 한다.
    - Empty 버퍼가 있는지 확인하고 없으면 기다린다.
    - 버퍼에 자리가 생기면 공유 데이터에 lock 을 건다.
    - 버퍼에 데이터를 추가한다. (버퍼 조작)
    - lock 을 푼다.
    - full buffer 하나 증가. (소비자를 깨우는 역할)
- 공유 버퍼가 비어버리면 소비자가 버퍼에 접근하더라도 공유 자원을 소비할 수 없다. 공유 버퍼에 자원이 추가될 때 까지 기다려야 한다.
    - full buffer 가 있는지 확인하고 없으면 기다린다.
    - 버퍼에 데이터가 추가되면 공유 데이터에 lock 을 건다.
    - 버퍼에서 데이터를 소비한다. (버퍼 조작)
    - lock 을 푼다.
    - empty buffer 하나 증가. (생산자를 깨우는 역할)
- 실제 생산자-소비자 문제가 발생하는 예시
    - 웹 브라우저와 동영상 스트리밍 서버
        - 네트워크 속도가 너무 느리면 브라우저는 버퍼에 있는 스트리밍 데이터를 모두 소진하고 영상 재생이 멈출 수 있다.
        - 네트워크 속도가 너무 빠르면 브라우저 버퍼가 가득 차서 스트리밍 서버가 데이터를 전송할 수 없게 된다.
    - 프린터와 여러 응용 프로그램 간의 관계
    - 웹 서버와 DB
        - 웹 서버의 요청이 너무 많아지면 DB 가 요청을 처리하는 속도가 느려질 수 있다.
        - DB 가 빠르게 처리하더라도 웹 서버로 부터의 요청이 적으면 리소스를 낭비하게 된다.
    - 로그 파일 생성기와 로그 분석기

### Readers-Writers Problem

- 한 프로세스가 DB 에 write 중일 때 다른 프로세스가 접근하면 안된다.
- read 는 동시에 여럿이 해도 된다.
    - 생산자-소비자 문제와의 차이점이다. 생산자-소비자 문제는 생산, 소비 과정에 모두 동기화가 필요했다.
- 해결 방법
    - Writer 가 DB 에 접근 허가를 아직 얻지 못한 상태에서는 모든 대기중인 Reader 들을 다 DB 에 접근하게 해준다.
    - Writer 는 대기중인 Reader 가 하나도 없을 때 DB 접근이 허용된다.
    - 일단 Writer 가 DB 에 접근 중이면 Reader 들은 접근이 금지된다.
    - Writer 가 DB 에서 빠져나가야만 Reader 의 접근이 허용된다.
- 공유 자원
    - DB
    - readCount (현재 DB 에 접근 중인 Reader 의 수)
- Synchronization variables (binary semaphore)
    - mutex
        - 공유 변수 readCount 를 접근하는 코드
    - db
        - reader 와 writer 가 공유 DB 자체를 올바르게 접근하게 만드는 역할
        - 최초로 DB 에 접근한 reader 가 P(db)로 DB 에 락을 건다.
        - 마지막 reader 가 DB 에서 빠져나가려할 때 V(db)로 DB 에 걸린 락을 푼다.

```
Reader 입장

P(mutex); //readCount 는 공유 자원이기 때문에 동기화 작업이 필요하다.
readCount++;
if (readCount == 1) {
    P(db);
}
V(mutex);

~

P(mutex);
readCount--;
if (readCount == 0) {
    V(db);
}
V(mutex);
```

> reader 가 P(mutex) 를 진행하는 동안 writer 가 P(db)를 먼저 해버리면, reader 는 P(db)를 수행하는 과정에 block 되어버린다.

### Dining-Philosophers Problem

- 원탁에서 식사를 하려는 철학자들이 있는 상황이다.
- 식사를 하기 위해선 오른쪽 젓가락과 왼쪽 젓가락이 모두 필요한데, 젓가락은 공유 자원이다.
- A 철학자가 왼쪽 젓가락을 보유한 채 오른쪽 젓가락을 가져오기 위해 대기한다.
- B 철학자가 오른쪽 젓가락을 보유한 채 왼쪽 젓가락을 가져오기 위해 대기한다.
- 이러면 Deadlock 이 발생한다.
- 해결 방안
    - 젓가락이 각각 5개 씩이라면 식탁에 4명의 철학자만 앉을 수 있게 한다.
    - 또는 양쪽 젓가락을 모두 짚을 수 있는 상황에만 젓가락을 짚을 수 있게 만든다.
    - 또는 짝수 철학자는 왼쪽 젓가락 부터, 홀수 철학자는 오른쪽 젓가락 부터 짚도록 젓가락 획득 순서를 정한다.

## Monitor

- Semaphore 의 문제점
    - 코딩하기가 힘들다.
    - 자발적 협력이 필요하다.
    - 한번의 실수가 모든 시스템에 치명적인 영향을 미친다.
        - P 연산 이후 V 연산을 진행해야 하는데 V 연산 이후 P 연산을 하도록 잘못 코딩한 상황
    - 이렇게 실수를 했을 때 검증하는 작업을 하기가 힘들다.
- Monitor 는 동시 수행중인 프로세스 사이에서 abstract data type 의 안전한 공유를 보장하기 위한 high-level synchronization construct 이다.
- Monitor 는 모니터 내부에 공유 데이터와 공유 데이터에 접근하기 위한 프로시저를 내부에 정의하고 있다.
- 모니터 내에서는 한번에 하나의 프로세스만이 활동할 수 있다.
    - 모니터에 접근하는 순간 자동으로 (모니터에 접근하기 위한)락이 걸리고 나가는 순간 자동으로 락이 풀린다.
- 따라서 세마포어 방식과 달리 개발자가 명시적으로 lock 을 관리할 필요가 없다.
    - 프로그래머가 동기화 제약 조건을 명시적으로 코딩할 필요가 없다.
- 프로세스가 모니터 안에서 기다릴 수 있도록 하기 위해 condition variable 을 사용한다.
- condition variable 은 wait 과 signal 연산에 의해서만 접근할 수 있다.
    - condition variable x 에 대해 `x.wait()` 을 호출한 프로세스는 다른 프로세스가 `x.signal()` 을 invoke 하기 전까지 suspend 된다.
    - `x.signal()`은 정확하게 하나의 suspend 된 프로세스를 resume 한다.
    - suspend 된 프로세스가 하나도 없다면 아무일도 일어나지 않는다.
    - condition variable 은 자바에서의 `ReentrantLock`, `Condition` 을 생각하면 된다.
    - condition variable 은 값을 가지지 않고, 자신의 큐에 프로세스를 메달아서 sleep 시키거나 큐에서 프로세스를 깨우는 역할을 한다.

> 모니터는 주로 객체 지향 프로그램에서 지원하는 방식이고, 프로그램 마다 모니터가 구현되는 방식은 상이할 수 있다.

### 자바 코드로 철학자 문제 이해하기 - Monitor 방식

- 철학자들은 원탁에 앉아있다.
- 각 철학자는 양쪽에 있는 포크(리소스)를 사용해야 한다.
- 철학자가 포크를 잡으려면 좌측과 우측의 포크를 모두 사용할 수 있어야 한다.
- 식사를 마치면 포크를 내려놓아 다른 철학자가 사용할 수 있도록 한다.
- 모니터를 활용하여 동기화를 관리하며 각 철학자의 상태를 추적한다.
    - THINKING, HUNGRY, EATING
- 철학자가 식사하려면 자신과 양 옆의 철학자가 모두 THINKING 상태여야 한다.
- 조건을 만족하지 않으면 대기(wait()), 조건이 만족되면 식사(notify())

```java
class DiningPhilosophersMonitor {
    private enum State {THINKING, HUNGRY, EATING}

    private final State[] states;
    private final int numPhilosophers;

    public DiningPhilosophersMonitor(int numPhilosophers) {
        this.numPhilosophers = numPhilosophers;
        states = new State[numPhilosophers];
        for (int i = 0; i < numPhilosophers; i++) {
            states[i] = State.THINKING;
        }
    }

    public synchronized void pickUpForks(int philosopherId) throws InterruptedException {
        states[philosopherId] = State.HUNGRY;
        test(philosopherId);
        while (states[philosopherId] != State.EATING) {
            wait();
        }
    }

    public synchronized void putDownForks(int philosopherId) {
        states[philosopherId] = State.THINKING;
        test((philosopherId + numPhilosophers - 1) % numPhilosophers); // Test left neighbor
        test((philosopherId + 1) % numPhilosophers); // Test right neighbor
    }

    private void test(int philosopherId) {
        int left = (philosopherId + numPhilosophers - 1) % numPhilosophers;
        int right = (philosopherId + 1) % numPhilosophers;
        if (states[philosopherId] == State.HUNGRY &&
                states[left] != State.EATING &&
                states[right] != State.EATING) {
            states[philosopherId] = State.EATING;
            notifyAll(); // Notify threads waiting for forks
        }
    }
}
```

- 자신의 상태와 양쪽 이웃의 상태를 확인하여 리소스를 얻기 때문에 Deadlock 을 회피할 수 있다.

# Deadlock

- 일련의 프로세스들이 서로가 가진 자원을 기다리며 block 된 상태를 의미한다.
- 여기서의 자원은 하드웨어, 소프트웨어 자원을 모두 포함한다.

## 데드락이 발생하는 조건

- **Mutual Exclusion**
    - 매 순간 하나의 프로세스만이 자원을 사용할 수 있음
- **No preemption**
    - 프로세스는 자원을 스스로 내어놓을 뿐 강제로 빼앗기지 않는다.
- **Hold and wait**
    - 자원을 가진 프로세스가 다른 자원을 기다릴 때 보유 자원을 놓지 않고 계속 가지고 있는 상태
- **Circular wait**
    - 자원을 기다리는 프로세스 간에 사이클이 형성되어야 한다.
    - 자원에 하나의 인스턴스만 있다면 사이클이 형성되었을 때 데드락이 발생한다.
    - 자원에 여러 개의 인스턴스가 있다면 사이클이 형성되더라도 데드락이 발생하지 않을 수 있다.
- 위 네가지 조건을 모두 충족해야 데드락이 발생한다.

## 데드락의 처리 방법

### Deadlock Prevention

- 자원 할당 시 데드락의 4가지 필요 조건 중 어느 하나가 만족되지 않도록 한다.
    - Hold and wait
        - 프로세스 시작 시 모든 필요 자원을 할당받게 하는 방법
        - 자원이 필요할 경우 보유 자원을 내려 놓고 다시 요청하도록 하는 방법
    - No preemption
        - process 가 어떤 자원을 기다려야 하는 경우 이미 보유한 자원을 선점한다.
        - 모든 필요한 자원을 얻을 수 있을 때 그 프로세스는 다시 시작된다.
        - state 를 쉽게 저장하고 복구할 수 있는 자원에서 주로 사용한다. (CPU, memory)
    - Circular wait
        - 모든 자원 유형에 대해 할당 순서를 정해서 정해진 순서대로만 자원을 할당한다.
- 하지만 해당 방법은 utilization, throughput 을 감소시키고 starvation 문제를 유발할 수 있다.
- 발생하지도 않은 데드락을 미리 예방하기 위해 너무 많은 것을 희생하고 있는 셈이다.

### Deadlock Avoidance

- 자원 요청에 대한 부가정보를 이용해서 자원 할당이 deadlock 으로 부터 안전한지를 동적으로 조사하고 안전한 경우에만 자원을 할당한다.
- 프로세스가 생성될 때 사용할 수 있는 자원의 총량을 미리 정의하고 시작해서 deadlock 이 발생할 우려가 있다면 재고가 있더라도 자원을 할당하지 않는다.
- **Banker`s algorithm**
    - 프로세스에게 할당된 자원의 갯수 -> Allocation
    - 프로세스가 최대로 할당받을 수 있는 자원의 갯수 -> Max
    - 현재 가용한 자원의 갯수 -> Available
    - Max - Allocation -> Need
    - 프로세스 P 의 Allocation 이 1, Max 4, Available 2 라고 가정해보자.
        - 프로세스 P 가 1개의 자원을 요청한다고 했을 때 Available 은 2개 이기 때문에 충분히 자원을 할당해줄 수 있는 상황이다.
        - 하지만 프로세스 P 가 추후 최대로 요청할 수 있는 자원의 갯수인 Need 가 현재 3으로 Available 을 초과한다.
        - 이경우 현재 요청엔 데드락이 발생하지 않음에도 미래에 데드락이 발생할 위험이 있으므로 자원을 할당해주지 않는다.
        - Banker`s Algorithm 은 굉장히 보수적이고 비관적인 방식이다.
        - 따라서 다른 프로세스가 자원을 반납하여 Available 의 갯수가 늘어나는 것은 차치하고 현재의 Available 만을 기준으로 자원을 배분한다.
        - 자원이 남아도는 데도 자원을 배분하지 않기 때문에 굉장히 비효율적인 방법이다.
- **Deadlock graph**(자원 할당 그래프)
    - 그래프를 통해 데드락의 여부를 확인하는 방법이다.

### Deadlock detection and recovery

#### Detection

- Resource type 당 single instance 인 경우
    - 자원 할당 그래프에서의 cycle 이 곧 deadlock 을 의미한다.
- Resource type 당 multiple instance 인 경우
    - Banker`s algorithm 과 비슷하게 테이블을 활용한 알고리즘을 사용한다.
- Wait-for graph
    - Wait-for graph 에 사이클이 존재하는지 주기적으로 조사한다.

#### Recovery

- **Process termination**
    - 데드락에 연루된 모든 프로세스를 제거하는 방법
    - 데드락에 연루된 하나의 프로세스를 먼저 종료시켜보고 해결되지 않으면 다른 프로세스를 종료시켜나가는 방법.
- **Resource Preemption**
    - 비용을 최소화할 프로세스(victim)를 찾아서 자원을 뺏어오는 방법이다.
    - 동일한 프로세스가 계속해서 victim 으로 선정되는 경우 starvation 문제가 발생할 수 있다.
    - starvation 문제를 해결하기 위해 자원을 선점당한 횟수를 고려해서 victim 우선순위를 낮추기도 한다.

### Deadlock Ignorance

- Deadlock 이 일어나지 않는다고 생각하고 아무런 조치도 취하지 않는다.
- Deadlock 은 매우 드물게 발생하므로 deadlock 에 대한 조치 자체가 더 큰 오버헤드일 수 있다.
- 대부분의 운영 체제가 Deadlock Ignorance 방식을 채용하고 있다.

# Memory Management

## Logical vs Physical Address

- **Logical Address**(Virtual Address)
    - 프로세스마다 독립적으로 가지는 주소 공간
    - 각 프로세스마다 0번지부터 시작한다.
    - **CPU 가 보는 주소는 Logical Address 다.**
        - CPU 가 읽는 Instruction 에는 논리적 주소가 사용된다.
- **Physical Address**
    - 메모리에 실제 올라가는 위치
- 주소 바인딩
    - 주소를 결정하는 것이다.
    - Symbolic Address -> Logical Address -> Physical Address

> Symbolic Address 는 개발자가 사용하는 주소 값이다. <br/>
> 개발자는 숫자 값으로 이루어진 메모리를 직접 다룰 일이 거의 없다. 변수나 객체로 이루어진 주소 값을 사용한다.

### 물리적 주소 바인딩은 언제 이루어지는가?

- **Compile time binding**
- **Load time binding**
    - 컴파일 시점에는 논리적인 주소 값만 결정이 된 상태에서 프로그램을 실행하면(프로세스가 메모리에 올라가면) 물리적 주소가 바인딩 된다.
- **Execution time binding**(Runtime binding)
    - 프로그램이 시작됐을 때 물리적 주소가 바인딩되는 건 똑같지만 해당 주소 값이 런타임동안 계속 바뀔 수 있다.
    - CPU 가 주소를 참조할 때 마다 binding 을 점검한다. (address mapping table)
    - 하드웨어적인 지원이 필요하다.

### MMU(Memory Management Unit)

- MMU 는 실행중인 프로그램의 논리적 주소를 물리적 주소로 동적으로 변환해주는 하드웨어다.
- 프로세스는 논리적 주소만 인식하고, 운영체제와 MMU 가 실제 물리적 주소를 관리하기 때문에 안정적이고 효율적인 메모리 활용이 가능해진다.

### Dynamic Relocation

- CPU 가 logical address 로 데이터를 요청한다.
- MMU 가 logical address 를 받는다.
- **relocation register**(base register) 와 **limit register** 로 logical address 를 physical address 로 변환한다.
- MMU 는 base register 에 프로그램의 시작 주소를 저장해둔다. (실제 메모리 상의 시작 위치)
    - 논리적 주소가 346 번이고, base register 에 저장된 프로그램의 시작 주소가 14000 번이라면 14346 번을 반환한다.
- limit register 는 프로그램 주소의 최대 크기(논리적 주소의 범위)를 저장해둔다.
    - 프로그램 주소의 최대 크기가 1000 인데 1001 번지 논리적 주소로 데이터를 요청하면 다른 프로그램의 데이터가 불러와 질 위험성이 있다.
    - trap 이 걸린다. addressing error 가 발생한다.

> 이러한 방식은 contiguous allocation 을 가정한 내용이다.

### Dynamic Loading

- 프로세스 전체를 메모리에 미리 다 올리는 것이 아니다.
- 해당 루틴이 불려질 때 메모리에 load 하는 것이다.
- memory utilization 이 향상된다.
- 가끔씩 사용되는 많은 양의 코드의 경우 유용하다.
    - ex) 오류 처리 루틴
- 운영체제의 특별한 지원 없이 프로그램 자체에서 구현할 수도 있다.
    - 운영체제는 paging 기법으로 dynamic loading 을 지원한다.

### Overlays

- 메모리에 프로세스의 부분 중 실제 필요한 정보만을 올린다.
- 프로세스의 크기가 메모리보다 클 때 유용하다.
- 용어의 의미로만 따지면 Dynamic Loading 과 다를게 없지만 Dynamic Loading 기법을 프로그래가 수작업으로 구현했다는 의미로 사용된다.
    - Manual Overlay 로 불리기도 한다.

### Swapping

- 프로세스를 일시적으로 메모리에서 **backing store** 로 쫓아내는 것을 의미한다.
- backing store (swap area)
    - 많은 사용자의 프로세스 이미지를 담을 만큼 충분히 빠르고 큰 저장 공간이다. (디스크)
- Swap in / Swap out
    - 일반적으로 중기 스케쥴러(Swapper) 에 의해 swap out 시킬 프로세스를 선정한다.
        - priority-based CPU scheduling algorithm 이 사용된다.
    - Execution time binding 의 경우 Swap in 과정에서 빈 메모리 영역 아무 곳에나 프로세스를 올릴 수 있다.
    - Swap time 의 대부분은 transfer time 이다.
    - Swap out 된다고 해서 프로세스 전체가 메모리에서 제거되는 것은 아니다.
    - 프로세스는 페이지 단위로 메모리에 올라가기 때문에 페이지 별로 Swap 이 결정된다.

### Dynamic Linking

- Linking 은 실행 시간까지 미루는 기법을 의미한다.
- **Static linking**
    - 라이브러리가 프로그램의 싪행 파일 코드에 포함된다.
    - 동일한 라이브러리를 각각의 프로세스가 메모리에 올리므로 메모리 낭비가 심해진다.
    - ex) printf 함수의 라이브러리 코드
- **Dynamic linking**
    - 라이브러리가 실행 시 연결(link)된다.
    - 라이브러리 호출 부분에 라이브러리 루틴의 위치를 찾기 위한 stub 이라는 작은 코드를 둔다.
    - 라이브러리가 이미 메모리에 있으면 그 루틴의 주소로 가고 없으면 디스크에서 읽어온다.(라이브러리는 별도의 파일로 존재한다.)
    - 운영체제의 도움이 필요하다.
    - 이러한 라이브러리를 Shared Library 라고 하며 윈도우에서는 dll 파일 확장자를 사용한다. (리눅스는 shared object)

## Allocation of Physical Memory

- 물리적 메모리는 일반적으로 두 영역으로 나뉘어 사용한다.
    - **OS 상주 영역**
    - **사용자 프로세스 영역**
- 사용자 프로세스 영역의 할당 방법
    - **Contiguous allocation**
        - 각각의 프로세스가 메모리의 연속적인 공간에 적재되도록 하는 것
        - **Fixed partition allocation**
        - **variable partition allocation**
    - **Noncontiguous allocation**
        - 하나의 프로세스가 메모리의 여러 영역에 분산되어 올라갈 수 있다.
        - **Paging**
        - **Segmentation**
        - **Paged Segmentation**

## Contiguous Allocation

### Fixed Partition Allocation(고정 분할 방식)

- 메모리를 몇 개의 영구적 분할로 나눈다.
- 프로그램을 메모리에 올릴 때 크기기에 맞는 메모리에 맞춰 끼우는 방식이다.
- 이렇게 되면 메모리 조각(빈 메모리)이 발생하게 된다.
    - 외부 조각
        - 프로그램에 할당되지 않은 빈 메모리다.
    - 내부 조각
        - 프로그램에게 할당은 됐지만 사용되지 않는 빈 메모리다.
- 동시에 메모리에 load 되는 프로그램의 수가 고정적이다.
- 최대 수행 가능한 프로그램의 크기가 제한된다.

### Variable Partition Allocation(가변 분할 방식)

- 프로그램의 크기를 고려해서 할당한다.
- 분할의 크기, 개수가 동적으로 변한다.
- 외부 조각이 발생할 수 있다.
    - 중간 중간에 프로그램이 메모리에서 빠져나가면 빈 메모리가 생긱된다.
    - 프로그램이 종료되어 생긴 가용 메모리를 Hole 이라고 한다.
    - Hole 은 가용 메모리지만, 외부 조각은 그 크기나 위치 때문에 필요한 작업에 할당할 수 없는 경우를 뜻합니다.
    - Hole 은 외부 조각화의 원인이 된다.

### Dynamic Storage-Allocation Problem

- 가변 분할 방식에서 size n 인 요청을 만족하는 가장 적절한 hole 을 찾는 문제
- **First-fit**
    - Size 가 n 이상인 것 중 최초로 찾아지는 hole 에 할당한다.
- **Best-fit**
    - Size 가 n 이상인 것 중 가장 작은 hole 을 찾아 할당한다.
- **Worst-fit**
    - 가장 큰 hole 에 할당한다.
- **compaction**
    - 외부 조각 문제를 해결하기 위한 방법 중 하나다.
    - 사용 중인 메모리 영역을 한 군데로 몰아 넣고, Hole 들을 다른 한 군데로 몰아 큰 Block 을 만드는 것이다.
    - 하지만 이 방식은 비용이 매우 많이 든다.
        - 따라서 전체 메모리를 전부 밀어 넣는 것 보다는 일부 메모리만 밀어서 공간을 확보하는 방법을 사용한다.
    - Compaction 은 프로세스의 주소가 실행 시간에 동적으로 재배치가 가능한 경우에만 수행될 수 있다. (Runtime binding)

## Noncontiguous Allocation

### Paging

- Process 의 virtual memory 를 동일한 사이즈의 page 단위로 나눈다.
- Virtual memory 의 내용이 page 단위로 noncontiguous 하게 저장된다.
    - 일부는 backing storage, 일부는 physical memory
- 메모리 상의 공간도 페이지가 들어갈 수 있게 페이지의 크기에 맞게 잘게 분할해둔다. (frame)
    - MMU 에 의한 주소 변환 과정이 복잡해진다.
    - 프로세스 전체가 아닌 페이지 단위로 주소 변환을 해야하기 때문
- page table 을 사용하여 logical address 를 physical address 로 변환한다.
    - page table 은 배열이라고 생각하면 이해가 쉽다.(배열과 같은 건 아니다.)
    - page table 에는 entry(인덱스)가 있다.
    - entry 마다 해당 logical address 가 어떤 physical address 에 할당되는지 나타낸다.
        - 논리적 주소의 페이지 번호를 물리적 주소의 프레임 번호와 매핑한다.
        - 여기서 페이지 번호가 배열의 인덱스 역할을 한다.
        - page table entry 는 페이지 테이블의 한 칸을 의미한다. (프레임 번호가 저장된 공간)
- 외부 조각은 발생하지 않는다.
- 내부 조각은 발생할 수 있다.
- Page table 은 굉장히 크다.
    - 일반적으로 페이지 테이블엔 100만개 이상의 엔트리가 존재한다.
    - 따라서 레지스터나. 캐시 메모리에 저장할 수 없다.
    - Page table 은 main memory 에 상주한다.
    - Page table base register(PTBR)가 page table 을 가리킨다.
    - Page table length register(PTLR)가 테이블 크기를 보관한다.
    - 모든 메모리 접근 연산에는 2번의 memory access 가 필요하다.
        - page table 접근 1번
        - data/instruction 접근 1번
        - 따라서 느릴 수 있다.
    - 속도 향상을 위해 associative register 혹은 translation look-aside buffer(TLB)라 불리는 고속의 lookup hardware cache 를 사용한다. (주소
      변환을 위한 캐시 메모리다.)
        - TLB hit -> TLB 를 통해 주소 변환을 한다.
        - TLB miss -> page table 에 직접 접근한다. (메인 메모리에 접근)
        - TLB 는 모든 페이지 정보를 갖고 있지 않다.
            - 캐시 메모리는 용량이 적어서 모든 정보를 저장해둘 수 없음
        - TLB 는 테이블 전체를 탐색해야 하기 때문에 비교적 느릴 수 있다.
            - TLB 는 페이지 번호를 직접 인덱스로 사용하지 않고 **연관 검색(associative search) 방식**으로 탐색한다.
            - TLB 는 자주 사용되는 매핑 정보만 캐싱한다.
            - 따라서 저장된 페이지 번호가 불규칙적이므로 배열의 인덱스 처럼 페이지 번호로 직접 접근할 수 없다.
        - **associative register** 를 활용하면 병렬 탐색이 가능하기 때문에 성능 개선이 가능하다.
- Address translation
    - page table 중 일부가 associative register 에 보관되어 있다.
    - 만약 해당 page 가 associative register 에 있는 경우 곧바로 frame 번호를 얻어온다.
    - 그렇지 않은 경우 main memory 에 있는 page table 로 부터 frame 번호를 얻어온다.
    - TLB 는 context switch 때 flush 된다.

> TLB 는 캐시 메모리 개념에 더 가깝지만 associative register 로 구현되어 있다. <br/>
> 동작 방식은 연관 레지스터를, 기능적으로는 캐시의 역할을 수행한다고 보면 된다.

### Two-Level Page Table

- 현대의 컴퓨터는 address space 가 매우 큰 프로그램을 지원한다.
    - 32 bit 주소 체계를 사용시 4GB 의 주소 공간이 필요하다.
    - page size 가 4KB 라면 페이지 테이블에 약 1M 개의 엔트리가 저장될 수 있다.
    - 각 page entry 가 4 byte 라면 프로세스당 4M 의 page table 이 필요하다.
    - 그러나 대부분의 프로그램은 4G 의 주소 공간 중 지극히 일부분만 사용하므로 page table 공간이 심하게 낭비된다.
- 따라서 사용되지 않는 페이지 테이블의 공간을 줄이기 위해서 2단계 페이지 테이블을 사용한다.
- 2단계 페이지 테이블은 외부 테이블(Page Directory)과 내부 테이블(Page Table), 페이지 오프셋으로 이루어져 있다.
    - 외부 테이블의 엔트리는 하위 페이지 테이블을 가리킨다.
    - 하위 테이블의 엔트리는 실제 프레임 번호를 저장한다.
    - 페이지 오프셋은 페이지 내부 데이터를 저장하고 있다.
- 사용되지 않는 테이블의 내부 테이블은 NULL 로 표시된다.
- 2단계 페이지 테이블을 사용해서 주소 공간 전체를 위한 테이블을 생성할 필요가 없으므로 메모리 낭비를 줄일 수 있다.
    - 필요한 하위 페이지 테이블만 메모리에 로드한다.
    - 비어 있는 주소 공간에 대해 메모리를 할당하지 않아도 된다.

> 페이지 디렉터리 인덱스 (상위 10 비트) <br/>
> 페이지 테이블 인덱스 (중간 10 비트) <br/>
> 페이지 내 오프셋 (하위 12 비트)

### 단일 레벨 페이지 테이블 vs 2단계 페이지 테이블

- 단일 레벨 페이지 테이블은 논리 주소 공간 전체에 대한 엔트리를 생성한다.
    - 논리 주소 공간이 크더라도, 실제로 사용하지 않는 주소에 대해서도 페이지 테이블 엔트리를 유지해야 한다.
    - 결과적으로 많은 엔트리가 비어있게 되어 메모리가 낭비된다.
- 반면에 **2단계 페이지 테이블은 실제로 해당 주소 공간이 사용될 때만 생성된다.**
    - 하위 페이지 테이블은 논리 주소의 특정 범위에 해당하는 페이지 정보를 저장한다.
    - 해당 범위에 페이지가 없으면 하위 페이지 테이블 자체를 생성하지 않는다.
    - 비어 있는 주소 공간에 대해서는 페이지 디렉토리 엔트리가 NULL 로 표시된다.
    - 디렉터리가 비어있는 상태에서는 하위 페이지 테이블도 존재하지 않으므로 메모리를 할당하지 않는다.
- 단일 페이지 테이블과 2단계 페이지 테이블의 근본적인 차이는 **테이블 구조 자체가 다르다는 것이다.**
    - 2단계 페이지 테이블은 단일 페이지 테이블 내부에 또다른 단일 페이지 테이블이 들어있는 것이 아니다.
    - 단일 페이지 테이블은 사용하지 않는 주소 공간도 테이블에 포함되어 항상 메모리를 차지하도록 설계되어 있다.
    - 2단계 페이지 테이블은 비어있는 주소 공간에 대해서는 메모리를 할당하지 않도록 설계되어 있다.

> 2단계 페이지 테이블도 TLB 를 사용한다. <br/>
> 논리 주소 변환 과정에서 TLB 에서 매핑을 먼저 검색한다. <br/>
> TLB hit -> 바로 물리 주소로 변환 <br/>
> TLB miss -> 페이지 디렉터리부터 순차적으로 탐색 후 그 결과를 TLB 에 저장한다. <br/>
> TLB 는 최종 매핑만 저장하기 때문에 2단계 테이블의 내부 구조를 고려하지 않아도 된다. <br/>
> 만약 페이지 테이블에 자주 사용되는 주소 값이 골고루 분포되어 있다면 TLB 에 저장된 매핑이 자주 교체되면서 TLB 미스가 증가한다.

#### 단일 테이블을 동적으로 관리하도록 구현하면 2단계 테이블보다 더 좋은거 아닌가?

- 단일 테이블을 동적으로 관리하는 방법은 이론적으로 가능하다. 하지만 다음과 같은 문제점이 있다.
- 설계의 복잡성이 증가한다.
    - 사용하지 않는 엔트리를 메모리에 올리지 않으면서도 단순한 구조를 유지하기 어렵다.
    - 동적 크기의 단일 테이블을 관리하려면 테이블의 크기를 조정하거나 특정 범위의 엔트리를 로드하는 데 추가적인 오버헤드가 발생한다.
- 단일 페이지 테이블의 경우 주소 공간의 크기가 커질수록 테이블의 크기가 비현실적으로 커진다.
    - 하위 페이지 테이블은 **주소 공간의 일부를 각각 분할해서 저장하고 있는 형식**이다.
    - 따라서 상위 페이지 디렉터리의 크기가 비현실적으로 커질 일이 없다.
    - 만약 **하위 페이지 테이블들에 자주 사용되는 주소 갑이 골고루 분포되어 있다면 비효율적인 상황이 발생할 수 있다.**
- 페이지 디렉터리는 메모리에 상주한다.
    - 하위 페이지 테이블만 메모리에 동적으로 로드되는 것이다.
    - 페이지 디렉터리는 논리 주소를 물리 주소로 변환하는데 필수적인 정보를 담고있기에 항상 빠르게 접근할 수 있어야 한다.
    - 페이지 디렉터리는 TLB 미스나 페이지 테이블 탐색 시 항상 참조가 가능해야하기 때문에 메모리에 상주한다.
    - 또한 페이지 디렉터리가 스왑 아웃되면 다시 메모리로 불러오는데 큰 오버헤드가 발생하기 때문에 메모리에 상주해야한다.

#### Multilevel Paging

- Address space 가 더 커지면 다단계 페이지 테이블이 필요하다.
- 각 단게의 페이지 테이블이 메모리에 존재하므로 논리 주소의 물리 주소 변환에 더 많은 메모리 접근이 필요하다.
- TLB 를 통해 메모리 접근 시간을 줄일 수 있다.

> 논리 주소는 프로그램이 사용하는 주소이며 페이지 주소는 이 논리 주소를 페이지 단위로 나누었을 때 그 페이지의 번호를 의미한다. <br/>
> 논리 주소는 페이지 번호와 오프셋으로 나뉜다. <br/>
> 페이지 번호는 페이지 테이블을 통해 물리 메모리 주소와 매핑되며, 오프셋은 해당 페이지 내의 실제 데이터의 구체적인 위치를 나타낸다. <br/>
> 페이지 테이블을 통해서는 페이지 테이블에서 얻는 정보는 페이지의 시작 주소만을 알려준다는 것이다. <br/>
> 즉 하위 페이지 테이블을 통해 어떤 물리적 메모리 페이지에 해당하는지 알게되지만, 그 페이지 내에서 정확한 데이터 위치는 알 수 없다. <br/>
> 결국 물리 메모리 주소는 물리 페이지 번호 + 페이지 오프셋으로 결정된다.

#### Valid (v) / Invalid (i) Bit in a Page Table

- 페이지 테이블 내부에서 사용되지 않는 페이지 번호는 `i`로 표시하고 사용되는 페이지 번호는 `v`로 표시한다.
- `i`는 두 가지 의미를 내포하고 있다.
    - 프로세스가 그 주소를 사용하지 않음
      -해당 페이지가 swap area 에 있음

#### Protection bit

- 다른 프로세스가 자신의 페이지 정보를 확인할 일은 발생하지 않는다. (불가능하다.)
- protection bit 은 **어떤 연산에 대한 접근 권한이 있는지를 확인하기 위함이다.**

#### Inverted Page Table

- 논리적 주소로 부터 물리적 주소를 추론해내는 일반적인 방식이 아닌 물리적 주소로 논리적 주소를 찾아내는 방식이다.
- Inverted Page Table 은 PID 와 페이지 번호를 저장하고 있다.
- 테이블을 전부 탐색해서 PID 에 해당하는 Page 번호를 찾고 해당 페이지 번호가 테이블의 시작 지점에서 얼마나 떨어져 있는지(f)를 확인한다.
- 그럼 f 를 통해 물리메모리 주소(d)를 찾아낸다.
- 그럼 물리 메모리의 시작지점 부터 f 떨어진 곳에 위치한 d 번째 데이터를 읽어온다.
- 이러한 방식을 사용하는 이유는 공간을 절약하기 위함이다. 테이블을 하나만 사용하기 때문에 공간을 크게 줄일 수 있다.
- 하지만 테이블 전체를 탐색해야 하기 때문에 성능적으로 안좋다.
- 이를 해결하기 위해 associative register 를 사용한다.

#### Shared Page

- 엑셀 프로그램 3개를 동시에 띄었다고 가정해보자.
- 각 엑셀 프로그램이 모두 공유하는 데이터가 존재할 것이다. 이를 Shared-Page 에서 관리한다.
- 이러한 코드를 Shared-code 라고하고 Shared-code 는 read-only 형식으로 메모리에 한번만 올라간다.
    - Shared-code 는 모든 프로세스의 logical address space 에서 동일한 위치에 있어야 한다.
    - Shared-code 는 Reentrant code 또는 Pure code 라고도 불린다.
        - **Reentrant 는 프로그래밍 적으로 여러 번 동시에 호출되어도 안전하게 실행될 수 있음을 의미한다.**

### Segmentation

- 프로그램 내부 공간을 의미있는 단위로 분할한다.
    - 따라서 paging 보다 Sharing 과 Protection 에 있어서 훨씬 효과적이다.
- code, data, stack 과 같은 의미있는 영역 단위(Segment)로 분할한다.
    - 세그먼트는 의미 단위로 프로그램을 나누기 때문에 세그먼트 테이블의 크기가 페이지 테이블의 크기보다 작을 수 밖에 없다.
- Segment 의 논리적 주소는 다음 두 가지로 구성된다.
    - segment-number
    - offset
- Segment table 의 엔트리는 다음과 같은 정보를 담고 있다.
    - **base**
        - segment 의 물리 주소 시작점
            - **limit**
        - segment 의 길이
        - page 와 다르게 segment 각 공간의 크기가 서로 상이하다.
- **Segment-table base register** (STBR)
    - 물리적 메모리에서의 segment table 의 위치
- **Segnebt-table length register** (STLR)
    - 프로그램이 사용하는 segment 의 수
    - STBR 과 STLR 은 세그먼트 테이블의 위치를 파악하기 위해 존재한다.
    - 세그먼트 테이블도 메모리에서 관리되기 때문이다. (MMU 에서 관리 되기도 한다.)
- 각 Segment 의 길이가 동일하지 않기 때문에 Hole, external fragmentation 이 발생할 수 있다.
    - first-fit 또는 best-fit 방식을 사용해야 한다.
- 공유 세그먼트가 존재해서 동일한 세그먼트는 메모리에 한번만 할당된다.(Shared segment)

### 세그먼트 주소 변환 과정

1. CPU 는 세그먼트 번호와 오프셋을 가지고 있다.
2. 세그먼트 번호로 가서 해당 세그먼트의 base 와 limit 을 확인한다.
3. CPU 가 알고 있는 오프셋이 limit 을 넘지 않는지 검사한다.
4. 문제가 없으면 세그먼트 테이블에서 가져온 base 위치에 해당하는 물리 주소 시작점으로 간다. (물리 주소는 base + offset 이다.)
5. 물리 주소의 시작점에서 offset 만큼 떨어진 곳에 위치한 데이터를 확인한다.

### Segmentation with Paging

- 세그먼트 내부가 page 로 나뉘어져 있다.
- 따라서 물리 메모리에는 page 가 올라간다.
    - 따라서 기존 세그먼트 방식의 문제점이었던 Hole, 외부 조각 발생 문제가 해결된다.
- pure segmentation 과의 차이점
    - segment-table entry 가 segment 의 base address 를 가지고 있는 것이 아니다.
    - segment 를 구성하는 page table 의 base address 를 가지고 있다.
        - segment-table entry 는 page table 의 base address 와 segment length(Limit) 를 가지고 있다.
- 논리 주소는 세그먼트 번호와 세그먼트 오프셋으로 이루어져 있다.
    - 세그먼트 번호로 세그먼트 테이블에서 세그먼트의 길이와 페이지 테이블의 시작 위치를 확인한다.
    - 세그먼트 오프셋은 페이지 번호와 페이지 오프셋으로 구성된다.
    - 따라서 페이지 테이블 시작 위치와 페이지 번호를 활용해 물리 메모리의 시작 위치를 확인하고,
    - 페이지 오프셋을 더해 물리 메모리 주소로 찾아갈 수 있다.

> 논리 주소가 테이블 번호 + 오프셋 형태로 이루어지는 것은 페이징, 세그먼트 방식과 같은 특정 메모리 관리 방식에서만 그렇다.

> Memory Management 챕터에서 운영체제가 메모리 관리에 직접적으로 관여한 부분은 없다. <br/>
> 모두 하드웨어를 통해 주소 변환이 이루어졌다.

# Virtual Memory

## Demand Paging

- 실제로 필요할 때 page 를 메모리에 올리는 것이다.
- Demand Paging 엔 valid, invalid bit 을 사용한다.
    - invalid 는 두 가지 의미를 지닌다.
        - 주소 영역이 사용되고 있지 않음
        - 페이지가 물리적 메모리에 없음
    - 처음엔 모든 page entry 가 invalid 로 초기화된다.
    - invalid 상태에서 address translation 시도 -> page fault 발생 (trap)

## page fault

- invalid page 에 접근하면 MMU(주소 변환을 위한 하드웨어)가 trap 을 발생시킨다. (page fault trap)
- 커널 모드로 진입해서 page fault handler 가 invoke 된다.
- **page fault 처리 과정**
    - invalid reference 발생
        - bad address
        - protection violation (접근 권한에 막힘)
    - abort process
    - 빈 페이지 프레임을 가져온다. (없으면 뺏어옴 replace)
    - 해당 페이지를 disk 에서 memory 로 읽어온다.
        - disk I/O 가 끝날 때까지 해당 프로세스는 CPU 를 preempt 당한다. (block)
        - disk read 가 끝나면 page table entry 를 기록한다. (valid bit)
        - ready queue 에 process 를 insert -> dispatch later
    - 프로세스가 CPU 를 잡고 다시 RUNNING
    - 중단되었던 instruction 을 재개한다.

### Free frame 이 없는 경우

- **Page replacement**
    - 어떤 frame 을 뺏어올지 결정해야 한다.
    - 동일한 페이지가 여러번 쫓겨날 수 있다.
- **Replacement Algorithm**
    - Page-fault rate 을 최소화하는 것이 목표다
    - **Optimal Algorithm**
        - 가장 먼 미래에 참조되는 page 를 replace 한다.
        - 미래에 사용될 page 들을 모두 알고있다고 가정한다.
        - 다른 알고리즘의 성능에 대한 upper bound 를 제공한다.
    - **FIFO Algorithm**
        - 메모리 크기를 늘리면 오히려 성능이 더 나빠질 수 있다.
    - **LRU(Least-Recently-Used) Algorithm**
        - 가장 오래 전에 참조된 것을 지운다.
        - 메모리 크기가 4, 메모리 참조 순서가 1, 2, 3, 4, 1, 2, 5 라면
        - 5가 추가될 때 가장 오래전에 참조된 3이 제거되고 그 자리에 5가 들어간다.
        - LinkedList 를 활용하면 구현이 간단하다. O(1) 의 성능
    - **LFU(Least-Frequently-Used) Algorithm**
        - 참조 횟수가 가장 적은 페이지를 제거한다.
        - List 로 구현하면 O(n) 으로 성능이 느리다.
        - 따라서 LFU 는 이진트리를 사용해서 O(log n)으로 성능을 개선한다.
