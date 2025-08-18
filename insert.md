## myDatabase.dart
```dart
import 'package:sqflite/sqflite.dart';
import 'package:path/path.dart';

class MyDatabase {
  Future<Database> initDB() async {
    String path = await getDatabasesPath();
    return openDatabase(
      join(
        path,
        'todo_database.db',
      ),
      onCreate: (db, version) async {
        await db.execute(
          'CREATE TABLE todos(id INTEGER PRIMARY KEY AUTOINCREMENT, title TEXT, description TEXT)',
        );
      },
      version: 1,
    );
  }



  Future<int> insertTodo({required String title, String? des}) async {
    Database db = await initDB();
    Map<String, dynamic> map = {};
    map['title'] = title;
    map['description'] = des;
    return db.insert('todos', map);
  }


```

## AddEditTodo.dart
```dart
import 'package:flutter/material.dart';
import 'myDatabase.dart';

class AddTodo extends StatefulWidget {
  const AddTodo({Key? key}) : super(key: key);

  @override
  State<AddTodo> createState() => _AddTodoState();
}

class _AddTodoState extends State<AddTodo> {
  final titleController = TextEditingController();
  final descController = TextEditingController();
  final MyDatabase db = MyDatabase();

  void _saveTodo() async {
    if (titleController.text.trim().isEmpty) return;

    // Insert only
    await db.insertTodo(
      title: titleController.text.trim(),
      des: descController.text.trim(),
    );

    Navigator.pop(context, true); // return true to refresh list
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text("Add Todo"),
      ),
      body: Padding(
        padding: const EdgeInsets.all(12.0),
        child: Column(
          children: [
            TextField(
              decoration: const InputDecoration(hintText: "Enter Todo Title"),
              controller: titleController,
            ),
            const SizedBox(height: 10),
            TextField(
              decoration: const InputDecoration(hintText: "Enter Todo Description"),
              controller: descController,
            ),
            const SizedBox(height: 20),
            ElevatedButton(
              onPressed: _saveTodo,
              child: const Text("Add"),
            ),
          ],
        ),
      ),
    );
  }
}
```

## TodoListPage.dart
```dart

      floatingActionButton: FloatingActionButton(
        child: const Icon(Icons.add),
        onPressed: () {
          Navigator.push(
            context,
            MaterialPageRoute(builder: (context) => const AddEditTodo()),
          ).then((value) {
            if (value == true) {
              setState(() {});
            }
          });
        },
      ),
    );
  }
}

```
