

В [[Что такое JavaScript (Основы и Подкапотные Особенности)|JavaScript]] есть два основных способа обработки асинхронного кода: Promise (ES6) и async / await (ES7). Эти синтаксисы дают нам равные базовые функции, но по-разному влияют на читаемость и область видимости. В этой статье мы увидим, как один синтаксис помогает, а другой отправляет нас в callback hell! Материал адаптирован на русский язык совместно с Тимофеем Тиуновым, системным архитектором в Сбермегамаркете и автором курса “JavaScript” в Skillbox.

**Тимофей Тиунов:** _“На самом деле основных способов обработки асинхронного кода три,_ — _есть ещё коллбэки. Суть третьего подхода в том, что вы просто передаете функцию как аргумент при вызове другой функции. Например, так работает addEventListener. В современном JavaScript этот способ занял свое место именно в обработке событий, а для написания кода, в котором нужно дождаться выполнения какой-то операции, он оказался неудобным и громоздким. Поэтому с развитием языка появился сначала Promise, а затем синтаксис с async/await”._ 

JavaScript запускает код построчно, переходя к следующей строке кода только после выполнения предыдущей. Но это может стать проблемой. Иногда нам нужно реализовать задачи, которые требуют длительного или непредсказуемого времени для выполнения: например, получение данных через API.

К счастью, блокировать основной поток нет необходимости, JavaScript позволяет выполнять задачи параллельно. В ES6 был представлен объект Promise, с методами then, catch и finally. Год спустя, в ES7, в язык добавили еще один подход и два новых ключевых слова: async и await.

Эта статья не ставит своей целью объяснить асинхронный JavaScript; для этого есть много хороших источников. Вместо этого освещается менее раскрытая тема: какой синтаксис — then / catch / finally или async / await — лучше?

**Тимофей Тиунов, автор курса “js” в Skillbox:** _“В действительности оба способа используются и часто сочетаются друг с другом. Автор создает ложное впечатление, что один лучше другого, но это не так”._

Сначала обратимся к главным функциям каждого синтаксиса, а затем перейдем к примерам.

## then, catch и finally

then, catch и finally — методы объекта Promise, они объединены в цепочку и идут один за другим. Каждый принимает функцию обратного вызова в качестве аргумента и возвращает Promise.

Создадим простой Promise:

```js
const greeting = new Promise((resolve, reject) => {  
	resolve("Hello!");
});
```

Используя then, catch и finally можно выполнить серию действий, в зависимости от того, разрешен (then) или отклонен (cach) Promise. В то время, как finally позволяет нам выполнять код после выполнения Promise, независимо от того, было ли оно разрешено или отклонено.

```js
greeting  
	.then((value) => {    
		console.log("The Promise is resolved!", value);  
	})  
	
	.catch((error) => {    
		console.error("The Promise is rejected!", error);  
	})  
	
	.finally(() => {    
		console.log("The Promise is settled, meaning it has been resolved or rejected.");  
	});
```

**Тимофей Тиунов:** _“Пример не совсем корректный, так как если сам коллбэк в catch не выкинет ошибку через throw или return Promise.reject, после него можно снова писать then. То есть в данном случае finally бессмысленный”._

В соответствии с целью статьи, использовать можно then. Объединив несколько then методов, мы получаем возможность выполнять последовательные операции над разрешенным Promise. Типичный шаблон для выборки данных с использованием then будет выглядеть примерно так:

```js
fetch(url)
	.then((response) => response.json())  
	
    .then((data) => {    
		return {      
			data: data,      
		    status: response.status,    
		};  
	})  
	
	.then((res) => {    
		console.log(res.data, res.status);  
	});
```

**Тимофей Тиунов:** _“Код в последнем примере написан некорректно, так как response недоступен”._

## async и await

async и await — это синтаксический сахар поверх Promise, который позволяет писать асинхронный код так, как будто инструкции выполняются сверху вниз одна за другой, местами значительно упрощает структуру кода и повышает читаемость. 

