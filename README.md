# Advanced-Backend-2026

В системе предусмотрено 3 роли пользователя помимо абитуриента:
Менеджер - сотрудник факультета, основной задачей которого является работа с абитуриентами и их документами.
Главный менеджер - помимо функционала актера с ролью “Менеджер” имеют возможность управлять течением приёмной кампании.
Администратор - основной задачей является управление и обслуживание системы. Кроме своих возможностей, он обладает всеми системными функциями пользователей с другими ролями.


## Функционал клиента (абитуриента)
[Доступ: <kbd>Все</kbd> ]
### 1. Возможность регистрации в системе:
* `POST api/v1/auth/register`

### Тело запроса: 
```json
{
  "fullname": "Ivanov Ivan Ivanovich",
  "email": "admin@example.com",
  "password": "Admin123!",
  "dateOfBirth":"01.01.2001",
  "gender": "Male",
  "citizenship": "Russian",
  "phoneNumber": "+7 999 876 43 21"
}
```
### Тело ответа: 
```json
{
  "access_token": "eyJhciOiJIzI1NiInR5cCI6Ikp...",
  "refresh_token": "wIiwieyJzdWIiOiIxMjMY3ODkb...",
  "expires_in": 900
}
```

[Доступ: <kbd>Все</kbd>]
### 2. Возможность аутентификации/авторизации в системе:
* `POST api/v1/auth/login`
### Тело запроса:
```json
{
  "email": "admin@example.com",
  "password": "Admin123!"
}
```
### Тело ответа:
```json
{
  "access_token": "eyJhciOiJIzI1NiInR5cCI6Ikp...",
  "refresh_token": "wIiwieyJzdWIiOiIxMjMY3ODkb...",
  "expires_in": 900
}
```

* `POST api/v1/auth/refresh`
### Тело ответа:
```json
{
  "access_token": "eyJhciOiJIzI1NiInR5cCI6Ikp...",
  "refresh_token": "wIiwieyJzdWIiOiIxMjMY3ODkb...",
  "expires_in": 900
}
```

* `POST api/v1/auth/logout`

### 3. Возможность сменить данные для входа (email, пароль)
* `PATCH api/v1/auth/credentials/password`
### Тело запроса:
```json
{
  "old_password": "Admin123!",
  "new_password": "Admin12345!",
}
```

* `PATCH api/v1/auth/credentials/email`
### Тело запроса:
```json
{
  "email": "admin@example.com",
  "password": "Admin123!"
}
```

### 4. Просмотр личного профиля 
* `GET api/v1/users/{id}/profile`
* `GET api/v1/users/me/profile` – id автоматически возьмётся из токена
### Тело ответа:
```json
{
  "id": "30dd879c-ee2f-11...",
  "fullname": "Ivanov Ivan Ivanovich",
  "email": "ivan.ivanov@example.com",
  "dateOfBirth": "2001-01-01",
  "gender": "Male",
  "citizenship": "Russian",
  "phoneNumber": "+7 999 876 43 21"
}
```

### 5. Обновление личных данных (ФИО, email, номер телефона, дата рождения, пол, гражданство)
* `PATCH api/v1/users/{id}`
* `PATCH api/v1/users/me` – id автоматически возьмётся из токена

### Тело запроса: 
```json
{
  "email": "admin@example.com",
  "fullname": "Ivanov Ivan Ivanovich",
  "email": "ivan.ivanov@example.com",
  "dateOfBirth": "2001-01-01",
  "gender": "Male",
  "citizenship": "Russian",
  "phoneNumber": "+7 999 876 43 21"
}
```

### 6. Просмотр документов (отдельно каждого вида)
* `GET api/v1/users/{id}/documents`
* `GET api/v1/users/me/documents` – id автоматически возьмётся из токена

 ### Query – параметры:
`type (enum, допустимые значения: "Passport", "Diploma")`
– фильтрация по типу документа
  
### Тело ответа:
```json
[
  {
    "type": "Passport",
    "id":"",
    "series": "",
    "number": "",
    "placeOfBirth": "",
    "issuedWhen": "",
    "issuedBy": "",
    "url": ""
  },
  {
    "type": "Diploma",
    "id":"",
    "name": "",
    "diplomaType": "",
    "url": ""
  }
]
```

### 7. Возможность скачать скан документа 
* `GET api/v1/users/{id}/documents/{docId}/file`
* `GET api/v1/users/me/documents/{docId}/file` – id автоматически возьмётся из токена

