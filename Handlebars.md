&nbsp; &nbsp; &nbsp; &nbsp; Handlebars – это шаблонизатор JavaScript, для создания динамических интерфейсов обновляя данные и манипулируя DOM деревом. Шаблон предназначен для хранения “образа“ разметки, невидимого и предназначенного для вставки куда-либо. При использовании этого шаблонизатора, реализующий шаблон View/Controller, код становиться чище и его легче модифицировать.
&nbsp; &nbsp; &nbsp; &nbsp; Для того что бы начать работать с Handlebars, необходимо его подключить, следующим образом:
```<script src="js/handlebars.js"></script>```
&nbsp; &nbsp; &nbsp; &nbsp; Handlebars генерирует HTML из JSON данных. Для того, что бы браузер распознал шаблон Handlebars, мы должны присвоить ему тип text/x-handlebars-template, а также ID, что бы ссылаться на него в дальнейшем. Приведу пример разметки, которая будет получать имя и выводить HTML:
```
<ul class="updates">
     <script id="template" type="text/x-handlebars-template">
        <li>
 	        <h2>{{name}}</h2>
        </li>
   </script>
</ul>
```
&nbsp; &nbsp; &nbsp; &nbsp; Имя будет получено из JSON и подставлено вместо {{name}} . Затем нужно указать источник данных:
```
var data = {
   name : 'John Doe'
},
```
&nbsp; &nbsp; &nbsp; &nbsp; Для того что бы свойство name у объекта data поместить в HTML нужно скомпилировать код шаблона и присоединить его к классу .updates в HTML, передав JSON объект data как параметр:
```
var template = Handlebars.compile( $('#template').html() );
$('.updates').append( template(data) );
```
&nbsp; &nbsp; &nbsp; &nbsp; Так же, если нам понадобиться обойти массив, то у Handlebars есть выражение each, которое поможет это сделать. Допустим теперь объект data, который я использовал выше для примера, это не просто объект, а массив объектов:
```
var data = { updates: [ 
 { 
    name: 'Jane Doe',
    update: 'Just Made my Breakfaast',
    from: 'Web', location: 'Canada'
},
{
    name: 'John Doe',
    update: 'What is going on with the weather?',
    from: 'Phone',
 }
]},
```
&nbsp; &nbsp; &nbsp; &nbsp; Что бы теперь обойти массив объектов в data нужно обернуть HTML выражением each:
```
{{#each updates}} 
    <li>
        <h2>{{name}}</h2>
        <p>{{{update}}}</p>
        <span>
            Sent From: {{from}} - In: {{location}}
        </span>
    </li>
{{/each}}
```
&nbsp; &nbsp; &nbsp; &nbsp; У объекта c именем ‘John Doe’, свойство location – нету значения, и что бы нам не выводить пустую строку можно включить условие if:
```
{{#each updates}}
<li> 
    <h2>{{name}}</h2>
    <p>{{{update}}}</p>
    <span>
        Sent From: {{from}}
        {{#if location}}
            - In: {{location}}
        {{/if}}
    </span>
</li>
{{/each}}
```
&nbsp; &nbsp; &nbsp; &nbsp; В статье я рассказал о базовых возможностях Handlebars. Handlebars — отличный вариант для приложений с постоянно изменяющимися данными. 
