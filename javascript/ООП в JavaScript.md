
В JavaScript существует 4 способа создать объект:  
  

- Функция-контруктор (constructor function)
- Класс (class)
- Связывание объектов (object linking to other object, OLOO)
- Фабричная функция (factory function)

  
Какой метод следует использовать? Какой из них является лучшим?  
  
Для того, чтобы ответить на эти вопросы мы не только рассмотрим каждый подход в отдельности, но и сравним между собой классы и фабричные функции по следующим критериям: наследование, инкапсуляция, ключевое слово «this», обработчики событий.  
  
Давайте начнем с того, что такое объектно-ориентированное программирование (ООП).  
  

### Что такое ООП?

  
По сути, ООП — это способ написания кода, позволяющий создавать объекты с помощью одного объекта. В этом также заключается суть шаблона проектирования «Конструктор». Общий объект, обычно, называется планом, проектом или схемой (blueprint), а создаваемые с его помощью объекты — экземплярами (instances).  
  
Каждый экземпляр имеет как свойства, наследуемые от родителя, так и собственные свойства. Например, если у нас имеется проект Human (человек), мы можем создавать на его основе экземпляры с разными именами.  
  
Второй аспект ООП состоит в структурировании кода, когда у нас имеется несколько проектов разного уровня. Это называется наследованием (inheritance) или классификацией (созданием подклассов) (subclassing).  
  
Третий аспект ООП — инкапсуляция, когда мы скрываем детали реализации от посторонних, делая переменные и функции недоступными извне. В этом заключается суть шаблонов проектирования «Модуль» и «Фасад».  
  
Перейдем с способам создания объектов.  
  

### Способы создания объекта

  

###### Функция-конструктор

  
Конструкторами являются функции, в которых используется ключевое слово «this».  
  

```js
    function Human(firstName, lastName) {        
	    this.firstName = firstName        
	    this.lastName = lastName    
    }
```

  
this позволяет сохранять и получать доступ к уникальным значениям создаваемого экземпляра. Экземпляры создаются с помощью ключевого слова «new».  
  

```js
const chris = new Human('Chris', 'Coyier')
console.log(chris.firstName) // Chris
console.log(chris.lastName) // Coyier

const zell = new Human('Zell', 'Liew')
console.log(zell.firstName) // Zell
console.log(zell.lastName) // Liew
```

  

###### Класс

  
Классы являются абстракцией («синтаксическим сахаром») над функциями-конструкторами. Они облегчают задачу создания экземпляров.  
  

```js
class Human {        
	constructor(firstName, lastName) {            
		this.firstName = firstName            
		this.lastName = lastName        
	}    
}
```

  
Обратите внимание, что constructor содержит тот же код, что и функция-конструктор, приведенная выше. Мы должны это делать, чтобы инициализировать this. Мы может опустить constructor, если нам не требуется присваивать начальные значения.  
  
На первый взгляд, классы кажутся сложнее, чем конструкторы — приходится писать больше кода. Придержите лошадей и не делайте поспешных выводов. Классы — это круто. Чуть позже вы поймете почему.  
  
Экземпляры также создаются с помощью ключевого слова «new».  
  

```js
const chris = new Human('Chris', 'Coyier')

console.log(chris.firstName) // Chris
console.log(chris.lastName) // Coyier
```

  

###### Связывание объектов

  
Данный способ создания объектов был предложен Kyle Simpson. В данном подходе мы определяем проект как обычный объект. Затем с помощью метода (который, как правило, называется init, но это не обязательно, в отличие от constructor в классе) мы инициализируем экземпляр.  
  

```js
const Human = {    
	init(firstName, lastName) {        
		this.firstName = firstName        
		this.lastName = lastName    
	}
}
```

  
Для создания экземпляра используется Object.create. После создания экземпляра вызывается init.  
  

```js
const chris = Object.create(Human)
chris.init('Chris', 'Coyier')

console.log(chris.firstName) // Chris
console.log(chris.lastName) // Coyier
```

  
Код можно немного улучшить, если вернуть this в init.  
  

```js
const Human = {  
	init () {    
		// ...    
		return this  
	}
}

const chris = Object.create(Human).init('Chris', 'Coyier')
console.log(chris.firstName) // Chris
console.log(chris.lastName) // Coyier
```

  

###### Фабричная функция

  
Фабричная функция — это функция, возвращающая объект. Можно вернуть любой объект. Можно даже вернуть экземпляр класса или связывания объектов.  
  
Вот простой пример фабричной функции.  
  

```js
function Human(firstName, lastName) {    
	return {        
		firstName,        
		lastName    
	}
}
```

  
Для создания экземпляра нам не требуется ключевое слово «new». Мы просто вызываем функцию.  
  

```js
const chris = Human('Chris', 'Coyier')

console.log(chris.firstName) // Chris
console.log(chris.lastName) // Coyier
```

  
Теперь давайте рассмотрим способы добавления свойств и методов.  
  

### Определение свойств и методов

  
Методы — это функции, объявленные в качестве свойств объекта.  
  

```js
const someObject = {        
	someMethod () { 
		/* ... */ 
	}    
}
```

  
В ООП существует два способа определения свойств и методов:  
  

- В экземпляре
- В прототипе

  

###### Определение свойств и методов в конструкторе

  
Для определения свойства в экземпляре необходимо добавить его в функцию-конструктор. Убедитесь, что добавляете свойство к this.  
  

```js
function Human (firstName, lastName) {  
	// Определяем свойства  
	this.firstName = firstName  
	this.lastname = lastName  
	
	// Определяем методы  
	this.sayHello = function () {    
		console.log(`Hello, I'm ${firstName}`)  
	}
}

	const chris = new Human('Chris', 'Coyier')
	console.log(chris)
```

  
![](https://habrastorage.org/r/w1560/webt/5c/ik/sn/5ciksn11exfeofob6qh7a7zgwwy.png)  
  
Методы, обычно, определяются в прототипе, поскольку это позволяет избежать создания функции для каждого экземпляра, т.е. позволяет всем экземплярам использовать одну функцию (такую функцию называют общей или распределенной).  
  
Для добавления свойства в прототип используют prototype.  
  

```js
function Human (firstName, lastName) {  
	this.firstName = firstName  
	this.lastname = lastName
}
	