### Тело ответа:
```json
{
  "url": "string",
  "expiresIn": 900
}
```

### 8. Возможность редактировать данные документа
* `PATCH api/v1/users/{id}/documents/{docId}`
* `PATCH api/v1/users/me/documents/{docId}` – id автоматически возьмётся из токена
### Тело запроса:
```json
{
   {
    "type": "Passport",
    "series": "",
    "number": "",
    "placeOfBirth": "",
    "issuedWhen": "",
    "issuedBy": ""
  },
  {
    "type": "Diploma",
    "name": "",
    "diplomaType": ""
  } 
}
```

### 9. Возможность удалить скан
* `DELETE api/v1/users/{id}/documents/{docId}/file`
* `DELETE api/v1/users/me/documents/{docId}/file` – id автоматически возьмётся из токена

### 10. Возможность загрузить новый скан документа
* `POST api/v1/users/{id}/documents/{docId}/file`
* `POST api/v1/users/me/documents/{docId}/file` – id автоматически возьмётся из токена

### Тело запроса:
```json
  (Content-Type: multipart/form-data)
  your_file.pdf
```

### 11. Возможность посмотреть список программ с возможностью пагинации и следующими фильтрациями:
* `GET api/v1/programs`

 ### Query – параметры:
`page (int, default: 1)`
– номер страницы

`size (int, default: 10, max: 100)`
– размер страницы

`faculty (string, опционально)`
– фильтрация по факультету

`level (int, опционально)`
 – фильтрация по уровню образования
 
`mode (string, опционально)`
 – фильтрация по форме обучения
 
`language (string, опционально)`
 – фильтрация по языку обучения
 
`program (string, опционально)`
 – поиск по названию/коду программы (по части).

### Тело ответа:
```json
{
  "programs": [
    {
      "id": "3fa85f64-5717-4562-b3fc-2c963f66afa6",
      "createTime": "2026-03-01T19:36:08.230Z",
      "name": "string",
      "code": "string",
      "language": "string",
      "educationForm": "string",
      "faculty": {
        "id": "3fa85f64-5717-4562-b3fc-2c963f66afa6",
        "createTime": "2026-03-01T19:36:08.230Z",
        "name": "string"
      },
      "educationLevel": {
        "id": 0,
        "name": "string"
      }
    }
  ],
  "pagination": {
    "size": 0,
    "count": 0,
    "current": 0
  }
}
```

### 12. Возможность добавить программу в список выбранных программ для обучения
* `POST api/v1/users/{id}/programs`
* `POST api/v1/users/me/programs` – id автоматически возьмётся из токена

### Тело запроса:
```json
{
  "progId": "3fa85f64-5717-45..."
}
```

Обратите внимание! 
Количество выбранных программ не может быть больше, чем N (N - конфигурируется в приложении).
Программы должны относиться к одной ступени обучения. Например:
абитуриент “A” не может одновременно выбрать программу с уровнем “Магистратура” и “Аспирантура”;
абитуриент “B” может выбрать программу с уровнем “Специалитет”, если у него  уже выбрана программа с уровнем “Бакалавриат”.
Если у абитуриента добавлен документ об образовании, уровень выбранной программы должен быть либо аналогичен уровню документа об образовании, либо входить в список доступных для обучения

### 13. Возможность изменить приоритет программы
* `PATCH api/v1/users/{id}/programs/{progId}`
* `PATCH api/v1/users/me/programs/{progId}` – id автоматически возьмётся из токена
### Тело запроса:
```json
{
  "prioriry": "int"
}
```

### 14. Возможность удалить программу из выбранного списка
* `DELETE api/v1/users/{id}/programs/{progId}`
* `DELETE api/v1/users/me/programs/{progId}` – id автоматически возьмётся из токена

### 15. Получить уведомление на email об изменении статуса поступления

## Функционал “Менеджера” 
### 1. Возможность аутентификации/авторизации в системе
* ~~`POST api/v1/auth/login`~~
* "уже описано"

### 2. Возможность изменить данные для входа (email, пароль)
* ~~`POST api/v1/`~~
* "уже описано"

### 3. Взять поступление абитуриента.
* `PATCH api/v1/admissions/{id}`

### 4. Отказаться от поступления абитуриента (вернуть его в общий пул заявок).
* `PATCH api/v1/admissions/{id}`

### Тело ответа:
```json
{
  "manager": "null"
}
```

