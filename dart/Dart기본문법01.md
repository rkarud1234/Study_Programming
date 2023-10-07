Docs : https://dart.dev/language        
웹 개발환경 : https://dartpad.dev/

Java에서와 같이 코드 실행을 위해서는 `main()` 함수 내부에 작성하면 된다.

```dart
void main() {
  print("Hello World!");
}
```

```
Hello World!
```

## 1. 주석

```dart
// 주석1

/**
* 주석2
*/

/// 이것은 문서 주석
/// 메서드나 클래스 위에 작성하면 dartdoc과 같은 문서 생성 도구를 통해 문서를 자동으로 생성해준다.
```

## 2. 문장을 써보자

```dart
// psvm이 아니다. main함수도 간단하게 선언할 수 있음
void main() {
  // 문장 끝에 세미콜론 필수
  print("Hello World!");
  // print가 new line 처리가 된다.
  // 작은 따옴표로도 string 입력 가능
  print('안녕방가');
}
```

```
Hello World!
안녕방가
```

## 3. 변수 선언하기

### 3.1. Dart의 기본 변수형

- `num` : 숫자 타입. `int` 와 `double` 이 `num` 타입에 포함될 수 있다.
    - `int` : 정수
    - `double` : 실수(소수점)
- `String` : 문자열
- `bool` : 불리언 타입

```dart
// Java와 동일하게 선언과 할당할 수 있다. 동시에도 가능
String name; // 선언
name = '갑갑이'; // 할당

int i = 11; // 선언과 할당
double d = 11.1;

num a = 101;
num b = 101.01;

double c = i; // 에러 : dart는 int -> double의 자동 형변환을 지원하지 않는다.
num d = i; // ok : num으로의 형변환은 가능하다.
```

### 3.2. 타입 추론 : `var`

Dart는 타입을 직접 명시하지 않고 `var` 로 대체할 수 있는 타입 추론을 지원한다.
일반적으로 `var` 로 많이 선언한다고 함

```dart
var i = 10; // int
var d = 10.0; // double
var s = 'hello'; // string
```

```dart
// 이건 에러가 난다
// 에러 메시지 : A value of type 'int' can't be assigned to a variable of type 'double'.
var i = 10;
double d = i;
```

- `dynamic` 타입
    - 알 수 없는 타입의 값을 할당하는 데 사용할 수 있다. 한 번 선언된 이후에 또 타입 바꿀수도 있음
    - 하지만 타입 안정성을 보장하지 않는다.

### 3.3. 상수 : `final` , `const`

`final` 또는 `const` 로 선언하면 값이 변하지 않는 상수값으로 선언할 수 있다.

- `final` : 런타임 상수로, 런타임에 값을 할당할 수 있지만 한 번 할당한 이후에는 값 변경이 불가능하다.
- `const` : 컴파일 타임 상수로, 컴파일 시점에 이미 값이 다 정의되어 있어야 하며 런타임에 값을 변경할 수 없다.

```dart
final int i = 1010;
const name = '갑갑이';

// 변수 출력하는 방법
print('${name}');
print('$name');
print('제 이름은 ${name}입니다.');
```

### 3.4. 산술 연산자

- `+` : 더하기
- `-` : 빼기
- `*` : 곱하기
- `/` : 나누기(`double` 타입 반환)
- `~/` : 몫(`int` 타입 반환)
- `%` : 나머지 연산(`int` 타입 반환)

```dart
// assert : 계산 결과의 참/거짓 결과를 반환. Java의 assert와 비슷하다고 생각하자,,,
// true/false값을 직접 쓰기 위한 용도는 아니고 디버깅용임
// 아래 결과들은 모두 참
assert(2 + 3 == 5);
assert(5 - 2 == 3);
assert(3 * 5 == 15);
assert(5 / 2 == 2.5);
assert(10 / 2 == 5.0);
assert(10 / 2 == 5); // .0 안붙여도 double 선언이나 할당 시 에러 안난다.
assert(5 ~/ 2 == 2);
assert(5 % 2 == 1);
assert('hello' + ' word' == 'hello world');
```

### 3.5. 증감, 비교, 논리 연산자

타 언어랑 동일하기에 설명만 적고 패쓰

1. 증감연산자
    - 후위 연산 : `a++`, `a—-`
    - 전위 연산 : `++a` . `--a`

```dart
var num = 0;
print(++num); // 1
print(num++); // 1
```

1. 비교연산자
    - `==` : 같다
    - `!=` : 다르다
    - `>` , `<` , `>=` , `<=` : 크,작,크같,작같
2. 논리연산자
    - `&&` , `||` , `!` : AND, OR, NOT

### 3.6. 타입 검사(`is` , `is!` )

- `is` : 같은 타입이면 `ture`
- `is!` : 다른 타입이면 `true`

```dart
int a = 10;
if(a is int){
  print('정수');
}
  
String text = 'hello';
if(text is! num){
  print('숫자가 아님');
}
```

### 3.7. 형변환 키워드 : `as`

다른 타입끼리의 변환은 불가능하며, 상위 타입으로 형변환할 수 있다.

```dart
// double -> int는 상관관계가 없는 다른 타입이기 때문에 변환이 불가능하다.
var a = 30.5;
int b = a as int; // 에러
  
dynamic c = 30.5;
num n = c; // as num 생략가능
```

## 4. 함수

반환 타입으로 모든 타입(+`void`) 지정이 가능하다.

클래스 안에 선언된 함수를 메서드라고 부른다.

```dart
반환타입 함수명(매개변수타입 매개변수명){
  // 함수 내용 작성
  return 반환값;
}
```

```dart
// 이렇게 최상단에 선언된 함수를 최상위 함수라고 부르며, 어디에서나 호출 가능
// static 메서드는 최상위 함수처럼 사용할 수 있다.
int sum(int x, int y){
  return x + y;
}

void main(){
  int a = 2;
  int b = 3;
  print(sum(a, b));
}
```

호출하는 곳에서 anonymous 함수와 람다식도 사용 가능하다.

### 4.1. 선택 매개변수

함수 정의 시 `{}` 로 감싼 녀석들은 선택적으로 사용할 수 있는 매개변수이다.

선택 매개변수는 꼭 이런 식으로 사용해줘야 한다.

```dart
// 선언 단계에서 기본값 설정해줌
void something({String name = 'gg', int i = 0}){
  print('something');
}

void main(){
  something(name:'aa', i:1);
}
```

## 5. 분기와 반복

### 5.1. 조건문

```dart
// if-else
String text = 'hello';

if(text is int) {
	print('정수');
} else if(text is double) {
	print('실수');
} else {
  print('뭘까?');
}
```

```dart
// 삼항연산자도 쓸 수 있음
print((1 is num) ? '정수':'정수 아님'); // 정수
```

```dart
// switch-case
enum Status { Uninitialized, Authenticated, Authenticating, Unauthenticated }

void main(){
  var status = Status.Authenticated;
  
  switch (status) {
    case Status.Uninitialized :
      print('초기화됨');
      break;
    case Status.Authenticated :
      print('인증됨');
      break;
    case Status.Authenticating :
      print('인증중');
      break;
    case Status.Unauthenticated :
      print('미인증');
      break;
  }
}
```

### 5.2. 반복문

```dart
void main(){
  var items = ['apple', 'banana', 'grape'];
  
  for(var i = 0; i < items.length; i++){
    print(items[i]);
  }
}
```