// Определяем метод в прототипе
Human.prototype.sayHello = function () {  
	console.log(`Hello, I'm ${this.firstName}`)
}
```

  
![](https://habrastorage.org/r/w1560/webt/82/ym/eu/82ymeuwgjqgwdll9h_tpxmerf98.png)  
  
Создание нескольких методов может быть утомительным.  
  

```js
// Определение методов в прототипе
Human.prototype.method1 = function () { 
	/*...*/ 
}

Human.prototype.method2 = function () { 
	/*...*/ 
}

Human.prototype.method3 = function () { 
	/*...*/ 
}
```

  
Можно облегчить себе жизнь с помощью Object.assign.  
  

```js
Object.assign(Human.prototype, {  
	method1 () { /*...*/ },  
	method2 () { /*...*/ },  
	method3 () { /*...*/ }
})
```

  

###### Определение свойств и методов в классе

  
Свойства экземпляра можно определить в constructor.  
  

```js
class Human {  
	constructor (firstName, lastName) {      
		this.firstName = firstName      
		this.lastName = lastName  
	    
		this.sayHello = function () {        
			console.log(`Hello, I'm ${firstName}`)      
		}  
	}
}
```

  
![](https://habrastorage.org/r/w1560/webt/gd/0x/z7/gd0xz7ckczw6u-ntrf-mbwsu0li.png)  
  
Свойства прототипа определяются после constructor в виде обычной функции.  
  

```js
class Human (firstName, lastName) {  
	constructor (firstName, lastName) { /* ... */ }  
	
	sayHello () {    
	console.log(`Hello, I'm ${this.firstName}`)  
	}
}
```

  
![](https://habrastorage.org/r/w1560/webt/ia/5r/io/ia5rioc_tereglizgfthqec7eda.png)  
  
Создание нескольких методов в классе проще, чем в конструкторе. Для этого нам не нужен Object.assign. Мы просто добавляем другие функции.  
  

```js
class Human (firstName, lastName) {  
	constructor (firstName, lastName) { /* ... */ }  
	
	method1 () { /*...*/ }  
	method2 () { /*...*/ }  
	method3 () { /*...*/ }
}
```

  

###### Определение свойств и методов при связывании объектов

  
Для определения свойств экземпляра мы добавляем свойство к this.  
  

```js
const Human = {  
	init (firstName, lastName) {    
		this.firstName = firstName    
		this.lastName = lastName    
		
		this.sayHello = function () {      
			console.log(`Hello, I'm ${firstName}`)    
		}    
		
		return this  
	}
}
	
const chris = Object.create(Human).init('Chris', 'Coyier')
console.log(chris)
```

  
![](https://habrastorage.org/r/w1560/webt/j0/te/yz/j0teyzmenbfzzcbzzy35-s3wvpe.png)  
  
Метод прототипа определяется как обычный объект.  
  

```js
const Human = {  
	init () { /*...*/ },  
	
	sayHello () {    
		console.log(`Hello, I'm ${this.firstName}`)  
	}
}
```

  
![](https://habrastorage.org/r/w1560/webt/u3/uo/na/u3uonarzmunfw_szdfs5nxofwmm.png)  
  

###### Определение свойств и методов в фабричных функциях (ФФ)

  
Свойства и методы могут быть включены в состав возвращаемого объекта.  
  

```js
function Human (firstName, lastName) {  
	return {    
		firstName,    
		lastName,    
		sayHello () {      
			console.log(`Hello, I'm ${firstName}`)    
		}  
	}
}
```

  
![](https://habrastorage.org/r/w1560/webt/-v/9z/gu/-v9zguadqcfh0yxwvwkhhhdd1bo.png)  
  
При использовании ФФ нельзя определять свойства прототипа. Если вам нужны такие свойства, можно вернуть экземпляр класса, конструктора или связывания объектов (но это не имеет смысла).  
  

```js
// Не делайте этого
function createHuman (...args) {  
	return new Human(...args)
}
```

  

### Где определять свойства и методы

  
Где следует определять свойства и методы? В экземпляре или в прототипе?  
  
Многие считают, что для этого лучше использовать прототипы.  
  
Однако на самом деле это не имеет особого значения.  
  
При определении свойств и методов в экземпляре, каждый экземпляр будет расходовать больше памяти. При определении методов в прототипах, память будет расходоваться меньше, но незначительно. Учитывая мощность современных компьютеров, эта разница является несущественной. Поэтому делайте так, как вам удобней, но все же предпочитайте прототипы.  
  
Например, при использовании классов или связывания объектов, лучше использовать прототипы, поскольку в этом случае код легче писать. В случае ФФ прототипы использовать нельзя. Можно определять только свойства экземпляров.  
  
Прим. пер.: позволю себе не согласиться с автором. Вопрос использования прототипов вместо экземпляров при определении свойств и методов — это не только вопрос расходования памяти, но, прежде всего, вопрос назначения определяемого свойства или метода. Если свойство или метод должны быть уникальными для каждого экземпляра, тогда они должны определяться в экземпляре. Если свойство или метод должны быть одинаковыми (общими) для всех экземпляров, тогда они должны определяться в прототипе. В последнем случае при необходимости внесения изменений в свойство или метод достаточно будет внести их в прототип, в отличие от свойств и методов экземпляров, которые корректируются индивидуально.  
  

### Предварительный вывод

  
На основе изученного материала можно сделать несколько выводов. Это мое личное мнение.  
  

- Классы лучше конструкторов, поскольку в них легче определять несколько методов.
- Связывание объектов кажется странным из-за необходимости использовать Object.create. Я постоянно забывал об этом при изучении данного подхода. Для меня это было достаточной причиной отказаться от его дальнейшего использования.
- Классы и ФФ использовать проще всего. Проблема состоит в том, что в ФФ нельзя использовать прототипы. Но, как я отметил ранее, это не имеет особого значения.

  
Далее мы будем сравнивать между собой классы и ФФ как два лучших способа создания объектов в JavaScript.  
  

### Классы против ФФ — Наследование

  
Прежде чем переходить к сравнению классов и ФФ, необходимо познакомиться с тремя концепциями, лежащими в основе ООП:  
  

- наследование
- инкапсуляция
- this

  
Начнем с наследования.  
  

###### Что такое наследование?

  
В JavaScript наследование означает передачу свойств от родительского объекта к дочернему, т.е. от проекта к экземпляру.  
  
Это происходит двумя способами:  
  

- с помощью инициализации экземпляра
- с помощью цепочки прототипов

  
Во втором случае имеет место расширение родительского проекта с помощью дочернего проекта. Это называется созданием подклассов, но некоторые также называют это наследованием.  
  

###### Понимание создания подклассов

  
Создание подклассов — это когда дочерний проект расширяет родительский.  
  
Рассмотрим это на примере классов.  
  

###### Создание подклассов с помощью класса

  
Для расширения родительского класса используется ключевое слово «extends».  
  

```js
class Child extends Parent {    
	// ...
}
```

  
Например, давайте создадим класс «Developer», расширяющий класс «Human».  
  

```js
// класс Human
class Human {  
	constructor (firstName, lastName) {    
		this.firstName = firstName    
		this.lastName = lastName  
	}  
		
