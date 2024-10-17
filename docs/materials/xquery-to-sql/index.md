# Как XQuery превращается в SQL

## Введение
Для выполнения запросов можно использовать коробочную функцию [`XQuery`](https://news.websoft.ru/_wt/wiki_base/6729342189327574487) или функцию [`tools.xquery`](https://news.websoft.ru/_wt/wiki_base/6774676213731516150)

В примерах будем использовать функцию `tools.xquery`

Всю основную информацию по запросам `XQuery` можно найти на портале WebSoft по следующим ссылкам:

* [Описание XQuery](https://news.websoft.ru/_wt/wiki_base/6729342189327574487)
* [Описание tools.xquery](https://news.websoft.ru/_wt/wiki_base/6774676213731516150)
* [Видео с базовым объяснением](https://news.websoft.ru/_wt/library_material/7229691353611750340)
* [Синтаксис XQuery запросов](https://news.websoft.ru/_wt/wiki_base/6680138390455715655)
* [Запросы XQuery](https://news.websoft.ru/_wt/wiki_base/6901595202309143964)

В примерах в блоке `XQuery` указан запрос XQuery, выполняющийся через `tools.xquery`

В примерах в блоке `SQL` указан запрос SQL, в который транспилируется запрос `XQuery` ядром системы WebSoft

!!! warning "Важно"
	`LuceneFTIndex` отключен, если индекс включен, то некоторые запросы SQL формируются иначе, в запрос подставляются сразу ИД объектов

## Запросы

### Запрос с выводом полей через запятую

**XQuery**

=== "649 + MSSQL"
	```xquery
	for 
		$elem in collaborators 
	return 
		$elem/id, $elem/fullname, $elem/position_name 
	```
=== "906 + PostgreSQL"
	```xquery
	for 
		$elem in collaborators 
	where 
		id = 1105387902724063494 
	return 
		$elem/id, $elem/fullname, $elem/position_name 
	```

**SQL**

=== "649 + MSSQL"
	```sql
	select	t_elem.[id], 
			t_elem.[fullname], 
			t_elem.[position_name] 
	from	dbo.[collaborators] t_elem 
	```
=== "906 + PostgreSQL" 
	```sql
	select	t_elem."id", 
			t_elem."fullname", 
			t_elem."position_name" 
	from 	dbo."collaborators" t_elem 
	where 	"id"=1105387902724063494 
	order by t_elem.id 
	```

### Запрос с выводом полей через Fields(field1, field2)

**XQuery**

=== "649 + MSSQL"
	```XQuery
	for 
		$elem in collaborators 
	return 
		$elem/Fields(id, fullname) 
	```
=== "906 + PostgreSQL"
	```XQuery
	for 
		$elem in collaborators 
	where 
		id = 1105387902724063494 
	return 
		$elem/Fields(id, fullname) 
	```

**SQL**

=== "649 + MSSQL"
	```sql
	select	t_elem.[id], 
			t_elem.[fullname] 
	from	dbo.[collaborators] t_elem 
	```
=== "906 + PostgreSQL"
	```sql
	select	t_elem."id", 
			t_elem."fullname" 
	from	dbo."collaborators" t_elem 
	where	"id"=1105387902724063494 
	order by t_elem.id 
	```

### Запрос с выводом полей через Fields('field1', 'field2')

**XQuery**

=== "649 + MSSQL"
	```XQuery
	for 
		$elem in collaborators 
	return 
		$elem/Fields('id', 'fullname', 'org_name') 
	```
=== "906 + PostgreSQL"
	```XQuery
	for 
		$elem in collaborators 
	where 
		id = 1105387902724063494 
	return 
		$elem/Fields('id', 'fullname', 'org_name') 
	```

**SQL**

=== "649 + MSSQL"
	```sql
	select	t_elem.[id], 
			t_elem.[fullname], 
			t_elem.[org_name] 
	from	dbo.[collaborators] t_elem 
	```
=== "906 + PostgreSQL"
	```sql
	select	t_elem."id", 
			t_elem."fullname", 
			t_elem."org_name" 
	from	dbo."collaborators" t_elem 
	where	"id"=1105387902724063494 
	order by t_elem.id 
	```

### Запрос к нескольким таблицам с выводом полей из одной таблицы

**XQuery**

=== "649 + MSSQL"
	```XQuery
	for 
		$elem in collaborators, 
		$pos in positions 
	where 
		$elem/position_id = $pos/id 
	return 
		$elem/Fields('id', 'position_name'), $elem/fullname 
	```
=== "906 + PostgreSQL"
	```XQuery
	for 
		$elem in collaborators, 
		$pos in positions 
	where 
		$elem/position_id = $pos/id and 
		$elem/id = 1105387902724063494 
	return 
		$elem/Fields('id', 'position_name'), $elem/fullname 
	```

**SQL**

=== "649 + MSSQL"
	```sql
	select	t_elem.[id], 
			t_elem.[position_name], 
			t_elem.[fullname] 
	from	dbo.[positions] t_pos, dbo.[collaborators] t_elem 
	where	t_elem.[position_id] = t_pos.[id] 
	```
=== "906 + PostgreSQL"
	```sql
	select	t_elem."id", 
			t_elem."position_name", 
			t_elem."fullname" 
	from	dbo."positions" t_pos, 
			dbo."collaborators" t_elem 
	where	t_elem."position_id"= t_pos."id" 
		and	t_elem."id"=1105387902724063494 
	order by t_elem.id 
	```

### Запрос к нескольким таблицам с выводом полей из нескольких таблиц

**XQuery**

!!! warning "Важно"
	При попытке вывести 2 поля с одинаковым именем из разных таблиц - в результат попадет только одно. Например, `return $elem/id, $pos/id` выведет в результат только 1 поле `id`

=== "649 + MSSQL"
	```XQuery
	for 
		$elem in collaborators, 
		$pos in positions 
	where 
		$elem/position_id = $pos/id 
	return 
		$elem/id, $elem/fullname, $pos/name 
	```
=== "906 + PostgreSQL"
	```XQuery
	for 
		$elem in collaborators, 
		$pos in positions 
	where 
		$elem/position_id = $pos/id and 
		$elem/id = 1105387902724063494 
	return 
		$elem/id, $elem/fullname, $pos/name 
	```

**SQL**

=== "649 + MSSQL"
	```sql
	select	t_elem.[id], 
			t_elem.[fullname], 
			t_pos.[name] 
	from	dbo.[positions] t_pos, dbo.[collaborators] t_elem 
	where	t_elem.[position_id] = t_pos.[id] 
	```
=== "906 + PostgreSQL"
	```sql
	select	t_elem."id", 
			t_elem."fullname", 
			t_pos."name" 
	from	dbo."positions" t_pos, 
			dbo."collaborators" t_elem 
	where	t_elem."position_id"= t_pos."id" 
		and	t_elem."id"=1105387902724063494 
	```

### Запрос к нескольким таблицам с выводом полей из нескольких таблиц через Fields()

**XQuery**

!!! warning "Важно"
	В запросе `XQuery` между `Fields` нет запятой. При попытке поставить запятую, запрос выкидывает ошибку

=== "649 + MSSQL"
	```XQuery
	for 
		$elem in collaborators, 
		$pos in positions 
	where 
		$elem/position_id = $pos/id 
	return 
		$elem/Fields(id, fullname) $pos/Fields(name) 
	```
=== "906 + PostgreSQL"
	```XQuery
	for 
		$elem in collaborators, 
		$pos in positions 
	where 
		$elem/position_id = $pos/id and 
		$elem/id = 1105387902724063494 
	return 
		$elem/Fields(id, fullname, org_name) $pos/Fields(name) 
	```

**SQL**

=== "649 + MSSQL"
	```sql
	select	t_elem.[id], 
			t_elem.[fullname], 
			t_pos.[name] 
	from	dbo.[positions] t_pos, 
			dbo.[collaborators] t_elem 
	where	t_elem.[position_id] = t_pos.[id] 
	```
=== "906 + PostgreSQL"
	```sql
	select	t_elem."id", 
			t_elem."fullname", 
			t_elem."org_name", 
			t_pos."name" 
	from	dbo."positions" t_pos, 
			dbo."collaborators" t_elem 
	where	t_elem."position_id"= t_pos."id" 
		and	t_elem."id"=1105387902724063494 
	```

### Запрос к нескольким таблицам с выводом полей из нескольких таблиц через Fields('')

**XQuery**

!!! warning "Важно"
	В запросе `XQuery` между `Fields` нет запятой. При попытке поставить запятую, запрос выкидывает ошибку

=== "649 + MSSQL"
	```XQuery
	for 
		$elem in collaborators, 
		$pos in positions 
	where 
		$elem/position_id = $pos/id 
	return 
		$elem/Fields('id', 'fullname') $pos/Fields('name') 
	```
=== "906 + PostgreSQL"
	```XQuery
	for 
		$elem in collaborators, 
		$pos in positions 
	where 
		$elem/position_id = $pos/id and 
		$elem/id = 1105387902724063494 
	return 
		$elem/Fields('id', 'fullname') $pos/Fields('name', 'code') 
	```

**SQL**

=== "649 + MSSQL"
	```sql
	select	t_elem.[id], 
			t_elem.[fullname], 
			t_pos.[name] 
	from	dbo.[positions] t_pos, dbo.[collaborators] t_elem 
	where	t_elem.[position_id] = t_pos.[id] 
	```
=== "906 + PostgreSQL"
	```sql
	select	t_elem."id", 
			t_elem."fullname", 
			t_pos."name", 
			t_pos."code" 
	from	dbo."positions" t_pos, 
			dbo."collaborators" t_elem 
	where	t_elem."position_id"= t_pos."id" 
		and	t_elem."id"=1105387902724063494 
	```

### Запрос к нескольким таблицам с переименованием полей

**XQuery**

!!! warning "Важно"
	При попытке вывести 2 поля с одинаковым именем из разных таблиц - в результат попадет только одно. Например, `return $elem/id, $pos/id` выведет в результат только 1 поле `id`

=== "649 + MSSQL"
	```XQuery
	for 
		$elem in collaborators, 
		$pos in positions 
	where 
		$elem/position_id = $pos/id 
	return 
		$elem/id uid, $elem/fullname full_name, $pos/name position_name 
	```
=== "906 + PostgreSQL"
	```XQuery
	for 
		$elem in collaborators, 
		$pos in positions 
	where 
		$elem/position_id = $pos/id and 
		$elem/id = 1105387902724063494 
	return 
		$elem/id uid, $elem/fullname full_name, $pos/name position_name 
	```

**SQL**

=== "649 + MSSQL"
	```sql
	select	t_elem.[id] [uid], 
			t_elem.[fullname] [full_name], 
			t_pos.[name] [position_name] 
	from	dbo.[positions] t_pos, dbo.[collaborators] t_elem 
	where	t_elem.[position_id] = t_pos.[id] 
	```
=== "906 + PostgreSQL"
	```sql
	select	t_elem."id" "uid", 
			t_elem."fullname" "full_name", 
			t_pos."name" "position_name" 
	from	dbo."positions" t_pos, 
			dbo."collaborators" t_elem 
	where	t_elem."position_id"= t_pos."id" 
		and	t_elem."id"=1105387902724063494 
	```

### Запрос к нескольким таблицам с переименованием полей через Fields()

**XQuery**

!!! warning "Важно"
	При попытке вывести 2 поля с одинаковым именем из разных таблиц - в результат попадет только одно. Например, `return $elem/id, $pos/id` выведет в результат только 1 поле `id`

!!! warning "Важно"
	В запросе `XQuery` между `Fields` нет запятой. При попытке поставить запятую, запрос выкидывает ошибку

=== "649 + MSSQL"
	```XQuery
	for 
		$elem in collaborators, 
		$pos in positions 
	where 
		$elem/position_id = $pos/id 
	return 
		$elem/Fields(id uid, fullname full_name) $pos/Fields(name position_name) 
	```
=== "906 + PostgreSQL"
	```XQuery
	for 
		$elem in collaborators, 
		$pos in positions 
	where 
		$elem/position_id = $pos/id and 
		$elem/id = 1105387902724063494 
	return 
		$elem/Fields(id uid, fullname full_name) $pos/Fields(name position_name) 
	```

**SQL**

=== "649 + MSSQL"
	```sql
	select	t_elem.[id] [uid], 
			t_elem.[fullname] [full_name], 
			t_pos.[name] [position_name] 
	from	dbo.[positions] t_pos, dbo.[collaborators] t_elem 
	where	t_elem.[position_id] = t_pos.[id] 
	```
=== "906 + PostgreSQL"
	```sql
	select	t_elem."id" "uid", 
			t_elem."fullname" "full_name", 
			t_pos."name" "position_name" 
	from	dbo."positions" t_pos, 
			dbo."collaborators" t_elem 
	where	t_elem."position_id"= t_pos."id" 
		and	t_elem."id"=1105387902724063494 
	```

### Запрос к нескольким таблицам через some/satisfies

**XQuery**

!!! warning "Важно"
	При использовании `some/satisfies` в результат можно вывести поля только из основного каталога `$elem`

!!! warning "Важно"
	Условия в скобках после `satisfies` `($elem/position_id = $pos/id)` будут перенесены в `ON` к `INNER JOIN`, все условия после скобок будут перенесены в `WHERE`

=== "649 + MSSQL"
	```XQuery
	for 
		$elem in collaborators 
	where 
		some $pos in positions satisfies ($elem/position_id = $pos/id) and 
		$pos/name = 'Тренер' 
	return 
		$elem/id 
	```
=== "906 + PostgreSQL"
	!!! warning "Важно"
		Запрос выполняется с учётом регистра

	```XQuery
	for 
		$elem in collaborators 
	where 
		some $pos in positions satisfies ($elem/position_id = $pos/id) and 
		$pos/name = 'Тренер' 
	return 
		$elem/id 
	```

**SQL**

=== "649 + MSSQL"
	```sql
	select	t_elem.[id] 
	from	dbo.[collaborators] t_elem 
	where	t_elem.[id] in ( 
			select	t_elem.[id] 
			from	dbo.[collaborators] t_elem 
				inner join dbo.[positions] t_pos ON t_elem.[position_id] = t_pos.[id] 
			where t_pos.[name] ='Тренер' 
		) 
	```
=== "906 + PostgreSQL"
	```sql
	select	t_elem."id" 
	from	dbo."collaborators" t_elem 
	where	t_elem."id" in ( 
				select	t_elem."id" 
				from	dbo."collaborators" t_elem 
						inner join dbo."positions" t_pos ON t_elem."position_id"= t_pos."id" 
				where t_pos."name"='Тренер' 
			) 
	order by t_elem.id 
	```

### Запрос к трём и более таблицам через some/satisfies

**XQuery**

!!! warning "Важно"
	При использовании `some/satisfies` в результат можно вывести поля только из основного каталога `$elem`

!!! warning "Важно"
	Условия в скобках после `satisfies` `($elem/position_id = $pos/id)` будут перенесены в `ON` к `INNER JOIN`, все условия после скобок будут перенесены в `WHERE`

!!! warning "Важно"
	Обратите внимание на порядок `some/satisfies` в `XQuery` запросе и в каком порядке они оказались собраны в `SQL` запросе. Если поменяем строки местами, то получим ошибку в SQL запросе

=== "649 + MSSQL"
	```XQuery
	for 
		$elem in collaborators 
	where 
		some $app in appointment_types satisfies ($pos/position_appointment_type_id = $app/id) and 
		some $pos in positions satisfies ($elem/position_id = $pos/id) and 
		$pos/name = 'Тренер' 
	return 
		$elem/Fields('id', 'position_name') 
	```
=== "906 + PostgreSQL"
	!!! warning "Важно"
		Запрос выполняется с учётом регистра

	```XQuery
	for 
		$elem in collaborators 
	where 
		some $app in appointment_types satisfies ($pos/position_appointment_type_id = $app/id) and 
		some $pos in positions satisfies ($elem/position_id = $pos/id) and 
		$pos/name = 'Тренер' 
	return 
		$elem/Fields('id', 'position_name') 
	```

**SQL**

=== "649 + MSSQL"
	```sql
	select	t_elem.[id], 
			t_elem.[position_name] 
	from	dbo.[collaborators] t_elem 
	where	t_elem.[id] in ( 
				select	t_elem.[id] 
				from	dbo.[collaborators] t_elem 
					inner join dbo.[positions] t_pos ON t_elem.[position_id] = t_pos.[id] 
					inner join dbo.[appointment_types] t_app ON t_pos.[position_appointment_type_id] = t_app.[id] 
				where t_pos.[name] = 'Тренер' 
			) 
	```
=== "906 + PostgreSQL"
	```sql
	select	t_elem."id", 
			t_elem."position_name" 
	from	dbo."collaborators" t_elem 
	where	t_elem."id" in ( 
				select	t_elem."id" 
				from	dbo."collaborators" t_elem 
						inner join dbo."positions" t_pos ON t_elem."position_id"= t_pos."id" 
						inner join dbo."appointment_types" t_app ON t_pos."position_appointment_type_id"= t_app."id" 
				where	t_pos."name"='Тренер' 
			) 
	order by t_elem.id 
	```

### Запрос с поиском через contains

**XQuery**

=== "649 + MSSQL"
	```XQuery
	for 
		$elem in collaborators 
	where 
		contains($elem/fullname, 'оскин') 
	return 
		$elem/Fields('id', 'fullname') 
	```
=== "906 + PostgreSQL"
	```XQuery
	for 
		$elem in collaborators 
	where 
		contains($elem/fullname, 'тихомиров') 
	return 
		$elem/Fields('id', 'fullname') 
	```

**SQL**

=== "649 + MSSQL"
	```sql
	select	t_elem.[id], 
		t_elem.[fullname] 
	from	dbo.[collaborators] t_elem 
	where	(t_elem.[fullname] like '%оскин%') 
	```
=== "906 + PostgreSQL"
	```sql
	select	t_elem."id", 
			t_elem."fullname" 
	from	dbo."collaborators" t_elem 
	where	( 
				t_elem."fullname" ilike '%тихомиров%' 
			) 
	order by t_elem.id 
	```

### Запрос с поиском через doc-contains

!!! warning "Важно"
	Поиск через `doc-contains` производится с начала слова, то есть, если в карточке сотрудника в поле `position_name` будет указана должность `Тренер`, поиск по фразе `трен` найдет всех тренеров, поиск по фразе `ренер` не найдет тренеров

**XQuery**

=== "649 + MSSQL"
	```XQuery
	for 
		$elem in collaborators 
	where 
		doc-contains($elem/id, '', 'Загос') 
	return 
		$elem/Fields('id', 'fullname') 
	```
=== "906 + PostgreSQL"
	```XQuery
	for 
		$elem in collaborators 
	where 
		doc-contains($elem/id, '', 'тихоми') 
	return 
		$elem/Fields('id', 'fullname') 
	```

**SQL**

=== "649 + MSSQL"
	```sql
	select	t_elem.[id], 
		t_elem.[fullname] 
	from	dbo.[collaborators] t_elem 
	where	t_elem.[id] in ( 
			select	[id] 
			from	dbo.[collaborator] 
			where	contains(*,'"Загос*"') 
		) 
	```
=== "906 + PostgreSQL"
	```sql
	select	t_elem."id", 
			t_elem."fullname" 
	from	dbo."collaborators" t_elem 
	where	t_elem."id" in ( 
				select	"id" 
				from	dbo."collaborator" 
				where	xmlexists('/collaborator/*[contains(text(),"Сид")]' passing by ref "data") = true 
			) 
	order by t_elem.id 
	```

### Запрос с поиском по кастомному полю через doc-contains

**XQuery**

!!! warning "Важно"
	Название поля и значение автоматически оборачиваются в кавычки, что можно наблюдать в полученном запросе `SQL`

!!! warning "Важно"
	Поиск через `doc-contains` производится с начала слова, то есть, если в карточке сотрудника в поле `position_name` будет указана должность `Тренер`, поиск по фразе `трен` найдет всех тренеров, поиск по фразе `ренер` не найдет тренеров

=== "649 + MSSQL"
	```XQuery
	for 
		$elem in collaborators 
	where 
		doc-contains($elem/id, '', '[test = олололо]') 
	return 
		$elem/Fields('id', 'fullname') 
	```
=== "906 + PostgreSQL"
	!!! warning "Важно"
		Запрос выполняется с учётом регистра
	
	```XQuery
	for 
		$elem in collaborators 
	where 
		doc-contains($elem/id, '', '[fld_city = Сидней]') 
	return 
		$elem/Fields('id', 'fullname') 
	```

**SQL**

=== "649 + MSSQL"
	```sql
	select	t_elem.[id], 
			t_elem.[fullname] 
	from	dbo.[collaborators] t_elem 
	where	t_elem.[id] in ( 
				select	[id] 
				from	dbo.[collaborator] 
				where	data.exist('collaborator/custom_elems/custom_elem[name = "test" and value[1] = "олололо"]') = 1 
			) 
	```
=== "906 + PostgreSQL"
	```sql
	select	t_elem."id", 
			t_elem."fullname" 
	from	dbo."collaborators" t_elem 
	where	t_elem."id" in ( 
				select	"id" 
				from	dbo."collaborator" 
				where	XMLExists('/collaborator/custom_elems/custom_elem[name = "fld_city" and value = "Сидней"]' PASSING by ref "data") 
			) 
	order by t_elem.id 
	```

### Запрос с поиском по кастомному полю через doc-contains и contains по значению

**XQuery**

!!! warning "Важно"
	Название поля и значение автоматически оборачиваются в кавычки, что можно наблюдать в полученном запросе `SQL`

!!! warning "Важно"
	Поиск через `doc-contains` производится с начала слова, то есть, если в карточке сотрудника в поле `position_name` будет указана должность `Тренер`, поиск по фразе `трен` найдет всех тренеров, поиск по фразе `ренер` не найдет тренеров
	
=== "649 + MSSQL"
	```XQuery
	for 
		$elem in collaborators 
	where 
		doc-contains($elem/id, '', '[test contains ло]') 
	return 
		$elem/Fields('id', 'fullname') 
	```
=== "906 + PostgreSQL"
	!!! warning "Важно"
		Запрос выполняется с учётом регистра

	```XQuery
	for 
		$elem in collaborators 
	where 
		doc-contains($elem/id, '', '[fld_city contains Сид]') 
	return 
		$elem/Fields('id', 'fullname') 
	```

**SQL**

=== "649 + MSSQL"
	```sql
	select	t_elem.[id], 
			t_elem.[fullname] 
	from	dbo.[collaborators] t_elem 
	where	t_elem.[id] in ( 
			select	[id] 
			from	dbo.[collaborator] 
			where	data.exist('collaborator/custom_elems/custom_elem[name = "test" and contains(value[1], "ло")]') = 1 
		) 
	```
=== "906 + PostgreSQL"
	```sql
	select	t_elem."id", 
			t_elem."fullname" 
	from	dbo."collaborators" t_elem 
	where	t_elem."id" in ( 
				select	"id" 
				from	dbo."collaborator" 
				where	XMLExists('/collaborator/custom_elems/custom_elem[name = "fld_city" and contains(value,  "Сид")]' PASSING by ref "data") 
			) 
	order by t_elem.id 
	```

### Запрос с поиском по кастомному полю через doc-contains с приведением типов

**XQuery**

=== "649 + MSSQL"
	```XQuery
	for 
		$elem in collaborators 
	where 
		doc-contains($elem/id, '', '[b_test = true~bool]') 
	return 
		$elem/Fields('id', 'fullname', 'position_name') 
	```
=== "906 + PostgreSQL"
	```XQuery
	for 
		$elem in collaborators 
	where 
		doc-contains($elem/id, '', '[fld_married = true~bool]') 
	return 
		$elem/Fields('id', 'fullname', 'position_name') 
	```

**SQL**

=== "649 + MSSQL"
	```sql
	select	t_elem.[id], 
			t_elem.[fullname], 
			t_elem.[position_name] 
	from	dbo.[collaborators] t_elem 
	where	t_elem.[id] in ( 
			select	[id] 
			from	dbo.[collaborator] 
			where	data.exist('collaborator/custom_elems/custom_elem[name = "b_test" and xs:boolean(value[1]) = true()]') = 1 
		) 
	```
=== "906 + PostgreSQL"
	```sql
	select	t_elem."id", 
			t_elem."fullname", 
			t_elem."position_name" 
	from	dbo."collaborators" t_elem 
	where	t_elem."id" in ( 
				select	"id" 
				from	dbo."collaborator" 
				where	XMLExists('/collaborator/custom_elems/custom_elem[name = "fld_married" and value = "true"]' PASSING by ref "data") 
			) 
	order by t_elem.id 
	```

### Запрос с использованием ForeignElem

**XQuery**

=== "649 + MSSQL"
	```XQuery
	for 
		$elem in collaborators 
	where 
		ForeignElem($elem/position_id)/name = 'Тренер' 
	return 
		$elem/Fields('id', 'fullname', 'position_name') 
	```
=== "906 + PostgreSQL"
	!!! warning "Важно"
		Запрос выполняется с учётом регистра

	```XQuery
	for 
		$elem in collaborators 
	where 
		ForeignElem($elem/position_id)/name = 'Тренер' 
	return 
		$elem/Fields('id', 'fullname', 'position_name') 
	```

**SQL**

=== "649 + MSSQL"
	```sql
	select	t_elem.[id], 
		t_elem.[fullname], 
		t_elem.[position_name] 
	from	dbo.[collaborators] t_elem 
		inner join dbo.[positions] [f-1783245827] on t_elem.[position_id] = [f-1783245827].[id] 
	where	[f-1783245827].[name] = 'Тренер' 
	```
=== "906 + PostgreSQL"
	```sql
	select	t_elem."id", 
			t_elem."fullname", 
			t_elem."position_name" 
	from	dbo."collaborators" t_elem 
			left join dbo."positions" "f1486165897" on t_elem."position_id" = "f1486165897"."id" 
	where	"f1486165897"."name"='Тренер' 
	order by t_elem.id 
	```

### Запрос с использованием true(), false(), null(), ''

**XQuery**

=== "649 + MSSQL"
	```XQuery
	for 
		$elem in collaborators 
	where 
		$elem/is_dismiss = false() or 
		$elem/is_dismiss != false() or 
		$elem/is_dismiss = true() or 
		$elem/is_dismiss != true() or 
		$elem/is_dismiss = null() or 
		$elem/is_dismiss != null() or 
		$elem/is_dismiss = '' or 
		$elem/is_dismiss != '' 
	return 
		$elem/Fields('id', 'fullname') 
	```
=== "906 + PostgreSQL"
	```XQuery
	for 
		$elem in collaborators 
	where 
		$elem/is_dismiss = false() or 
		$elem/is_dismiss != false() or 
		$elem/is_dismiss = true() or 
		$elem/is_dismiss != true() or 
		$elem/is_dismiss = null() or 
		$elem/is_dismiss != null() or 
		$elem/is_dismiss = '' or 
		$elem/is_dismiss != '' 
	return 
		$elem/Fields('id', 'fullname') 
	```

**SQL**

=== "649 + MSSQL"
	```sql
	select	t_elem.[id], t_elem.[fullname] 
	from	dbo.[collaborators] t_elem 
	where	(t_elem.[is_dismiss] = 0 OR t_elem.[is_dismiss] IS NULL) or -- $elem/is_dismiss = false() 
		t_elem.[is_dismiss] <> 0 or -- $elem/is_dismiss != false() 
		t_elem.[is_dismiss] = 1 or -- $elem/is_dismiss = true() 
		(t_elem.[is_dismiss] <> 1 OR t_elem.[is_dismiss] IS NULL) or -- $elem/is_dismiss != true() 
		t_elem.[is_dismiss] IS NULL or -- $elem/is_dismiss = null() 
		t_elem.[is_dismiss] IS NOT NULL or -- $elem/is_dismiss != null() 
		t_elem.[is_dismiss] IS NULL or -- $elem/is_dismiss = '' 
		t_elem.[is_dismiss] IS NOT NULL -- $elem/is_dismiss != '' 
	```
=== "906 + PostgreSQL"
	```sql
	select	t_elem."id", 
			t_elem."fullname" 
	from	dbo."collaborators" t_elem 
	where	((t_elem."is_dismiss"=false) OR ((t_elem."is_dismiss") IS NULL)) 
		or	t_elem."is_dismiss"<>false 
		or	t_elem."is_dismiss"=true 
		or	((t_elem."is_dismiss"<>true) OR ((t_elem."is_dismiss") IS NULL)) 
		or	t_elem."is_dismiss" IS NULL 
		or	t_elem."is_dismiss" IS NOT NULL 
		or	t_elem."is_dismiss" IS NULL 
		or	t_elem."is_dismiss" IS NOT NULL 
	order by t_elem.id 
	```

### Запрос с использованием date()

**XQuery**

=== "649 + MSSQL"
	```XQuery
	for 
		$elem in collaborators 
	where 
		$elem/hire_date = date('2010-03-17') 
	return 
		$elem/Fields('id', 'fullname', 'hire_date') 
	```
=== "906 + PostgreSQL"
	```XQuery
	for 
		$elem in collaborators 
	where 
		$elem/hire_date = date('2010-03-17') 
	return 
		$elem/Fields('id', 'fullname', 'hire_date') 
	```

**SQL**

=== "649 + MSSQL"
	```sql
	SET DATEFORMAT dmy; 
	select	t_elem.[id], t_elem.[fullname], t_elem.[hire_date] 
	from	dbo.[collaborators] t_elem 
	where	t_elem.[hire_date] = '17.03.2010 0:00:00' 
	```
=== "906 + PostgreSQL"
	```sql
	select	t_elem."id", 
			t_elem."fullname", 
			t_elem."hire_date" 
	from	dbo."collaborators" t_elem 
	where	t_elem."hire_date"='2010-03-17 00:00:00' 
	order by t_elem.id 
	```

### Запрос иерархии с использованием IsHierChild()

!!! warning "Важно"
	Перед функцией `IsHierChild` обязательно должен стоять пробел, знак табуляции приводит к ошибке 

!!! warning "Важно"
	Не работает через `XQuery`

**XQuery**

=== "649 + MSSQL"
	```XQuery
	for 
		$elem in subdivisions 
	where 
		IsHierChild($elem/id, 6327975429225669221) 
	order by 
		$elem/Hier() 
	return 
		$elem/id 
	```
=== "906 + PostgreSQL"
	```XQuery
	for 
		$elem in subdivisions 
	where 
		IsHierChild($elem/id, 6327975429225669221) 
	order by 
		$elem/Hier() 
	return 
		$elem/id , $elem/name
	```

**SQL**

=== "649 + MSSQL"
	```sql
	WITH [subdivisions_cte] ( 
		[id], 
		[code], 
		[name], 
		[org_id], 
		[parent_object_id], 
		[is_disbanded], 
		[knowledge_parts], 
		[tags], 
		[experts], 
		[place_id], 
		[region_id], 
		[kpi_profile_id], 
		[bonus_profile_id], 
		[cost_center_id], 
		[is_faculty], 
		[modification_date], 
		[app_instance_id], 
		[kpi_profiles_id], 
		[__hlevel], 
		[__sort_level], 
		[__hcc] 
	) AS ( 
		SELECT	[id], 
				[code], 
				[name], 
				[org_id], 
				[parent_object_id], 
				[is_disbanded], 
				[knowledge_parts], 
				[tags], 
				[experts], 
				[place_id], 
				[region_id], 
				[kpi_profile_id], 
				[bonus_profile_id], 
				[cost_center_id], 
				[is_faculty], 
				[modification_date], 
				[app_instance_id], 
				[kpi_profiles_id], 
				0 as [__hlevel], 
				cast((CAST(FLOOR(LOG10(ROW_NUMBER() over(order by e.id))) as varchar) + cast(ROW_NUMBER() over(order by e.id) as varchar(256))) as varchar(256)) as [__sort_level], 
			(select top 1 1 from dbo.[subdivisions] f where f.parent_object_id = e.id) as [__hcc] 
		FROM	dbo.[subdivisions] e 
		WHERE	e.parent_object_id = 6327975429225669221 
		UNION ALL 
		SELECT	e.[id], 
				e.[code], 
				e.[name], 
				e.[org_id], 
				e.[parent_object_id], 
				e.[is_disbanded], 
				e.[knowledge_parts], 
				e.[tags], 
				e.[experts], 
				e.[place_id], 
				e.[region_id], 
				e.[kpi_profile_id], 
				e.[bonus_profile_id], 
				e.[cost_center_id], 
				e.[is_faculty], 
				e.[modification_date], 
				e.[app_instance_id], 
				e.[kpi_profiles_id], 
				[__hlevel] + 1, 
				cast((d.[__sort_level] + '.' + CAST(FLOOR(LOG10(ROW_NUMBER() over(order by e.id))) as varchar) + cast(ROW_NUMBER() over(order by e.id) as varchar(256))) as varchar(256)) as [__sort_level], 
				(select 1 WHERE EXISTS (SELECT id FROM dbo.[subdivisions] f WHERE e.id = f.parent_object_id)) as [__hcc] 
		FROM	dbo.[subdivisions] e 
			INNER JOIN [subdivisions_cte] d ON e.parent_object_id = d.id 
	) 

	select	t_elem.[id],
			[__hcc],
			[__hlevel] 
	from	[subdivisions_cte] t_elem 
	order by t_elem.[__sort_level] asc
	```
=== "906 + PostgreSQL"
	```sql
	WITH RECURSIVE "subdivisions_cte" ( 
		"id", 
		"code", 
		"name", 
		"org_id", 
		"parent_object_id", 
		"is_disbanded", 
		"knowledge_parts", 
		"tags", 
		"experts", 
		"place_id", 
		"region_id", 
		"kpi_profile_id", 
		"kpi_profiles_id", 
		"bonus_profile_id", 
		"cost_center_id", 
		"is_faculty", 
		"modification_date", 
		"app_instance_id", 
		"__hlevel", 
		"__sort_level", 
		"__hcc" 
	) AS (
		SELECT	"id", 
				"code", 
				"name", 
				"org_id", 
				"parent_object_id", 
				"is_disbanded", 
				"knowledge_parts", 
				"tags","experts", 
				"place_id", 
				"region_id", 
				"kpi_profile_id", 
				"kpi_profiles_id", 
				"bonus_profile_id", 
				"cost_center_id", 
				"is_faculty", 
				"modification_date", 
				"app_instance_id", 
				0 as __hlevel, 
				cast((CAST(FLOOR(LOG(ROW_NUMBER() over(order by e."id"))) as varchar)||cast(ROW_NUMBER() over(order by e."id") as varchar(256))) as varchar(256)) as "__sort_level", 
				(select 1 from dbo."subdivisions" f where f.parent_object_id = e.id limit 1) as "__hcc" 
		FROM	dbo."subdivisions" e 
		WHERE	e.parent_object_id = '6327975429225669221' 
		UNION ALL 
		SELECT	e."id", 
				e."code", 
				e."name", 
				e."org_id", 
				e."parent_object_id", 
				e."is_disbanded", 
				e."knowledge_parts", 
				e."tags", 
				e."experts", 
				e."place_id", 
				e."region_id", 
				e."kpi_profile_id", 
				e."kpi_profiles_id", 
				e."bonus_profile_id", 
				e."cost_center_id", 
				e."is_faculty", 
				e."modification_date", 
				e."app_instance_id","__hlevel"+1, 
				cast((d."__sort_level"||'.'||CAST(FLOOR(LOG(ROW_NUMBER() over(order by e."id"))) as varchar)||cast(ROW_NUMBER() over(order by e."id") as varchar(256))) as varchar(256)) as "__sort_level", 
				(select 1 WHERE EXISTS (SELECT id FROM dbo."subdivisions" f WHERE e.id = f.parent_object_id)) as "__hcc" 
		FROM	dbo."subdivisions" e 
				INNER JOIN "subdivisions_cte" d ON e.parent_object_id = d.id 
	) 

	select	t_elem."id", 
			t_elem."name", 
			"__hcc", 
			"__hlevel" 
	from	"subdivisions_cte" t_elem 
	order by t_elem."__sort_level" asc nulls first 
	```

### Запрос иерархии с использованием IsHierChildOrSelf()

!!! warning "Важно"
	Перед функцией `IsHierChild` обязательно должен стоять пробел, знак табуляции приводит к ошибке 

!!! warning "Важно"
	Не работает через `XQuery`

**XQuery**

=== "649 + MSSQL"
	```XQuery
	for 
		$elem in subdivisions 
	where 
		IsHierChildOrSelf($elem/id, 6327975429225669221) and 
		$elem/is_disbanded = false() 
	order by 
		$elem/Hier() 
	return 
		$elem/id, $elem/name 
	```
=== "906 + PostgreSQL"
	```XQuery
	for 
		$elem in subdivisions 
	where 
		IsHierChildOrSelf($elem/id, 6327975429225669221) and 
		$elem/is_disbanded = false() 
	order by 
		$elem/Hier() 
	return 
		$elem/Fields(id, name) 
	```

**SQL**

=== "649 + MSSQL"
	```sql
	WITH [subdivisions_cte] ( 
		[id], 
		[code], 
		[name], 
		[org_id], 
		[parent_object_id], 
		[is_disbanded], 
		[knowledge_parts], 
		[tags], 
		[experts], 
		[place_id], 
		[region_id], 
		[kpi_profile_id], 
		[bonus_profile_id], 
		[cost_center_id], 
		[is_faculty], 
		[modification_date], 
		[app_instance_id], 
		[kpi_profiles_id], 
		[__hlevel], 
		[__sort_level], 
		[__hcc] 
	) AS ( 
		SELECT	[id], 
				[code], 
				[name], 
				[org_id], 
				[parent_object_id], 
				[is_disbanded], 
				[knowledge_parts], 
				[tags], 
				[experts], 
				[place_id], 
				[region_id], 
				[kpi_profile_id], 
				[bonus_profile_id], 
				[cost_center_id], 
				[is_faculty], 
				[modification_date], 
				[app_instance_id], 
				[kpi_profiles_id], 
				0 as [__hlevel], 
				cast((CAST(FLOOR(LOG10(ROW_NUMBER() over(order by e.id))) as varchar) + cast(ROW_NUMBER() over(order by e.id) as varchar(256))) as varchar(256)) as [__sort_level], 
				(select top 1 1 from dbo.[subdivisions] f where f.parent_object_id = e.id) as [__hcc] 
		FROM	dbo.[subdivisions] e 
		WHERE	e.id = 6327975429225669221 
		UNION ALL 
		SELECT	e.[id], 
				e.[code], 
				e.[name], 
				e.[org_id], 
				e.[parent_object_id], 
				e.[is_disbanded], 
				e.[knowledge_parts], 
				e.[tags], 
				e.[experts], 
				e.[place_id], 
				e.[region_id], 
				e.[kpi_profile_id], 
				e.[bonus_profile_id], 
				e.[cost_center_id], 
				e.[is_faculty], 
				e.[modification_date], 
				e.[app_instance_id], 
				e.[kpi_profiles_id], 
				[__hlevel] + 1, 
				cast((d.[__sort_level] + '.' + CAST(FLOOR(LOG10(ROW_NUMBER() over(order by e.id))) as varchar) + cast(ROW_NUMBER() over(order by e.id) as varchar(256))) as varchar(256)) as [__sort_level], 
				(select 1 WHERE EXISTS (SELECT id FROM dbo.[subdivisions] f WHERE e.id = f.parent_object_id)) as [__hcc] 
		FROM	dbo.[subdivisions] e 
				INNER JOIN [subdivisions_cte] d ON e.parent_object_id = d.id 
	) 

	select	t_elem.[id], 
			t_elem.[name], 
			[__hcc], 
			[__hlevel] 
	from	[subdivisions_cte] t_elem 
	where 	(( t_elem.[is_disbanded] = 0) OR (( t_elem.[is_disbanded]) IS NULL)) 
	order by t_elem.[__sort_level] asc 
	```
=== "906 + PostgreSQL"
	```sql
	WITH RECURSIVE "subdivisions_cte" ( 
		"id", 
		"code", 
		"name", 
		"org_id", 
		"parent_object_id", 
		"is_disbanded", 
		"knowledge_parts", 
		"tags", 
		"experts", 
		"place_id", 
		"region_id", 
		"kpi_profile_id", 
		"kpi_profiles_id", 
		"bonus_profile_id", 
		"cost_center_id", 
		"is_faculty", 
		"modification_date", 
		"app_instance_id", 
		"__hlevel", 
		"__sort_level","__hcc" 
		) AS ( 
			SELECT	"id", 
					"code", 
					"name", 
					"org_id", 
					"parent_object_id", 
					"is_disbanded", 
					"knowledge_parts", 
					"tags", 
					"experts", 
					"place_id", 
					"region_id", 
					"kpi_profile_id", 
					"kpi_profiles_id", 
					"bonus_profile_id", 
					"cost_center_id", 
					"is_faculty", 
					"modification_date", 
					"app_instance_id", 
					0 as __hlevel, 
					cast((CAST(FLOOR(LOG(ROW_NUMBER() over(order by e."id"))) as varchar)||cast(ROW_NUMBER() over(order by e."id") as varchar(256))) as varchar(256)) as "__sort_level", 
					(select 1 from dbo."subdivisions" f where f.parent_object_id = e.id limit 1) as "__hcc" 
			FROM dbo."subdivisions" e 
			WHERE e.id = '6327975429225669221' 
			UNION ALL 
			SELECT	e."id", 
					e."code", 
					e."name", 
					e."org_id", 
					e."parent_object_id", 
					e."is_disbanded", 
					e."knowledge_parts", 
					e."tags", 
					e."experts", 
					e."place_id", 
					e."region_id", 
					e."kpi_profile_id", 
					e."kpi_profiles_id", 
					e."bonus_profile_id", 
					e."cost_center_id", 
					e."is_faculty", 
					e."modification_date", 
					e."app_instance_id", 
					"__hlevel"+1, 
					cast((d."__sort_level"||'.'||CAST(FLOOR(LOG(ROW_NUMBER() over(order by e."id"))) as varchar)||cast(ROW_NUMBER() over(order by e."id") as varchar(256))) as varchar(256)) as "__sort_level", 
					(select 1 WHERE EXISTS (SELECT id FROM dbo."subdivisions" f WHERE e.id = f.parent_object_id)) as "__hcc" 
			FROM	dbo."subdivisions" e 
					INNER JOIN "subdivisions_cte" d ON e.parent_object_id = d.id 
	) 

	select	t_elem."id", 
			t_elem."name", 
			"__hcc", 
			"__hlevel" 
	from	"subdivisions_cte" t_elem 
	where 	(( t_elem."is_disbanded"=false) OR (( t_elem."is_disbanded") IS NULL)) 
	order by t_elem."__sort_level" asc nulls first 
	```

### Запрос с использованием MatchSome()

**XQuery**

=== "649 + MSSQL"
	```XQuery
	for 
		$elem in collaborators 
	where 
		MatchSome($elem/code, ('13744', '386792', 'nothing')) 
	return 
		$elem/Fields('id', 'code', 'fullname') 
	```
=== "906 + PostgreSQL"
	```XQuery
	for 
		$elem in collaborators 
	where 
		MatchSome($elem/code, ('13744', '386792', 'nothing')) 
	return 
		$elem/Fields('id', 'code', 'fullname') 
	```

**SQL**

=== "649 + MSSQL"
	```sql
	select	t_elem.[id], 
			t_elem.[code], 
			t_elem.[fullname] 
	from	dbo.[collaborators] t_elem 
	where	(t_elem.[code] in ('13744', '386792', 'nothing')) 
	```
=== "906 + PostgreSQL"
	```sql
	select	t_elem."id", 
			t_elem."code", 
			t_elem."fullname" 
	from	dbo."collaborators" t_elem 
	where	(t_elem."code" in ('13744','386792','nothing')) 
	order by t_elem.id 
	```

### Запрос с использованием CatalogHierSubset()

**XQuery**

=== "649 + MSSQL"
	```XQuery
	CatalogHierSubset('subdivisions', 6327975429225669221) 
	```
=== "906 + PostgreSQL"
	```XQuery
	CatalogHierSubset('subdivisions', 6327975429225669221) 
	```

**SQL**

=== "649 + MSSQL"
	```sql
	WITH [subdivisions_cte] ( 
		[id], 
		[code], 
		[name], 
		[org_id], 
		[parent_object_id], 
		[is_disbanded], 
		[knowledge_parts], 
		[tags], 
		[experts], 
		[place_id], 
		[region_id], 
		[kpi_profile_id], 
		[bonus_profile_id], 
		[cost_center_id], 
		[is_faculty], 
		[modification_date], 
		[app_instance_id], 
		[kpi_profiles_id], 
		[__hlevel], 
		[__sort_level], 
		[__hcc] 
	) AS ( 
		SELECT	[id], 
				[code], 
				[name], 
				[org_id], 
				[parent_object_id], 
				[is_disbanded], 
				[knowledge_parts], 
				[tags], 
				[experts], 
				[place_id], 
				[region_id], 
				[kpi_profile_id], 
				[bonus_profile_id], 
				[cost_center_id], 
				[is_faculty], 
				[modification_date], 
				[app_instance_id], 
				[kpi_profiles_id], 
				0 as [__hlevel],cast((CAST(FLOOR(LOG10(ROW_NUMBER() over(order by e.id))) as varchar) + cast(ROW_NUMBER() over(order by e.id) as varchar(256))) as varchar(256)) as [__sort_level], 
				(select top 1 1 from dbo.subdivisions f where f.parent_object_id = e.id) as [__hcc] 
		FROM	dbo.subdivisions e 
		WHERE	parent_object_id = 6327975429225669221 
		UNION ALL 
		SELECT	e.[id], 
				e.[code], 
				e.[name], 
				e.[org_id], 
				e.[parent_object_id], 
				e.[is_disbanded], 
				e.[knowledge_parts], 
				e.[tags], 
				e.[experts], 
				e.[place_id], 
				e.[region_id], 
				e.[kpi_profile_id], 
				e.[bonus_profile_id], 
				e.[cost_center_id], 
				e.[is_faculty], 
				e.[modification_date], 
				e.[app_instance_id], 
				e.[kpi_profiles_id], 
				[__hlevel] + 1, 
				cast((d.[__sort_level] + '.' + CAST(FLOOR(LOG10(ROW_NUMBER() over(order by e.id))) as varchar) + cast(ROW_NUMBER() over(order by e.id) as varchar(256))) as varchar(256)) as [__sort_level], 
				(select 1 WHERE EXISTS (SELECT id FROM dbo.subdivisions f WHERE e.id = f.parent_object_id)) as [__hcc] 
		FROM	dbo.subdivisions e 
				INNER JOIN [subdivisions_cte] d ON e.parent_object_id = d.id 
	) 

	select	t_x.* 
	from	[subdivisions_cte] t_x 
	```
=== "906 + PostgreSQL"
	```sql
	WITH RECURSIVE "subdivisions_cte" ( 
		"id", 
		"code", 
		"name", 
		"org_id", 
		"parent_object_id", 
		"is_disbanded", 
		"knowledge_parts", 
		"tags","experts", 
		"place_id", 
		"region_id", 
		"kpi_profile_id", 
		"kpi_profiles_id", 
		"bonus_profile_id", 
		"cost_center_id", 
		"is_faculty", 
		"modification_date", 
		"app_instance_id", 
		"__hlevel", 
		"__sort_level", 
		"__hcc" 
	) AS ( 
		SELECT	"id", 
				"code", 
				"name", 
				"org_id", 
				"parent_object_id", 
				"is_disbanded", 
				"knowledge_parts", 
				"tags", 
				"experts", 
				"place_id", 
				"region_id", 
				"kpi_profile_id", 
				"kpi_profiles_id", 
				"bonus_profile_id", 
				"cost_center_id", 
				"is_faculty", 
				"modification_date", 
				"app_instance_id", 
				0 as "__hlevel", 
				cast((CAST(FLOOR(LOG(ROW_NUMBER() over(order by e."id"))) as varchar)||cast(ROW_NUMBER() over(order by e."id") as varchar(256))) as varchar(256)) as "__sort_level", 
				(select 1 from dbo.subdivisions f where f.parent_object_id = e.id limit 1) as "__hcc" 
		FROM	dbo.subdivisions e 
		WHERE	parent_object_id = '6327975429225669221' 
		UNION ALL 
		SELECT	e."id", 
				e."code", 
				e."name", 
				e."org_id", 
				e."parent_object_id", 
				e."is_disbanded", 
				e."knowledge_parts", 
				e."tags", 
				e."experts", 
				e."place_id", 
				e."region_id", 
				e."kpi_profile_id", 
				e."kpi_profiles_id", 
				e."bonus_profile_id", 
				e."cost_center_id", 
				e."is_faculty", 
				e."modification_date", 
				e."app_instance_id", 
				"__hlevel"+1, 
				cast((d."__sort_level"||'.'||CAST(FLOOR(LOG(ROW_NUMBER() over(order by e."id"))) as varchar)||cast(ROW_NUMBER() over(order by e."id") as varchar(256))) as varchar(256)) as "__sort_level", 
				(select 1 WHERE EXISTS (SELECT id FROM dbo.subdivisions f WHERE e.id = f.parent_object_id)) as "__hcc" 
		FROM	dbo.subdivisions e 
				INNER JOIN "subdivisions_cte" d ON e.parent_object_id = d.id 
	) 

	select	t_x.* 
	from	"subdivisions_cte" t_x 
	```

### Запрос с использованием IsEmpty()

**XQuery**

=== "649 + MSSQL"
	```XQuery
	for 
		$elem in collaborators 
	where 
		IsEmpty($elem/birth_date) = true() 
	return 
		$elem/Fields('id', 'fullname', 'position_name') 
	```
=== "906 + PostgreSQL"
	```XQuery
	for 
		$elem in collaborators 
	where 
		IsEmpty($elem/birth_date) = true() 
	return 
		$elem/Fields('id', 'fullname', 'position_name') 
	```

**SQL**

=== "649 + MSSQL"
	```sql
	SELECT	[t_elem].[id], 
			[t_elem].[fullname], 
			[t_elem].[birth_date] 
	FROM	[dbo].[collaborators] [t_elem] 
	WHERE	(case when [t_elem].[birth_date] IS NULL then 1 else 0 end) = 1 
	```
=== "906 + PostgreSQL"
	```sql
	select	t_elem."id", 
			t_elem."fullname", 
			t_elem."position_name" 
	from	dbo."collaborators" t_elem 
	where	(case when t_elem."birth_date" is null then true else false end)=true 
	order by t_elem.id 
	```

### Запрос к нескольким таблицам через ljoin/on (LEFT JOIN)

!!! warning "Важно"
	Обратите внимание на порядок таблиц в `XQuery` запросе и в каком порядке они оказались собраны в `SQL` запросе, `ljoin` джойнит правую таблицу `collaborators` к левой `positions`

**XQuery**

=== "649 + MSSQL"
	```XQuery
	for 
		$pos in positions ljoin $elem in collaborators 
			on $elem/position_id = $pos/id 
	where 
		MatchSome($elem/code, ('13744', '386792')) 
	return 
		$elem/id, $pos/name 
	```
=== "906 + PostgreSQL"
	```XQuery
	for 
		$pos in positions ljoin $elem in collaborators 
			on $elem/position_id = $pos/id 
	where 
		MatchSome($elem/code, ('13744', '386792')) 
	return 
		$elem/id, $pos/name 
	```

**SQL**

=== "649 + MSSQL"
	```sql
	select	t_elem.[id], t_pos.[name] 
	from	dbo.[collaborators] t_elem 
			LEFT JOIN dbo.[positions] t_pos on t_elem.[position_id] = t_pos.[id] 
	where	(t_elem.[code] in ('13744', '386792')) 
	```
=== "906 + PostgreSQL"
	```sql
	select	t_elem."id", 
			t_pos."name" 
	from	dbo."collaborators" t_elem 
			LEFT JOIN dbo."positions" t_pos on t_elem."position_id"= t_pos."id" 
	where	(t_elem."code" in ('13744','386792')) 
	```

### Запрос к нескольким таблицам через rjoin/on (RIGHT JOIN)

!!! warning "Важно"
	Обратите внимание на порядок таблиц в `XQuery` запросе и в каком порядке они оказались собраны в `SQL` запросе, `rjoin` джойнит правую таблицу `collaborators` к левой `positions`

**XQuery**

=== "649 + MSSQL"
	```XQuery
	for 
		$pos in positions rjoin $elem in collaborators 
			on $elem/position_id = $pos/id 
	where 
		MatchSome($elem/code, ('13744', '386792')) 
	return 
		$elem/id, $pos/name 
	```
=== "906 + PostgreSQL"
	```XQuery
	for 
		$pos in positions rjoin $elem in collaborators 
			on $elem/position_id = $pos/id 
	where 
		MatchSome($elem/code, ('13744', '386792')) 
	return 
		$elem/id, $pos/name 
	```

**SQL**

=== "649 + MSSQL"
	```sql
	select	t_elem.[id], t_pos.[name] 
	from	dbo.[collaborators] t_elem 
			RIGHT JOIN dbo.[positions] t_pos on t_elem.[position_id] = t_pos.[id] 
	where	(t_elem.[code] in (@p0,@p1)) ('13744', '386792')) 
	```
=== "906 + PostgreSQL"
	```sql
	select	t_elem."id", 
			t_pos."name" 
	from	dbo."collaborators" t_elem 
			RIGHT JOIN dbo."positions" t_pos on t_elem."position_id"= t_pos."id" 
	where	(t_elem."code" in ('13744','386792')) 
	```

### Запрос к нескольким таблицам через join/on (INNER JOIN)

!!! warning "Важно"
	Обратите внимание на порядок таблиц в `XQuery` запросе и в каком порядке они оказались собраны в `SQL` запросе, `join` джойнит правую таблицу `collaborators` к левой `positions`

!!! warning "Важно"
	Обратите внимание, что в запросе `XQuery` между `Fields` нет запятой. При попытке поставить запятую, запрос выкидывает ошибку

**XQuery**

=== "649 + MSSQL"
	```XQuery
	for 
		$pos in positions join $elem in collaborators 
			on $elem/position_id = $pos/id 
	where 
		MatchSome($elem/code, ('13744', '386792')) 
	return 
		$elem/Fields('id') $pos/Fields('name') 
	```
=== "906 + PostgreSQL"
	```XQuery
	for 
		$pos in positions join $elem in collaborators 
			on $elem/position_id = $pos/id 
	where 
		MatchSome($elem/code, ('13744', '386792')) 
	return 
		$elem/Fields('id') $pos/Fields('name') 
	```

**SQL**

=== "649 + MSSQL"
	```sql
	select	t_elem.[id], t_pos.[name] 
	from	dbo.[collaborators] t_elem 
			INNER JOIN dbo.[positions] t_pos on t_elem.[position_id] = t_pos.[id] 
	where	(t_elem.[code] in ('13744', '386792')) 
	```
=== "906 + PostgreSQL"
	```sql
	select	t_elem."id", 
			t_pos."name" 
	from	dbo."collaborators" t_elem 
			INNER JOIN dbo."positions" t_pos on t_elem."position_id"= t_pos."id" 
	where	(t_elem."code" in ('13744','386792')) 
	```

### Запрос с исключением записей через MatchSome

!!! warning "Важно"
	Обратите внимание на порядок таблиц в `XQuery` запросе и в каком порядке они оказались собраны в `SQL` запросе, `ljoin` джойнит правую таблицу `collaborators` к левой `collaborators`

**XQuery**

=== "649 + MSSQL"
	```XQuery
	for 
		$el in collaborators ljoin $elem in collaborators 
			on $elem/id = $el/id and MatchSome($el/code, ('13744', '386792')) 
	where 
		$el/id = null() 
	return 
		$elem/Fields('id') 
	```
=== "906 + PostgreSQL"
	```XQuery
	for 
		$elem_ex in collaborators ljoin $elem in collaborators 
			on $elem_ex/id = $elem/id and MatchSome($elem_ex/code, ('13744', '386792')) 
	where 
		$elem_ex/id = null() 
	return 
		$elem/Fields('id', 'fullname') 
	```

**SQL**

=== "649 + MSSQL"
	```sql
	select	t_elem.[id] 
	from	dbo.[collaborators] t_elem 
		LEFT JOIN dbo.[collaborators] t_el on t_elem.[id]= t_el.[id] and (t_el.[code] in ('13744', '386792')) 
	where	t_el.[id] IS NULL 
	```
=== "906 + PostgreSQL"
	```sql
	select	t_elem."id", 
			t_elem."fullname" 
	from	dbo."collaborators" t_elem 
			LEFT JOIN dbo."collaborators" t_elem_ex on t_elem_ex."id"= t_elem."id" and (t_elem_ex."code" in ('13744','386792')) 
	where	t_elem_ex."id" IS NULL 
	order by t_elem_ex.id 
	```

### Запрос с формированием уникального списка DISTINCT

**XQuery**

=== "649 + MSSQL"
	!!! warning "Важно"
		Работает только в том случае, когда в запросе `XQuery` возвращается только одно поле, по которому нужна уникальность

	```XQuery
	for 
		$elem in group_collaborators 
	return 
		distinct($elem/group_id) 
	```
=== "906 + PostgreSQL"
	!!! warning "Важно"
		Для корректной работы необходимо явно указать сортировку `order by` по одному из выводимых полей. Проблема в том, что по умолчанию в запрос всегда добавляется сортировка `order by t_elem.id`, но в запросе выводятся другие поля, что приводит к ошибке.

	```XQuery
	for 
		$elem in learnings 
	order by 
		$elem/course_name
	return 
		distinct $elem/person_fullname, $elem/course_name
	```

**SQL**

=== "649 + MSSQL"
	```sql
	select	distinct(t_elem.[group_id]) 
	from	dbo.[group_collaborators] t_elem 
	```
=== "906 + PostgreSQL"
	```sql
	select	distinct t_elem."person_fullname", 
			t_elem."course_name" 
	from	dbo."learnings" t_elem 
	order by t_elem."course_name" asc nulls first
	```