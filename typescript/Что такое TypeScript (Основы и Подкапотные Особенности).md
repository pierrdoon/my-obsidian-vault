

TypeScript — это строго типизированный надмножество [[Что такое JavaScript (Основы и Подкапотные Особенности)|JavaScript]], разработанное Microsoft. Он добавляет статическую типизацию, интерфейсы, декораторы и другие возможности, компилируясь в чистый JavaScript. Рассмотрим ключевые аспекты и внутренние механизмы.

---

#### 🚀 Основные Возможности
1. **Статическая типизация**  
   Позволяет определять типы переменных, параметров и возвращаемых значений.  
   ```typescript
   let age: number = 25;
   function greet(name: string): string {
     return `Hello, ${name}!`;
   }
   ```

2. **Совместимость с JavaScript**  
   Весь JS-код валиден в TypeScript. Типы можно добавлять постепенно.

3. **Интерфейсы и Классы**  
   ```typescript
   interface User {
     id: number;
     name: string;
   }
   
   class Admin implements User {
     constructor(public id: number, public name: string) {}
   }
   ```

4. **Generics (Обобщения)**  
   Позволяют создавать универсальные компоненты.  
   ```typescript
   function identity<T>(arg: T): T {
     return arg;
   }
   ```

5. **Декораторы**  
   Используются для метапрограммирования (например, в Angular):  
   ```typescript
   @Component({ selector: 'app-root' })
   class AppComponent {}
   ```

---

### 🔧 Подкапотные Механизмы

#### 1. **Транспиляция в JavaScript**
- TypeScript-компилятор (`tsc`) преобразует TS-код в JS, удаляя все типы и аннотации.  
- Пример:  
  **Исходный TS:**
  ```typescript
  const add = (a: number, b: number): number => a + b;
  ```
  **Скомпилированный JS:**
  ```javascript
  const add = (a, b) => a + b;
  ```

#### 2. **Структурная типизация**
- TypeScript проверяет **форму** объектов, а не их явные имена (в отличие от номинативной типизации в Java/C#).  
- Пример:  
  ```typescript
  interface Point { x: number; y: number; }
  const point: Point = { x: 0, y: 0, z: 0 }; // Ошибка: лишнее свойство 'z'
  ```

#### 3. **Type Erasure**
- Типы существуют только на этапе компиляции и удаляются в рантайме.  
- **Следствие:** Невозможно проверить типы во время выполнения (например, `if (typeof user === "User")` не работает).

#### 4. **Утилиты типов**
- Встроенные помощники для манипуляции типами:  
  ```typescript
  type PartialUser = Partial<User>; // Все свойства опциональны
  type ReadonlyUser = Readonly<User>; // Свойства нельзя изменять
  ```

#### 5. **Условные типы (Conditional Types)**
- Позволяют создавать типы на основе условий:  
  ```typescript
  type Check<T> = T extends string ? "STRING" : "OTHER";
  type A = Check<"hello">; // "STRING"
  type B = Check<42>;      // "OTHER"
  ```

#### 6. **Mapped Types**
- Генерация типов на основе существующих:  
  ```typescript
  type Getters<T> = {
    [K in keyof T as `get${Capitalize<string & K>}`]: () => T[K];
  };
  type UserGetters = Getters<User>; // { getName: () => string; getId: () => number; }
  ```

#### 7. **Декораторы под капотом**
- Декораторы — это функции, которые модифицируют классы/методы. При компиляции они превращаются в вызовы функций.  
  **Исходный код:**
  ```typescript
  @sealed
  class Person {}
  ```
  **Скомпилированный JS:**
  ```javascript
  let Person = class Person {};
  Person = __decorate([sealed], Person);
  ```

#### 8. **Строгая проверка null/undefined**
- С опцией `strictNullChecks` TypeScript предотвращает случайное использование `null` или `undefined`:  
  ```typescript
  let name: string;
  name = null; // Ошибка, если strictNullChecks = true
  ```

#### 9. **Модули и Пространства имен**
- TypeScript поддерживает ES-модули и свои пространства имен (namespace), которые компилируются в IIFE:  
  ```typescript
  namespace MathUtils {
    export function sum(a: number, b: number) { return a + b; }
  }
  ```

#### 10. **Эмитация async/await для старых версий JS**
- При компиляции в ES5, async/await превращаются в цепочки промисов с генераторами.

---

### 🌍 Экосистема
- **tsconfig.json** — конфигурация компилятора (target, module, strict и т.д.).
- **Declaration Files (.d.ts)** — файлы с описанием типов для JS-библиотек (например, `@types/react`).
- **Интеграция с инструментами** — ESLint, Webpack, Babel (через `@babel/preset-typescript`).

---

### 💡 Пример: Как TypeScript предотвращает ошибки
**Код на TypeScript:**
```typescript
function fetchData(url: string): Promise<Response> {
  return fetch(url);
}
fetchData(123); // Ошибка: аргумент должен быть строкой!
```

**После компиляции:**
```javascript
function fetchData(url) {
  return fetch(url);
}
fetchData(123); // Выполнится, но упадет с ошибкой в рантайме.
```

---

### ⚙️ Вывод
TypeScript добавляет слой безопасности и удобства, сохраняя совместимость с JavaScript. Его "магия" работает на этапе компиляции, оставляя чистый JS без накладных расходов в рантайме.