# Работа с WebSoft.Zip.dll

## Подключение
!!! warning "Работа с .Net библиотеками возможна только на сервере"

!!! warning "Проверялось на следующих версиях WebSoft.Zip.dll"
    - 1.22.11.1

Создание класса для работы с библиотекой
```js
oRegExp = tools.get_object_assembly("RegExp")
```

## Методы

- [**GetVersion()**](#getversion)  
    Возвращает версию сборки `WebSoft.RegExp.dll`.

- [**Pattern**](#pattern)  
    Геттер/Сеттер. Шаблон регулярного выражения.

- [**Global**](#global)  
    Геттер/Сеттер. Не используется в текущей реализации. Предположительно зарезервировано для расширения (например, аналог `g` в JavaScript).

- [**IgnoreCase**](#ignorecase)  
    Геттер/Сеттер. Влияет на чувствительность к регистру при поиске.

- [**MultiLine**](#multiline)  
    Геттер/Сеттер. Управляет тем, как символы начала `^` и конца строки `$` работают в многострочном режиме.

- [**IsMatch(source)**](#ismatch)  
    Проверяет, соответствует ли хотя бы одна подстрока из `source` шаблону.

- [**Replace(source, repl)**](#replace)  
    Заменяет вхождения, соответствующие шаблону `Pattern`, на строку `repl` в переданной строке `source`.

- [**Execute(source)**](#execute)  
    Выполняет поиск всех совпадений по шаблону в строке `source`, возвращает коллекцию совпадений.

### GetVersion

_Код_

```js
oRegExp = tools.get_object_assembly('RegExp')
alert(regExp.GetVersion())
```

_Результат_

```log
17:10:35 [0135]	1.22.11.1
```

### Pattern

!!! warning "ВАЖНО"
    Шаблон задается строкой, обратите внимание на то, что нужно экранировать некоторые спецсимволы. На примере ниже показан шаблон без экранирования

_Код_

```js
oRegExp = tools.get_object_assembly('RegExp')
alert('Паттерн по умолчанию: ' + oRegExp.Pattern)

oRegExp.Pattern = 'Name:\s*(\w+),\s*Age:\s*(\d+)'
alert('Паттерн без экранирования: ' + oRegExp.Pattern)

oRegExp.Pattern = 'Name:\\s*(\\w+),\\s*Age:\\s*(\\d+)' // Обязательное экранирование обратной косой черты
alert('Паттерн с экранированием: ' + oRegExp.Pattern)
```

_Результат_

``` log
17:22:39 [0865]	Паттерн по умолчанию: undefined
17:22:39 [0865]	Паттерн без экранирования: Name:s*(w+),s*Age:s*(d+)
17:22:39 [0865]	Паттерн с экранированием: Name:\s*(\w+),\s*Age:\s*(\d+)
```

### Global

Судя по коду в dll - не используется в текущей реализации 

### IgnoreCase

_Код_

```js
oRegExp = tools.get_object_assembly('RegExp')
oRegExp.Pattern = 'ББ'
var text = '0000_бб_0000'

alert('Шаблон поиска: ' + oRegExp.Pattern)
alert('Текст: ' + text)

oRegExp.IgnoreCase = true
alert('IgnoreCase = true -> ' + oRegExp.IsMatch(text))

oRegExp.IgnoreCase = false
alert('IgnoreCase = false -> ' + oRegExp.IsMatch(text))
```

_Результат_

```log
17:42:38 [0537]	Шаблон поиска: ББ
17:42:38 [0537]	Текст: 0000_бб_0000
17:42:38 [0537]	IgnoreCase = true -> true
17:42:38 [0537]	IgnoreCase = false -> false
```

### MultiLine

_Код_

```js
oRegExp = tools.get_object_assembly('RegExp')
oRegExp.Pattern = '^Error:.*$'
var text = 'Info: Server starting...\nError: Неизвестная ошибка\nInfo: Server started fail'

alert('Шаблон поиска: ' + oRegExp.Pattern)
alert('Текст: ' + text)

// MultiLine = true: ^ и $ работают на каждой строке отдельно
oRegExp.MultiLine = true
alert('MultiLine = true -> ' + oRegExp.Execute(text).Item)

// MultiLine = false: ищет ^ и $ только в начале и конце всей строки
oRegExp.MultiLine = false
alert('MultiLine = false -> ' + oRegExp.Execute(text).Item)
```

_Результат_

```log
17:55:43 [0104]	Шаблон поиска: ^Error:.*$
17:55:43 [0104]	Текст: Info: Server starting...
Error: Неизвестная ошибка
Info: Server started fail
17:55:43 [0104]	MultiLine = true -> Error: Неизвестная ошибка
17:55:43 [0104]	MultiLine = false -> undefined
```

### IsMatch

_Код_

```js
oRegExp = tools.get_object_assembly('RegExp')
oRegExp.Pattern = 'Name:\\s*(\\w+),\\s*Age:\\s*(\\d+)'

alert('Регулярка: ' + oRegExp.Pattern)

var text1 = 'Name: Владислав, Age: 99'
alert('Текст: ' + text1)
alert('Результат соответствия: ' + oRegExp.IsMatch(text1))

var text2 = 'Name - Владислав, Age - 99'
alert('Текст: ' + text2)
alert('Результат соответствия: ' + oRegExp.IsMatch(text2))
```

_Результат_

```log
18:27:58 [0654]	Регулярка: Name:\s*(\w+),\s*Age:\s*(\d+)
18:27:58 [0654]	Текст: Name: Владислав, Age: 99
18:27:58 [0654]	Результат соответствия: true
18:27:58 [0654]	Текст: Name - Владислав, Age - 99
18:27:58 [0654]	Результат соответствия: false
```

### Replace

_Код_

```js
var oRegExp = tools.get_object_assembly('RegExp')
oRegExp.Pattern = '[0-9]'

// Простая замена. Заменяем каждую цифру в строке на символ *.
var text1 = '1a2b3c'
alert(oRegExp.Replace(text1, '*'))

// Замена с использованием групп. 
// Регулярное выражение извлекает имя и возраст из строки и подставляет их в новую строку
var text2 = 'Name: Владислав, Age: 99'
oRegExp.Pattern = 'Name:\\s*(\\w+),\\s*Age:\\s*(\\d+)'
alert(oRegExp.Replace(text2, '[$1] - [$2]'))
```

_Результат_

```log
19:26:04 [0164]	*a*b*c
19:26:04 [0164]	[Владислав] - [99]
```

### Execute

Метод `Execute` возвращает коллекцию `MatchCollection`

_Код_

```js
var oRegExp = tools.get_object_assembly('RegExp')

oRegExp.Pattern = '\\d{1,3}'
var text = '+7 111 222 33 44'

// Метод Execute возвращает обёртку над коллекцией совпадений
// — фактически это объект MatchCollection
var oMatches = oRegExp.Execute(text)
alert(oMatches) // Websoft.RegExp.MatchCollection
```

_Результат_

```log
19:46:54 [0779]	Websoft.RegExp.MatchCollection
```

Коллекция `MatchCollection` имеет свои собственные методы, которые мы рассмотрим дальше

#### Count

Возвращает количество найденных совпадений

_Код_

```js
var oRegExp = tools.get_object_assembly('RegExp')

oRegExp.Pattern = '\\d{1,3}'
var text = '+7 111 222 33 44'
var oMatches = oRegExp.Execute(text)

alert('Количество совпадений: ' + oMatches.Count)
```

_Результат_

```log
19:57:44 [0876]	Количество совпадений: 5
```

#### Item

Метод `Item()` возвращает элемент коллекции `Match`

_Код_

```js
var oRegExp = tools.get_object_assembly('RegExp')

oRegExp.Pattern = '\\d{1,3}'
var text = '+7 111 222 33 44'
var oMatches = oRegExp.Execute(text)

for (i = 0; i < oMatches.Count; i++) {
	alert(oMatches.Item(i))
}
```

_Результат_

```log
20:01:54 [0121]	7
20:01:54 [0121]	111
20:01:54 [0121]	222
20:01:54 [0121]	33
20:01:54 [0121]	44
```

Элемент коллекция `Match` имеет свои собственные методы, которые мы рассмотрим дальше

##### Value

Возвращает значение элемента коллекции

_Код_

```js
var oRegExp = tools.get_object_assembly('RegExp')

oRegExp.Pattern = '\\d{1,3}'
var text = '+7 111 222 33 44'
var oMatches = oRegExp.Execute(text)

for (i = 0; i < oMatches.Count; i++) {
	oItem = oMatches.Item(i)
	alert('Item(' + i + ').Value: ' + oItem.Value)
}
```

_Результат_

```log
20:23:40 [0381]	Item(0).Value: 7
20:23:40 [0381]	Item(1).Value: 111
20:23:40 [0381]	Item(2).Value: 222
20:23:40 [0381]	Item(3).Value: 33
20:23:40 [0381]	Item(4).Value: 44
```

##### Length

Возвращает длину элемента коллекции

_Код_

```js
var oRegExp = tools.get_object_assembly('RegExp')

oRegExp.Pattern = '\\d{1,3}'
var text = '+7 111 222 33 44'
var oMatches = oRegExp.Execute(text)

for (i = 0; i < oMatches.Count; i++) {
	oItem = oMatches.Item(i)
	alert('Item(' + i + ').Length: ' + oItem.Length)
}
```

_Результат_

```log
20:26:30 [0444]	Item(0).Length: 1
20:26:30 [0444]	Item(1).Length: 3
20:26:30 [0444]	Item(2).Length: 3
20:26:30 [0444]	Item(3).Length: 2
20:26:30 [0444]	Item(4).Length: 2
```

##### FirstIndex

Возвращает номер позиции с которой начинается найденное совпадение в исходной строке. Нумерация с 0

_Код_

```js
var oRegExp = tools.get_object_assembly('RegExp')

oRegExp.Pattern = '\\d{1,3}'
var text = '+7 111 222 33 44'
var oMatches = oRegExp.Execute(text)

for (i = 0; i < oMatches.Count; i++) {
	oItem = oMatches.Item(i)
	alert('Item(' + i + ').FirstIndex: ' + oItem.FirstIndex)
}
```

_Результат_

```log
20:27:08 [0419]	Item(0).FirstIndex: 1
20:27:08 [0419]	Item(1).FirstIndex: 3
20:27:08 [0419]	Item(2).FirstIndex: 7
20:27:08 [0419]	Item(3).FirstIndex: 11
20:27:08 [0419]	Item(4).FirstIndex: 14
```

##### SubMatches

Метод `SubMatches` возвращает значения захваченных групп.

_Код_

```js
var oRegExp = tools.get_object_assembly('RegExp')

oRegExp.Pattern = '\\d{1,3}'
var text = '+7 111 222 33 44'
var oMatches = oRegExp.Execute(text)

for (i = 0; i < oMatches.Count; i++) {
	oItem = oMatches.Item(i)
	alert('Item(' + i + ').SubMatches(): ' + oItem.SubMatches())
}
```

_Результат_

```log
20:31:13 [0514]	Item(0).SubMatches(): Websoft.RegExp.SubMatches
20:31:13 [0514]	Item(1).SubMatches(): Websoft.RegExp.SubMatches
20:31:13 [0514]	Item(2).SubMatches(): Websoft.RegExp.SubMatches
20:31:13 [0514]	Item(3).SubMatches(): Websoft.RegExp.SubMatches
20:31:13 [0514]	Item(4).SubMatches(): Websoft.RegExp.SubMatches
```

Это способ получить значения групп захвата (то, что находится в круглых скобках в регулярном выражении) для одного совпадения.

Поменяем шаблон регулярки и добавим в него группы. Выведем найденные группы в цикле, а потом соберем из этих групп новую строку

_Код_

```js
var oRegExp = tools.get_object_assembly('RegExp')

oRegExp.Pattern = '(\\d)-(\\d{3})-(\\d{3})-(\\d{2})-(\\d{2})'
var text = '+7-111-222-33-44'
var oMatches = oRegExp.Execute(text)

for (i = 0; i < oMatches.Count; i++) {
	oItem = oMatches.Item(i)
	alert('Item(' + i + ').SubMatches(): ' + oItem.SubMatches().Count())

	for (j = 0; j < oItem.SubMatches().Count; j++) {
		oSubMatch = oItem.SubMatches().Item(j)
		alert('\tItem.SubMatches().Item(' + j + '): ' + oSubMatch)
	}

	alert(
		'Номер телефона: ' + '8' + 
		oItem.SubMatches(1) + 
		oItem.SubMatches(2) + 
		oItem.SubMatches(3) + 
		oItem.SubMatches(4)
	)
}
```

_Результат_

!!! info "При сборке номера телефона использовалась короткая запись для получения значения группы"
```log
20:47:36 [0575]	Item(0).SubMatches(): 5
20:47:36 [0575]		Item.SubMatches.Item(0): 7
20:47:36 [0575]		Item.SubMatches.Item(1): 111
20:47:36 [0575]		Item.SubMatches.Item(2): 222
20:47:36 [0575]		Item.SubMatches.Item(3): 33
20:47:36 [0575]		Item.SubMatches.Item(4): 44
20:47:36 [0575]	Номер телефона: 81112223344
```
