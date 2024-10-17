# Выполнение скриптов при запуске сервера

## Описание

Доработка предназначена для автоматического выполнения различных скриптов, в том числе регистрации глобальных библиотек, при запуске сервера.

## Настройка

Открыть файл `source/api_ext.xml` (если файл отсутствует, нужно создаеть его) и добавить блок `api`

```xml
<?xml version="1.0" encoding="utf-8"?>
<api_ext>
	<apis>
        <!-- В файле могут находиться другие блоки -->

        <!-- Блок, который необходимо добавить -->
		<api>
            <libs>
                <lib>
                    <path>x-local://source/server_startup_scripts.xml</path>
                </lib>
            </libs>
        </api>
        <!-- Блок, который необходимо добавить -->

	</apis>
</api_ext>
```

Скопировать файл `server_startup_scripts.xml` в каталог `source`. Файл должен находиться по тому же пути, который мы указали в `api_ext.xml` в блоке `api` в узле `path`. Все файлы находятся в приложенном архиве.

Код в файле `server_startup_scripts.xml` считывает содержимое кастомной папки `x-local://wtv/_custom_libs`, находит все файлы с расширением `js` или `bs` и последовательно выполняет их код.

Перезапустить сервер и убедиться, что при запуске сервера в логах нет ошибок

## Пример

Создадим каталог `x-local://wtv/_custom_libs`.

В каталоге `_custom_libs` создадим следующюу структуру

```
_custom_libs/
├─ global_variables.js
└─ global_variables/
   └─ const.js
```

```js
// global_variables.js

try {
    RegisterCodeLibrary('./global_variables/const.js');
    alert('Модуль const успешно загружен');
} catch (err) {
    alert('При загрузке модуля const произошла ошибка ' + err)
}
```

```js
// global_variables/const.js

"META:NAMESPACE:const";

var SERVER_MODE = "dev";
```

Перезагрузим сервер.

В логе `xhttp` должны увидеть следующие строки

```ini
18:38:18 [0001] Demo license is used
18:38:18 [0001] [START] Выполнение скриптов из папки x-local://wtv/_custom_libs
18:38:18 [0001] Модуль const успешно загружен
18:38:18 [0001] [FINISH] Выполнение скриптов из папки x-local://wtv/_custom_libs
18:38:18 [0001] External API Lib: x-local://source/server_startup_scripts.xml. Loaded.
18:38:18 [0001] License records num processed.
18:38:18 [0045] Global Server Agent Manager initialized
18:38:18 [0047] Global Person hierarchy update initializing...
18:38:18 [0047] Global Person hierarchy update initialized.
18:38:18 [0048] Global Handle Statements update initializing.
18:38:18 [0050] Global Server Queue Service initialized
18:38:18 [0055] Global Neural Handler initializing...
18:38:18 [0054] Global Media Handler initializing...
18:38:18 [0055] Max Parallel Neural Captures: 8
18:38:18 [0054] Global Media Handler initialized...
18:38:18 [0057] Code library Queue initialized...
18:38:18 [0058] Update Proctoring Sessions Handler initializing...
18:38:18 [0058] Update Proctoring Sessions Handler initialized...
18:38:18 [0001] Server started.
```

В логе видим, что наши глобальные костанты успешно загрузились

## Файлы

[server_startup_scripts.zip](./server_startup_scripts.zip)