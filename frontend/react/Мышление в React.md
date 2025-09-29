React может изменить ваше представление о дизайне и создаваемых приложениях. При создании пользовательского интерфейса с помощью [[Библиотека React|React]] вы сначала разбиваете его на части, называемые _компонентами_ . Затем вы описываете различные визуальные состояния каждого из компонентов. Наконец, вы соединяете компоненты между собой, чтобы данные проходили через них. В этом руководстве мы покажем вам процесс создания таблицы данных о продуктах с возможностью поиска с помощью React.

## Начните с макета

Представьте, что у вас уже есть JSON API и макет от дизайнера.

API JSON возвращает данные, которые выглядят примерно так:

```c
[ 
	{ category: "Fruits", price: "$1", stocked: true, name: "Apple" }, 
	{ category: "Fruits", price: "$1", stocked: true, name: "Dragonfruit" }, 
	{ category: "Fruits", price: "$2", stocked: false, name: "Passionfruit" },       { category: "Vegetables", price: "$2", stocked: true, name: "Spinach" }, 
	{ category: "Vegetables", price: "$4", stocked: false, name: "Pumpkin" }, 
	{ category: "Vegetables", price: "$1", stocked: true, name: "Peas" }
]
```

Макет выглядит так:

![картинка лол](./images/мышление1.png)

Чтобы реализовать пользовательский интерфейс в React, обычно необходимо выполнить те же пять шагов.

## Шаг 1: Разбейте пользовательский интерфейс на иерархию компонентов

Начните с того, что нарисуйте рамки вокруг каждого компонента и подкомпонента в макете и дайте им названия. Если вы работаете с дизайнером, он, возможно, уже дал этим компонентам названия в своей программе проектирования. Спросите его!

В зависимости от вашего опыта вы можете рассматривать разделение дизайна на компоненты разными способами:

