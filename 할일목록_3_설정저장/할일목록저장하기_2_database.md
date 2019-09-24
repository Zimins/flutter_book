이번 장에서는 SharedPreference 를 통해 저장하던 정보를 데이터베이스를 통해서 저장 할 수 있도록 변경해보겠습니다. 이번에는 Android의 sqllite, iOS 의 FMDB 를 사용하기 위해 만들어진 sqflite 를 사용해보겠습니다. 이전 장에서 한 것 처럼, https://pub.dev/packages/sqflite 사이트로 접속한 후, Versions 탭으로 들어가서 최신 버전을 확인 후, 프로젝트의 pubspec.yaml 에 추가하겠습니다. 

```yml
dependencies:
  ...
  sqflite: ^1.1.6 // 최신 버전으로 설정해주세요. 
```

`Packages get` 버튼을 눌러서 정상적으로 설정되는지 확인해주세요.



### 데이터베이스란? 

프로그램이 종료되어도 유지되는 데이터가 복잡할 때 사용합니다. 간단한 경우 파일, 혹은 각 프레임워크에서 제공하는 방법을 통해 (SharedPreference 등) 저장 할 수 있지만, 복잡한 데이터 (주변 위치 정보, 달력에 표시할 약속 등)은 다른 방법이 필요합니다. 데이터베이스는 원하는 데이터를 테이블 이라는 공간에 저장하며, 이를 쿼리 를 통해서 가져올 수 있습니다. 데이터베이스에 대한 것을 여기서 설명하기에는 책 몇권으로도 모자라기에, 이번 장에서는 직접 필요한 기능을 사용해보면서 친해진다고 생각하시면 되겠습니다. 



###  sqflite 초기화 

앞의 장에서 preference 를 초기화 한 것 처럼, sqflite 도 초기화를 해주겠습니다. TodoHomeState 클래스에 필드를 추가해줍니다. import 해주는 것을 잊지 마세요! (보통은 해당 패키지의 클래스를 사용한다면 ide에서 자동으로 추가해주지만, 만약약 그렇지 않다면 `import 'package:sqflite/sqflite.dart';`를 파일 상단에 추가합니다. )

```
class TodoHomeState extends State<TodoHome> {
  List<String> todos = List();
  static const _KEY_TODOS = "key_todos";
  var todoInputController = TextEditingController();
  SharedPreferences preferences;
  Database database;
  ...
  ...
```

다음으로 이 database 필드를 초기화 하는 메소드를 정의합니다. openDatabase() 를 사용하면 database 를 초기화 할 수 있습니다. 파라미터로는 해당 데이터베이스 파일의 이름이 될 값을 사용합니다. 이번에는 todolist.db 를 로 사용하겠습니다. 데이터베이스를 초기화 하는 과정도 시간이 걸리는 일이기 때문에, async 키워드를 사용해주겠습니다. 

```
Future<void> initDatabase() async {
  database = await openDatabase('todolist.db');
}
```



이젠 테이블을 초기화 해줘야 합니다. 위 initDatabase() 에 추가로 작성해보겠습니다. 데이터베이스에 뭔가 변경을 하기위해서는 쿼리를 해주어야 합니다. 사용 할 쿼리는 다음과 같습니다. 

```
CREATE TABLE TODOITEMS (id INTEGER PRIMARY KEY, title TEXT, isCompleted BOOLEAN)
```

하나씩 확인해보겠습니다. 

`CREATE TABLE TODOITEMS`

TODOITEMS 라는 테이블을 만듭니다. 데이터베이스는 많은 양의 정보를 테이블 단위로 관리합니다. 

### 데이터베이스 모델링

먼저, 어떤 정보를 데이터베이스에 저장 할 지 결정해야 합니다. 그리고 이를 클래스로 정의 할 것입니다. 이 프로젝트에서는 하나의 "할 일" 이 해당합니다. 

하나의 할 일 에는 

* 이 일이 무엇인지 
* 이 항목이 완료되었는지 

2가지가 저장되어야 합니다. 

클래스로 작성해보겠습니다. main.dart 가 있는 패키지에 새로운 todoItem.dart 를 만들어보겠습니다. 이 항목의 제목을 나타내는 title과 완료되었는지를 나타내는 isCompleted 2가지를 추가해주겠습니다. 

```
class TodoItem {
  String title;
  bool isCompleted;

  TodoItem({this.title, this.isCompleted});
}
```



### sqflite 설정하기 



### sqflite 초기화 



### 데이터베이스 모델링 





### 위젯에서 사용하기 