	sayHello () {    
		console.log(`Hello, I'm ${this.firstName}`)  
	}
}
```

  
Класс «Developer» будет расширять Human следующим образом:  
  

```js
class Developer extends Human {  
	constructor(firstName, lastName) {    
		super(firstName, lastName)  
	}  
	  
	// ...
}
```

  
Ключевое слово «super» вызывает constructor класса «Human». Если вам это не нужно, super можно опустить.  
  

```js
class Developer extends Human {  
	// ...
}
```

  
Допустим, Developer умеет писать код (кто бы мог подумать). Добавим ему соответствующий метод.  
  

```js
class Developer extends Human {  
	code (thing) {    
		console.log(`${this.firstName} coded ${thing}`)  
	}
}
```

  
Вот пример экземпляра класса «Developer».  
  

```js
const chris = new Developer('Chris', 'Coyier')
console.log(chris)
```

  
![](https://habrastorage.org/r/w1560/webt/rb/w_/62/rbw_62_mr_pioocghg5vihha6k4.png)  
  

###### Создание подклассов с помощью ФФ

  
Для создания подклассов с помощью ФФ необходимо выполнить 4 действия:  
  

- создать новую ФФ
- создать экземпляр родительского проекта
- создать копию этого экземпляра
- добавить в эту копию свойства и методы

  
Данный процесс выглядит так.  
  

```js
function Subclass (...args) {  
	const instance = ParentClass(...args)  
	
	return Object.assign({}, instance, {    
		// Свойства и методы  
	})
}
```

  
Создадим подкласс «Developer». Вот как выглядит ФФ «Human».  
  

```js
function Human (firstName, lastName) {  
	return {    
		firstName,    
		lastName,    
		sayHello () {      
			console.log(`Hello, I'm ${firstName}`)    
		}  
	}
}
```

  
Создаем Developer.  
  

```js
function Developer (firstName, lastName) {  
	const human = Human(firstName, lastName)  
	
	return Object.assign({}, human, {    
		// Свойства и методы  
	})
}
```

  
Добавляем ему метод «code».  
  

```js
function Developer (firstName, lastName) {  
	const human = Human(firstName, lastName)  
	
	return Object.assign({}, human, {    
		code (thing) {      
			console.log(`${this.firstName} coded ${thing}`)    
		}  
	})
}
```

  
Создаем экземпляр Developer.  
  

```js
const chris = Developer('Chris', 'Coyier')
console.log(chris)
```

  
![](https://habrastorage.org/r/w1560/webt/s2/lt/4k/s2lt4kzptmnrm-wzh5l6pmpqzcq.png)  
  

###### Перезапись родительского метода

  
Иногда возникает необходимость перезаписать родительский метод внутри подкласса. Это можно сделать следующим образом:  
  

- создать метод с тем же именем
- вызвать родительский метод (опционально)
- создать новый метод в подклассе

  
Данный процесс выглядит так.  
  

```js
class Developer extends Human {  
	sayHello () {    
		// Вызываем родительский метод    
		super.sayHello()    

		// Создаем новый метод   
		console.log(`I'm a developer.`)  
	 }
 }
 
 const chris = new Developer('Chris', 'Coyier')
 chris.sayHello()
```

  
![](https://habrastorage.org/r/w1560/webt/s8/ez/h8/s8ezh8zmjdnel7orhqg28htrzzq.png)  
  
Тот же процесс с использованием ФФ.  
  

```js
function Developer (firstName, lastName) {  
	const human = Human(firstName, lastName)  
	
	return Object.assign({}, human, {      
		sayHello () {        
			// Вызываем родительский метод        
			human.sayHello()        
			
			// Создаем новый метод        
			console.log(`I'm a developer.`)      
		}  
	})
}

const chris = new Developer('Chris', 'Coyier')
chris.sayHello()
```

  
![](https://habrastorage.org/r/w1560/webt/s8/ez/h8/s8ezh8zmjdnel7orhqg28htrzzq.png)  
  

###### Наследование против композиции

  
Разговор о наследовании редко обходится без упоминания композиции. Эксперты вроде Eric Elliot считают, что всегда, когда это возможно, следует использовать композицию.  
  
Что же такое композиция?  
  

###### Понимание композиции

  
По сути, композиция — это объединение нескольких вещей в одну. Наиболее распространенным и самым простым способом объединения объектов является использование Object.assign.  
  

```js
const one = { one: 'one' }
const two = { two: 'two' }
const combined = Object.assign({}, one, two)
```

  
Композицию легче всего объяснить на примере. Допустим, у нас имеется два подкласса, Developer и Designer. Дизайнеры умеют разрабатывать дизайн, а разработчики — писать код. Оба наследуют от класса «Human».  
  

```js
class Human {  
	constructor(firstName, lastName) {    
		this.firstName = firstName    
		this.lastName = lastName  
	}  
		
	sayHello () {    
		console.log(`Hello, I'm ${this.firstName}`)  
	}
}
	
class Designer extends Human {  
	design (thing) {    
		console.log(`${this.firstName} designed ${thing}`)  
	}
}

