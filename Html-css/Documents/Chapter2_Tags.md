# Chapter 2 Tags

## Editing text

###  Headings 
```html
<h1>Page title</h1>
<h2>Subheading</h2>
<h3>Tertiary heading</h3>
<h4>Quaternary heading</h4>
```
###  Paragraph 
```html
<p style="text-align: center;">text</p>`
```

###  Horizontal Line 
```html 
<hr /> 
```

###  Line break 
```html 
<br />
```

### Specisl charecters 
Starts with &, ends with ; : `&name_of_chareter`;
```html
&copy;  <!-- copyright sign -->
```

### Subscripts & Superscripts
**Superscripts** - верхний регистр. **Subscripts** - нижний регистр.   
```html
Hello I'm 1<sup>st</sup> dude. Number a<sub>12</sub>. 
```

### Strong & italic
Bold(`<strong>`) & italic (`<em>`).   
```html 
This text is in <strong>bold</strong>. This text is in <em> italic! </em>.
```

---------------------------------------------------------------------------

## Forms
```html
<form action = "" method = "">
	<label for = "some_id">Put your text here </label>
        <input type = "text" name = "name" id = "some_id"/>
	<button type="submit">Submit</button>		<!-- текст под тегом - это то, что будет написано на теге -->
	<!-- вместо <button> тега можно использовать еще один <input> те: <input type ="submit"> будет то же самое -->
</form>
```
- `type` - тип ожидаемой информации при вводе. Их много, см [тут](https://www.w3schools.com/tags/att_input_type.asp). Если вводимая информация не соответствует типу input, то предупреждение. Например, `<input type = "email">`, если ввод не содержит @, то будет выведена ошибка.
- Если использовать `<label>` тег, то под name - идет название id для `<input>`. Тогда при назатии на текст (внутри label тега) курсор автоматически перейдет на соответвующую форму.
- `<button type="submit">Submit</button>` и ` <input type ="submit">` работают одинаково.

### input 
```html
<input type = "password" placeholder = "password here" /> 
<input type = "email" value = "some_email@gmail.com" /> 
<input type = "submit" value = "SSSSSUBMIT!" />
```
- `type` - тип ожидаемой информации при вводе. Их много, см [тут](https://www.w3schools.com/tags/att_input_type.asp). Если вводимая информация не соответствует типу input, то предупреждение. Например, `<input type = "email">`, если ввод не содержит @, то будет выведена ошибка.
- `placeholder = ""` - данная строка будет отобразаться в форме для ввода пока туда что-нибудь не начнут писать (динамически).
- `value` - пишется статический текст, те его нельзя убрать/удалить. Это удобно например в случаи `<input type = "submit" value = "SSSSSUBMIT!" >` - если нет value, то автоматически появится кнопка "Submit" (имя по дефолту), а так кнопка будет называться "SSSSSUBMIT!".

#### radio buttons (once choice answers)
Нужно для форм с одним возможным ответом.
```html
<input type = "radio" name = "code_lang" value = "java"/> Java
<input type = "radio" name = "code_lang" value = "javascript"/> JavaScript
<input type = "radio" name = "code_lang" value = "python"/> Python
<input type = "radio" name = "code_lang" value = "c_plus"/> C++
```
- Получиься форма с единственно возможным ответом. Необходимо убедиться, что у все совпадает атрибут `name` тк ответы относятся к одной форме, если у них совпадает name. Если name разные, то - это разные формы. 
- `value` - должны быть уникальны для каждого ответа, тк это то значение, которое передается дальше на бекэнд после того, как пользователь ответил.

#### check boxes (multichoice answers)
Нужно для форм с несколькими ответоми.
```html
<input type = "checkbox" name = "code_lang" value = "java"/> Java
<input type = "checkbox" name = "code_lang" value = "javascript"/> JavaScript
<input type = "checkbox" checked name = "code_lang" value = "python"/> Python
<input type = "checkbox" checked = "checked" name = "code_lang" value = "c_plus"/> C++
```
- Атрибут `checked` делает эту позицию изначально выбранной, пользователь может убрать этот выбор. `checked = "checked"` то же самое, что и `checked`. 

### Select (dropdown selection menu)
Нужно для форм, где ответ выбирается из откидного меню
```html
<select name = "languges" id="">
	<option value = "java"> Java </option>
	<option value = "javascript"> JavaScript </option>
	<option value = "python"> Python </option>
	<option value = "c_plus"> C++ </option>
