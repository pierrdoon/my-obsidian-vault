
[Оригинал статьи](https://habr.com/ru/companies/otus/articles/834310/)

---

Правильно настроенные индексы могут значительно ускорить доступ к данным и улучшить производительность запросов. В [[Что такое NoSQL|NoSQL]] разнообразие решений настолько велико, что порой можно заблудиться.

В статье разберемся, какие виды индексов существуют, какие задачи они помогают решать и как выбирать подходящий индекс.

## Типы индексов в NoSQL

#### Первичные индексы:

Первичные индексы дают уникальную идентификацию каждой записи в базе данных и организуют данные в соответствии с определенным ключом. В NoSQL-базах данных первичные индексы играют основную роль в распределении данных между узлами.

Первичные индексы определяются автоматически при создании записи и не требуют доп. усилий для их поддержки. Например, в MongoDB `_id` является первичным индексом по умолчанию, а в Cassandra используется первичный ключ, определяемый пользователем.

В MongoDB поле `_id` автоматически индексируется:

```js
// поиск документа по первичному индексу 
_iddb.collection.find({ _id: ObjectId("64bd1c9f4a4e19d8f84f06f9") })
```

В Cassandra первичный ключ определяется при создании таблицы, который автоматически индексируется:

```sql
-- cоздание таблицы с первичным ключом
CREATE TABLE users (    
	user_id UUID PRIMARY KEY,    
	name TEXT,    
	email TEXT
);

-- поиск по первичному индексу
SELECT * FROM users WHERE user_id = '550e8400-e29b-41d4-a716-446655440000';
```

Первичные индексы дают быстрый доступ к записям, т.к данные отсортированы по ключу. Но при этом подходят только для операций, связанных с ключом.

#### Вторичные индексы

Вторичные индексы дают возможность быстрого поиска по полям, отличным от первичного ключа. Они помогают выполнять сложные запросы, которые не могут быть реализованы с использованием только первичных индексов.

**MongoDB:**

Создание вторичного индекса для оптимизации поиска по полю `name`:

```js
// создание вторичного индекса на поле 
namedb.collection.createIndex({ name: 1 });

// поиск по вторичному индексу
db.collection.find({ name: "Artem" });
```

**Cassandra:**

Создание вторичного индекса для оптимизации поиска по полю `email`:

```sql
-- создание вторичного индекса на поле email
CREATE INDEX ON users (email);

-- поиск по вторичному индексу
SELECT * FROM users WHERE email = 'artem@example.com';
```

Рассмотрим пример использования первичных и вторичных ключей, где идет работа с коллекцией пользователей в MongoDB и нужно оптимизировать поиск по нескольким полям:

```js
// создание коллекции пользователей с автоматическим первичным индексом 
_iddb.users.insertMany([    
	{ name: "Ivan", email: "ivan@example.com", age: 28 },    
	{ name: "Artem", email: "artem@example.com", age: 32 },    
	{ name: "Kolya", email: "kolya@example.com", age: 25 }
]);
	
// создание вторичных индексов
db.users.createIndex({ name: 1 });
db.users.createIndex({ email: 1 });
	
// поиск пользователя по имени
db.users.find({ name: "Alice" });
	
// поиск пользователя по email
db.users.find({ email: "bob@example.com" });
```

Создали индексы на полях `name` и `email`, таким образом можно быстро находить пользователей по этим параметрам.

#### Индексы на основе диапазонов

Индексы на основе диапазонов позволяют обрабатывать запросы, где необходимо работать с интервалами значений: _временные метки_, _числовые диапазоны_ или _алфавитные строки_.

Elasticsearch активно использует индексы на основе диапазонов для обработки временных данных и сложных запросов. Пример:

```js
PUT /logs
{  
	"mappings": {    
		"properties": {      
			"timestamp": { "type": "date" },      
			"message": { "type": "text" }    
		}  
	}
}

// поиск по диапазону дат
GET /logs/_search
{  
	"query": {    
		"range": {      
			"timestamp": {        
				"gte": "2024-08-01",        
				"lte": "2024-08-31"      
			}    
		}  
	}
}
```

Cassandra поддерживает индексацию на основе диапазонов для числовых и временных данных:

```sql
-- создание таблицы с временными метками
CREATE TABLE events (    
	event_id UUID PRIMARY KEY,    
	timestamp TIMESTAMP,    
	description TEXT
);
	
-- поиск событий за определенный период
SELECT * FROM events WHERE timestamp >= '2024-08-01' AND timestamp <= '2024-08-31';
```

Представим задачу, в которой идет работа с логами событий и нужно быстро находить события за определенные временные промежутки:

```js
// создание коллекции логов событий
db.logs.insertMany([    
	{ timestamp: new Date("2024-08-01T10:00:00Z"), message: "User login" },    
	{ timestamp: new Date("2024-08-02T12:00:00Z"), message: "Data backup" },    
	{ timestamp: new Date("2024-08-03T14:00:00Z"), message: "System update" }
]);

// создание индекса на поле 
timestampdb.logs.createIndex({ timestamp: 1 });

// поиск событий за август 2024
db.logs.find({    
	timestamp: {        
		$gte: new Date("2024-08-01"),        
		$lte: new Date("2024-08-31")    
	}
});
```

#### Геопространственные индексы

Геопространственные индексы предназначены для работы с географическими данными, такими как координаты точек, линии и полигоны. Они позволяют выполнять географические запросы, например: "_поиск ближайших_" и "_вхождение в область_".

В MongoDB используется `2dsphere` индекс для поддержки сложных географических запросов:

```js
// создание коллекции с географическими данными
db.places.insertMany([    
	{ name: "Central Park", location: { type: "Point", coordinates: [-73.9654, 40.7829] } },    
	{ name: "Golden Gate Park", location: { type: "Point", coordinates: [-122.4862, 37.7694] } }
]);

// создание геопространственного индекса
db.places.createIndex({ location: "2dsphere" });

// поиск мест в радиусе 10 км от заданной точки
db.places.find({    
	location: {        
		$near: {            
			$geometry: { type: "Point", coordinates: [-73.935242, 40.730610] },
			$maxDistance: 10000        
		}    
	}
});
```

Elasticsearch поддерживает геопространственные индексы для быстрого поиска по координатам:

```js
PUT /places
{  
	"mappings": {    
		"properties": {      
			"location": { "type": "geo_point" }    
		}  
	}
}

// Поиск ближайших мест
GET /places/_search
{  
	"query": {    
		"geo_distance": {      
			"distance": "10km",      
			"location": {        
				"lat": 40.730610,        
				"lon": -73.935242      
			}    
		}  
	}
}
```

Допустим, мы разрабатываем приложение для поиска ближайших кафе, используя MongoDB:

```js
// создание коллекции с данными о кафе
db.cafes.insertMany([    
	{ name: "Cafe 1", location: { type: "Point", coordinates: [-73.935242, 40.730610] } },    
	{ name: "Cafe 2", location: { type: "Point", coordinates: [-73.985428, 40.748817] } }]);
	
// создание геопространственного индекса
db.cafes.createIndex({ location: "2dsphere" });
	
// поиск кафе в радиусе 5 км от заданной точки
db.cafes.find({    
	location: {        
		$near: {            
			$geometry: { type: "Point", coordinates: [-73.961452, 40.768063] },
			$maxDistance: 5000        
		}    
	}
});
```

#### Полнотекстовые индексы

Полнотекстовые индексы позволяют обрабатывать запросы, связанные с текстовым содержимым.

MongoDB поддерживает полнотекстовые индексы с использованием `text` индекса для текстового поиска:

```js
// создание коллекции с текстовыми данными
db.articles.insertMany([    
	{ title: "NoSQL: A Comprehensive Guide", content: "This article explains the key concepts of NoSQL databases." },    
	{ title: "Understanding MongoDB Indexes", content: "Indexes in MongoDB are crucial for performance optimization." }
]);

// создание полнотекстового индекса
db.articles.createIndex({ content: "text" });

// поиск по ключевым словам
db.articles.find({ $text: { $search: "NoSQL databases" } });
```

А вот Elasticsearch изначально проектировался для полнотекстового поиска:

```js
PUT /articles
{  
	"mappings": {    
		"properties": {      
			"content": { "type": "text" }    
		}  
	}
}

// Поиск по ключевым словам
GET /articles/_search
{  
	"query": {    
		"match": {      
			"content": "NoSQL databases"    
		}  
	}
}
```

Представим, что хотим реализовать поиск по статьям с MongoDB:

```js
// MongoDB: Создание коллекции статей
db.blog.insertMany([    
	{ title: "The Rise of NoSQL", content: "NoSQL databases have become increasingly popular..." },    
	{ title: "Indexing in MongoDB", content: "Indexes are essential for query performance in MongoDB..." }
]);

// создание полнотекстового индекса на поле content
db.blog.createIndex({ content: "text" });

// поиск статей, содержащих слово "NoSQL"
db.blog.find({ $text: { $search: "NoSQL" } });
```

---

Индексация — это мощный инструмент, который может улучшить производительность и гибкость проектов.