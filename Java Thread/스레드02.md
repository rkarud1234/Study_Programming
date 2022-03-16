# 스레드 문맥 스위칭(Thread)
## 1. 스레드 이전의 시분할
스레드에 대해서 본격적으로 알아보기 전에 기존의 동기방식에서의 시분할에 대해서 다시 한 번 알아보도록 하자.      

### 1.1 스레드 이전의 시분할     
스레드 이전의 시분할 방식에서 웹 브라우저에서 다운로드를 눌렀다고 하자. 그러면 프로세스는 다음과 같이 형성되었을 것이다.     


<div align="center">
  <img src="https://user-images.githubusercontent.com/84266499/158658173-efcacb2f-1003-4f02-baa2-5cbbe84fe3c4.png" width="60%" height="60%"/>
</div>

파일 다운로드 프로세스는 웹 브라우저 프로세스에서 파생된 자식 프로세스이지만, 각각의 프로세스가 각각의 영역(Data, Heap, Stack)을 가지며 OS에서는
각각의 프로세스에게 시분할을 한다.           
이 때의 문제점은 프로세스 간 불평등이 발생할 수 있다는 것이며 파일 다운로드 프로세스는 웹 브라우저에서 파생된 프로세스임에도 불구하고
컨텍스트 스위칭으로 인한 시간의 소모가 추가적으로 발생한다는 것이다.       

이러한 문제점을 해결하기 위해 프로세스 단위의 시분할을 넘어선 시분할의 계층화가 일어나게 되는데, 이 단위가 바로 스레드이다.       


### 1.2 스레드 이후의 시분할
스레드를 이용한 스위칭 방법을 그림으로 나타내자면 아래와 같다. 자식 프로세스를 갖는 경우에도 OS의 시간 할당은 오로지 메인 프로세스인
웹 브라우저 프로세스에게만 주어진다.   

<div align="center">
  <img src="https://user-images.githubusercontent.com/84266499/158660668-1e88ad76-9911-42ac-bac0-a1da7c29cecc.png" width="70%" height="70%"/>
</div>

프로세스 문맥 스위칭과 다른 특징을 몇 가지 짚어보자.     
1. 가장 눈에띄는 변경점은 자식 프로세스를 많이 가진 프로세스가 나타나더라도 각각의 프로세스 간의 **형평성 문제가 해소**되었다는 것이다.
2. 프로세스 문맥 스위칭에서는 각각의 자식 프로세스가 독립적인 프로세스로 인식되어 각각의 Data Segment/Heap Segment/Stack segment를 가졌지만
   스레드 스위칭에서는 프로세스의 Data Segment와 Heap Segment는 공유하며 각각의 스레드마다 코드 흐름에 대한 진행정보와 코드 진행에 필요한
   지역변수가 저장되는 Stack Segment에서만 스위칭이 일어나게 된다.
   

즉, 자식 프로세스를 대신해 스레드 스위칭 방식으로 변화하면서 **스위칭의 경량화** 및 **스레드간의 데이터 공유가 쉬워지게** 되었다!     
그러면 이 스레드라는 녀석을 어떻게 활용할 수 있을지 간단하게 알아보도록 하자.

## 2. 멀티 스레드와 싱글 스레드      
다른 프로세스를 실행하려면 한 프로세스가 종료될 때 까지 기다려야 했던 기존의 동기 방식과는 다르게 스레드를 이용하면 프로세스의 비동기식
실행이 가능하게 된다.        
예를 들어서 프로세스를 동기로 처리하던 때는 세 개의 파일을 다운로드 받을 때, 파일1이 완전히 다 받아져야지 파일2를 받을 수 있고 이어서 파일2가 
완전히 다운로드받야져야만 파일3을 받을 수 있었다.    
``` java
main(){
  // 코드 예시: 코드가 위에서 아래로 순차적으로 실행된다.
  download1();
  download2();
  download3();
}
```
이러한 방식은 메인 스레드 하나만으로 세 개의 프로세스를 동작하는 방식이기 때문에 `Single Thread` 방식이라고 한다.     

