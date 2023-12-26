## [AOCCO-1468] debug-service: create basic service
## Learn
[Linux и Unix инструменты](https://www.youtube.com/playlist?list=PLAk6CfuV7hyqHyQVHZMQRihAfebXpxn2O)
- [x] [[tmux]]
- [ ] [[Fish shell]]
- [ ] [[CMake]]
- [ ] [[QEMU]]
- [ ] [[Debug from VS Code]]
## Current Tasks:
- [x] Create endpoint, which allows to execute arbitrary SQL queries   Body shall contain a list of SQL-query-strings, which are executed as a single transaction, one by one, in the order they are provided  
- [x] Create endpoint, which allows to send custom mqtt message to custom topic Body: string:    string: payload  
- [x] Add endpoint to controller  
- [x] Add endpoint to dto
- [x] Add endpoint method to service  
- [x] Find out how to send a custom MQTT message
- [x] Create endpoint, which “clears” all the installers/users   Completely, ignoring “leave at least one administrator”   t_installers and t_auth_users are contain not any row
- [x] Сделать подключение к БД в режиме администратора  
- [x] сделать отдельный флажок на включение сервиса и по умолчанию (если не передавалось значение извне), он будет равен build_type == "Debug". И этот флажок нужно засунуть в корневой CMakeLists  
- [x] there should be subsystem test, which checks that non of the debug endpoints is reachable   The test should always fail for PR/nightly builds   The test should never fail for release builds
- [x] Rename t_auth_installer to t_auth_installer_company
- [x] Rename CMake project to debug_endpoints
- [x] Check if the service is run on start
- [x] Write control_client: нужно написать свой client, как это сделано для doors, persons, transponders, ...
- [x] Prepare PyTest (call REST API "send_mqtt_message" and catch the message)
- [x] Setup Python venv and dependences
- [x] Run Python tests
- [x] Забилить часы!!!
- [x] Test if the endpoints are avaliable if the flag is OFF
- [x] Create Jira task: check if aoc_debug_endpoints are not avaliable in release builds (see [[#18/12/2023]])
- [x] Copy PyTest settings from Alex's brunch (see [[#18/12/2023]])
- [x] Пробежаться по всем моим файлам и сохранить, чтобы поправить выравнивание
- [x] По всем файлам проверить имена на соответствие camel_ case
- [x] По всем хедерам проверить, что private members are first
- [ ] [[#Нужно поправить API тесты]]
- [ ] Add Python test _def test_WHEN_execute_sql_query_THEN_results_are_visible(self):_
- [x] Delete unused endpoint: `"/v1/debug_endpoints`
- [ ] [[#SQL query execution should return JSON]]
- [ ]  [[#Resolve subsystem test preconditions]]
	- [ ] Add precondition
	- [ ] Save data from t_auth_users
	- [ ] Save data from t_auth_installer_company
	- [ ] Add postcondition
	- [ ] Insert data to the tables
	- [ ] Test by SELECT

- [ ] **debug_endpoints_subsystemtests.cc**
	- [x] Check unused includes in debug_endpoints_subsystemtests.cc
	- [ ] debug_endpoints_subsystemtests.cc line 70: If the "setup" part of the test is failed, then continuation of the test is meaningless
	- [x] `TEST_F(Debug_endpoints_Test, GIVEN_clean_state_WHEN_delete_all_users_THEN_success) But the state is not "clean"... (You are inserting some contents into the tables)`
	- [x] line 101: UB if vector/list is empty
- [x] debug_endpoints_service.cc line 17: UB, if first/second is nullptr
- [ ] test_debug_endpoints.py name test_WHEN_post_debug_endpoints_THEN_user_created_mqtt_trigger(self) is wrong
- [ ] How to read errors related to openapi project?
- [ ] Is it possible to build and test openapi project locally?


## Description

Aleksandr Ganiukhin formulated the idea of a debug-service: The idea is to “move” all debug/testing purpose endpoints, which are not expected to be in the release image, into a separate executable (lets call it aoc_debug_endpoints). And this aoc_debug_endpoints is never built/installed/whateverelse during preparing the release.

With this task here, the basic service shall be setup
- basic service   
- providing REST endpoints   
- having the ability to access the DB
- with administrator privileges 
- Helpful, if you need to temporarily disable triggers to quickly purge some table
  
- Use cases
- Fast “wipe” of the blacklist (protected by triggers)  
- Moving database into some inconsistent state to check error recovery 
- subscribing to API gateway 
  
The actual move of debug-endpoints to this service is the task of the originating follow-up task https://abusgroup.atlassian.net/browse/AOCCO-1424 

- Create endpoint, which allows executing arbitrary SQL queries
- Body shall contain a list of SQL-query-strings, which are executed as a single transaction, one by one, in the order they are provided 
- Create endpoint, which allows sending custom mqtt message to custom topic
- Body: 
- string: topic   
- string: payload   
- Create endpoint, which “clears” all the installers/users   
- Completely, ignoring “leave at least one administrator”  
- t_installers and t_auth_users contain not any row 
- protection/authentication for the debug REST endpoints 
- opt 2: no authentication, since it is “debug” scenario
### Acceptance Criteria 
- Implemented according to the description 
- check, that in release builds and SIM-3 like builds this service is not built and thus not running, accessible etc. 
- Ideally there should be a subsystem test, which checks that none of the debug endpoints is reachable 
- The test should always fail for PR/nightly builds 
- The test should never fail for release builds 
### Implementation Hints 
The debug-service will later be used to e.g. GET sensitive information, e.g. DB-user-passwords for development-testing. Thus, it is important, that the debug-service is not part of the release built.



## Daily notes
### 26/12/2023 Tuesday
Утром работал над openapi - внес правки в соответствии с комментариями Александра к pull-реквесту. Но оно все равно не собирается, Дженкинс кидает ошибку при сборке и тестировании, но для меня не информативную. Хочу спросить у Александра как ее читать и можно ли билдить и тестить openapi локально.
### 25/12/2023 Monday

Работал над комментариями к pull реквесту:
##### Нужно поправить API тесты:
в файле debug_endpoints_tests.json после
```
15 "exec": [
16	"pm.test('Endpoint should not exist', function () {",
17	" pm.response.to.have.status(404);",
```
добавить
```
build_type = pm.environment.get("build_type");
if ( build_type === 'Release' ) {
	pm.response.to.be.error;
	pm.response.to.have.status(404);
} else { 
	// ... status 200/400/500?? 
}
```

##### SQL query execution should return JSON
```
{ 
	"results": [ 
		{ 
			"success": true, 
			"message": "" 
		},
		{ 
			"success": false, 
			"message": "Error goes here" 
		} 
	]
}
```

##### Resolve subsystem test preconditions
**Alex' comment:** "*In file **debug_endpoints_subsystemtests.cc** If I got it properly, the test will leave DB not in the state is was initially. This is not a good practice in general; and this is completely not acceptable for this particular case, since after the tests the image may be used in various tasks. But the pre-created JWT tokens can no longer be used to authorize REST requests, since the database has been cleared.*"
Нужно добавить предусловие, вернее, этап перед началом цикла тестов, который считывает и сохраняет содержимое таблиц t_auth_users и t_auth_installer_company. И пост-действие, которое восстанавливает эти данные в этих таблицах.

Ударно поработал, много сделал. Но не успеваю закончить задачу, т.к. Александр уходит на праздники в среду.


### 20/12/2023
Дописал питоновский тест, нужно проверить.
Переименовал все, убрал из названия слово aoc_, оказывается, оно было лишним. Проверил, что Дженкинс все собрал и даже не упал на тестах (кроме API-тестов, которые должны валиться в Debug-е).
Выкачал с Дженкинса вайловую систему для QEMU, нужно проверить, что ничего не сломалось, а мой сервис запускается автоматически (скорее всего, так и есть, иначе API тесты бы не упали)

### 19/12/2023
Пофиксил API тесты: добавил для каждой endpoint, как сакзал Александр.
#Изучаю с [[tmux]] и [[Fish shell]], думаю не переключиться ли на ZSH.
#Изучаю Obsidian.
#Изучаю PyTest
Пофиксил автозапуск своего сервиса: нужно было прописать его в aoc_rest_group.target.in (плюс была опечатка) и переименовать файл aoc_aoc_debug_endpoints.service.in в aoc_debug_endpoints.service.in
Возможно, нужно переименовать весь этот сервис в просто debug_endpoints?

Accroding to ticket [[AOCCO-1443] auth: rename `installer` to `installer_company` - ABUS Group Jira (atlassian.net)](https://abusgroup.atlassian.net/browse/AOCCO-1443)
t_auth_installer table was renamed to t_auth_installer_company

### 18/12/2023

Завеститикет чтобы в релизе флажок был обязательно снят. Прописать, что обязательно нужен тест, который будет проверять, что endpoint-ы не доступны (написать какие)
Проверить, что если опция включена, то билдится, иначе не билдится.
Когда релиз, то опция должна быть мандатори офф. Добавить тест, что в релизе эти endpoint-ы unrichable.
 

- [x] В API тестах добавить еще 3 теста, чтобы был для каждой endpoint. Причем с тем методом, который нужен (POST, DELETE). 
- [x] Убрать строку 18 из теста (где проверяется body ответа)
- [x] Убрать слэш в конце адреса endpoint-а

Чтобы запустить Python-тесты: нужно зайти в папку, запустить виртуальный environment, сделать pip install requirements и вызвать pytest с параметрами (логин, пароль… ![](https://lh7-us.googleusercontent.com/M-AQ0_SLUS16QqlYRMfR2yelX2RoO9On2z7ptrILDBM9KG2dqUg5UFRgp8DBxQM7a591un8QmvZZXBt_3NZkuzKc21-80zZxloWApO6dkOCkfewkT9mBLuwZpyDnnz8_TKWT1ZGYN8jjb_cMbD8mpPI))

Отключить флаг для Python тестов, чтобы они не фейлились, от того, что не все сервисы запущены

![](https://lh7-us.googleusercontent.com/6EjF6LMh1_4_p39eJmj2Vn-zrQzRuc3XhkVvRmZVadQfkNSI_YibCAkc8pNm6EYzRhoBqWzsSILeHlR3CFMvfcpULoMEqngQFceTeeCJkGEWjELwWN0bdUBiCkCQka_82PyOcwCkUOcr_N9WM7QomsY)

 
 

### 15/12/2023


  Сгенерировать Python тест  
  Как добавить мой сервис в автозагрузку  
  Узнать про PyTest  
  How to subscribe to MQTT topic  
  Настроить Debug from VS Code  
  Запускать API тесты Jenkins-ом (как настроить)  
  Возвращать результат выполнения SQL 

Вчера ударно поработал, много сделал. Сегодня продолжаю.

Сделал API тест для проверки существования aoc_debug_endpoints. 
Узнал у Максима, что он дебажится через отладочные сообщения, GDB не запускает. 

Не понятно, как и куда добавить API тесты, чтобы они запускались Jenkins-ом 

Body запроса на публикацию MQTT сообщения:

```
{
  "MQTT messages": {
    "topic" : "payload",
    "payload" : "payload"
  }
}
```

 

### 14/12/2023

Все это время разбираюсь с тестами. Очень долго. Спросил у Максима какие тесты нужны по его мнению:

[Yesterday 11:23 AM] Maksim Kotkov:
Добавь питоновский тестов на выполнение sql строки. Например - добавить какую нибдуь сущность через запрос Проверить что сущность создалась через REST 
Сейм с mqtt, отправить через сервис - проверить что услышалось - на питоне это удобнее всего будет

Готовлю тест API с помощью ChatGPT. Если не сработает, можно будет спросить Миомира как он пишет эти тесты (он сам предложил помощь).

Скачал QEMU, запустил. Вроде, что-то работает, хотя многие тесты фэйлятся. Скорее всего, из-за авторизации - там что-то поменялось, но я пока не хочу в этом разбираться, отложим на потом.

Мой сервис не стартует автоматически. Нужно разобраться как его запускать, где прописать зависимости и пр.

Тестировать под WSL не удобно: нужно запускать кучу сервисов, которые у меня к тому же почему-то не запускаются. Решил, что буду тестировать под QEMU. Только нужно настроить окружение, чтобы собиралось под правильную платформу. Потом хорошо бы настроить Debug из под VS Code, чтобы было как у Electreon-а и не нужно было бы копировать исполняемый файл вручную.

- Скачать QUEMU образ
- Запустить QUMU  
- Запустить API тест, 
- Спросить у Миомира как писать API тесты
- Скачать SDK для QEMU  
- Настроить среду на сборку под ARM 

### 06/12/2023

Продолжаю разбираться с тестами. Допилил немного функционал, то, что упустил. 

Вчера смотрел видео про GTest Framework, в очень общих чертах разобрался что там к чему. Хорошо бы копнуть поглубже, но это позже.

Я уже многое узнал на этом проекте и продолжаю узнавать: REST API, MQTT, GTest, PyTest - это как минимум.

-   Прочитать в описании задачи какие тесты нужны
-   How to get number of rows in a table
-   How to add a row in a table
-   How to reset a table
  
### 04/12/2023

На выходных и сегодня закончил реализацию функционала и устранил некоторые замечания от Александра. Начинаю разбираться с тестами.

Думаю над тем, как билить время - всякие созвоны и работа над задачами. Сложно уследить и в конце недели это делать сложно. Каждый день - лень, но кажется, что это лучше.


---
### 01/12/2023

Сделал Pull-request, чтобы Александр мог смотреть в правильном ли направлении я двигаюсь. Вроде, большая часть встала на свои места, разобрался с тем, как все работает, осталось закодить.

Еще нужно написать тесты, а до этого разобраться как они работают и как их писать.

Была куча созвонов.

### 30/11/2023

Созвон с Александром (30 мин) 
Postgree - как отключить/включить триггер? На таблице администраторов стоит триггер, не позволяющий удалить всех администраторов (нужно оставить хотя бы одного). Нужно его отключить, а затем включить обратно.

Если в _service бросается исключение (и не ловится), то AOTPP возвращает 500 респонз. При этом если бросить http::error, то можно добавить информацию в этот респонз.

QUERY возвращает query_result, нужно посмотреть какие поля он содержит и что можно вернуть: возвращаем 200 с результатом выполнения SQL запроса.

Endpoint clear all users изменить на тип Delete, а слово clear убрать из названия.

Посмотреть в сторону PyTest. Нужно будет добавить эти тесты, а так же subsystemests

### 29/11/2023

|  |  |  |  |
|---|---|---|---|
|Начал|Закончил|Помидоры|Продуктивность|
|10:00<br><br>Тренинги 5 часов|||Средняя|

 -   Нужно найти endpoint, который должен принимать dto и посмотреть как это работает, как парсится тело dto
  
-   Найти endpoint, который выполняет SQL запрос и сделать заготовку для 3-ей endpoint по аналогии
-   Создать свой класс db
- Найти примеры использования mqtt_publisher-а и понять то ли это, что мне нужно
-   Создать класс как в файле access_db_component.h и вместо db_connection_string захардкодить строку для подключения к БД с админскими правами (см. init_database.sh)
  
Проходил тренинги: the mandatory Induction trainings and taking corresponding tests

1. [ISO27001 Information Security Training](https://nam02.safelinks.protection.outlook.com/?url=https%3A%2F%2Fu2255152.ct.sendgrid.net%2Fls%2Fclick%3Fupn%3Dzlht7yItyQ3MoF8zLCFpygWx7MBg4HE4Bc2usgJ-2FVFmK2BJjgNTeDrwyFLwjdgsycXy7_MqcJvfNW4WRmXrQqy3Us4kUqbQuWI1L-2FR0AEcnnPc566KBq8yse0JE1Ou2-2FqQjkn75zqLczL6aqY-2BefTShumQTaUtOYPFcusXNti3JNeQCBHEMqH-2B3eNWH6-2BceVKC7KYmtjKj6hd0AqCp3I4LjMkFv38fa73azF-2B-2FpsqQmyvCgvaCO5uoANZ0CGldwUobDXcmVpKHtQFyWjY802hK9ZwLg-3D-3D&data=05%7C01%7Canatolii.selivanov%40orioninc.com%7C33a095d848e049f311a408dbe9e5dd95%7Cadbbbd8276e5495285313cc59f3c1fdd%7C0%7C0%7C638360945731898876%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C3000%7C%7C%7C&sdata=Dw3lT4EaY%2FmJ2QnPd249RpwBgo0GbjebG4gMlWE7KJ8%3D&reserved=0) – training held online, access recording on this [link](https://nam02.safelinks.protection.outlook.com/?url=https%3A%2F%2Fu2255152.ct.sendgrid.net%2Fls%2Fclick%3Fupn%3Dzlht7yItyQ3MoF8zLCFpyhpbR8SjtTeKrA2AY-2FeoDY3dBJFPDF-2Bp4-2BYZu9iWuXiOUkyOXYRx0fgKvNICZqp5RqxateDzw-2BriGL9ZUpjMCjQPEG859Z3bl0CMeSFhsi9pCRnsCBJ06aD1WLVvApyCb9RYAubK-2BUqhPUKsEqysnrohWUORMUb7ZHE32F79mvNnh1OClVgu-2FZFXE-2BqD9DIrIPQDmJ0jW2FWzTnaI235stq7xrQ097yc52he1N6ytJulDGbN5-2FdcCN1L3cglkJ6SOYExHh1HaHJmDtvbM66754-2FWiP7-2BErAJ3OAf180QJU-2BoIKO47ABjDIIvBzrP4SehGCNkqjKWA9VAX7I7s5RzQe5brq525c2nJKaxYspy7n-2BaDHOF_MqcJvfNW4WRmXrQqy3Us4kUqbQuWI1L-2FR0AEcnnPc566KBq8yse0JE1Ou2-2FqQjknsac-2BL5dsTwq9qFxjUdkweJZDOvSPgoY3kBvA3DBCJShKW7GmNQtVcM1oWkkEfybpv0b8ktSVpUd5FLH620tTBEVwsXKHe4F16JpOTklxrtmHM9rk5WROJyfxxMP7WYKHwNq3FhRIeDPGbmZE0m6IDg-3D-3D&data=05%7C01%7Canatolii.selivanov%40orioninc.com%7C33a095d848e049f311a408dbe9e5dd95%7Cadbbbd8276e5495285313cc59f3c1fdd%7C0%7C0%7C638360945731898876%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C3000%7C%7C%7C&sdata=5QUcK4UjkRftS5egKMgw4%2BVx%2FoAorO8MJ3lef%2Fw8Kew%3D&reserved=0);

2. [Quality requirements and standards awareness training](https://nam02.safelinks.protection.outlook.com/?url=https%3A%2F%2Fu2255152.ct.sendgrid.net%2Fls%2Fclick%3Fupn%3Dzlht7yItyQ3MoF8zLCFpygWx7MBg4HE4Bc2usgJ-2FVFnJYBrzB9MEZKW2BFYbuV3ZBYVw_MqcJvfNW4WRmXrQqy3Us4kUqbQuWI1L-2FR0AEcnnPc566KBq8yse0JE1Ou2-2FqQjknVZbmaaLdvMp0iFM40uuuyFx9BjeUdv-2BtjUFfvVkNpuNippWwtGQWud76pbVhhWptWaJvh4XpMUjyJX2i10mM3Tt5IYqwyvASN0YMAWG6VW-2F0brUkSqRVDj-2BRvFdCZTz9Qku1ypzWbOMx6eelNW-2FtyA-3D-3D&data=05%7C01%7Canatolii.selivanov%40orioninc.com%7C33a095d848e049f311a408dbe9e5dd95%7Cadbbbd8276e5495285313cc59f3c1fdd%7C0%7C0%7C638360945731898876%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C3000%7C%7C%7C&sdata=1u%2FodqceU4dH%2B2GOjb9CG7Kc8rK%2BHBwVAmglgWDyZ%2B0%3D&reserved=0) – training held online, access recording on this [link](https://nam02.safelinks.protection.outlook.com/?url=https%3A%2F%2Fu2255152.ct.sendgrid.net%2Fls%2Fclick%3Fupn%3Dzlht7yItyQ3MoF8zLCFpyhpbR8SjtTeKrA2AY-2FeoDY3dBJFPDF-2Bp4-2BYZu9iWuXiOUkyOXYRx0fgKvNICZqp5RqxateDzw-2BriGL9ZUpjMCjQPEG859Z3bl0CMeSFhsi9pCRnsCBJ06aD1WLVvApyCb9RYAubK-2BUqhPUKsEqysnrohWUORMUb7ZHE32F79mvNnfhMdvCZPCV7hTJ1l4Z6yaQicukcGEj7vvbTSJnErOSpkcNldnZtpiH72cNxFlRoyDIE7-2F5Q9GO1LxWznP2xmtTUwz9FS8tf75zHxLHul0NNJllJa9WZZs9I8H1TDLe9-2FvwEQ5K8JLj-2FlZZlPOy20L2uminY9bGVKz4eA6arFO2VVMYb3rCsEZjjTrj2u1F20TOZg_MqcJvfNW4WRmXrQqy3Us4kUqbQuWI1L-2FR0AEcnnPc566KBq8yse0JE1Ou2-2FqQjknGdsKPBrJXQCcsVtp5IyXeIvvH34XxOXy5weuPIl-2B1Fr3aVNXWzvVI7ftMmq6w5gvmHjEpzR2-2BYGaUpJB-2BgRef5EEn27b0KrBtm8jP4raNw5VOJVcKhDVXQgMtb9jhHirRTB2sF11zvhtBplX4IBqUQ-3D-3D&data=05%7C01%7Canatolii.selivanov%40orioninc.com%7C33a095d848e049f311a408dbe9e5dd95%7Cadbbbd8276e5495285313cc59f3c1fdd%7C0%7C0%7C638360945731898876%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C3000%7C%7C%7C&sdata=lu2OrWmLU6b3y79LFeSwJgQTqsu%2BOYUl106gkASiss8%3D&reserved=0);

3. [ElectroStatic Discharge (ESD) protection: rules of handling of ESD sensitive hardware and components](https://nam02.safelinks.protection.outlook.com/?url=https%3A%2F%2Fu2255152.ct.sendgrid.net%2Fls%2Fclick%3Fupn%3Dzlht7yItyQ3MoF8zLCFpygWx7MBg4HE4Bc2usgJ-2FVFmkV1JVq4rjwPkTAYvNfwJ8yZXA_MqcJvfNW4WRmXrQqy3Us4kUqbQuWI1L-2FR0AEcnnPc566KBq8yse0JE1Ou2-2FqQjknfjuWP-2BdQW7aOLXKGAkucA2eq1lk71O4yMwDnWan2bRzobw3oVYYFV6mCDoFI7uDwXvwhAw0yvwdM0V6mYLeLcR-2BBp8EUjLloTz600-2Fxg-2Bic2ODUN3PkJnSJVr2XxFzoaChxS1JJPrr-2BUoui-2BpzBZcQ-3D-3D&data=05%7C01%7Canatolii.selivanov%40orioninc.com%7C33a095d848e049f311a408dbe9e5dd95%7Cadbbbd8276e5495285313cc59f3c1fdd%7C0%7C0%7C638360945731898876%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C3000%7C%7C%7C&sdata=HUXpWLu9npnzEkTBglFFZKoefTVTfVeLdNVOEUqgk4Q%3D&reserved=0)- self-study;

4. [General Data Protection Regulation training](https://nam02.safelinks.protection.outlook.com/?url=https%3A%2F%2Fu2255152.ct.sendgrid.net%2Fls%2Fclick%3Fupn%3Dzlht7yItyQ3MoF8zLCFpygWx7MBg4HE4Bc2usgJ-2FVFkTpUek9J2w-2BHtjoxyNIsh0baI-_MqcJvfNW4WRmXrQqy3Us4kUqbQuWI1L-2FR0AEcnnPc566KBq8yse0JE1Ou2-2FqQjknnOAnBR4U3IanVu462-2Fu0JSY4n7bCzden-2FOg5JfT1PG1f8aqW8JjGvBmOtGy3LLL-2BOh0m-2FiqFB9lo2kIUW3O7ZmenFaCFFQUmibaqKtzw8NXlq721K-2Bs0tTJFApgMCiYIzbUIZ5DZ-2BwgI4RFhhuqX6w-3D-3D&data=05%7C01%7Canatolii.selivanov%40orioninc.com%7C33a095d848e049f311a408dbe9e5dd95%7Cadbbbd8276e5495285313cc59f3c1fdd%7C0%7C0%7C638360945731898876%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C3000%7C%7C%7C&sdata=ObQAtQZBqk%2BVyOpZSCHYer938d5UIIXdAokCNGgpbFE%3D&reserved=0)- self-study;

5. [Orion Anti-Bribery & Anti-Corruption training](https://nam02.safelinks.protection.outlook.com/?url=https%3A%2F%2Fu2255152.ct.sendgrid.net%2Fls%2Fclick%3Fupn%3Dzlht7yItyQ3MoF8zLCFpygWx7MBg4HE4Bc2usgJ-2FVFkfapo-2FK8G1WnLswX4w1FRb-44k_MqcJvfNW4WRmXrQqy3Us4kUqbQuWI1L-2FR0AEcnnPc566KBq8yse0JE1Ou2-2FqQjknBmQID0aPdmoCFit4AkkT55sHLiZleHVphLIlx4lW1Q7BsWl8-2FyysuG-2F6LoJoF8kpbGibjPw9GweDwm7HuRw1YVkpmZcSUkMWLFMO6mp-2Fqq5Qo10FzILkwpQPTg4wTcxWQ5KImtx1mxzwqXtiHM88RA-3D-3D&data=05%7C01%7Canatolii.selivanov%40orioninc.com%7C33a095d848e049f311a408dbe9e5dd95%7Cadbbbd8276e5495285313cc59f3c1fdd%7C0%7C0%7C638360945731898876%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C3000%7C%7C%7C&sdata=oh%2F6UTdVnPs5xdL5shNHWr%2BPkJx%2B%2BI7bkWNUUGlMVyQ%3D&reserved=0)- self-study.

Можно списать на них 5 часов.

### 28/11/2023 

|  |  |  |  |
|---|---|---|---|
|Начал|Закончил|Помидоры|Продуктивность|
|8:30<br><br>Консультация Михаила 1:30<br><br>Консультация с Александром 30 мин|||Средняя|

Вчера мучился с системой сборки - не собиралось, не хватало зависимостей и путей, были опечатки в путях и еще какие-то проблемы. Наконец-то все пофиксил, собирается.

Добавил обертку для первой endpoint. Сегодня нужно добавить остальные.

Хочу задать вопросы Александру:
- Как сделать подключение к БД с правами администратора?
- Я сделал тип endpoint-ов post, это ОК или нужен другой? Нужно ли добавлять еще какие-то (DELETE, READ и пр.)?  
- Что по времени? Я не укладываюсь в заложенный срок, вижу 2 варианта: отвлекать кого-то кучей вопросов или выбиваться из срока. Если задавать вопросы, то:
- Как посылать SQL запросы в БД?
- Как отправлять MQTT сообщение?
  
Controller:
Types of Responses: <Object<Status_dto>>, <Object<your_custom_dto>>, String

Service:
IMgtt_Publisher m_mqtt_publisher - нужно узнать что он делает, где и как используется
Blacklist_db m_database - определен в db.h. Содержит возможные SQL запросы.

### 27/11/2023

|  |  |  |  |
|---|---|---|---|
|Начал|Закончил|Помидоры|Продуктивность|
|8|18||Средняя|

Продолжаю работать над задачей. За прошлую неделю забилил на нее 24 часа, осталось 16. Похоже, я не укладываюсь в выделенное время. Это плохо.

Кажется, разобрался как добавлять endpoint-ы. В процессе добавления одного. Нужно описать здесь как это работает, собрать рядом все названия, чтобы было нагляднее.

Для Blacklist:

Controller:
DTO:
Service:
Controller

ENDPOINT_INFO( send_mqtt_message )

ENDPOINT( util::http::http_post_method_name,

       "v1/aoc_debug_endpoints/mqtt",

       send_mqtt_message,

       BODY_DTO( Object<Aoc_debug_endpoints_post_mqtt_dto>, dto ),

       AUTHORIZATION( std::shared_ptr<External_authorization_object>, auth_object ) )

  {

    std::ignore = auth_object;

    return createDtoResponse( Status::CODE_200, m_aoc_debug_endpoints_service.aoc_debug_endpoints_send_mqtt( dto ) );

  }
Service
DTO

---

 

### 24/11/2023 

|  |  |  |  |  |  |  |  |  |  |  |
|---|---|---|---|---|---|---|---|---|---|---|
|Начал|Закончил|Помидоры|  |  |  |  |  |  |  |Продуктивность|
|9:10|6:30|6||||||||Средняя|

- Разобраться как работают сервисы: кто кого вызывает/создает
- Добавить 3 endpoint-а  
- Прочитать про AOTPP - он генерит код и на нем многое завязано  

Вчера начал по-немногу вникать и понимать как что работает. Еще не до конца, но хоть какие-то мысли появляются. Вообще, нужно ускоряться, конечно. Вчера день прошел так-себе из-за того, что не выспался.

Что я понял о том, как все работает:

В папке сервиса есть main.cc c функцией main для этого сервиса. Там и создается сам сервис. Он наследуется от aoc::Oatpp_application, конструктор принимает имя сервиса, которое определено в `include/…constants`. Там же определены: 
`host
`port
`monitoring_interval
`endpoint_path

В main() вызывается перегруженный оператор () созданного сервиса. Он добавляет сервис в мониторинг.

Создает MQTT клиента и endpoint-service. endpoint-service принимает параметром объект Aoc_debug_endpoints_controller. 

Контроллер содержит в себе Aoc_service. В контроллере описаны endpoint-ы:

ENDPOINT_INFO( send_mqtt_message )

ENDPOINT( util::http::http_post_method_name,

       "v1/aoc_debug_endpoints/mqtt",
       send_mqtt_message,
       BODY_DTO( Object<Aoc_debug_endpoints_post_mqtt_dto>, dto ),
       AUTHORIZATION( std::shared_ptr<External_authorization_object>, auth_object ) )

В dto определены классы, соответствующие принимаемым данным: название endpoint

Вопросы:
- Как работает db  
- Как работает dto  
- Какие типы добавить в ENUM  
- Как назвать endpoint-ы?  
- Где посмотреть описание сервиса blacklist: какие у него endpoint-ы?  
- Что делает service и для чего он нужен?
  

### 23/11/2023

|  |  |  |  |  |  |  |  |  |  |  |
|---|---|---|---|---|---|---|---|---|---|---|
|Начал|Закончил|Помидоры|  |  |  |  |  |  |  |Продуктивность|
|9:10||||||||||Низкая|

Мало спал, тяжело работать, но продвинулся по задаче. Не делал помидоров. 
Сегодня первый раз был дэйли митинг с командой, познакомились.

Александр сказал, что с сервисом все Ок. Нужно немного условие включения сервиса изменить: сделать отдельный флажок на включение сервиса и по умолчанию (если не передавалось значение извне), он будет равен build_type == "Debug". И этот флажок нужно засунуть в корневой CMakeLists.

Максим пишет:

Привет, 
LABSETUP:
GUI MANAGER http://demsbld001:50002/job/aoc_gui/
TDP_AND FRID_PROV: http://demsbld001:50002/job/aoc_tdp/job/aoc_tdp_nightly/
director_template : https://bitbucket.abus.com/projects/ACC/repos/transponder-data-processor/browse/code/rfid-reader/tools 
Если у тебя доступ появился - вот это качай и приходи как будет желание - покажу как с бордой работать.

 

Был внутренний митинг. Обсуждали проблемы с предыдущими задачами: одна заняла в 4 раза больше времени, чем предполагалось. Предложили использовать TDD, т.е. В начале писать тесты и use-кейсы и ориентироваться на них. А так же задавать вопросы и при этом рисовать диаграммы для большей наглядности, чтобы как можно точнее понять задачу и не приходилось ее переделывать.

### 22/11/2023

До сих пор проблема с пониманием что такое базовый сервис. Есть идея как к нему подступиться: нужно создать пустой класс только с конструктором по аналогии с другими сервисами, посмотреть что этот конструктор принимает в качестве параметров и по-немногу добавлять необходимое.

Еще идея - посмотреть в git как создавали другие сервисы, с чего начинали и как коммитили и повторить эти шаги.

Нашел как создавали blacklist сервис, он изначально тоже был basic, поэтому пробую начать оттуда.

Скопировал basic service из blacklist-а, побилдил.

В CMake настроил чтобы билдилось только для Дебага.

Написал Александру, чтобы он посмотрел. 

Теперь нужно разобраться с endpoint-ами и подключением к БД.

Отработал 6 помидоров (может, чуть больше из-за погрешности измерений). Больше сегодня не могу, не соображаю =( 
Начал около 11, заканчиваю в 17.

### 21/11/2023

Вчера побилдил проект, пришлось спрашивать помощи, сейчас как минимум билдится. Тесты не все проходят, не разбирался почему.

Прислали первую рабочую задачу: [AOCCO-1468] debug-service: create basic service

Пытаюсь разобраться что вообще нужно сделать. Договорился созвониться с Александром, чтобы он рассказал подробнее.

Разговаривал с Александром, задавал глупые вопросы и не понятно зачем созванивался. Нужно было подумать получше и повнимательнее читать описание задачи.

Правда, все равно не до конца понимаю как выполнить задачу =) Зато теперь понятно что нужно делать:

- В папке endpoint_services создать базовый сервис (что такое базовый сервис?)
- Controller
- Db
- Dto
- service
- Добавить в этот сервис 3 endpoints
- Подготовить CMakeLists чтобы этот сервис собирался только для Debug

## [Onboarding]

### 20/11/2023

- Поставить SDK
- Build project  
-   Run tests
В пятницу наконец-то дали доступ к ресурсам Abus-а. Скачал SDK и исходники проекта, но не успел ничего установить и настроить. Сегодня с утра смотрю видео по установке и настройке SDK, а также сборке проекта.
