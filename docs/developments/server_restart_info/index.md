# Отслеживаем и получаем информацию о перезагрузке сервера

## Описание

Добавляем глобальную переменную `server_restart_info`, в которой содержится информация о перезагрузке сервера

* `date`: дата последней перезагрузки
* `token`: уникальный ИД текущего сеанса

## Настройка

Настройка происходит через механизм загрузки модулей при старте сервера. Подробнее можно прочитать [по этой ссылке](https://galifrey.github.io/websoft-guide/developments/server_startup_scripts/).

Содержимое архива [server_restart_info.zip](./files/server_restart_info.zip) скопировать в папку `x-local://wtv/_custom_libs`.

Должны получить следующю структуру

```
_custom_libs/
├─ server_restart_info.js
└─ server_restart_info/
   └─ store.js
```

Файл server_restart_info.js является инициализирующим
```js
// server_restart_info.js

try {
    RegisterCodeLibrary('./server_restart_info/store.js');
    alert('Модуль server_restart_info успешно загружен');
} catch (err) {
    alert('При загрузке модуля server_restart_info произошла ошибка ' + err)
}
```

Файл store.js выполняет роль хранилища и одновременно регистрируется как глобальная переменная
```js
// server_restart_info/store.js

"META:NAMESPACE:server_restart_info";

var date = Date();
var token = Random(1000000000000000, 9999999999999999);
```

Перезагрузим сервер.

В логе `xhttp` должны увидеть следующие строки

```ini
18:38:18 [0001] Demo license is used
18:38:18 [0001] [START] Выполнение скриптов из папки x-local://wtv/_custom_libs
18:38:18 [0001] Модуль server_restart_info успешно загружен
18:38:18 [0001] [FINISH] Выполнение скриптов из папки x-local://wtv/_custom_libs
18:38:18 [0001] External API Lib: x-local://source/server_startup_scripts.xml. Loaded.
18:38:18 [0001] License records num processed.
18:38:18 [0045] ...
18:38:18 [0001] Server started.
```

## Пример

### Получаем дату запуска сервера и ИД текущего сеанса

```js
alert(server_restart_info.date)
alert(server_restart_info.token)
```

```ini
# xhttp_2024-10-17.log

17.10.2024 20:03:35  17.10.2024 19:53:29
17.10.2024 20:03:35  2433600558484341
```

## Файлы

[server_restart_info.zip](./files/server_restart_info.zip)