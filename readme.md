# WebSoft Guide

Для создания сайта используется генератор статических сайтов [MkDocs](https://www.mkdocs.org/) и плагин [Material for MkDocs](https://squidfunk.github.io/mkdocs-material/).

## Удаленные репозитории

* `github`: `git@github.com:GaliFrey/websoft-guide.git`

## Github Page

Mkdocs автоматически собирает проект, делает коммит в ветку `gh-pages` и отправляет ветку `gh-pages` в репозиторий. Для создания Github Page сипользуется команда

```bash
mkdocs gh-delpoy -r github
```