- **Программирование** — используйте те же методы для принятия решения о создании новой функции или объекта. Один из таких методов — [принцип единственной ответственности](https://en.wikipedia.org/wiki/Single_responsibility_principle) , то есть компонент в идеале должен выполнять только одну функцию. Если компонент разрастается, его следует разбить на более мелкие подкомпоненты.
- **CSS** — подумайте, для чего вы бы создали селекторы классов. (Однако компоненты немного менее детализированы.)
- **Дизайн** — продумайте, как бы вы организовали слои дизайна.

Если ваш JSON-файл хорошо структурирован, вы часто обнаружите, что он естественным образом соответствует структуре компонентов вашего пользовательского интерфейса. Это связано с тем, что пользовательский интерфейс и модели данных часто имеют одинаковую информационную архитектуру, то есть одинаковую форму. Разделите пользовательский интерфейс на компоненты, каждый из которых соответствует одному элементу вашей модели данных.

На этом экране есть пять компонентов:

![картинка лол](./images/мышление2.png)

1. `FilterableProductTable`(серый) содержит все приложение.
2. `SearchBar`(синий) принимает пользовательский ввод.
3. `ProductTable`(лаванда) отображает и фильтрует список в соответствии с введенными пользователем данными.
4. `ProductCategoryRow`(зеленый) отображает заголовок для каждой категории.
5. `ProductRow` (желтый) отображает строку для каждого продукта.

Если взглянуть на `ProductTable`(лаванду), то можно увидеть, что заголовок таблицы (содержащий метки «Имя» и «Цена») не является отдельным компонентом. Это вопрос предпочтений, и вы можете выбрать любой из вариантов. В данном примере он является частью , `ProductTable`поскольку находится внутри `ProductTable`списка . Однако, если этот заголовок станет сложным (например, если вы добавите сортировку), вы можете перенести его в отдельный `ProductTableHeader`компонент.

Теперь, когда вы определили компоненты в макете, расположите их в иерархии. Компоненты, которые присутствуют в другом компоненте макета, должны отображаться как дочерние элементы в этой иерархии:

- `FilterableProductTable`
    - `SearchBar`
    - `ProductTable`
        - `ProductCategoryRow`
        - `ProductRow`

## Шаг 2: Создание статической версии в React

Теперь, когда у вас есть иерархия компонентов, пора реализовать приложение. Самый простой подход — создать версию, которая отображает пользовательский интерфейс из вашей модели данных, не добавляя никакой интерактивности… пока! Часто проще сначала создать статическую версию, а интерактивность добавить позже. Создание статической версии требует много текста и не требует размышлений, а вот добавление интерактивности требует много текста и размышлений.

Чтобы создать статическую версию приложения, отображающую вашу модель данных, вам потребуется создать [компоненты](https://react.dev/learn/your-first-component) , которые повторно используют другие компоненты и передают данные с помощью [свойств.](https://react.dev/learn/passing-props-to-a-component) Свойства — это способ передачи данных от родительского элемента к дочернему. (Если вы знакомы с концепцией состояния [,](https://react.dev/learn/state-a-components-memory) вообще не используйте состояние для создания этой статической версии. Состояние зарезервировано только для интерактивности, то есть для данных, которые изменяются со временем. Поскольку это статическая версия приложения, оно вам не нужно.)

Вы можете строить либо «сверху вниз», начиная с компонентов, расположенных выше в иерархии (например `FilterableProductTable`, ), либо «снизу вверх», начиная с компонентов, расположенных ниже (например, `ProductRow`). В простых примерах обычно проще двигаться сверху вниз, а в более крупных проектах — снизу вверх.

```JavaScript
function ProductCategoryRow({ category }) {
  return (
    <tr>
      <th colSpan="2">
        {category}
      </th>
    </tr>
  );
}

function ProductRow({ product }) {
  const name = product.stocked ? product.name :
    <span style={{ color: 'red' }}>
      {product.name}
    </span>;

  return (
    <tr>
      <td>{name}</td>
      <td>{product.price}</td>
    </tr>
  );
}

function ProductTable({ products }) {
  const rows = [];
  let lastCategory = null;

  products.forEach((product) => {
    if (product.category !== lastCategory) {
      rows.push(
        <ProductCategoryRow
          category={product.category}
          key={product.category} />
      );
    }
    rows.push(
      <ProductRow
        product={product}
        key={product.name} />
    );
    lastCategory = product.category;
  });

  return (
    <table>
      <thead>
        <tr>
          <th>Name</th>
          <th>Price</th>
        </tr>
      </thead>
      <tbody>{rows}</tbody>
    </table>
  );
}

function SearchBar() {
  return (
    <form>
      <input type="text" placeholder="Search..." />
      <label>
        <input type="checkbox" />
        {' '}
        Only show products in stock
      </label>
    </form>
  );
}

function FilterableProductTable({ products }) {
  return (
    <div>
      <SearchBar />
      <ProductTable products={products} />
    </div>
  );
}

const PRODUCTS = [
  {category: "Fruits", price: "$1", stocked: true, name: "Apple"},
  {category: "Fruits", price: "$1", stocked: true, name: "Dragonfruit"},
  {category: "Fruits", price: "$2", stocked: false, name: "Passionfruit"},
  {category: "Vegetables", price: "$2", stocked: true, name: "Spinach"},
  {category: "Vegetables", price: "$4", stocked: false, name: "Pumpkin"},
  {category: "Vegetables", price: "$1", stocked: true, name: "Peas"}
];

export default function App() {
  return <FilterableProductTable products={PRODUCTS} />;
}

```

После создания компонентов у вас появится библиотека повторно используемых компонентов, которые визуализируют вашу модель данных. Поскольку это статическое приложение, компоненты будут возвращать только JSX. Компонент наверху иерархии ( `FilterableProductTable`) будет использовать вашу модель данных в качестве свойства. Это называется _односторонним потоком данных_ , поскольку данные передаются от компонента верхнего уровня к компонентам нижнего уровня дерева.

> ### Ловушка
> 
> На этом этапе вам не следует использовать значения состояния. Это для следующего шага!


## Шаг 3: Найдите минимальное, но полное представление состояния пользовательского интерфейса.

Чтобы сделать пользовательский интерфейс интерактивным, необходимо разрешить пользователям изменять базовую модель данных. Для этого используется _состояние ._

Представьте состояние как минимальный набор изменяющихся данных, которые ваше приложение должно запомнить. Важнейший принцип структурирования состояния — соблюдение принципа [DRY (не повторяться).](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself) Определите минимальное представление состояния, необходимое вашему приложению, и вычисляйте всё остальное по мере необходимости. Например, если вы составляете список покупок, вы можете хранить элементы в виде массива в состоянии. Если вы также хотите отображать количество элементов в списке, не храните количество элементов как ещё одно значение состояния — вместо этого считывайте длину массива.

Теперь подумайте обо всех фрагментах данных в этом примере приложения:

1. Первоначальный список продуктов
2. Текст поиска, введенный пользователем
3. Значение флажка
4. Отфильтрованный список продуктов

Какие из них являются государственными? Укажите те, которые не являются государственными:

- Остаётся ли оно **неизменным** со временем? Если да, то это не государство.
- **Передаётся** ли он от родителя через свойства? Если да, то это не состояние.
- **Можно ли вычислить его** на основе текущего состояния или свойств компонента? Если да, то это _точно_ не состояние!

Вероятно, останется государство.

Давайте еще раз рассмотрим их по порядку:

1. Исходный список продуктов **передается как реквизит, поэтому он не является состоянием.**
2. Текст поиска, по-видимому, является состоянием, поскольку он меняется со временем и не может быть вычислен ни на основании чего.
3. Значение флажка, по-видимому, является состоянием, поскольку оно меняется со временем и не может быть вычислено из чего-либо.
4. Отфильтрованный список продуктов **не является постоянным, поскольку его можно вычислить** , взяв исходный список продуктов и отфильтровав его в соответствии с искомым текстом и значением флажка.

Это означает, что отображаются только текст поиска и значение флажка! Отлично сделано!

## Шаг 4: Определите, где будет располагаться ваш штат

После определения минимальных данных о состоянии вашего приложения вам необходимо определить, какой компонент отвечает за изменение этого состояния или _владеет_ им. Помните: React использует односторонний поток данных, передавая данные по иерархии компонентов от родительского к дочернему. Может быть не сразу понятно, какой компонент должен владеть каким состоянием. Это может быть сложно, если вы новичок в этой области, но вы можете разобраться, выполнив следующие шаги!

Для каждого пункта вашего заявления:

1. Определите _каждый_ компонент, который отображает что-либо на основе этого состояния.
2. Найдите их ближайший общий родительский компонент — компонент, который находится выше всех в иерархии.
3. Решите, где должно располагаться государство:
    1. Зачастую состояние можно поместить непосредственно в их общего родителя.
    2. Вы также можете поместить состояние в какой-либо компонент, находящийся выше их общего родителя.
    3. Если вы не можете найти компонент, которому имеет смысл владеть состоянием, создайте новый компонент, предназначенный исключительно для хранения состояния, и добавьте его где-нибудь в иерархии выше общего родительского компонента.

На предыдущем шаге вы обнаружили в этом приложении два фрагмента состояния: текст поискового запроса и значение флажка. В этом примере они всегда появляются вместе, поэтому логично разместить их в одном месте.

Теперь давайте рассмотрим нашу стратегию для них:

1. **Определите компоненты, которые используют состояние:**
    - `ProductTable`необходимо отфильтровать список продуктов на основе этого состояния (текст поиска и значение флажка).
    - `SearchBar`необходимо отобразить это состояние (текст поиска и значение флажка).
2. **Найдите их общего родителя:** первый родительский компонент, который разделяют оба компонента, — это `FilterableProductTable`.
3. **Определите, где находится штат** : мы сохраним текст фильтра и проверенные значения штата в `FilterableProductTable`.

Поэтому государственные ценности будут жить `FilterableProductTable`.

Добавьте состояние к компоненту с помощью [`useState()`хука.](https://react.dev/reference/react/useState) Хуки — это специальные функции, позволяющие «подключаться» к React. Добавьте две переменные состояния в начало `FilterableProductTable`и укажите их начальное состояние:

```JavaScript
function FilterableProductTable({ products }) {  
	const [filterText, setFilterText] = useState('');  
	const [inStockOnly, setInStockOnly] = useState(false);
}
```

Затем передайте `filterText`и `inStockOnly`в `ProductTable`и `SearchBar`как реквизит:

```JavaScript
<div>  
	<SearchBar     
		filterText={filterText}     
		inStockOnly={inStockOnly} />  
	<ProductTable     
		products={products}    
		filterText={filterText}    
		inStockOnly={inStockOnly} />
</div>
```

Вы можете начать наблюдать за поведением вашего приложения. Измените `filterText`начальное значение с `useState('')`на `useState('fruit')`в приведенном ниже коде песочницы. Вы увидите как текст поискового запроса, так и обновление таблицы:

```JavaScript
import { useState } from 'react';

function FilterableProductTable({ products }) {
  const [filterText, setFilterText] = useState('');
  const [inStockOnly, setInStockOnly] = useState(false);

  return (
    <div>
      <SearchBar 
        filterText={filterText} 
        inStockOnly={inStockOnly} />
      <ProductTable 
        products={products}
        filterText={filterText}
        inStockOnly={inStockOnly} />
    </div>
  );
}

function ProductCategoryRow({ category }) {
  return (
    <tr>
      <th colSpan="2">
        {category}
      </th>
    </tr>
  );
}

function ProductRow({ product }) {
  const name = product.stocked ? product.name :
    <span style={{ color: 'red' }}>
      {product.name}
    </span>;

  return (
    <tr>
      <td>{name}</td>
      <td>{product.price}</td>
    </tr>
  );
}

function ProductTable({ products, filterText, inStockOnly }) {
  const rows = [];
  let lastCategory = null;

  products.forEach((product) => {
    if (
      product.name.toLowerCase().indexOf(
        filterText.toLowerCase()
      ) === -1
    ) {
      return;
    }
    if (inStockOnly && !product.stocked) {
      return;
    }
    if (product.category !== lastCategory) {
      rows.push(
        <ProductCategoryRow
          category={product.category}
          key={product.category} />
      );
    }
    rows.push(
      <ProductRow
        product={product}
        key={product.name} />
    );
    lastCategory = product.category;
  });

  return (
    <table>
      <thead>
        <tr>
          <th>Name</th>
          <th>Price</th>
        </tr>
      </thead>
      <tbody>{rows}</tbody>
    </table>
  );
}

function SearchBar({ filterText, inStockOnly }) {
  return (
    <form>
      <input 
        type="text" 
        value={filterText} 
        placeholder="Search..."/>
      <label>
        <input 
          type="checkbox" 
          checked={inStockOnly} />
        {' '}
        Only show products in stock
      </label>
    </form>
  );
}

const PRODUCTS = [
  {category: "Fruits", price: "$1", stocked: true, name: "Apple"},
  {category: "Fruits", price: "$1", stocked: true, name: "Dragonfruit"},
  {category: "Fruits", price: "$2", stocked: false, name: "Passionfruit"},
  {category: "Vegetables", price: "$2", stocked: true, name: "Spinach"},
  {category: "Vegetables", price: "$4", stocked: false, name: "Pumpkin"},
  {category: "Vegetables", price: "$1", stocked: true, name: "Peas"}
];

export default function App() {
  return <FilterableProductTable products={PRODUCTS} />;
}

```


В песочнице выше `ProductTable`прочитайте `SearchBar`свойства `filterText`и `inStockOnly`для рендеринга таблицы, входных данных и флажка. Например, вот как `SearchBar`заполняется входное значение:

```JavaScript
function SearchBar({ filterText, inStockOnly }) {  
return (   
	<form>     
		<input         
		type="text"         
		value={filterText}         
		placeholder="Search..."
	/>
);
```

Однако вы пока не добавили код для реагирования на действия пользователя, например, на ввод текста. Это будет ваш последний шаг.

## Шаг 5: Добавьте обратный поток данных

В настоящее время ваше приложение корректно отображается, а свойства и состояние передаются вниз по иерархии. Но для изменения состояния в соответствии с пользовательским вводом вам потребуется поддержка передачи данных в обратном направлении: компоненты формы, находящиеся глубоко в иерархии, должны обновлять состояние в `FilterableProductTable`.

React делает этот поток данных явным, но требует немного больше ввода, чем двусторонняя привязка данных. Если вы попытаетесь ввести данные или установить флажок в примере выше, вы увидите, что React проигнорирует ваши входные данные. Это сделано намеренно. Написав `<input value={filterText} />`, вы устанавливаете `value`свойство , которое `input`всегда будет равно состоянию `filterText`, переданному из `FilterableProductTable`. Поскольку `filterText`состояние никогда не устанавливается, входные данные никогда не меняются.

Необходимо, чтобы при каждом изменении пользователем данных в форме состояние обновлялось в соответствии с этими изменениями. Состояние принадлежит `FilterableProductTable`, поэтому только он может вызывать `setFilterText`и `setInStockOnly`. Чтобы разрешить `SearchBar`обновление `FilterableProductTable`состояния , необходимо передать следующие функции `SearchBar`:

```JavaScript
function FilterableProductTable({ products }) {  
	const [filterText, setFilterText] = useState('');  
	const [inStockOnly, setInStockOnly] = useState(false);  
	
	return (    
		<div>      
		<SearchBar        
			filterText={filterText}         
			inStockOnly={inStockOnly}        
			onFilterTextChange={setFilterText}        
			onInStockOnlyChange={setInStockOnly} 
		/>
```

Внутри `SearchBar`вы добавите `onChange`обработчики событий и установите родительское состояние из них:

```JavaScript
function SearchBar({  
	filterText,  
	inStockOnly,  
	onFilterTextChange,  
	onInStockOnlyChange
}) {  
	
	return (    
		<form>      
		<input        
			type="text"        
			value={filterText}        
			placeholder="Search..."        
			onChange={(e) => onFilterTextChange(e.target.value)}      
		/>      
		<label>        
			<input          
				type="checkbox"          
				checked={inStockOnly}          
				onChange={(e) => onInStockOnlyChange(e.target.checked)}
```

Теперь приложение полностью работает!

```JavaScript
import { useState } from 'react';

function FilterableProductTable({ products }) {
  const [filterText, setFilterText] = useState('');
  const [inStockOnly, setInStockOnly] = useState(false);

  return (
    <div>
      <SearchBar 
        filterText={filterText} 
        inStockOnly={inStockOnly} 
        onFilterTextChange={setFilterText} 
        onInStockOnlyChange={setInStockOnly} />
      <ProductTable 
        products={products} 
        filterText={filterText}
        inStockOnly={inStockOnly} />
    </div>
  );
}

function ProductCategoryRow({ category }) {
  return (
    <tr>
      <th colSpan="2">
        {category}
      </th>
    </tr>
  );
}

function ProductRow({ product }) {
  const name = product.stocked ? product.name :
    <span style={{ color: 'red' }}>
      {product.name}
    </span>;

  return (
    <tr>
      <td>{name}</td>
      <td>{product.price}</td>
    </tr>
  );
}

function ProductTable({ products, filterText, inStockOnly }) {
  const rows = [];
  let lastCategory = null;

  products.forEach((product) => {
    if (
      product.name.toLowerCase().indexOf(
        filterText.toLowerCase()
      ) === -1
    ) {
      return;
    }
    if (inStockOnly && !product.stocked) {
      return;
    }
    if (product.category !== lastCategory) {
      rows.push(
        <ProductCategoryRow
          category={product.category}
          key={product.category} />
      );
    }
    rows.push(
      <ProductRow
        product={product}
        key={product.name} />
    );
    lastCategory = product.category;
  });

  return (
    <table>
      <thead>
        <tr>
          <th>Name</th>
          <th>Price</th>
        </tr>
      </thead>
      <tbody>{rows}</tbody>
    </table>
  );
}

function SearchBar({
  filterText,
  inStockOnly,
  onFilterTextChange,
  onInStockOnlyChange
}) {
  return (
    <form>
      <input 
        type="text" 
        value={filterText} placeholder="Search..." 
        onChange={(e) => onFilterTextChange(e.target.value)} />
      <label>
        <input 
          type="checkbox" 
          checked={inStockOnly} 
          onChange={(e) => onInStockOnlyChange(e.target.checked)} />
        {' '}
        Only show products in stock
      </label>
    </form>
  );
}

const PRODUCTS = [
  {category: "Fruits", price: "$1", stocked: true, name: "Apple"},
  {category: "Fruits", price: "$1", stocked: true, name: "Dragonfruit"},
  {category: "Fruits", price: "$2", stocked: false, name: "Passionfruit"},
  {category: "Vegetables", price: "$2", stocked: true, name: "Spinach"},
  {category: "Vegetables", price: "$4", stocked: false, name: "Pumpkin"},
  {category: "Vegetables", price: "$1", stocked: true, name: "Peas"}
];

export default function App() {
  return <FilterableProductTable products={PRODUCTS} />;
}
```


Подробности обработки событий и обновления состояния можно узнать в разделе [[Добавление интерактивности]]