class Developer extends Designer {  
	code (thing) {    
		console.log(`${this.firstName} coded ${thing}`)  
	}
}
```

  
Теперь предположим, что мы хотим создать третий подкласс. Этот подкласс должен быть смесью дизайнера и разработчика — он должен уметь как разрабатывать дизайн, так и писать код. Назовем его DesignerDeveloper (или, если угодно, DeveloperDesigner).  
  
Как нам его создать?  
  
Мы не может одновременно расширить классы «Designer» и «Developer». Это невозможно, поскольку мы не можем решить, какие свойства должны быть первыми. Это называется [проблемой ромба (ромбовидным наследованием)](https://ru.wikipedia.org/wiki/%D0%A0%D0%BE%D0%BC%D0%B1%D0%BE%D0%B2%D0%B8%D0%B4%D0%BD%D0%BE%D0%B5_%D0%BD%D0%B0%D1%81%D0%BB%D0%B5%D0%B4%D0%BE%D0%B2%D0%B0%D0%BD%D0%B8%D0%B5).  
  
![](https://habrastorage.org/r/w1560/webt/ar/ax/0j/arax0jxftzbfxdnb-sqifnl5bjg.png)  
  
Проблема ромба может быть решена с помощью Object.assign, если мы отдадим одному объекту приоритет над другим. Однако, в JavaScript не поддерживается множественное наследование.  
  

```js
// Не работает
class DesignerDeveloper extends Developer, Designer {  
	// ...
}
```

  
Здесь нам пригодится композиция.  
  
Данный подход утверждает следующее: вместо создания подкласса «DesignerDeveloper», создайте объект, содержащий навыки, которые можно включать в тот или иной подкласс по необходимости.  
  
Реализация этого подхода приводит к следующему.  
  

```js
const skills = {    
	code (thing) { /* ... */ },    
	design (thing) { /* ... */ },    
	sayHello () { /* ... */ }
}
```

  
Нам больше не нужен класс «Human», ведь мы можем создать три разных класса с помощью указанного объекта.  
  
Вот код для DesignerDeveloper.  
  

```js
class DesignerDeveloper {  
	constructor (firstName, lastName) {    
		this.firstName = firstName    
		this.lastName = lastName  
		  
		Object.assign(this, {      
			code: skills.code,      
			design: skills.design,      
			sayHello: skills.sayHello    
		})  
	}
}

const chris = new DesignerDeveloper('Chris', 'Coyier')
console.log(chris)
```

  
![](https://habrastorage.org/r/w1560/webt/s7/dh/6u/s7dh6ukcwi4duhgiuqzlh1twfio.png)  
  
Мы можем сделать тоже самое для Designer и Developer.  
  

```js
class Designer {  
	constructor (firstName, lastName) {    
		this.firstName = firstName    
		this.lastName = lastName    
		
		Object.assign(this, {      
			design: skills.design,      
			sayHello: skills.sayHello    
		})  
	}
}

class Developer {  
	constructor (firstName, lastName) {    
		this.firstName = firstName    
		this.lastName = lastName    
		
		Object.assign(this, {      
			code: skills.code,      
			sayHello: skills.sayHello    
		})  
	}
}
```

  
Вы заметили, что мы создаем методы в экземпляре? Это лишь один из возможных вариантов. Мы также можем поместить методы в прототип, но я нахожу это лишним (при таком подходе кажется, что мы вернулись к конструкторам).  
  

```js
class DesignerDeveloper {  
	constructor (firstName, lastName) {    
		this.firstName = firstName    
		this.lastName = lastName  
	}
}

Object.assign(DesignerDeveloper.prototype, {  
	code: skills.code,  
	design: skills.design,  
	sayHello: skills.sayHello
})
```

  
![](https://habrastorage.org/r/w1560/webt/_n/hu/r4/_nhur452mh1ai-xdkhzncf2kpla.png)  
  
Используйте тот подход, который считаете самым подходящим. Результат будет одинаковым.  
  

###### Композиция с помощью ФФ

  
Композиция с помощью ФФ заключается в добавлении распределенных методов в возвращаемый объект.  
  

```js
function DesignerDeveloper (firstName, lastName) {  
	return {    
		firstName,    
		lastName,    
		code: skills.code,    
		design: skills.design,    
		sayHello: skills.sayHello  
	}
}
```

  
![](https://habrastorage.org/r/w1560/webt/_k/rb/hb/_krbhbnc4x_negfjyzcyo-ebatc.png)  
  

###### Наследование и композиция

  
Никто не говорил, что мы не можем использовать наследование и композицию одновременно.  
  
Возвращаясь к примеру с Designer, Developer и DesignerDeveloper, нельзя не отметить, что они также являются людьми. Поэтому они могут расширять класс «Human».  
  
Вот пример наследование и композиции с использованием синтаксиса классов.  
  

```js
class Human {  
	constructor (firstName, lastName) {    
		this.firstName = firstName    
		this.lastName = lastName  
	}  
	
	sayHello () {    
		console.log(`Hello, I'm ${this.firstName}`)  
	}
}

class DesignerDeveloper extends Human {}

Object.assign(DesignerDeveloper.prototype, {  
	code: skills.code,  
	design: skills.design
})
```

  
![](https://habrastorage.org/r/w1560/webt/y0/hl/xm/y0hlxm2euhqrc8anh8g_w8solxm.png)  
  
А вот тоже самое с использованием ФФ.  
  

```js
function Human (firstName, lastName) {  
	return {    
		firstName,    
		lastName,    
		sayHello () {      
			console.log(`Hello, I'm ${this.firstName}`)    
		} 
	}
}

function DesignerDeveloper (firstName, lastName) {  
	const human = Human(firstName, lastName)  
	
	return Object.assign({}, human, {    
		code: skills.code,    
		design: skills.design  
	})
}
```

  
![](https://habrastorage.org/r/w1560/webt/mo/gc/pe/mogcpex8_d58s89e7rphuu2rvke.png)  
  

###### Подклассы в реальном мире

  
Несмотря на то, что многие эксперты утверждают, что композиция по сравнению с подклассами является более гибкой (и поэтому более полезной), подклассы нельзя сбрасывать со счетов. Многие вещи, с которыми мы имеем дело, основаны на этой стратегии.  
  
Например: событие «click» является MouseEvent (событием мыши). MouseEvent — это подкласс UIEvent (событие пользовательского интерфейса), который, в свою очередь, является подклассом Event (событие).  
  
![](https://habrastorage.org/r/w1560/webt/xx/w_/lm/xxw_lm5wntfjy2dqadvf5fydvjo.png)  
  
Другой пример: HTML Elements (элементы) являются подклассами Nodes (узлов). Поэтому они могут использовать все свойства и методы узлов.  
  
![](https://habrastorage.org/r/w1560/webt/lm/mz/87/lmmz873cfa_oogckfcokjkuqny8.png)  
  

###### Предварительный вывод относительно наследования

  
Наследование и композиция могут использоваться как в классах, так и в ФФ. В ФФ композиция выглядит «чище», но это незначительное преимущество перед классами.  
  
Продолжим сравнение.  
  

### Классы против ФФ — Инкапсуляция

  
По сути, инкапсуляция — это скрытие одной вещи внутри другой, из-за чего внутренняя сущность становится недоступной снаружи.  
  
В JavaScript скрываемыми сущностями являются переменные и функции, которые доступны только в текущем контексте. В данном случае контекст — это тоже самое, что область видимости.  
  

###### Простая инкапсуляция

  
Простейшей формой инкапсуляции является блок кода.  
  

```js
{  
	// Переменные, объявленные здесь, будут иметь блочную область видимости
}
```

  
Находясь в блоке, можно получить доступ к переменной, объявленной за его пределами.  
  

```js
const food = 'Hamburger'
{  
	console.log(food)
}
```

  
![](https://habrastorage.org/r/w1560/webt/6w/fp/g8/6wfpg8hz_mhxs9o40e6ekomlrge.png)  
  
Но не наоборот.  
  
```js
{  
	const food = 'Hamburger'
}
console.log(food)
```

  
![](https://habrastorage.org/r/w1560/webt/zl/_t/py/zl_tpyghfxxxumza4_8zykqrjgg.png)  
  
Обратите внимание, что переменные, объявленные с помощью ключевого слова «var», имеют глобальную или фукнциональную область видимости. Старайтесь не использовать var для объявления переменных.  
  

###### Инкапсуляция с помощью функции

  
Функциональная область видимости похожа на блочную. Переменные, объявленные в функции, доступны только внутри нее. Это относится ко всем переменным, даже объявленным с помощью var.  
  

```js
function sayFood () {  
	const food = 'Hamburger'
}

