# Работа с WebSoft.Zip.dll

## Подключение
!!! warning "Работа с .Net библиотеками возможна только на сервере"

!!! warning "Проверялось на следующих версиях WebSoft.Zip.dll"
    - 1.22.6.8
    - 1.22.11.1

Создание класса для работы с библиотекой
```js
oZip = tools.get_object_assembly("Zip")
```

## Методы

* [GetVersion()](#getversion) - Возвращает версию библиотеки.
* [GetError()](#geterror) - Возвращает текст последней ошибки.
* [Save()](#save) - Заглушка: метод возвращает 1, если архив открыт.
* [Close()](#close) - Закрывает архив и освобождает ресурсы.
* [CharSet](#charset) - Свойство с геттером и сеттером. Кодировка для имён файлов.
* [CompressionLevel](#compressionlevel) - Свойство с геттером и сеттером. Уровень сжатия (0 — без сжатия, 1 — сжатие).
* [SetCompressionLevel](#setcompressionlevel)(level) - Устанавливает уровень сжатия (0 — без сжатия, 1 — сжатие).
* [CreateArchive(path)](#createarchive) - Создаёт новый ZIP-архив, удаляя старый при наличии.
* [OpenArchive(path, access)](#openarchive) - Открывает архив с доступом на чтение.
* [OpenOrCreate(path)](#openorcreate) - Открывает существующий архив или создаёт новый.
* [AddFiles(path)](#addfileaddfiles) - Добавляет файлы по маске (*.ext) в корень архива.
* [AddFile(path)](#addfileaddfiles) - То же самое, что AddFiles().
* [AddFilesToPath(path, pathInArchive)](#addfilestopath) - Добавляет файлы с указанием папки в архиве.
* [AddDirectory(path)](#adddirectory) - Добавляет все файлы и папки из указанной директории.
* [AddDirectoryToPath(path, pathInArchive)](#adddirectorytopath) - То же самое, но с указанием относительного пути в архиве.
* [Extract(outputPath)](#extract) - Извлекает всё содержимое архива в указанный каталог.
* [ExtractFiles(path, dirInArchive, outputPath)](#extractfiles) - Извлекает файл из подкаталога архива.
* [ListFiles()](#listfiles) - Возвращает список всех файлов в архиве.

### GetVersion

Возвращает текующую версию библиотеки

_Код_

```js
oZip = tools.get_object_assembly("Zip")
var version = oZip.GetVersion()
alert(version)
```

_Результат_

![getversion_1](./img/getversion_1.png)

### GetError

Возвращает текст последней ошибки.

!!! warning "ВАЖНО"
    Эксперименты проводились в агенте, который запускался на стороне сервера. Если при первом запуске агента сгенерировать ошибку, потом запустить агент, где будет **только** вывод ошибки, `GetError()` будет содержать ошибку из первого запуска. Перезагрузка сервера ошибку не очищает.
    
    Библиотека кешируется.

Для получения ошибки попытаемся открыть несуществующий архив

_Код_

```js
oZip = tools.get_object_assembly("Zip")
// Архив other.zip не существует
oZip.OpenArchive(UrlToFilePath('x-local://trash/other.zip'))
alert(oZip.GetError())
```

_Результат_

![geterror_0](./img/geterror_0.png)

### Save

Используется в связке с `Close()` в коробочном коде. Но в `dll` он просто возвращает 1, если архив открыт.

_Код_

```js
oZip = tools.get_object_assembly("Zip")

...
// Работа с архивом
...

oZip.Save()
oZip.Close()
oZip = null
```

### Close

Закрывает архив

_Код_

```js
oZip = tools.get_object_assembly("Zip")

...
// Работа с архивом
...

oZip.Save()
oZip.Close()
oZip = null
```

### CharSet

!!! note "Информация"
    Кодировка меняется глобально, так как после перезагрузки сервера значение сохраняется.

    Библиотека кешируется.

Использование в качестве геттера возвращает текущую кодировку по умолчанию

_Код_

```js
oZip = tools.get_object_assembly("Zip")
var charset = oZip.CharSet
alert(charset)
```

_Результат_

![charset_getter_1](./img/charset_getter_1.png)

Использование в качестве сеттера - устанавливаем свою кодировку

_Код_

```js
oZip = tools.get_object_assembly("Zip")
var charsetDefault = oZip.CharSet
alert("Кодировка по умолчанию: " + charsetDefault)

oZip.CharSet = "utf-8"
var charsetNew = oZip.CharSet
alert("Кодировка новая: " + charsetNew)
```

_Результат_

![charset_setter_1](./img/charset_setter_1.png)

При работе с файлами и папками в названии которых используется кириллица, необходимо менять кодировку на `utf-8`, иначе названия файлов превращаются в иероглифы

Создадим архивы с кодировкой `windows-1251` и кодировкой `utf-8`. Посмотрим что вышло в каждом случае

_Структура данных_

![charset_structure](./img/charset_structure.png)

_Код создания архивов_

```js
oZip = tools.get_object_assembly("Zip")
oZip.CharSet = "windows-1251" // "utf-8", "windows-1251"
oZip.OpenOrCreate(UrlToFilePath('x-local://trash/archiveWindows1251.zip'))
oZip.AddDirectory(UrlToFilePath('x-local://trash/test_structure'))
oZip.Save()
oZip.Close()
oZip = null

oZip = tools.get_object_assembly("Zip")
oZip.CharSet = "utf-8" // "utf-8", "windows-1251"
oZip.OpenOrCreate(UrlToFilePath('x-local://trash/archiveUtf8.zip'))
oZip.AddDirectory(UrlToFilePath('x-local://trash/test_structure'))
oZip.Save()
oZip.Close()
oZip = null
```

_Результат_

Распаковываем арихивы `archiveWindows1251.zip` и `archiveUtf8.zip` и смотрим кодировку

![charset_unpack_win1251](./img/charset_unpack_win1251.png)
![charset_unpack_utf8](./img/charset_unpack_utf8.png)

!!! note "Информация"
    Все манипуляции распаковки производились стандартным распаковщиком Windows 11 и 7-zip.  
    При распаковке WinRar кодировка корректная

### CompressionLevel

!!! note "Информация"
    Уровень сжатия меняется глобально, так как после перезагрузки сервера значение сохраняется. 

    Библиотека кешируется.

Использование в качестве геттера возвращает текущий уровень сжатия

_Код_

```js
oZip = tools.get_object_assembly("Zip")
var compressionLevel = oZip.CompressionLevel
alert("Сжатие по умолчанию: " + compressionLevel)
```

_Результат_

![compressionlevel_getter_1](./img/compressionlevel_getter_1.png)

Использование в качестве сеттера - устанавливаем своё сжатие

_Код_

```js
oZip = tools.get_object_assembly("Zip")
var compressionLevelDefault = oZip.CompressionLevel
alert("Сжатие по умолчанию: " + compressionLevelDefault)

oZip.CompressionLevel = 0
var compressionLevelNew = oZip.CompressionLevel
alert("Сжатие новое: " + compressionLevelNew)
```

_Результат_

![compressionlevel_setter_1](./img/compressionlevel_setter_1.png)

Измерения уровней сжатия в качестве эксперимента от 0 до 10 показали, что есть только 2 уровня 0 или 1.

![compresslevel_0_10](./img/compresslevel_0_10.png)

### SetCompressionLevel

Устанавливает уровень сжатия. Делает то же самое что и геттер `CompressionLevel`, только тут это метод с параметром.

Возвращает `1` при успешной установке уровня сжатия и `0` - если что-то пошло не так

_Код_

```js
oZip = tools.get_object_assembly("Zip")
oZip.SetCompressionLevel(0)
oZip.OpenOrCreate(UrlToFilePath('x-local://trash/archiveCompress_0.zip'))
oZip.AddDirectory(UrlToFilePath('x-local://trash/test_structure'))
oZip.Save()
oZip.Close()
oZip = null

oZip = tools.get_object_assembly("Zip")
oZip.SetCompressionLevel(1)
oZip.OpenOrCreate(UrlToFilePath('x-local://trash/archiveCompress_1.zip'))
oZip.AddDirectory(UrlToFilePath('x-local://trash/test_structure'))
oZip.Save()
oZip.Close()
oZip = null
```

_Результат_

![setcompressionlevel_0](./img/setcompressionlevel_0.png)

### CreateArchive

Создает пустой архив по переданному пути.  
Возвращает `1` при успешном создании архива и `0` - если что-то пошло не так

!!! warning "ВАЖНО"
    Работает только с файловыми путями. Не работает, если указать путь вида `x-local://trash/archive.zip`

!!! warning "ВАЖНО"
    Если такой архив уже существует, то он будет удален и создан новый пустой

_Код_

```js
oZip = tools.get_object_assembly("Zip")
oZip.CreateArchive(UrlToFilePath('x-local://trash/archive.zip'))
oZip.Save()
oZip.Close()
oZip = null
```

_Результат_

![createarchive_0](./img/createarchive_0.png)

### OpenArchive

Открывает существующий архив. Архив доступен только для чтения.  
Возвращает `1` при успешном открытии архива и `0` - если что-то пошло не так

!!! note "Информация"
    Второй параметр `access` как будто-бы не работает, в dll указано 6 доступов открытия, но указание любого уровня вторым параметром генерирует ошибку

    CreateNew = 1, Create = 2, Open = 3, OpenOrCreate = 4, Truncate = 5, Append = 6,

_Код_

```js
oZip = tools.get_object_assembly("Zip")
oZip.OpenArchive(UrlToFilePath('x-local://trash/archive.zip'))
var listFiles = oZip.ListFiles()
alert(tools.object_to_text(listFiles, "json"))

oZip.Save()
oZip.Close()
oZip = null
```

_Результат_

![openarchive_0](./img/openarchive_0.png)

### OpenOrCreate

Метод позволяет открыть архив и дописать в него файлы, что нельзя сделать через `OpenArchive`. Если архив не существует, то метод его создает.

_Код_

```js
oZip = tools.get_object_assembly("Zip")
oZip.CreateArchive(UrlToFilePath('x-local://trash/archive.zip'))
oZip.AddFile(UrlToFilePath('x-local://trash/test_structure/new document.txt'))
oZip.Save()
oZip.Close()
oZip = null

oZip = tools.get_object_assembly("Zip")
oZip.OpenOrCreate(UrlToFilePath('x-local://trash/archive.zip'))
oZip.AddFile(UrlToFilePath('x-local://trash/test_structure/wtv_tools.xml'))
oZip.Save()
oZip.Close()
oZip = null
```

_Результат_

![openorcreate_0](./img/openorcreate_0.png)

### AddFile/AddFiles

Добавляем один файл

_Список файлов_

![addfiles_files](./img/addfiles_files.png)

_Код_

```js
oZip = tools.get_object_assembly("Zip")
oZip.OpenOrCreate(UrlToFilePath('x-local://trash/archive.zip'))
oZip.AddFile(UrlToFilePath('x-local://trash/test_structure/mask/file_1.txt'))

oZip.Save()
oZip.Close()
oZip = null
```

_Результат_

![addfile_one](./img/addfile_one.png)

Добавляем несколько файлов используя символ подстановки `*`

_Список файлов_

![addfiles_files](./img/addfiles_files.png)

_Код_

```js
oZip = tools.get_object_assembly("Zip")
oZip.OpenOrCreate(UrlToFilePath('x-local://trash/archive.zip'))
oZip.AddFile(UrlToFilePath('x-local://trash/test_structure/mask/*.txt'))

oZip.Save()
oZip.Close()
oZip = null
```

_Результат_

![addfiles_txt_0](./img/addfiles_txt_0.png)

### AddFilesToPath

_Список файлов_

![addfilestopath_files](./img/addfilestopath_files.png)

_Код_

```js
oZip = tools.get_object_assembly("Zip")
oZip.CreateArchive(UrlToFilePath('x-local://trash/archive.zip'))
oZip.AddFilesToPath(UrlToFilePath('x-local://trash/test_structure/mask/file_*.docx'), 'deep_folder')

oZip.Save()
oZip.Close()
oZip = null
```

_Результат_

!!! note "Файлы в архиве находятся в папке `deep_folder`"
![addfilestopath_0](./img/addfilestopath_0.png)

### AddDirectory

_Список файлов_

![adddirectory_structure](./img/adddirectory_structure.png)

_Код_

```js
oZip = tools.get_object_assembly("Zip")
oZip.CreateArchive(UrlToFilePath('x-local://trash/archive.zip'))
oZip.AddDirectory(UrlToFilePath('x-local://trash/test_structure'))

oZip.Save()
oZip.Close()
oZip = null
```

_Результат_

![adddirectory_0](./img/adddirectory_0.png)

### AddDirectoryToPath

_Список файлов_

![adddirectorytopath_structure](./img/adddirectorytopath_structure.png)

_Код_

```js
oZip = tools.get_object_assembly("Zip")
oZip.CreateArchive(UrlToFilePath('x-local://trash/archive.zip'))
oZip.AddDirectoryToPath(UrlToFilePath('x-local://trash/test_structure'), 'deep_folder')

oZip.Save()
oZip.Close()
oZip = null
```

_Результат_

![adddirectorytopath_0](./img/adddirectorytopath_0.png)

### Extract

_Код_

```js
oZip = tools.get_object_assembly("Zip")
oZip.OpenArchive(UrlToFilePath('x-local://trash/archive.zip'))
oZip.Extract(UrlToFilePath('x-local://trash/extract_archive/'))

oZip.Close()
oZip = null
```

_Результат_

![extract_0](./img/extract_0.png)

### ExtractFiles

!!! warning "ВАЖНО"
    Первый параметр - не используется.  
    Второй параметр - путь к файлу относительно корня архива. Подстановки не работают

_Код_

```js
oZip = tools.get_object_assembly("Zip")
oZip.OpenArchive(UrlToFilePath('x-local://trash/archive.zip'))
oZip.ExtractFiles('', 'mask/file_1.txt', UrlToFilePath('x-local://trash/testUnzipFolder'))

oZip.Close()
oZip = null
```

_Результат_

![extractfiles_0](./img/extractfiles_0.png)

### ListFiles

_Код_

```js
oZip = tools.get_object_assembly("Zip")
oZip.OpenArchive(UrlToFilePath('x-local://trash/archive.zip'))
var listFiles = oZip.ListFiles()
alert(tools.object_to_text(listFiles, "json"))

oZip.Close()
oZip = null
```

_Результат_

![listfiles_0](./img/listfiles_0.png)