하지만 비동기를 이용한다면 세 개의 파일을 한번에 다운로드 받을 수 있다.      

<div align="center">
  <img src="https://user-images.githubusercontent.com/84266499/158663981-24d2500a-66ed-4052-8162-4062554f04b5.png" width="50%" height="50%"/>
</div>

메인 스레드에서 프로세스를 생성하는 방식이 아닌 프로세스에서 스레드를 생성하는 방식이다. 이렇게 여러 개의 스레드로 동작하는 방식을
`Multi Thread` 방식이라고 하며, 위 예제는 한 개의 메인 스레드와 3 개의 서브 스레드로 이루어져 있다.


## 3. 스레드의 간단한 활용      
`java` 소스코드에서는 스레드를 어떻게 사용할 수 있는지 간단한 예제를 통해서 알아보자.      

### 3.1 Single Thread
우선 싱글 스레드로 동작하는 방식부터 보자.        
아래 코드는 하나의 메인 스레드(메인함수)를 갖는 싱글 스레드 예시이다.      
```` java
// 소스코드
public class SingleThreadExample {

	public static void main(String[] args) {
		print1();
		print2();

		for (int i = 1; i <= 10; i++) {
			System.out.println("main: " + i);
		}
	}

	private static void print1() {
		for (int i = 1; i <= 10; i++) {
			System.out.println("sub1: " + i);
		}
	}

	private static void print2() {
		for (int i = 1; i <= 10; i++) {
			System.out.println("sub2: " + i);
		}
	}
}
````
출력결과      
![image](https://user-images.githubusercontent.com/84266499/158664927-50643481-4f07-4c51-be19-d0cfcf062fb7.png)      
위와 같이 첫 번째 프린트 메서드가 완전히 실행되고 나서야 이후의 메서드들이 순차적으로 실행되는 것을 볼 수 있다.    

### 3.2 Multi Thread        
비동기 처리를 위해 스레드를 사용하고 싶을 때는 `java.lang` 패키지에 있는 `Thread` 클래스를 사용하면 된다.     
클래스에 대해서는 추후에 자세히 공부해보자. (참고링크: [java8](https://docs.oracle.com/javase/8/docs/api/))     
우선 필수적인 것들만 짚고 넘어가자면,,, 
- 멀티 스레드 동작 환경을 위해서는 `Thread` 객체를 만들어야한다.
- 메인 스레드 이외의 새로운 문맥의 흐름을 시작하고 싶을 때는 `Runnable Interface`의 `run()` 함수로 시작할 수 있다.
- `Runnable Interface`는 `Thread` 객체를 생성할 때 구현해줘야 한다.
- `Thread`의 `start()` 메서드를 이용해서 `Runnable`의 `run()` 메서드를 호출할 수 있다.

아래 코드는 멀티 스레드 예시이다.     
```` java
public class MutiThreadExample {

	public static void main(String[] args) {
		Thread th1 = new Thread(new Runnable() {
			@Override
			public void run() {
				print1();
			}
		});

		Thread th2 = new Thread(new Runnable() {
			@Override
			public void run() {
				print2();
			}
		});

		th1.start();
		th2.start();

		for (int i = 1; i <= 10; i++) {
			System.out.println("main: " + i);
		}
	}

	private static void print1() {
		for (int i = 1; i <= 10; i++) {
			System.out.println("sub1: " + i);
		}
	}

	private static void print2() {
		for (int i = 1; i <= 10; i++) {
			System.out.println("sub2: " + i);
		}
	}
}
````
출력결과     
![image](https://user-images.githubusercontent.com/84266499/158665879-fa7931f4-b956-44ed-8bfd-ea5266d97fe8.png)       
메서드의 실행 순서에 상관없이 멀티 스레드로 메서드들이 동시에 실행되고 있는 것을 확인할 수 있다.     
이 때, 시간을 분할해서 쓰는 형태가 매번 다르기때문에 출력의 순서 또한 매번 달라질 것이다.      


더 자세한 내용은 다음에 이어서 