</select>
```

### texarea
Создает область куда пользователь вписывает свой текст. Размер области определяется атрибутами `cols` и `rows`.
```html
<textarea name = "" id = "" cols = "20" rows = "10"> some text here <\textarea>
```
---------------------------------------------------------------------------

## Lists (списки)
There are 2 types: unoreder (`<ul> </ul>`) and ordered (`<ol> </ol>`) lists. Весь контент списка заключен в свои внешними тегами, а сами объекты внутри обозначаются тегом `<li> ... </li>` (вне зависимости от вида списка). Можно создавать nested lists:
```html
<ol>
	<li> Vasya </li>	<!-- Обычный элемент списка -->
	<li> 			<!-- nested unordered list -->
		<ul>
			<li> one </li>
			<li> two </li>
		</ul>
	</li> 
</ol>	
```

---------------------------------------------------------------------------

## Tabels
```html 
<table>					<!-- Табилица заполнятеся по-строчно -->	
	<tr>				<!-- Открытие тега строки (row) -->
		<th> Fisrt </th>	<!-- Далее идут все названия столбцов -->
		<th> Second </th>
		<th> Third </th>
	</tr>

	<tr>				<!-- Открытие тега строки (row) -->
		<td> Vasya </td>	<!-- Далее идут все данные (td) в этой строке -->
		<td> Katya </td>
		<td> Petya </td>
	</tr>
	
</table>
```
- Данные в таблицах запоняются по строчно. `<th>` (table heading), `<td>` (table row), `<td>` (table data).

---------------------------------------------------------------------------

## Other

###  Div Section 
```html
<div>Block element</div>
```

###  Image 
```html
<img src="./demo.jpg" alt="description" height="48" width="100" />
```
- `scr` - path to the picture (on machine or web address). Path is presented the Linux way: `./` - в той же папке.   
- `alt` - if path is broken alternative text will be displayed.    
- `width` or `height` - если указать только длинну/ширину, то второй атрибут будет подогнан автоматически (без явного указания). Если явно указаны оба атрибута, то картинка будет соответстующих размеров. Можно также указать `width = "100%"`, тогда картинка будет адаптирована под размер браузера.

###  Outbound Link 
```html
<a href="https://htmlg.com/" target="_blank" rel="nofollow">Click here</a>	
```
- `href` - артрибут, где указывается path. Может быть до локального html файла (напр., `href = "./about.html")` или до web страницы (`href="https://htmlg.com/"`). Так же это может быть ссылка внутри файла используя **id** (`href = "#id_mane"`. В определенном месте страницы надо поставить атрибут `id = "id_name"` внутри какого-либо тега). Пустая ссылка (на на что): `href = "#"`.    
- То, что внутри тега `<a ..> </a>` будет кликабельной ссылкой. Туда можно вставить текст или другие теги, например, картинку (используя теги для картинок по стандартному для них синтаксису).     
- `target="_blank"` - при переходе по ссылке будет открываться новая вкладка. Если этого атрибута нет, то переход по ссылке произойдет с той же вкладки.    

###  Mailto link 
```html
<a href="mailto:me@ruwix.com?Subject=Hi%20mate" target="_top">Send Mail</a>
```

###  Inner anchor (jump on page 
```html
<a href="#footer">Jump to footnote</a>
<br />
<a name="footer"></a>Footnote content
```

###  Underlined text 
```html
<span style="text-decoration: underline;">Underlined text</span>
```

###  Iframe 
```html
<iframe src="link.html" width="200" height="200">
</iframe>
```

###  Abbreviation 
```html
<abbr title="Hypertext Markup Language">HTML</abbr>
```

###  Comment 
```html
<!-- HTML
Comment -->
```

###  Quotation 
```html
<q>Success is a journey not a destination.</q>
<blockquote cite="https://ruwix.com/">
The Rubik's Cube is the World’s best selling puzzle toy.
</blockquote> 
```

###  Video 
```html
<video width="200" height="150" controls>
	<source src="vid.mp4" type="video/mp4">
	<source src="vid.ogg" type="video/ogg">
	No video support.
</video>
```

###  Audio 
```html
<audio controls>
    <source src="sound.ogg" type="audio/ogg">
    <source src="sound.mp3" type="audio/mpeg">
    No audio support.
</audio> 
```
