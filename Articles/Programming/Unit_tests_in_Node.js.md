# Unit tests in Node.js

Как долго разработчики собираются терпеть несправедливость в существующем цикле разработке приложения? Задумайтесь, как часто к вам прибегали из отдела тестирования с ликующим восклицанием: "Нашел!", "У тебя опять не работает!". И как часто оказывалось, что это не концептуальная ошибка, а какой-нибудь недочет, возникший при добавлении нового функционала? Но сейчас у нас, разработчиков, наконец, есть возможность это исправить: самими сопровождать определенные слои тестирования. 

В разных источниках можно найти различную классификацию тестов и подходов к их написанию. В стандартном варианте описании тестов используют следующее представление: пирамида, основание которой unit tests, затем integtation и на вершине end-to-end. 

![](https://github.com/PerminovaAnastasia/Images/blob/master/rules-of-thumb.png)

Данная графическая интерпретация характеризует оптимальное количественное соотношение тестов в приложении.

### Так что же такое unit тесты?

Unit тестирование - это процесс в программировании, который позволяет проверить на корректность отдельные модули или функции программы. 

Другими словами, данный вид тестов является первым заградительным пунктом на пути потенциальных багов, и именно они принимают на себя основном удар изменений в коде.

### Какими должны быть unit тесты?

* Быстрыми в выполнении
> Это связано с тем, что существует практика, при которой каждое изменение в коде основной ветки проверяется уже реализованной системой тестов (не end-to-end тестов) и в случае, когда хотя бы один тест не проходит - внесенные правки не принимаются до исправления ситуации, с тестами(вдруг устарели?) или с кодом. Так называемый подход красно-зеленого тестирования. 
* Независимыми от окружения
* Легко поддерживаемыми и изменяемыми
> Как часто вы пишете код, который никогда после не меняете? Так и с тестами, рефакторинг кода может повлечь за собой необходимость поправить и тесты его проверяющие.
* Оформление теста должно быть своеобразной документацией для тестируемого участка
> Документация не часто отображает самые последние изменения. Зато тесты - почти всегда (заисключением случаев, когда они реализованы не совсем корректно: существенные изменения тестируемого участка, не привели к не прохождению теста). И поскольку разработчик напрямую работает с кодом, то и не до конца понятные участки будет удобно уточнить тоже в коде, а не в документации.
* Достоверными
> Не делайте так, что тестируемая функция считает сумму двух чисел, в то время как в тесте проверяется умножение. 
* По unit тестам должно быть четко понятно, что именно в коде пошло не так
> Это является существенным отличием от остальных видов тестов : unit предназначены для локализации ошибок 

## Реализация unit тестов в Node.js.

Для Node.js можно найти различные фреймворки и библиотеки, позволяющих автоматизировать тестирование. Остановимся на следующей их   комбинации: Mocha, Chai, Sinon.

###### Итак, необходимые средства для работы:

| Объект | Для чего используется |
| :---:       |     :---      |
|[Mocha](https://mochajs.org)|Фреймворк для асинхронного тестирования, включает в себя describe и it. *Для создания окружения тестирования* |
|[Chai](http://chaijs.com)|Библиотека, предоставляющая функции проверки: *should, expect, assert*|
|[Sinon](http://sinonjs.org)|Библиотека, которая предоставляет функции для эмуляции и проверки требуемого поведения: *spy, stub и mock* |

Ну и разумеется тестируемый код. Для примера это будут следующие функции: суммы двух комплексных чисел и изменение email пользователя:

```
Комплексное число представим в виде объекта:
{
	re: number,
	im: number
}

function complexSum(a, b){
  return {re: a.re + b.re, im: a.im + b.im}
  }
```

и с обращением к стороннему ресурсу - к базе данных

```
function changeUser(userId, changedEmail){
  return DB_name.getById(userId)
  	.then(user => {
		if(ifUserExists(user)){
			user.email = changedEmail;
			return Promise.resolve(user);
			}
		else {
			return Promise.reject({code: 404, message: `The user with '${userId}' is not found`});
		}
	})
```
Реализация теста для функции сложения может быть представлена в таком виде:
```
describe("Summarize complex numbers", () => () {

  it("Summarize complex numbers", () => { 
    let complexNumber = {re:2, im:3};
    assert.equal(sumComplex(complexNumber, 0), complexNumber);
    assert.equal(sumComplex(complexNumber, {re:1, im:4}), {re:3, im:7});
  });

});
```

*Что же здесь проиходит?:*

	1. describe(название, () => { ... }) //Возможно также и с помощью обычных функций function() { ... } 
	Задает, что именно описывается - помогает формировать логические блоки it
	2. it(название, () => { ... })
	Название блока it используется для описания того, что должна собственно сделать функция в том или ином случае
	3. assert.equal(...) - в этом месте и происходит проверка, в результате которой тест либо проходит, либо нет 
Итак, в первом assert проверяем сумму двух комплексных чисел, во втором - сумму с нулем. Не беспокойтесь, так запланировано, что при запуске теста мы получим ошибку. Ноль - не объект. И оказывается, что в функции не предусмотрена возможность сложения с обычным числом. Вы можете самостоятельно поправить функцию, чтобы она проходила тест. 
>Но что еще неправильного в примере теста вы видите?
Если мы оставим только второй assert, то так и не узнаем, завершиться ли он успешно. Поэтому и стоит не забывать важную мысль: `один тест должен проверять что-то одно`. И в случае, если вы хотите проверить работу сервиса на разном наборе параметров одновременно, стоит их разделить на отдельные блоки. 

```
Предпочтительный вариант разделения на отдельные блоки it:

describe("Summarize complex numbers", function() {

  it("Summarize complex number with zero", function() {
    let complexNumber = {re:2, im:3};
    assert.equal(sumComplex(complexNumber, 0), complexNumber);
  });
  
  it("Summarize 2 complex numbers", function() {
    let complexNumber = {re:2, im:3};
    assert.equal(sumComplex(complexNumber, {re:1, im:4}), {re:3, im:7});
  });
});
```
Это была простая функция. Она не зависела ни от каких внешних сервисов или ресурсов: базы данных, http взаимодействия, чтения из файловой системы. Но вот вторая функция changeUser - запрашивает реальный объект. Получается, надо его завести в базе данных. Ну, это не страшно думаете вы. А если его потом надо еще и сохранить изменённого, или удалить? А другой тест обращался к старому варианту данных, для него тогда надо заводить собственный экземпляр? 
Другой пример: для теста вам нужно получить список социальных друзей и обработать его соответственным образом. Вы будет посылать реальный токен? Но он имеет свое время жизни (это вообще в случае, если он не одноразовый). Каждый раз генерировать его руками? 

Что касается функции changeUser, то она обращается к базе данных. Предполагаем, что к локальной. Протестируем ее, и на ее примере увидим, как решать описанную выше проблему. 
```
Первый вариант теста с обращением к реальным данным:
describe("#changeUser", () => () {
  it("Should change email", () => { 
  	let user = {
		id: 12345,
		nickname: "nickname",
		email: "email@email.com"
		};
		
	let changedUser = {
		id: 12345,
		nickname: "nickname",
		email: "new_email@email.com"
		};
		
	changeUser(12345, "new_email@email.com")
		.then(result => {
			assert.deepEqual(result, changedUser);
			});
  });
  
   it("Should retrun 404 if not found user by id", () => { 
	changeUser(62345, "new_email@email.com")
		.catch(err => {
			assert.deepEqual(err, {code: 404, message: `The user with '62345' is not found`});
			});
  });
});
```

<details>
  <summary>В данном примере можно заметить, что нарушается одна существенная характеристика unit тестов.</summary>
  <p>
Они не должны зависеть от окружения. Если второй разработчик попробует запустить, то первый блок it не пройдет, пока он не заведет нужный объект самостоятельно. Для одного теста - это не проблемно, а если их больше?
  </p></details>
</p></details>

*Как же этого избежать?*

Из перечисленных инструментов тестирования остался Sinon, у которого есть такие замечательные тестовые объекты: spy, stub и mock. 

### Stub

**Stub** (заглушка) — это функция с предварительно запрограммированным поведением. Она позволяет обертывать существующую функцию, и тогда исходная не вызывается.

В первом варианте теста для функции изменения пользователя это пригодится как нельзя кстати:
```
Второй вариант теста:
describe("#changeUser", () => () {

	let user = {
		id: 12345,
		nickname: "nickname",
		email: "email@email.com"
		};
		
	let changedUser = {
		id: 12345,
		nickname: "nickname",
		email: "new_email@email.com"
		};
		
  it("Should change email", () => { 
	let stub = sinon.stub(DB_name, "getById").returns(Promise.resolve(user));
	changeUser(12345, "new_email@email.com")
		.then(result => {
			assert.deepEqual(result, changedUser);
			});
  });
  
   it("Should retrun 404 if not found user by id", () => { 
  	let stub = sinon.stub(DB_name, "getById").returns(Promise.resolve(null));
	changeUser(62345, "new_email@email.com")
		.catch(err => {
			assert.deepEqual(err, {code: 404, message: `The user with '62345' is not found`});
			});
  });
});
```

Stub позволяют подменять и в более сложных случаях. Например, у вас происходит вызов двух одинаковых функций getById, но с разными параметрами. Если вы замените как на примере, то будет возвращаться *один и тот же* объект на разные входные параметры.
Чтобы этого избежать можно воспользоваться следующими вариантами

```
let stub = sinon.stub(name, "method_name");
stub.withArgs(12345).returns(Promise.resolve(user));
stub.withArgs(62345).returns(Promise.resolve(users_2));

или 

stub.onFirstCall().returns(Promise.resolve(user));
stub.onSecondCall().returns(Promise.resolve(users_2));
```

> Как видите, stubs очень полезны при тестировании изолированного кода, но стоит лишний раз убедиться, что  заглушка ведет себя аналогично реальному коду. Иначе тесты станут бессмысленными.

#### Не забудьте убрать за собой

Простейший пример. Вы замокали какую-нибудь функцию, и на 1234 она возвращает никнейм "katya". Но в другом месте, вам все-таки нужно вызвать исходную функцию. Как быть?
Не забывать вызывать `object.method.restore()` или `stub.restore()`.

Но в sinon можно найти и другое решение: **Sandbox**
```
let sandbox = sinon.sandbox.create();

  afterEach(done => { //специальные хуки, которые выполняются полсе(или перед - beforeEach) каждым блоком it
    sandbox.restore();
  });
  
И дальнейшее обращение к stub происходит в виде sandbox.stub(...)
```
### Mock

**Mocks** являются имитационным методами с предварительно запрограммированным ожиданием и поведением. Тест будет считаться пройденным, если он выполнится так, как ожидался.

Для нашего примера может быть следующий вариант:

```
Мы хотим, чтобы метод getById вызвался ровно один раз и с определенными аргументами:
it("Should call getByIdmethod only one time if not found user by id", () => { 
	let mock = sinon.mock(DB_name); //задаем имя объекта
	changeUser(62345, "new_email@email.com")
		.catch(err => {
			mock.expects("getById").once().withArgs(62345);
			mock.verify();
			});
  });
```

Функций позволяющих проверить ожидаемое поведение - достаточное количество для различных ситуаций, более подробную их вариативность можно найти в официальной документации.

### Spy

**Spy** — это функция, которая позволяет проверить аргументы, возвращаемое значение, выданные ошибки (если таковые были) для всех вызовов. Используется не так часто, как уже перечисленные заглушки, поэтому не будем останавливаться подробно.

Для нашей функции changeUser можно реализовать следующую проверку:

```
Мы хотим, чтобы метод getById вызвался ровно один раз и с определенными аргументами:
it("Should call getById method only one time if not found user by id", () => { 
	let spy = sinon.spy(DB_name, "getById"); //задаем имя объекта и метода
	changeUser(62345, "new_email@email.com")
		.catch(err => {
			assert(spy.withArgs(62345).calledOnce);
			});
  });
```

>Впрочем, если проверяемый участок кода связан с б*о*льшим количеством вызовов других функций - вы можете проверять и последовательно вызовов разных spy функций: spy.calledBefore(anotherSpy) или spy.calledAfter(anotherSpy); и тот факт, что между ними не было других spy: spy.calledImmediatelyBefore(anotherSpy) или spy.calledImmediatelyAfter(anotherSpy); на каком объекте вызвано и прочее.

### Общий алгоритм реализации тестов с помощью заглушек:
1. Инициализация - подготовка необходимых данных для теста. 
2. Выполнение проверяемой функции.
3. Проверка.
4. Освобождение ресурсов.

### Вы еще сомневаетесь в необходимости тестов? 

У вас до сих пор не возникло желание писать тесты? 

А даром предвиденья случайно не обладаете? 
Ваш код сам знает, когда ему изменятся при внесении нового функционала? 
Или быть может вы единственный, кто разрабатывает проект? Правда и в этом случае уже через пару-тройку месяцев вы не вспомните, почему в коде происходят те или иные действия. А если надо срочно внести изменения? Какого вероятность, что они не сломают что-нибудь в коде? 

Захотели покрыть тестами все-все  случаи?
Как бы оптимистично вы к этому не относились, все равно полное покрытие не дает гарантий, что нет багов. Скорее, что реализованный код работает так, вы это придумали.

Увы, тесты не панацея от всех проблем разработчиков, но как же они упрощают написание и *поддержание* кода!

### Основные выводы:

- Тесты нужны не для того, чтобы находить ошибки - тут уже не отбирайте последнее у тестировщиков, тесты - для проверки написанного вами только что функционала. 
- Тесты пишутся для обеспечения корректных изменений.
- Когда пишешь тест, ты смотришь на свой код под другим углом зрения и замечаешь лишние/недостающие моменты - это помогает улучшить структуру.
- Тесты - самая свежая документация.
- Они упрощают понимание логики проверяемого участка.
- Не все объекты, приходящие в систему, легко сконструировать. Но решение все же существует - нужные объекты можно замокать.