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
CREATE TABLE TodoItems (id INTEGER PRIMARY KEY, title TEXT, isCompleted BOOLEAN)
```

하나씩 확인해보겠습니다. 

`CREATE TABLE TodoItems`

TodoItems 라는 테이블을 만듭니다. 데이터베이스는 많은 양의 데이터를 테이블 단위로 관리합니다. 테이블에는 같은 형태의 데이터만 들어갈 수 있습니다.  

`(id INTEGER PRIMARY KEY, title TEXT, isCompleted BOOLEAN)` 

TodoItems 테이블에 들어갈 데이터를 정의하는 부분입니다. 총 3가지의 데이터를 저장합니다. () 안에 쉼표로 구분해서 정의하며, 각각 [이름] [타입] [설정] 순서로 입력합니다.  각각을 데이터베이스 용어로는 Column 이라고 부릅니다. 

 **INTEGER 타입의 id (PRIMARY KEY)**

각 항목의 id 가 될 정보입니다. 중복되는 데이터가 없게 하기 위해 사용합니다. 데이터베이스에 해당 데이터를 삽입 시, id가 자동으로 만들어지게 됩니다. 

**TEXT 타입의 title**

항목의 제목을 저장합니다. 입력하는 제목이 해당 Column으로 들어갈 것입니다. TEXT 타입은 문자열이라고 생각하시면 됩니다. 

**BOOLEAN 타입의 isCompleted** 

항목이 완료되었는지 아닌지의 값이 저장될 것입니다. 앱에서 체크표시를 선택하여 켜고 끌 때 마다 변경될 것입니다. 



작성한 쿼리를 사용해서 데이터베이스를 초기화해보겠습니다. initDatabase() 를 다음과 같이 변경해보겠습니다. 

```
Future<void> initDatabase() async {
  database = await openDatabase('todolist.db', version: 1, onCreate: (Database db, int version) {
    db.execute('CREATE TABLE TodoItems (id INTEGER PRIMARY KEY, title TEXT, isCompleted BOOLEAN)');
  });
```

openDatabase 에 새로운 파라미터를 2가지 추가하였습니다. 

**version**

데이터베이스의 버전을 설정합니다. 데이터베이스도 일종의 파일이라고 생각할 수 있는데, 그 업데이트의 기준이 되는 버전입니다. (앱스토어에서 앱을 업데이트 할 때도 앱의 버전을 기준으로 하듯이, 데이터베이스도 버전이 필요한 것입니다.) 

**onCreate**

초기화 시 실행되어야 할 로직을 익명 함수를 통해 정의합니다. 해당 익명함수에서 사용하는 것은 Database 타입의 db, int 타입의 version을 정의해주었습니다. 작성한 코드에서는 db.excute를 통해 앞서 정의했던 쿼리문을 실행하였습니다. version의 경우 기존에 있던 데이터베이스의 버전이 전달되고, 해당 버전에 따라 추가적으로 필요한 로직을 정의 할 수 있게 됩니다. 



### 데이터베이스와 사용할 클래스 정의

데이터베이스에서 사용할 "하나의 할 일" 에는 

* 이 항목의 id 

* 이 일이 무엇인지 
* 이 항목이 완료되었는지 

3가지가 필요합니다. 데이터베이스를 사용하는 형태는 위 형태로 계속 될 것이므로 편하게 사용하기 위해 클래스를 만들어주겠습니다. main.dart 가 있는 패키지에 새로운 todoItem.dart 파일을 만듭니다[만드는 방법이 책에 들어가야 하나?]. 이후 다음과 같은 클래스를 만들어주세요. 데이터베이스 연동을 진행하면서 조금씩 변경할것입니다. 

```
class TodoItem {
  int id; 
  String title;
  bool isCompleted;
}
```



이제, TodoHomeState 에 있던 List<String> todos; 를 더이상 사용하지 않고, 새로 정의한 클래스의 리스트로 사용할 것입니다. TodoHomeState 클래스에 새로운 필드를 추가해주세요. 

```
List<TodoItem> todoItems = [];
```

새로 정의한 todoItems 를 기준으로 코드를 바꿔보겠습니다. 

TodoHomeState > build 안의 ListView.builder 부분을 먼저 변경합니다. 

```
ListView.builder(
    itemCount: todoItems.length, // todoItem의 length 로 변경합니다. 
    itemBuilder: (BuildContext context, int index) {
      return TodoColumn(
        title: todoItems[index].title, // index를 통해 가져온 후 title을 참조합니다. 
        onDismissed: (direction) async {
          setState(() {
            todoItems.removeAt(index);
          });
          // preference 사용은 제거해주세요. 
         // await preferences.setStringList(_KEY_TODOS, todos);
        },
      );
    }),
```



다음으로는 아이템 추가 부분을 변경합니다. 

```
floatingActionButton: FloatingActionButton(
  child: Icon(Icons.add),
  onPressed: () async {
    if (todoInputController.text.isNotEmpty) {
    
      // preference 사용은 제거합니다. 
      // await preferences.setStringList(_KEY_TODOS, todos);

      setState(() {
        // 아이템 추가시, TodoItem 인스턴스를 생성해서 추가하도록합니다. 
        todoItems.add(
                  TodoItem(
                      title: todoInputController.text, 
                      isCompleted: false
                  ));
        todoInputController.clear();
      });
    }
  },
),
```



추가적으로 preference도 더이상 사용하지 않을것이므로, initPreference() 메소드 사용은 제거해주세요.



### 데이터베이스에 항목 추가하기 

이전에는 preference에서 String목록을 가져온 후 항목을 추가해서 업데이트하였지만, 이제는 데이터베이스에서 목록을 가져와야 합니다. 역시 쿼리를 사용해야 하는데요, 해당 쿼리는 다음과 같습니다. 

```sql
INSERT INTO TodoItems (title, isCompleted) VALUES ($todoTitle, false)
```

**INSERT INTO TodoItems**

INSERT는 데이터베이스 쿼리 중 새로운 항목을 넣을 때 사용하는 쿼리입니다. 이어서 INTO 를 통해 테이블 이름을 지정해줍니다. 이름은 처음에 설정한 TodoItems 를 사용합니다. 

**(title, isCompleted) VALUES ($todoTitle, false)**





### sqflite 설정하기 



### sqflite 초기화 



### 데이터베이스 모델링 





### 위젯에서 사용하기 



