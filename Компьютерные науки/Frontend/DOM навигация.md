### Вниз (к потомкам)

```javascript
const div = document.getElementById('container');

// Все дочерние узлы (включая текстовые)
const allNodes = div.childNodes;

// Только дочерние элементы
const elements = div.children;

// Первый/последний узел (может быть #text)
const first = div.firstChild;
const last = div.lastChild;

// Первый/последний элемент
const firstEl = div.firstElementChild;
const lastEl = div.lastElementChild;
```

---

### Вверх (к родителям)

```javascript
const child = document.querySelector('.item');

// Родительский узел (любой тип)
const parent = child.parentNode;

// Родительский элемент (только Element)
const parentEl = child.parentElement;

// Подняться до первого элемента с классом 'section'
const section = child.closest('.section');
```

---

### Вбок (к соседям)

```javascript
const current = document.querySelector('.active');

// Следующий/предыдущий узел (может быть #text)
const nextNode = current.nextSibling;
const prevNode = current.previousSibling;

// Следующий/предыдущий элемент
const nextEl = current.nextElementSibling;
const prevEl = current.previousElementSibling;
```

---

### Поиск по дереву

```javascript
const container = document.getElementById('app');

// Первый потомок под селектор
const title = container.querySelector('.title');

// Все потомки под селектор (статический NodeList)
const items = container.querySelectorAll('.item');

// По id (только от document)
const header = document.getElementById('header');

// По классу (живая HTMLCollection)
const reds = document.getElementsByClassName('red');

// По тегу (живая HTMLCollection)
const paragraphs = document.getElementsByTagName('p');

// По атрибуту name (живая NodeList)
const radios = document.getElementsByName('gender');
```

---

### Проверка вложенности

```javascript
const parent = document.getElementById('list');
const child = document.getElementById('item-5');

// Проверить, содержит ли parent child
const isInside = parent.contains(child); // true/false

// Точное отношение (битовая маска)
const relation = parent.compareDocumentPosition(child);
// 4 - потомок, 2 - предшествует, 1 - следует
```

---

### Итераторы (ручной обход)

```javascript
// TreeWalker (с фильтрацией)
const walker = document.createTreeWalker(
  document.body,
  NodeFilter.SHOW_ELEMENT,
  {
    acceptNode: function(node) {
      if (node.dataset.hidden === 'true') {
        return NodeFilter.FILTER_REJECT; // отсечь ветку
      }
      return NodeFilter.FILTER_ACCEPT;
    }
  }
);

let node = walker.nextNode();
while (node) {
  console.log(node.tagName);
  node = walker.nextNode();
}

// NodeIterator (проще, только вперёд)
const iterator = document.createNodeIterator(
  document.body,
  NodeFilter.SHOW_TEXT,
  null
);

let textNode = iterator.nextNode();
while (textNode) {
  console.log(textNode.textContent.trim());
  textNode = iterator.nextNode();
}
```

---

### По координатам

```javascript
const el = document.querySelector('.box');

// Координаты относительно вьюпорта
const rect = el.getBoundingClientRect();
console.log(rect.top, rect.left, rect.width, rect.height);

// Если элемент перенесён на несколько строк
const rects = el.getClientRects();

// Получить элемент по координатам (например, при клике)
document.addEventListener('click', (e) => {
  const target = document.elementFromPoint(e.clientX, e.clientY);
  console.log('Кликнули по:', target);
  
  // Все элементы в точке (стек перекрытий)
  const allAtPoint = document.elementsFromPoint(e.clientX, e.clientY);
});
```

---

### Работа с коллекциями

```javascript
// Живая коллекция (обновляется при мутациях)
const live = document.getElementsByClassName('item');
console.log(live.length); // 3

// Статическая коллекция
const static = document.querySelectorAll('.item');
console.log(static.length); // 3

// Преобразовать в массив
const arr1 = Array.from(live);
const arr2 = [...static];

// Безопасная итерация с удалением
const items = [...document.getElementsByClassName('item')];
items.forEach(item => item.remove()); // Всё ок
```

---

### Ссылки живые

```javascript
const el = document.getElementById('test');
console.log(el.parentElement); // <div>

el.remove();
console.log(el.parentElement); // null
console.log(el); // объект всё ещё существует в памяти
```

---

### Комбинированный навигационный пайплайн

```javascript
// Найти родительский контейнер активного элемента,
// затем найти в нём все вложенные span,
// взять следующий элемент после каждого
const active = document.querySelector('.active');
const container = active.closest('.container');
const spans = container.querySelectorAll('span');

spans.forEach(span => {
  const next = span.nextElementSibling;
  if (next && next.tagName === 'BUTTON') {
    next.classList.add('highlight');
  }
});
```