### 5. Просмотреть заявки абитуриентов с пагинацией  и следующими фильтрациями и сортировками:
* `GET api/v1/admissions`

### Query – параметры:
`page (int, default: 1)`
– номер страницы

`size (int, default: 10, max: 100)`
– размер страницы

`name (string, опционально)`
– поиск по части имени

`program (string, опционально)`
– фильтр по программе

`faculty (string, опционально)`
– фильтрация по факультету - multiselect (у абитуриента должна быть выбрана хотя бы одна программа данного факультета)

`status (string, опционально)`
– Фильтрация по статусу поступления

`name (string, опционально)`
– Отображение только тех абитуриентов, у которых еще не назначен менеджер

`name (string, опционально)`
– Отображение абитуриентов, привязанных к данному менеджеру

`name (string, опционально)`
– Сортировка по дате внесения последних изменений (по убыванию, по возрастанию)

### Тело ответа:
```json
{
  "admissions":
    [
      "wip": "wip"
    ],
  "pagination": {
    "size": 0,
    "count": 0,
    "current": 0
  }
}
```

### 6. Посмотреть данные абитуриента:
* `GET api/v1/users/{id}/profile`

### Тело ответа:
```json
{
  "fullname": "Ivanov Ivan Ivanovich",
  "email": "ivan.ivanov@example.com",
  "dateOfBirth": "2001-01-01",
  "gender": "Male",
  "citizenship": "Russian",
  "phoneNumber": "+7 999 876 43 21",
  "documents": []
}
```
  Личные данные ФИО, email, номер телефона, дата рождения, пол, гражданство)
  Документы (отдельно каждого вида) с возможность посмотреть/скачать сканы документа
  Выбранные программы
  
### 7. Обновление личных данных абитуриента
* `PATCH api/v1/users/{id}`

### Тело запроса: 
```json
{
  "password": "Admin123!",
  "email": "admin@example.com",
  "fullname": "Ivanov Ivan Ivanovich",
  "email": "ivan.ivanov@example.com",
  "dateOfBirth": "2001-01-01",
  "gender": "Male",
  "citizenship": "Russian",
  "phoneNumber": "+7 999 876 43 21"
}
```

### 8.~~Возможность редактировать данные документа абитуриента~~
* `PATCH api/v1/users/{id}/documents/{id}`
### Тело запроса:
```
уже описано
```

### 9. Возможность удалить скан документа абитуриента
* `DELETE api/v1/users/{id}/documents/{id}/file`

### 10. Возможность загрузить новый скан документа абитуриента
* `POST api/v1/users/{id}/documents/{id}/file`
### Тело запроса:
```json
{
  "wip": "wip"
}
```

### 11. Возможность изменить приоритет программы
* `PATCH api/v1/users/{id}/programs/{id}`
### Тело запроса:
```json
{
  "prioriry": ""
}
```

### 12. Возможность удалить программу из выбранного списка
* `DELETE api/v1/users/{id}/programs/{id}`

### 13. Изменить статус поступления
* `POST api/v1/admission/{id}`

Обратите внимание! Менеджер может просматривать данные всех абитуриентов, но редактировать только тех абитуриентов, для которых он назначен менеджером.

## Функционал “Главного менеджера” 

### 1. Возможность посмотреть список менеджеров, главных менеджеров
* `POST api/v1/managers`

### Query – параметры:
`position (enum, Допустимые значения: "Manager", "MainManager")`
– должность менеджера

### 2. Возможность назначить менеджера на поступление, если оно свободно
* `PATCH api/v1/admissions{id}`

Примечание: при назначении менеджера, абитуриенту и менеджеру должно быть отправлено уведомление об этом.
Обратите внимание! Главный менеджер и администратор могут просматривать и редактировать данные всех абитуриентов.  

Функционал “Администратора” 
### 1. Возможность импорта справочников: факультет, программа, уровень образования
* `GET api/v1/admin/references`

### 2. Возможность посмотреть статус импорта справочников
* `POST api/v1/references`

### 3. Возможность создать нового менеджера, главного менеджера (Примечание: при создании менеджера на указанный электронный адрес должно быть отправлено уведомление.)
* `POST api/v1/managers`

### 4. Возможность отредактировать данные менеджера 
* `PATCH api/v1/managers/{id}`

### 5. Возможность удалить менеджера, главного менеджера
* `DELETE api/v1/managers/{id}`