**Тимофей Тиунов:** _“Ключевое слово async ставится перед функцией при ее объявлении. Это позволяет внутри такой функции использовать другое ключевое слово_ — _await. await же даёт возможность остановить выполнение функции, чтобы дождаться разрешения promise'а или завершения другой async-функции”._

Обратите внимание, как размещение ключевого слова async зависит от того, используем ли мы обычные или стрелочные функции:

```js
async function doSomethingAsynchronous() {  
	// logic
}

const doSomethingAsynchronous = async () => {  
	// logic
};
```

await можно написать перед любой асинхронной функцией или объектом Promise. Таким образом выполнение кода асинхронной функции “зависнет” на этой строке кода до окончания выполнения операции. Пример с greeting:

```js
async function doSomethingAsynchronous() {  
	const value = await greeting;
}
```

Переменную value можно использовать после этого, как будто бы она часть нормального синхронного кода. Что касается обработки ошибок, мы можем заключить любой асинхронный код в оператор try ... catch ... finally, например:

```js
async function doSomethingAsynchronous() {  
	try {    
	
		const value = await greeting;   
		 
		console.log("The Promise is resolved!", value);  
		
	} catch((error) {    
		
		console.error("The Promise is rejected!", error);  
		
	} finally {    
	
		console.log("The Promise is settled, meaning it has been resolved or rejected.");  
		
	}
}
```

Для возвращения Promise в рамки функции async не нужно использовать await. Пример:

```js
async function getGreeting() {  
	return greeting;
}
```

**Тимофей Тиунов:** _“В данном примере async не нужен. Вот пример, где как бы есть await, но в конце возвращается promise:_

```js
const res = await fetch(...);

return await res.json(); // тут await можно убрать”.
```

Однако есть одно исключение из этого правила: вам действительно нужно написать return await, если вы хотите обработать Promise, отклоненный в блоке try ... catch.

```js
async function getGreeting() {  
	try {    
		return await greeting;  
	} catch (e) {    
		console.error(e);  
	}
}
```

Использование абстрактных примеров может помочь нам понять каждый синтаксис, но без наглядных примеров сложно осознать, почему один может быть предпочтительнее другого.

## Проблема

Представим, что нам нужно выполнить операцию с большим набором данных для книжного магазина. Наша задача — найти всех авторов, написавших более 10 книг, в нашем наборе данных и вернуть их биографические данные. У нас есть доступ к библиотеке с тремя асинхронными методами:

- `getAuthors` - returns all the authors in the database;
    
- `getBooks` - returns all the books in the database;
    
- `getBio` - returns the bio of a specific author.
    

Объекты выглядят следующим образом:

-  ```json
  Author: { 
	 id: "3b4ab205", 
	 name: "Frank Herbert Jr.", 
	 bioId: "1138089a" 
  }
```
    
- ```json
  Book: { 
	  id: "e31f7b5e", 
	  title: "Dune", 
	  authorId: "3b4ab205" 
  }
  ```
    
- ```json
  Bio: { 
	  id: "1138089a", 
	  description: "Franklin Herbert Jr. was an American science-fiction author..." 
  }
  ```


Также нам понадобится вспомогательная функция filterProlificAuthors, которая принимает все записи и все книги в качестве аргументов и возвращает идентификаторы авторов с более чем 10 книгами:

```js
function filterProlificAuthors(authors, books, minBookCount = 10) {  

	return authors.filter(    
		({ id }) => books.filter(({ authorId }) => authorId === id).length > minBookCount  
		
	);
}
```

### Решение 

#### Часть 1.

Чтобы решить эту проблему, нам нужно получить всех авторов и все книги, отфильтровать результаты на основе заданных критериев, а затем получить биографические данные всех авторов, которые соответствуют этим критериям. В псевдокоде наше решение может выглядеть примерно так:

```js
FETCH all authors

FETCH all books

FILTER authors with more than 10 books

FOR each filtered author

FETCH the author’s bio
```

