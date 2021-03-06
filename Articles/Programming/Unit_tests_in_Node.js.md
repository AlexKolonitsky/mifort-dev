# Unit tests in Node.js

Как долго разработчики собираются терпеть несправедливость в существующем цикле разработке приложения? Задумайтесь, как часто к вам прибегали из отдела тестирования с ликующим восклицанием: "Нашел!", "У тебя опять не работает!". И как часто оказывалось, что это не концептуальная ошибка, а какой-нибудь недочет, возникший при добавлении нового функционала? Но сейчас у нас, разработчиков, наконец, есть возможность это исправить: самими сопровождать определенные слои тестирования. 

У вас не возникло желание писать тесты? 

А даром предвиденья случайно не обладаете? 
Ваш код сам знает, когда ему изменятся при внесении нового функционала? 
Или быть может вы единственный, кто разрабатывает проект? Правда и в этом случае уже через пару-тройку месяцев вы не вспомните, почему в коде происходят те или иные действия. А если надо срочно внести изменения? Какая вероятность, что они не сломают что-нибудь в коде? 


### Какими бывают тесты?
В разных источниках можно найти различную классификацию тестов и подходов к их написанию. В стандартном варианте описании тестов используют следующее представление: пирамида, основание которой unit tests, затем integtation и на вершине end-to-end. 

