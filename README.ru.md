# Простая библиотека для работы с базами данных SQLite и PostgreSQL

https://pypi.org/project/sqlet/

Простое и удобное взаимодействие с базами данных PostgreSQL и SQLite. Подходит для небольших или пэт проектов, когда необходима быстрая интеграция с БД без подключения ORM .

## Установка

```
pip install sqlet
```

## Использование

### Подключение к базе данных

```
from sqlet import db
```

#### SQLite
```
database = db(SUBD='sqlite', connection='путь/к/вашей/базе_данных.db')
```

#### PostgreSQL
```
database = db(SUBD='postgre', connection='хост+порт+имя_пользователя+пароль+имя_базы_данных')
```

#### Пример
```
database = db(SUBD='postgre', connection='localhost+5432+user+password+your_db')
```

### Выбор данных

#### select()

Фильтрация с помощью аргументов ключевых слов

Выбрать все столбцы из таблицы `users`, где возраст равен 30, а город — «Лондон»:
```
users = database.users.select(age=30, city='London')
```
или
```
users = database.select(table='users', age=30, city='London')
```

Выбрать конкретные столбцы: `name` и `email`:
```
users = database.users.select('name', 'email', age=25)
```

#### selectf()

Фильтрация с использованием SQL

Выбрать все столбцы из таблицы `users` с фильтром:
```
users = database.users.selectf(filter="age > 25")
```
или
```
users = database.selectf(table='users', filter="city = 'New York'")
```

Выбрать конкретные столбцы:
```
users = database.selectf(table='users', columns=['name', 'email'], filter="city = 'New York' or age > 18")
```

#### select1()

Получение одного результата

Получить первого пользователя с именем «John»:
```
user = database.users.select1(name='John')
```
```
if user:
    print(user.email)  # Доступ к атрибуту email объекта user
else:
    print("Пользователь не найден")
```

### Объект вывода sqlet

Как видно, в результате вызова функций выбора возвращается специальный объект вывода.

Для каждой строки, возвращённой запросом, создаётся объект вывода sqlet. Атрибуты этого объекта динамически создаются на основе названий столбцов таблицы.

Предположим, что таблица `users` имеет столбцы `id`, `name` и `email`:
```
users = database.users.select(name='John')  # Возвращает массив с объектами вывода
```
```
if users[0]:
    print(users[0].id)    # Доступ к столбцу id
    print(users[0].name)  # Доступ к столбцу name
    print(users[0].email) # Доступ к столбцу email
```

Вы можете выбрать только один объект без массива, используя метод `select1()`:
```
user = database.users.select1(name='John')
```

#### dict()

Если вам нужны данные в виде словаря, вы можете использовать метод `dict()`. Он возвращает словарь, где ключи — это названия столбцов, а значения — соответствующие данные.
```
user_dict = user.dict()
print(user_dict['name'])  # Доступ к 'name' с использованием нотации словаря
```

#### update()

Этот метод обновляет соответствующую строку в базе данных. Вы передаёте словарь с названиями столбцов и новыми значениями, которые хотите установить. Объект вывода sqlet автоматически обрабатывает подключение к базе данных и формирует соответствующий SQL‑запрос `UPDATE`.
```
user.update(email='john.doe.updated@example.com', age=31)  # Обновляет email и возраст John
```

#### delete()

Этот метод удаляет соответствующую строку из базы данных.
```
user.delete()  # Удаляет пользователя с именем John из базы данных
```

### Вставка данных

#### insert()

Вставить нового пользователя в таблицу `users`:
```
database.users.insert(name='Alice', email='alice@example.com', age=28)
```
или
```
database.insert('users', name='Bob', email='bob@example.com', age=32)
```

#### insertf()

Более сложный способ вставки данных: вставка данных с использованием списков столбцов и значений
```
database.insertf(table='users', columns=['name', 'email'], values=["'Charlie'", "'charlie@example.com'"])
```

### Обновление данных

#### update()

**Не рекомендуется**

Обновить адрес электронной почты пользователя с определённым именем:
```
database.users.update(filter="name = 'Alice'", values={'email': 'new_alice@example.com'})
```

**Рекомендуется** — использование объекта вывода для обновления:
```
user = database.users.select1(name='John')  # Получить объект
user.update(email='john.new@example.com')  # Обновить email этого объекта
```

### Удаление данных

#### delete()

Удалить пользователя с определённым ID:
```
database.users.delete(id=123)
```

#### deletef()

Удалить всех пользователей младше 18 лет:
```
database.users.deletef(filter='age < 18')
```

#### Использование объекта вывода для удаления
```
user = database.users.select1(name='John')  # Получить объект
user.delete()  # Удалить пользователя
```

### Выполнение сырого SQL‑запроса

#### sql()
```
results = database.sql("SELECT COUNT(*) FROM users")
print(results)
```
