# Comparable과 Comparator
Comparable과 Comparator 모두 **객체의 정렬** 기준을 명시하기 위한 **인터페이스** 이다. 그렇다면 이 인터페이스들은 정확히 어떤 역할을 수행하며 어떻게 사용하는 것인지 알아보도록 하자.     

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
- `java.lang.Comparable`       
java.lang 패키지 내에 존재하기때문에 별다른 import 없이 사용 가능하다.
