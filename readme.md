# WebSoft Guide

Для создания сайта используется генератор статических сайтов [MkDocs](https://www.mkdocs.org/) и плагин [Material for MkDocs](https://squidfunk.github.io/mkdocs-material/).

## Сайт

* [galifrey.github.io/websoft-guide/](https://galifrey.github.io/websoft-guide/)

## Удаленные репозитории

* `github`: `git@github.com:GaliFrey/websoft-guide.git`

## Github Page

Mkdocs автоматически собирает проект, делает коммит в ветку `gh-pages` и отправляет ветку `gh-pages` в репозиторий. Для создания Github Page сипользуется команда

```bash
mkdocs gh-deploy -r github
```

## Установка и настройка MkDocs

Устанавливаем python и pip. Проверяем их версии 
```bash
$ python --version
Python 3.13.1
$ pip --version
pip 25.1.1 from C:\Python313\Lib\site-packages\pip (python 3.13)
```

Устанавливаем окружение
```bash
$ python -m venv venv
$ source venv/bin/activate
```

Устанавливаем mkdocs, инструкции для Windows
```bash
$ pip install mkdocs
```

Проверяем весрию mkdocs
```bash
$ mkdocs --version
mkdocs, version 1.6.1 from C:\Python313\Lib\site-packages\mkdocs (Python 3.13)
```

Устанавливаем Material
```bash
$ pip install mkdocs-material
```

Запускаем live-server проекта
```bash
$ mkdocs serve
```

Публикуем на GitHubPage
```bash
$ mkdocs gh-deploy
```