![](https://github.com/PerminovaAnastasia/Images/blob/master/rules-of-thumb.png)

Данная графическая интерпретация характеризует оптимальное количественное соотношение тестов в приложении.

### Так что же такое unit тесты?

Unit тестирование - это процесс в программировании, который позволяет проверить на корректность отдельные модули или функции программы. 

Другими словами, данный вид тестов является первым барьером на пути потенциальных багов, и именно они принимают на себя основном удар изменений в коде.

### Какими должны быть unit тесты?

* Быстрыми в выполнении
> Это связано с тем, что существует практика, при которой каждое изменение в коде основной ветки проверяется уже реализованной системой тестов (не end-to-end тестов) и в случае, когда хотя бы один тест не проходит - внесенные правки не принимаются до исправления ситуации, с тестами(вдруг устарели?) или с кодом. Так называемый подход красно-зеленого тестирования. 
* Независимыми от окружения
* Легко поддерживаемыми и изменяемыми
> Как часто вы пишете код, который никогда после не меняете? Так и с тестами, рефакторинг кода может повлечь за собой необходимость поправить и тесты его проверяющие.
* Оформление теста должно быть своеобразной спецификацией для тестируемого участка
> Существует специальный синтаксис и набор функций позволяющий это обеспечить, о которых позже.
* Достоверными
> Не делайте так, что тестируемая функция считает сумму двух чисел, в то время как в тесте проверяется умножение. 
* По unit тестам должно быть четко понятно, что именно в коде пошло не так
> Это является существенным отличием от остальных видов тестов : unit предназначены для локализации ошибок.

## Реализация unit тестов в Node.js.

Для Node.js можно найти различные фреймворки и библиотеки, позволяющие автоматизировать тестирование. Остановимся на следующей их   комбинации: Mocha и Chai.

###### Итак, необходимые средства для работы:

| Объект | Для чего используется |
| :---:       |     :---      |
|[Mocha](https://mochajs.org)|Фреймворк для создания окружения тестирования; включает в себя функции describe и it *для спецификации тестов* |
|[Chai](http://chaijs.com)|Библиотека, предоставляющая функции проверки: *should, expect, assert*|

Ну и разумеется тестируемый код. Для примера будем проверять следующую функцию: сумму двух комплексных чисел.

```
Комплексное число представим в виде объекта:
{
	re: number,
	im: number
}
function complexSum(a, b) {
  return {
    re: a.re + b.re,
    im: a.im + b.im
  }
}
```
Реализация теста для функции сложения может быть представлена в таком виде:
```
const assert = require('chai').assert;
const complex = require('./complex');

describe("Summarize complex numbers", () => {

  it("Summarize complex numbers", (done) => {

    let complexNumber = {
      re: 2,
      im: 3
    };

    assert.deepEqual(complex.complexSum(complexNumber, {re: 1, im: 4}), {re: 3, im: 7});
    assert.deepEqual(complex.complexSum(complexNumber, 0), complexNumber);
    
    done();
  });

});

```

*Что же здесь проиходит?:*

	1. describe(название, () => { ... })
	Задает, что именно описывается - помогает формировать логические блоки it
	2. it(название, () => { ... })
	Название блока it используется для описания того, что должна собственно сделать функция в том или ином случае
	3. assert.equal(...) - в этом месте и происходит проверка, в результате которой тест либо проходит, либо нет 
	
Итак, в первом assert проверяем сумму двух комплексных чисел, во втором - сумму с нулем. Не беспокойтесь, так запланировано, что при запуске теста мы получим ошибку. Ноль - не объект. И оказывается, что в функции не предусмотрена возможность сложения с обычным числом. 

Консольный вывод результата работы теста:

![](https://github.com/PerminovaAnastasia/Images/blob/master/Unit-Test-1.png)

Если нажать на `Click to see difference`, то можно увидеть разницу между текущим и ожидаемым результатом.

![](https://github.com/PerminovaAnastasia/Images/blob/master/Unit-Test-3.png)

Так исправим же нашу функцию!

```
function isComplex(value) {

  return !isNaN(Number(value.re)) && !isNaN(Number(value.im)) || !isNaN(Number(value));
}

function complexSum(a, b) {

  if(isComplex(a) && isComplex(b)){

    let result = {};

    result.re = a.re && b.re ? a.re + b.re : result.re = a.re ? a.re + b : result.re = b.re ? b.re + a : a + b;
    result.im = a.im && b.im ? a.im + b.im : result.im = a.im ? a.im : result.im = b.im ? b.im : 0;

    return result;

  }
  return null;
}

```
Запустим тесты и убедимся что все произошло как и запланировали.

![](https://github.com/PerminovaAnastasia/Images/blob/master/Unit-Test-2.png)


>Но что еще неправильного в примере теста вы видите?
Если мы оставим только второй assert, то так и не узнаем, завершиться ли он успешно (как это и произошло в примере, поскольку 2 комплексных числа исходная функция смогла бы вычислить). Поэтому и стоит не забывать важную мысль: `один тест должен проверять что-то одно`. И в случае, если вы хотите проверить работу сервиса на разном наборе параметров одновременно, стоит их разделить на отдельные блоки. 

```
Предпочтительный вариант разделения на отдельные блоки it:

const assert = require('chai').assert;
const complex = require('./complex');

describe("Summarize complex numbers", () => {

  it("Summarize complex number with zero", function() {

    let complexNumber = {re:2, im:3};

    assert.deepEqual(complex.complexSum(complexNumber, 0), complexNumber);
  });

  it("Summarize 2 complex numbers", function() {

    let complexNumber = {re:2, im:3};

    assert.deepEqual(complex.complexSum(complexNumber, {re:1, im:4}), {re:3, im:7});
  });

});
```	

![](https://github.com/PerminovaAnastasia/Images/blob/master/test-result-3.1.png)

Как вы можете заметить, предложенное разделение позволит вам сэкономить время, которое потребуется для определения того, какой же случай не прошел намеченный тест. 

### Основные выводы:

- Тесты нужны не для того, чтобы предотвратить *все* потенциальные ошибки - тут уже не отбирайте последнее у тестировщиков! Но вы как разработчик несете ответственность за качество кода.
- Ваш код всегда меняется: добавляется новый функционал, рефакторится существующий код. И тесты – ваш гарант того, что эти изменения не навредили текущему состоянию приложения. 
- Тесты уменьшают время цикла разработки программы. 
Через сколько дней и как долго происходит проверка Вашего функционала? Пару дней, неделя? А найденная ошибка касалась того, что вы не учли какой-то простейший случай, ошиблись в работе с объектом и др. Вы исправите - и на проверку. И снова все по кругу. 

![](https://github.com/PerminovaAnastasia/Images/blob/master/Diagram.png)

- Когда пишешь тест, ты смотришь на свой код под другим углом зрения и замечаешь лишние/недостающие моменты - это помогает улучшить структуру.
- Иногда они могут помочь упростить понимание логики проверяемого участка.
