### Dart 알아보기



### Dart 

Flutter 에서는 Dart를 언어로 사용합니다. Dart 는 구글에서 2011년에 만든 프로그래밍언어입니다.  

주요 특징으로는 

* 객체지향 언어이다 
* 함수가 일급객체이다. 
* 여러 플랫폼에서 사용가능하다. 

등이 있습니다. 

자바와 매우 비슷한 문법을 가지고 있어서 자바 언어에 익숙한 경우 아주 쉽게 배울 수 있습니다. 이번 장에서는 Dart 언어에 대한 문법을 간략하게 살펴보도록 하겠습니다. 

먼저, 배울 문법들을 빠르게 확인해볼 수 있는 사이트 사용법을 설명하겠습니다. 

https://dartpad.dev/ 사이트로 접속합니다.  다음과 같은 화면을 볼 수 있습니다. 

![스크린샷 2019-09-13 오후 1.12.45](스크린샷 2019-09-13 오후 1.12.45.png)

기본 main 함수가 작성되어 있고, 화면의 Run 을 누르면 오른쪽에 결과가 나오는 것을 확인 할 수 있습니다. 

### 변수

Dart 는 변수를 여러가지 형태로 사용할 수 있습니다.

1. 자료형을 명시적으로 선언하기 

```
String name = 'android'; // 문자열 변수 name 을 선언합니다. 
int age = '10'; // 정수형 변수 age를 선언합니다. 

print(name);
print(age);
```

결과:

```
android
10
```



2. 자료형을 dynamic 으로 사용하기 

dynamic은 어떤 타입으로도 사용이 가능한 타입입니다. 아래 코드처럼 동시에 여러가지 타입의 값을 할당하여도 컴파일 에러가 나지 않습니다. 

``` 
void main() {
  dynamic something = 'some word'; // 문자역을 할당합니다. 
  
  print(something);
  
  something = 100; // 정수를 할당합니다. 
  
  print(something);
  
  something = true; // bool 타입을 할당합니다. 
  
  print(something);
}
```

결과

```
some word
100
true
```



3. 명시적인 타입 선언 생략하기 

```
var price = 1000;
print(price); // 1000이 출력됩니다. 
```

pirce 라는 변수에 1000 을 할당했습니다. 명시적으로 정수라고 코드에 선언하지는 않았지만, 할당한 1000 이 정수형이기 때문에 price 변수는 이후의 코드에서 int로 취급됩니다. 즉, 이 변수에 다른 타입을 할당하려고 하면 컴파일 에러가 발생합니다. 



### 상수 

Dart에서 상수는 final 과 const 로 만들 수 있습니다. 

final은 한번 값을 할당하면 더이상 값을 바꿀 수 없습니다. 

```
final int height;
height = 180;
heignt = 200; // 컴파일 에러
```



const 변수는 선언과 동시에 초기화를 해야 하고, 더이상 바꿀 수 없습니다. 

```
const dayOfYear = 365;
const dayOfWeek = 7;
```



### 문자열 사용

Dart에서는 문자열 값을 나타낼 때, 그 값을 ' ' 나 " "  로 감싸서 표시합니다. 

```
var message1 = 'first message with single quote';
var message2 = "second message with double quotes";
```

문자열에 안에 다른 문자열 변수를 사용해서 표시하고 싶은 경우, $ 기호를 사용합니다. 

dartpad 에서 테스트 해보세요. 

```
void main() {
  var name = 'andy';
  var greeting = 'Hello $name!';
  print(greeting);
}

```

결과

```
Hello andy!
```



선언시에, 보기 편하도록 여러 줄로 표시할 수도 있습니다. 첫번째는 여러개의 문자열 선언을 합치는 방법이고, 두번째는 입력하는 문자열 자체를 그대로 표시하는 방법입니다. 

```
void main() {
  var greeting1 = 'Hello ' 
    'my name is '
    'andy'; // 3개의 문자열 선언이 그대로 합쳐집니다. 
  
  var greeting2 = '''
    Hello 
    my name is 
    andy
    '''; // ''', ''' 사이의 문자를 그대로 표시합니다.(여백, 줄바꿈 포함)
  
  print(greeting1);
  print("-----");
  print(greeting2);
}
```

결과

```

Hello my name is andy
-----
    Hello 
    my name is 
    andy
    
```



