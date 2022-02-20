# Chapter 1 General

- В HTML все называется **элементами (elements)** или **тегами**. Например, `<h1></h1>` - это heading element or heading tag. То, что лежит внутри тега - это **атрибуты (attribute)**. Например, `<img src="/demo.jpg" alt="description" height="48" width="100" />` - тут `src`, `alt`, `height` и `width` - это атрибуты.
  
- **Двойной тег (doulbe)** - тег, который надо открыть и закрыть (напр., `<ul> ... </ul>`). **Унитарный тег (unitary)** - тег, который только открывает и не требует закрытия (напр., `<li> ... ` ).


## Общая структура файла
```html
<!DOCTYPE html>                         <!-- html version-->
<html>                                  <!-- root element -->
    <head>                              <!-- info about the page (meta, links, title) -->
        <title>Page Title</title>       <!-- page title -->
        ...
    </head>
    <body>                              <!-- what is actually displayed on web page -->
         ...
    </body>
</html>
```
- Под `<head> </head>` стоит мета дата, те информация о сайте, его файлах и свойствах, которая не рендерится и не влияет на его внешний вид.
- Под `<body> </body>` идет тело сайта. Все, что стоит тут - будет отобржаться на сайте.

## Emmet autocomplete
В VSC предустановлен emmet autocomplete. Если начать писать название элемента/тега без <>, то emmet предложит его.   

`lorem` генерировует рандомный текст для заполнения параграфов. Можно сгенирировать текст опреденной длинны `lorem10`, `lorem500`, `lorem123` и тд.
