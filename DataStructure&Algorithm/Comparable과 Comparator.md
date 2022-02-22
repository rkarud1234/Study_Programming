# Comparable과 Comparator
Comparable과 Comparator 모두 **객체의 정렬** 기준을 명시하기 위한 **인터페이스** 이다. primitive type을 정렬하는 데는 사용하지 않는다.      
그렇다면 이 인터페이스들은 정확히 어떤 역할을 수행하며 어떻게 사용하는 것인지 알아보도록 하자.     

<details>
<summary>이 문서에서 참고하고있는 링크들</summary>
<div markdown="1">       
  
[Java8 API(영문)](https://docs.oracle.com/javase/8/docs/api/)       
[Java 2 Platform Standard Edition 5.0 API (한글번역)](http://cris.joongbu.ac.kr/course/java/api/index.html?help-doc.html/)       
  
</div>
</details>

## Comparable
- `java.lang.Comparable`       
java.lang 패키지 내에 존재하기때문에 별다른 import 없이 사용 가능하다.
### description
> Comparable은 해당 인터페이스를 구현하는 각 클래스의 객체에 전체 순서를 부과합니다. 이 순서를 클래스의 자연 순서라고 하며, 클래스의 compareTo 메서드를 자연 비교 방법이라고 합니다.     
> 이 인터페이스를 구현한 객체는 `Collections.sort` / `Arrays.sort`에 의해 별다른 비교자(Comparator) 없이 자동으로 정렬될 수 있습니다.   
     
Comparable 인터페이스를 구현하기 위해서는 해당 인터페이스의 Abstract Method인 `compareTo(T o)` 메서드를 구현해야 한다.
![image](https://user-images.githubusercontent.com/84266499/155008942-9a85f8ee-c21e-47e9-b74d-c88cbae0342a.png)        
이 메서드의 구현을 통해 객체의 정렬 순서를 지정할 수 있게 된다. 만약 compareTo를 구현하지 않고 implements만 하면 컴파일 에러가 발생한다.       
Comparable과 같이 구현해야 할 Abstract Method가 하나밖에 없는 인터페이스를 Functional Interface라고 한다.      
        
<br>

### Method Detail        
> 해당 개체와 지정된 개체를 비교합니다. (해당 개체: 나, 지정된 개체: 상대 라고 이해하면 편함)       
> 두 개체를 비교하여 해당 개체가 지정된 개체보다 작으면 음의 정수, 같으면 0, 크면 양의 정수를 반환합니다.               
> 해당 메서드는 모든 x, y에 대해 아래의 조건들을 보장해야 합니다. 그렇지 못할경우 예외를 발생시킬 수 있습니다.
> - `sgn(x.compareTo(y)) == -sgn(y.compareTo(x))`
> - `(x.compareTo(y) > 0 && y.compareTo(z) > 0) `
> - `x.compareTo(y)==0 && sgn(x.compareTo(z)) == sgn(y.compareTo(z))`

그러면 Comparable을 직접 구현해보면서 코드에 대해서 알아보도록 하자.      

<details>
<summary>Comparable 코드</summary>
<div markdown="1">       
   
학생 정보를 저장하기 위한 Student 클래스가 아래와 같이 정의되어있다고 하자.
````java
class Student {
	String stuNum;
	String name;
	int age;
	String major;

	Student(String stuNum, String name, int age, String major) {
		this.stuNum = stuNum;
		this.name = name;
		this.age = age;
		this.major = major;
	}
}
````   
                 
학생들의 정보를 보다 쉽게 다루기 위해서, `Student[] students`배열에 학생들의 정보를 저장하고 저장된 데이터를 학번 순으로 정렬하려고 한다.       
![image](https://user-images.githubusercontent.com/84266499/155011277-3c8583fb-502d-4021-9bf8-f631b43241c6.png)       
배열의 정렬을 위해서는 `Arrays.sort` 메서드를 이용할 수 있다.     
하지만 바로  `Arrays.sort(students)`라고 코드를 작성하면 다음과 같은 에러를 볼 수 있을 것이다.     
![image](https://user-images.githubusercontent.com/84266499/155011555-00abcc92-211d-4584-b968-795c18d217ef.png)        
주요 에러 메세지는 `java.lang.ClassCastException: class Student cannot be cast to class java.lang.Comparable`인데, `Arrays.sort`를 이용하여 해당 객체를 정렬하려고 할 때 컴파일러는 Comparable interface가 구현되어 있을 것이라고 예상하고 컴파일을 시키려고 하지만, 실제로 구현되어있지 않기 때문에 에러가 나는 것이다.      
에러 메세지만 봐도 Comparable로 Cast될 수 없다는 것을 볼 수 있다.(당연히도 Comparable이 구현되어있지 않기 때문)           
그러면 어떻게 정렬할까? Comparable을 구현해주면 된다. 예시로 나이를 기준으로 정렬해보도록 하자.

````java
class Student implements Comparable<Student> {
  // 클래스 내부 구조는 생략
  
  @Override
  public int compareTo(Student s) {
    return age - o.age;
  }
}
````          
해당 개체(this)의 나이가 지정된 개체(s)의 나이보다 클 때는 양수, 같을 때는 0, 작을 때는 음수를 반환하게 되면서 나이 순서대로 정렬이 가능하게 되는 것이다.      
![image](https://user-images.githubusercontent.com/84266499/155012591-5fd73eef-c01b-4b80-bad0-c2bd438196ed.png)        
       
나이가 같을 때는 이름으로 정렬하려고 한다면 다음과 같이 메서드 구조를 변경해주면 된다.         
````java
class Student implements Comparable<Student> {
  // 클래스 내부 구조는 생략
  
  @Override
  public int compareTo(Student s) {
    if(age == o.age)
      return name.compareTo(o.name);
    return age - o.age;
  }
}
````        
compareTo 메서드 내부에서 이름을 compareTo메서드로 비교하는 것이 보이는가?     
String 클래스에 오버라이딩된 compareTo로 비교하고 있는 것이다!

</div>
</details>
  
<br>
  
## Comparator
- `java.util.Comparator`       
java.util 패키지 내에 존재하기때문에 util package를 import 해야만 사용 가능하다.         
### description
> Comparator는 일부 개체 컬렉션에 순서를 부과하는 비교 기능입니다. 원하는 대로 정렬 순서를 제어할 수 있도록 비교자(Comparator)를 정렬 메서드(`Collections.sort` / `Arrays.sort`)에 **전달** 할 수 있습니다.            

Comparator 인터페이스를 구현하기 위해서는 해당 인터페이스의 Abstract Method인 compare(T o1, T o2) 메서드를 구현해야 한다.        
![image](https://user-images.githubusercontent.com/84266499/155013879-22816a67-78de-44c0-8438-5fcfe2394690.png)          
Method Summary에서는 Abstract Method가 `compare`와 `equals` 두 개인 것으로 보이지만, Comparator 또한 Comparable과 동일한 Functional Interface이다. `equals` 메서드는 Object에서 구현되어있기 때문에 인터페이스에서 별도로 (의무적으로) 구현할 필요가 없기 때문인데, 자세한 설명은 아래 링크를 참고하면 좋을 것 같다.       
[참고 ](https://stackoverflow.com/questions/43616649/how-can-comparator-be-a-functional-interface-when-it-has-two-abstract-methods/43616692)      

### Method Detail        
> 순서에 대한 두 인수를 비교합니다. 첫 번째 인수가 두 번째 인수보다 작으면 음수, 같으면 0, 크면 양수를 반환합니다. (이외의 조건은 Comparable과 동일)


<details>
<summary>Comparable 코드</summary>
<div markdown="1">       
   
Comparable에서 이용했던 Student 클래스를 이용하도록 하자. (Comparable 인터페이스는 구현되지 않은 상태)          
Comparator를 이용해서 정렬을 하기 위해서는 Comparator 객체를 `Collections.sort` / `Arrays.sort` 메서드의 매개변수로 전달해주어야 한다.

![image](https://user-images.githubusercontent.com/84266499/155014731-71156841-3bd5-4883-bfee-3d1a34a9b9ce.png)    
![image](https://user-images.githubusercontent.com/84266499/155014800-e9ed036d-2a74-4cc1-9917-60afd458b2bf.png)      

Comparator를 매개변수로 전달하는 방법으로는 두 가지가 있다.
### Comparator를 implements하는 객체를 생성해서 해당 객체를 전달하는 방식           
아래와 같이 omparator를 implements하는 객체를 만들어준다. 역시나 Abstract Method인 compare을 구현하지 않으면 컴파일 에러가 발생한다.     
객체 정렬 기준은 학번을 기준으로 해 보았다.    
````java
class StudentComparator implements Comparator<Student> {
	@Override
	public int compare(Student s1, Student s2) {
		return s1.stuNum.compareTo(s2.stuNum);
	}
}
````  	
이렇게 구현한 StudentComparator 객체를 만들어서 전달해주면 된다.      
````java
StudentComparator sc = new StudentComparator();
Arrays.sort(students, sc);
````     
![image](https://user-images.githubusercontent.com/84266499/155015307-d6ac6f8f-d43c-4306-b9c4-26c329e8fbaa.png)          
익명 객체로도 전달이 가능하다.     
````java
Arrays.sort(students, new StudentComparator());
````    
	

### 익명 Comparator 객체를 매개변수로 바로 구현하는 방식           
만약 해당 객체에서 한 번의 정렬만 일어나고 있는 상황이라면, 굳이 외부에서 Comparator 객체를 따로 만들어줄 필요 없이, 다음과 같이 익명 객체로 사용할 수 있다.      
````java
Arrays.sort(students, new Comparator<Student>() {
	@Override
	public int compare(Student s1, Student s2) {
		return s1.stuNum.compareTo(s2.stuNum);
	}
});
````     
또한, 람다식으로도 구현 가능하다.
````java
Arrays.sort(students, (s1, s2) -> {
	return s1.stuNum.compareTo(s2.stuNum);
});
```` 
</div>
</details>

     
<br>
	
## 정리 및 사용 시 주의점
여기까지 Comparable과 Comparator의 차이점 및 사용방법에 대해서 알아보았다.      
두 인터페이스의 가장 큰 차이는 클래스 내부에서 구현해서 사용하느냐, 함수에 전달해서 사용하느냐일 것이다.    
Comparable의 경우 클래스 내부에서 구현하기 때문에, 해당 객체의 모든 인스턴스에 적용될 수 있지만, Comparator의 경우 함수에 전달해서 사용하는 것이기 때문에 일부 인스턴스에만 적용된다.       
만약 Comparable을 구현한 객체에 Comparator를 전달한다면 Comparator의 정렬 기준이 우선적으로 적용된다.       
즉, 클래스의 내부 구조를 건드릴 수 있는 사용자 정의 클래스 등과 같은 경우 Comparable을, 2차원 배열과 같이 구조를 건드릴 수 없는 경우에 Comparator를 사용하여 정렬하는 것이 적절할 것이다.

### Collections.reverseOrder()
배열이나 컬렉션을 역순으로 정렬할 때, 전달 인자로 `Collections.reverseOrder()`를 줄 수 있다.      
![image](https://user-images.githubusercontent.com/84266499/155016186-a189be6e-da61-446e-96db-6952c4fe412c.png)        
하지만 `primitive type`의 경우 이를 이용해서 정렬하는 것이 불가능하다. 위에서 보는 것과 같이 해당 메서드는 Comparator 객체를 반환해주는 함수인데, Comparator는 primitive type이 아닌 객체(Object)의 정렬 기준을 만들어주는 인터페이스이기 때문이다.       
기본 타입을 역순으로 정렬해주고 싶다면 반복문 등을 이용해 직접 구현하거나, Wrapper Class형으로 변환하는 방법이 있다.     
       
### 비교 함수의 오버플로우
위에서도 예시를 들었지만 `compare`나 `compareTo`같은 경우 객체의 두 값을 비교하여 음수, 0, 양수 중 한 값을 리턴하고, 이 리턴값에 따라서 정렬 순서가 정해지는데 만약 아래와 같은 경우. . . . 
````java
public class Test {
	public static void main(String[] args) throws IOException {
		Number[] numbers = { new Number(-200), new Number(Integer.MAX_VALUE), new Number(3), new Number(1000) };
		Arrays.sort(numbers, (n1, n2) -> {
			return n1.num - n2.num;
		});

		for (Number number : numbers)
			System.out.println(number.num);
	}
}

class Number {
	int num;

	Number(int num) {
		this.num = num;
	}
}
````       
![image](https://user-images.githubusercontent.com/84266499/155016928-fd2717c5-8441-41c1-a556-839bb37a57e6.png)        
숫자를 비교할 때 int범위를 초과하게 되면서 정상적인 값이 return되지 않고, 정렬 순서에 오류가 발생하게 되는 것이다.      
이러한 경우를 주의해주도록 하자.