sayFood()
console.log(food)
```

  
![](https://habrastorage.org/r/w1560/webt/im/xl/mf/imxlmfe_blprhtj70vqyhlymrnw.png)  
  
Когда же мы находимся внутри функции, то имеем доступ к переменным, объявленным за ее пределами.  
  

```js
const food = 'Hamburger'

function sayFood () {  
	console.log(food)
}

sayFood()
```

  
![](https://habrastorage.org/r/w1560/webt/6w/fp/g8/6wfpg8hz_mhxs9o40e6ekomlrge.png)  
  
Функции могут возвращать значения, которые могут быть использованы впоследствии за пределами функции.  
  

```js
function sayFood () {  
	return 'Hamburger'
}

console.log(sayFood())
```

  
![](https://habrastorage.org/r/w1560/webt/6w/fp/g8/6wfpg8hz_mhxs9o40e6ekomlrge.png)  
  

###### Замыкание

  
Замыкание — это продвинутая форма инкапсуляции. Это просто функция внутри другой функции.  
  

```js
// Пример замыкания
function outsideFunction () {  
	function insideFunction () { 
		/* ... */ 
	}
}
```

  
  
Переменные, объявленные в outsideFunction, могут использоваться в insideFunction.  
  

```js
function outsideFunction () {  
	const food = 'Hamburger'  
	console.log('Called outside') 
	 
	return function insideFunction () {    
		console.log('Called inside')    
		console.log(food)  }
	}
	// Вызываем outsideFunction, которая возвращает insideFunction
	// Сохраняем insideFunction в переменной "fn"
	const fn = outsideFunction()
```

  
![](https://habrastorage.org/r/w1560/webt/mj/8k/f_/mj8kf_fkmxiidc74okzy-vsgaxg.png)  
  

###### Инкапсуляция и ООП

  
При создании объектов мы хотим, чтобы одни свойства были открытыми (публичными), а другие закрытыми (частными или приватными).  
  
Рассмотрим пример. Скажем, у нас имеется проект «Car». При создании нового экземпляра мы добавляем ему свойство «fuel» (топливо) со значением 50.  
  

```js
class Car {  
	constructor () {    
		this.fuel = 50  
	}
}
```

  
  
Пользователи могут использовать это свойство для определения количества оставшегося топлива.  
  

```js
const car = new Car()
console.log(car.fuel) // 50
```

  
  
Пользователи также могут самостоятельно устанавливать количество топлива.  
  

```js
const car = new Car()
car.fuel = 3000
console.log(car.fuel) // 3000
```

  
Давайте добавим условие, согласно которому бак автомобиля вмещает максимум 100 литров топлива. Мы не хотим, чтобы пользователи имели возможность самостоятельно устанавливать количество топлива, потому что они могут сломать машину.  
  
Существует два способа это сделать:  
  

- использование частных свойств по соглашению
- использование настоящих частных полей

  

###### Частные свойства по соглашению

  
В JavaScript частные переменные и свойства, обычно, обозначаются с помощью нижнего подчеркивания.  
  

```js
class Car {  
	constructor () {    
		// Отмечаем свойство "fuel" как частное, которое не должно использоваться за пределами класса    
		this._fuel = 50  
	}
}
```

  
Как правило, мы создаем методы для управления частными свойствами.  
  

```js
class Car {  
	constructor () {    
		this._fuel = 50  
	}  
	
	getFuel () {    
		return this._fuel  
	}  
	
	setFuel (value) {    
		this._fuel = value    
		// Определяем вместимость бака    
		if (value > 100) this._fuel = 100  
	}
}
```

  
Для определения и установки количества топлива пользователи должны использовать методы «getFuel» и «setFuel», соответственно.  
  

```js
const car = new Car()
console.log(car.getFuel()) // 50

car.setFuel(3000)
console.log(car.getFuel()) // 100
```

  
Но переменная "_fuel" в действительности не является частной. Она доступна извне.  
  

```js
const car = new Car()
console.log(car.getFuel()) // 50