Каждый раз, когда мы видим FETCH, нужно выполнить асинхронную задачу. Итак, как мы могли превратить это в JavaScript? Во-первых, давайте посмотрим, как мы можем закодировать эти шаги, используя then:

```js
getAuthors().then((authors) =>  

	getBooks()    
	
		.the((books) => {      
		
			const prolificAuthorIds = filterProlificAuthors(authors, books);      
			
			return Promise.all(prolificAuthorIds.map((id) => getBio(id)));    
			
		})    
			
		.then((bios) => {      
			
			// Do something with the bios    
			
		})	
);
```

Этот участок действительно делает, что требуется. Но вот вложенность затрудняет понимание кода с первого взгляда. Второй then вкладывается в первый then, а третий then параллелен второму.

**Тимофей Тиунов:** _“Это скорее должно выглядеть так (ниже). Получение авторов и книг происходит последовательно, хотя ничего не мешает сделать это параллельно. Для этого можно передать вызовы getAuthors/Books в Promise.all, который вернёт promise, разрешающийся тогда, когда все переданные в него promise'ы тоже разрешатся._

```js
Promise.all([  

	getAuthors(),  
	
	getBooks(),
	
]).then(([authors, books]) => {  

	const prolificAuthorIds = filterProlificAuthors(authors, books);  
	
	return Promise.all(prolificAuthorIds.map((id) => getBio(id)));
	
}).then(bios => { 

	 // …
	 
})”
```

Код мог бы стать немного более читаемым, если использовать then для возврата даже синхронного кода. Можно дать filterProlificAuthors собственный метод then, как показано ниже:

```js
getAuthors().then((authors) =>  

	getBooks()    
	
		.then((books) => filterProlificAuthors(authors, books))    
		
		.then((ids) => Promise.all(ids.map((id) => getBio(id))))    
		
		.then((bios) => {      
		
			// Do something with the bios    
			
		})
		
);
```

У этой версии есть преимущество - каждый then помещается в одну строку. Правда, и это не избавляет от нескольких уровней вложенности.

Ну а что насчет async и await? Вот примерно такое решение:

```js
async function getBios() {  

	const authors = await getAuthors();  
	
	const books = await getBooks();  
	
	const prolificAuthorIds = filterProlificAuthors(authors, books);  
	
	const bios = await Promise.all(prolificAuthorIds.map((id) => getBio(id)));  
	
	// Do something with the bios
	
}
```

Это решение кажется уже более простым. Здесь нет вложенности, и всего четыре строки. Но преимущества async / await станут еще более очевидными по мере изменения критериев.

**Тимофей Тиунов:** _“А вот это очень вредный пример. Вызовы с await, которые идут подряд сверху вниз, будут исполняться последовательно, хотя в данном случае можно параллельно. Здесь подойдёт гибридное решение на основе promise'ов и await:_

```js
const [authors, books] = await Promise.all([

	getAuthors(),
	
	getBooks(),
	
]);”
```

#### Часть 2

Введем новое требование. На этот раз, когда у нас есть массив bios, мы хотим создать объект, содержащий bios, общее количество авторов и общее количество книг.

Сразу начнем с async/await:

```js
async function getBios() {  

	const authors = await getAuthors();  
	const books = await getBooks();  
	
	const prolificAuthorIds = filterProlificAuthors(authors, books);  
	const bios = await Promise.all(prolificAuthorIds.map((id) => getBio(id)));  
	
	const result = {    
		bios,    
		totalAuthors: authors.length,    
		totalBooks: books.length,  
	};
}
```

Легко и просто. С кодом ничего не нужно делать, поскольку все переменные уже есть. Стоит просто определить результирующий объект в конце.

С then это непросто. В решении из предыдущей части переменные books и bios находятся на разных уровнях. В принципе, можно было бы ввести еще и переменную books, но это усложнило бы ситуацию. Одно из решений - ввести третий уровень вложенности:

```js
getAuthors().then((authors) =>  

	getBooks().then((books) => {    
	
		const prolificAuthorIds = filterProlificAuthors(authors, books);    
	
		return Promise.all(prolificAuthorIds.map((id) => getBio(id))).then(      
	
			(bios) => {        
	
				const result = {          
		
					bios,          
			
					totalAuthors: authors.length,          
			
					totalBooks: books.length,        
			
				};      
		
			}    
	
		);  

	})

);
```

Кроме того, можно деструктурировать массив для передачи books по цепочке на каждом этапе:

```js
getAuthors().then((authors) =>  

	getBooks()    
	.then((books) => [books, filterProlificAuthors(authors, books)])    
	
	.then(([books, ids]) =>      
		Promise.all([books, ...ids.map((id) => getBio(id))])    
	)    
	
	.then(([books, bios]) => {      
		const result = {        
			bios,        
			totalAuthors: authors.length,        
			totalBooks: books.length,      
		}; 
	})
);
```

Мне кажется, ни одно из этих решений не является хорошо читаемым. Сложно определить с первого взгляда переменные и их доступность.

**Тимофей Тиунов:** _“А вот тут прям классный пример, где действительно async/await сильно упрощает код. В коде на async/await все значения находятся в рамках одной фукнции, то есть в одной области видимости. Это значит, что в любом месте функции после объявления нужного значения мы имеем к нему доступ. В случае с promise'ами результаты вычислений одного блока then/catch нам нужно явно передавать в другой, если это необходимо. В таких ситуациях, конечно, async/await может сильно выручить.”_

#### Часть 3

В качестве оптимального варианта можно улучшить предыдущее решение, немного его очистив. Для этого можно использовать Promise.all, что дает возможность выбрать и авторов и книги параллельно.

```js
Promise.all([getAuthors(), getBooks()]).then(([authors, books]) => {  

	const prolificAuthorIds = filterProlificAuthors(authors, books);  
	
	return Promise.all(prolificAuthorIds.map((id) => getBio(id))).then((bios) => {    
		const result = {      
			bios,      
			totalAuthors: authors.length,      
			totalBooks: books.length,    
		};  
	});
});
```

Это может быть лучшим решением для then. Код работает быстрее, нет многоуровневой вложенности.

```js
async function getBios() {  

	const [authors, books] = await Promise.all([getAuthors(), getBooks()]);  
	
	const prolificAuthorIds = filterProlificAuthors(authors, books);  
	
	const bios = await Promise.all(prolificAuthorIds.map((id) => getBio(id)));  
	
	const result = {    
		bios,    
		totalAuthors: authors.length,    
		totalBooks: books.length,  
	};
}
```

### Вывод

Использование методов chained then во многих случаях может потребовать утомительных изменений, особенно когда хочется убедиться, что определенные переменные находятся в области видимости. Даже для простого сценария, подобного тому, который указан выше, не было очевидного лучшего решения. Так, каждое из пяти решений, где использовалось then, имело разные компромиссы для удобочитаемости. Напротив, async / await предоставил самое удобочитаемое решение, которое практически нет необходимости модифицировать.

В реальных приложениях требования нашего асинхронного кода часто будут более сложными, чем сценарий, представленный здесь. В то время как async / await предоставляет нам простую для понимания основу для написания более сложной логики, добавление множества методов then может легко подтолкнуть нас дальше по пути к callback hell — со множеством скобок и уровней отступов. В итоге становится неясно, где заканчивается один блок и начинается следующий.

**Тимофей Тиунов, автор курса “JavaScript” в Skillbox: _“Утверждать, что один инструмент лучше другого — не совсем корректно, что показано на примерах выше. Promise и async/await существуют вместе и часто дополняют друг друга. Бывают ситуации, когда удобно одно, а когда другое. Вы вольны смешивать оба подхода для максимальной читаемости и эффективности кода. Ниже еще один пример в контексте краткости кода:_ 

```js
// на await

const res = await fetch('...');

const json = await res.json();

// на promise + await

const json = await fetch('...').then(res => res.json());
```

_Второй вариант гибридный и просто компактнее.”_


---

[Оригинал статьи](https://habr.com/ru/companies/skillbox/articles/651781/)