car._fuel = 3000
console.log(car.getFuel()) // 3000
```

  
Для ограничения доступа к переменным следует использовать настоящие частные поля.  
  

###### По-настоящему частные поля

  
Поля — это термин, объединяющий переменные, свойства и методы.  
  

###### Частные поля классов

  
Классы позволяют создавать частные переменные с помощью префикса "#".  
  

```js
class Car {  
	constructor () {    
		this.#fuel = 50  
	}
}
```

  
К сожалению, данный префикс нельзя использовать в конструкторе.  
  
![](https://habrastorage.org/r/w1560/webt/s7/bi/bv/s7bibvildoohezbleukmzuzqbmk.png)  
  
Частные переменные должны определяться вне конструктора.  
  

```js
class Car {  
	// Определяем частную переменную  #fuel  
	constructor () {    
		// Используем ее    
		this.#fuel = 50  
	}
}
```

  
В данной случае мы можем инициализировать переменную при определении.  
  

```js
class Car {  
	#fuel = 50
}
```

  
Теперь переменная "#fuel" доступна только внутри класса. Попытка получить к ней доступ за пределами класса приведет к возникновению ошибки.  
  

```js
const car = new Car()
console.log(car.#fuel)
```

  
![](https://habrastorage.org/r/w1560/webt/s7/bi/bv/s7bibvildoohezbleukmzuzqbmk.png)  
  
Для управления переменной нам нужны соответствующие методы.  
  

```js
class Car {  
	#fuel = 50  
	
	getFuel () {    
		return this.#fuel  
	}  
	
	setFuel (value) {    
		this.#fuel = value    
		if (value > 100) this.#fuel = 100  
	}
}

const car = new Car()
console.log(car.getFuel()) // 50

car.setFuel(3000)
console.log(car.getFuel()) // 100
```

  
Лично я предпочитаю использовать для этого геттеры и сеттеры. Я нахожу такой синтаксис более читаемым.  
  

```js
class Car {  
	#fuel = 50  
	
	get fuel () {    
		return this.#fuel  
	}  
	
	set fuel (value) {    
		this.#fuel = value   
		if (value > 100) this.#fuel = 100  
	}
}

const car = new Car()
console.log(car.fuel) // 50

car.fuel = 3000
console.log(car.fuel) // 100
```

  

###### Частные поля ФФ

  
ФФ создают частные поля автоматически. Нам нужно лишь объявить переменную. Пользователи не смогут получить доступ к этой переменной извне. Это происходит благодаря тому, что переменные имеют блочную (или функциональную) область видимости, т.е. являются инкапсулированными по умолчанию.  
  

```js
function Car () {  
	const fuel = 50
}

const car = new Car()
console.log(car.fuel) // undefined
console.log(fuel) // Error: "fuel" is not defined
```

  
Для управления частной переменной «fuel» также используются геттеры и сеттеры.  
  

```js
function Car () {  
	const fuel = 50  
	
	return {    
		get fuel () {      
			return fuel    
		},    
		
		set fuel (value) {      
			fuel = value      
			if (value > 100) fuel = 100    
		}  
	}
}

const car = new Car()
console.log(car.fuel) // 50

car.fuel = 3000
console.log(car.fuel) // 100
```

  
Вот так. Легко и просто!  
  

###### Предварительный вывод относительно инкапсуляции

  
Инкапсуляция с помощью ФФ проще и легче для восприятия. Она основана на области видимости, которая является важной частью JavaScript.  
  
Инкапсуляция с помощью классов предполагает использование префикса "#", что может быть несколько утомительным.  
  

### Классы против ФФ — this

  
this — главный аргумент против использования классов. Почему? Потому что значение this зависит от того, где и как this используется. Поведение this часто сбивает с толку не только новичков, но и опытных разработчиков.  
  
Однако, на самом деле концепция this не так уж и сложна. Всего существует 6 контекстов, в которых может использоваться this. Если вы разбираетесь в этих контекстах, у вас не должно возникать проблем с this.  
  
Названными контекстами являются:  
  

- глобальный контекст
- контекст создаваемого объекта
- контекст свойства или метода объекта
- простая функция
- стрелочная функция
- контекст обработчика событий

  
Но вернемся к статье. Давайте рассмотрим особенности использования this в классах и ФФ.  
  

###### Использование this в классах

  
При использовании в классе this указывает на создаваемый экземпляр (контекст свойства/метода). Вот почему экземпляр инициализируется в constructor.  
  

```js
class Human {  
	constructor (firstName, lastName) {    
		this.firstName = firstName    
		this.lastName = lastName    
		console.log(this)  
	}
}

const chris = new Human('Chris', 'Coyier')
```

  
![](https://habrastorage.org/r/w1560/webt/q7/y0/ma/q7y0mael2her44f-pprqsrhuucw.png)  
  

###### Использование this в функциях-конструкторах

  
При использовании this внутри функции и new для создания экземпляра, this будет указывать на экземпляр.  
  

```js
function Human (firstName, lastName) {  
	this.firstName = firstName  
	this.lastName = lastName  
	console.log(this)
}

const chris = new Human('Chris', 'Coyier')
```

  
![](https://habrastorage.org/r/w1560/webt/z-/40/tp/z-40tpaqgjlwpvbt5dawybxufyi.png)  
  
В отличии от ФК в ФФ this указывает на window (в контексте модуля this вообще имеет значение «undefined»).  
  

```js
// Для создания экземпляра не используется ключевое слово "new"
function Human (firstName, lastName) {  
	this.firstName = firstName  
	this.lastName = lastName  
	console.log(this)
}
	
const chris = Human('Chris', 'Coyier')
```

  
![](https://habrastorage.org/r/w1560/webt/pv/is/ed/pvisedt-n2-8zju9d4e0ukfxc0u.png)  
  
Таким образом, в ФФ не следует использовать this. В этом состоит одно из основных отличий между ФФ и ФК.  
  

###### Использование this в ФФ

  
Для того, чтобы иметь возможность использовать this в ФФ, необходимо создать контекст свойства/метода.  
  

```js
function Human (firstName, lastName) {  
	return {    
		firstName,    
		lastName,    
		sayThis () {      
			console.log(this)    
		}  
	}
}

const chris = Human('Chris', 'Coyier')
chris.sayThis()
```

  
![](https://habrastorage.org/r/w1560/webt/hx/6-/_4/hx6-_4x2guufyceemivc4idxtf8.png)  
  
Несмотря на то, что мы можем использовать this в ФФ, нам это не нужно. Мы можем создать переменную, указывающую на экземпляр. Такая переменная может использоваться вместо this.  
  

```js
function Human (firstName, lastName) {  
	const human = {    
		firstName,    
		lastName,    
	
		sayHello() {      
			console.log(`Hi, I'm ${human.firstName}`)    
		}  
	}  
	return human
}

const chris = Human('Chris', 'Coyier')
chris.sayHello()
```

  
human.firstName является более точным, нежели this.firstName, поскольку human явно указывает на экземпляр.  
  
На самом деле нам даже не нужно писать human.firstName. Мы можем ограничиться firstName, поскольку данная переменная имеет лексическую область видимости (это когда значение переменной берется из внешнего окружения).  
  

```js
function Human (firstName, lastName) {  
	const human = {    
		firstName,    
		lastName,    
	
		sayHello() {      
			console.log(`Hi, I'm ${firstName}`)    
		}  
	}  
	return human
}

const chris = Human('Chris', 'Coyier')
chris.sayHello()
```

  
![](https://habrastorage.org/r/w1560/webt/5r/nx/10/5rnx10f8sgov7zw-ngxhol_xjqa.png)  
  
Рассмотрим более сложный пример.  
  

### Сложный пример

  
Условия таковы: у нас имеется проект «Human» со свойствами «firstName» и «lastName» и методом «sayHello».  
  
Также у нас имеется проект «Developer», наследующий от Human. Разработчики умеют писать код, поэтому у них должен быть метод «code». Кроме того, они должны заявлять о своей принадлежности к касте разработчиков, поэтому нам необходимо перезаписать метод «sayHello».  
  
Реализуем указанную логику с помощью классов и ФФ.  
  

###### Классы

  
Создаем проект «Human».  
  

```js
class Human {  
	constructor (firstName, lastName) {    
		this.firstName = firstName    
		this.lastname = lastName  
	}  

	sayHello () {    
		console.log(`Hello, I'm ${this.firstName}`)  
	}
}
```

  
Создаем проект «Developer» с методом «code».  
  

```js
class Developer extends Human {  
	code (thing) {    
		console.log(`${this.firstName} coded ${thing}`)  
	}
}
```

  
Перезаписываем метод «sayHello».  
  

```js
class Developer extends Human {  
	code (thing) {    
		console.log(`${this.firstName} coded ${thing}`)  
	}  
	
	sayHello () {    
		super.sayHello()    
		console.log(`I'm a developer`)  
	}
}
```

  

###### ФФ (с использованием this)

  
Создаем проект «Human».  
  

```js
function Human () {  
	return {    
		firstName,    
		lastName,    
		
		sayHello () {      
			console.log(`Hello, I'm ${this.firstName}`)    
		}  
	}
}
```

  
Создаем проект «Developer» с методом «code».  
  

```js
function Developer (firstName, lastName) {  
	const human = Human(firstName, lastName)  
	
	return Object.assign({}, human, {    
		code (thing) {      
			console.log(`${this.firstName} coded ${thing}`)    
		}  
	})
}
```

  
Перезаписываем метод «sayHello».  
  

```js
function Developer (firstName, lastName) {  
	const human = Human(firstName, lastName)  
	
	return Object.assign({}, human, {    
		code (thing) {      
			console.log(`${this.firstName} coded ${thing}`)    
		},    
		
		sayHello () {      
			human.sayHello()      
			console.log('I\'m a developer')    
		}  
	})
}
```

  

###### ФФ (без this)

  
Поскольку firstName за счет лексической области видимости доступна напрямую мы можем опустить this.  
  

```js
function Human (firstName, lastName) {  
	return {    
		// ...    
		sayHello () {      
			console.log(`Hello, I'm ${firstName}`)    
		}  
	}
}

function Developer (firstName, lastName) {  
	// ...  
	return Object.assign({}, human, {    
		code (thing) {      
			console.log(`${firstName} coded ${thing}`)    
		},    
		sayHello () { 
			/* ... */ 
		}  
	})
}
```

  

###### Предварительный вывод относительно this

  
Простыми словами, классы требуют использования this, а ФФ нет. В данном случае я предпочитаю использовать ФФ, поскольку:  
  

- контекст this может меняться
- код, написанный с помощью ФФ, является более коротким и чистым (в том числе, благодаря автоматической инкапсуляции переменных)

  

### Классы против ФФ — Обработчики событий

  
Во многих статьях по ООП упускается из виду тот факт, что как фронденд-разработчики мы постоянно имеем дело с обработчиками событий. Именно они обеспечивают взаимодействие с пользователями.  
  
Поскольку обработчики событий изменяют контекст this, работа с ними в классах может быть проблематичной. В тоже время в ФФ таких проблем не возникает.  
  
Однако изменение контекста this не имеет значения, если мы знаем, как с этим справиться. Рассмотрим простой пример.  
  

###### Создание счетчика

  
Для создания счетчика воспользуемся полученными знаниями, включая частные переменные.  
  
Наш счетчик будет содержать две вещи:  
  

- сам счетчик
- кнопку для увеличения его значения

  
![](https://habrastorage.org/r/w1560/webt/wf/gx/if/wfgxifgrs2srwb08t1ny4qyjfhi.png)  
  
Вот как может выглядеть разметка:  
  

```js
<div class="counter"> 
	 <p>Count: <span>0</span></p>  
	 <button>Increase Count</button>
</div>
```

  

###### Создание счетчика с помощью класса

  
Для облегчения задачи попросим пользователя найти и передать разметку счетчика классу «Counter»:  
  

```js
class Counter {  
	constructor (counter) {    
		// ...  
	}
}

// Использование
const counter = new Counter(document.querySelector('.counter'))
```

  
В классе необходимо получить 2 элемента:  
  

- `<span>`, содержащий значение счетчика — нам нужно обновлять это значение при увеличении счетчика
- `<button>` — нам нужно добавить обработчик событий, вызываемых данным элементом

```js 
class Counter {  
	constructor (counter) {    
		this.countElement = counter.querySelector('span')    
		this.buttonElement = counter.querySelector('button')  
	}
}
```
  
Далее мы инициализируем переменную «count» текстовым содержимым countElement. Указанная переменная должна быть частной.  
  

```js
class Counter {  
	#count  
	constructor (counter) {    
		// ...    
		this.#count = parseInt(countElement.textContent)  
	}
}
```

  
При нажатии кнопки значение счетчика должно увеличиваться на 1. Реализуем это с помощью метода «increaseCount».  
  

```js
class Counter {  
	#count  
	constructor (counter) { 
		/* ... */ 
	}  
	
	increaseCount () {    
		this.#count = this.#count + 1  
	}
}
```

  
Теперь нам необходимо обновить DOM. Реализуем это с помощью метода «updateCount», вызываемого внутри increaseCount:  
  

```js
class Counter {  
	#count  
	constructor (counter) { 
		/* ... */ 
	}  
	
	increaseCount () {    
		this.#count = this.#count + 1    
		this.updateCount()  
	}  
	
	updateCount () {    
		this.countElement.textContent = this.#count  
	}
}
```

  
Осталось добавить обработчик событий.  
  

###### Добавление обработчика событий

  
Добавим обработчик к this.buttonElement. К сожалению, мы не можем использовать increaseCount в качестве функции обратного вызова. Это приведет к ошибке.  
  

```js
class Counter {  
	// ...  
	constructor (counter) {    
		// ...    
		this.buttonElement.addEventListener('click', this.increaseCount)  
	}  
	// Методы
}
```

  
![](https://habrastorage.org/webt/dt/wb/zj/dtwbzj5ypnsddwodlt4c4oi2b_8.gif)  
  
Исключение выбрасывается, потому что this указывает на buttonElement (контекст обработчика событий). В этом можно убедиться, если вывести значение this в консоль.  
  
![](https://habrastorage.org/webt/od/z0/4l/odz04lg-0uulik07bsoqbhg0lmi.gif)  
  
Значение this необходимо изменить таким образом, чтобы оно указывало на экземпляр. Это можно сделать двумя способами:  
  

- с помощью bind
- с помощью стрелочной фукнции

  
Большинство использует первый способ (однако второй проще).  
  

###### Добавление обработчика событий с помощью bind

  
bind возвращает новую функцию. В качестве первого аргумента ему передается объект, на который будет указывать this (к которому this будет привязан).  
  

```js
class Counter {  
	// ...  
	constructor (counter) {    
		// ...    
		this.buttonElement.addEventListener('click', 
		this.increaseCount.bind(this))  
	}  
	// ...
}
```

  
Это работает, но выглядит это не очень хорошо. К тому же bind — это продвинутая функция, с которой сложно иметь дело новичкам.  
  

###### Стрелочные функции

  
Стрелочные функции, помимо прочего, не имеют собственного this. Они заимствуют его из лексического (внешнего) окружения. Поэтому код счетчика может быть переписан следующим образом:  
  

```js
class Counter {  
	// ...  
	constructor (counter) {    
		// ...    
		this.buttonElement.addEventListener('click', () => {      
			this.increaseCount()    
		})  
	}  
	// Методы
}
```

  
Есть еще более простой способ. Мы можем создать increaseCount в виде стрелочной функции. В этом случае this будет указывать на экземпляр.  
  

```js
class Counter {  
	// ...  
	constructor (counter) {    
		// ...    
		this.buttonElement.addEventListener('click', this.increaseCount)  
	}  
	
	increaseCount = () => {    
		this.#count = this.#count + 1    
		this.updateCounter()  
	}  
	// ...
}
```

###### Создание счетчика с помощью ФФ

  
Начало аналогичное — мы просим пользователя найти и передать разметку счетчика:  
  

```js
function Counter (counter) {  
	// ...
}

const counter = Counter(document.querySelector('.counter'))
```

  
Получаем необходимые элементы, которые по умолчанию будут частными:  
  

```js
function Counter (counter) {  
	const countElement = counter.querySelector('span')  
	const buttonElement = counter.querySelector('button')
}
```

  
Инициализируем переменную «count»:  
  

```js
function Counter (counter) {  
	const countElement = counter.querySelector('span')  
	const buttonElement = counter.querySelector('button') 
	 
	let count = parseInt(countElement.textContext)
}
```

  
Значение счетчика будет увеличиваться с помощью метода «increaseCount». Вы можете использовать обычную функцию, но я предпочитаю другой подход:  
  

```js
function Counter (counter) {  
	// ...  
	const counter = {    
		increaseCount () {      
			count = count + 1    
		}  
	}
}
```

  
DOM будет обновляться с помощью метода «updateCount», который вызывается внутри increaseCount:  
  

```js
function Counter (counter) {  
	// ...  
	const counter = {    
		increaseCount () {      
			count = count + 1      
			counter.updateCount()    
		},    
		
		updateCount () {      
			increaseCount()    
		}  
	}
}
```

  
Обратите внимание, что вместо this.updateCount мы используем counter.updateCount.  
  

###### Добавление обрабочика событий

  
Мы можем добавить обработчик событий к buttonElement, используя counter.increaseCount в качестве колбэка.  
  
Это будет работать, поскольку мы не используем this, поэтому для нас не имеет значения то обстоятельство, что обработчик меняет контекст this.  
  

```js
function Counter (counterElement) {  
	// Переменные 
	 
	// Методы  
	const counter = { 
		/* ... */ 
	}  
	
	// Обработчики событий  
	buttonElement.addEventListener('click', counter.increaseCount)}
```

  

###### Первая особенность this

  
Вы можете использовать this в ФФ, но только в контексте метода.  
  
В следующем примере при вызове counter.increaseCount будет вызван counter.updateCount, поскольку this указывает на counter:  
  

```js
function Counter (counterElement) {  
	// Переменные  
	
	// Методы  
	const counter = {    
		increaseCount() {      
			count = count + 1      
			this.updateCount()    
		}  
	}  
	
	// Обработчики событий  
	buttonElement.addEventListener('click', counter.increaseCount)}
```

  
Однако обработчик событий работать не будет, потому что значение this изменилось. Данная проблема может быть решена с помощью bind, но не с помощью стрелочных функций.  
  

###### Вторая особенность this

  
При использовании синтаксиса ФФ, мы не можем создавать методы в виде стрелочных функций, потому что методы создаются в контексте функции, т.е. this будет указывать на window:  
  

```js
function Counter (counterElement) {  
	// ...  
	const counter = {    
		// Не делайте так    
		// Не работает, поскольку this указывает на window    
		increaseCount: () => {      
			count = count + 1      
			this.updateCount()    
		}  
	}  
	// ...
}
```

  
Поэтому при использовании ФФ я настоятельно рекомендую избегать использования this.  

###### Вердикт относительно обработчиков событий

  
Обработчики событий меняют значение this, поэтому использовать this следует крайне осторожно. При использовании классов советую создавать колбэки обработчиков событий в виде стрелочных функций. Тогда вам не придется прибегать к услугам bind.  
  
При использовании ФФ рекомендую вообще обходиться без this.  
  

### Заключение

  
Итак, в данной статье мы рассмотрели четыре способа создания объектов в JavaScript:  
  

- Функции-конструкторы
- Классы
- Связывание объектов
- Фабричные функции

  
Во-первых, мы пришли к выводу, что наиболее оптимальными способами создания объектов являются классы и ФФ.  
  
Во-вторых, мы увидели, что подклассы легче создавать с помощью классов. Однако, в случае композиции лучше использовать ФФ.  
  
В-третьих, мы резюмировали, что, когда речь идет об инкапсуляции, ФФ имеют преимущество перед классами, поскольку последние требуют использования специального префикса "#", а ФФ делают переменные частными автоматически.  
  
В-четвертых, ФФ позволяют обойтись без использования this в качестве ссылки на экземпляр. В классах приходится прибегать к некоторым хитростям, дабы вернуть this исходный контекст, измененный обработчиком событий.