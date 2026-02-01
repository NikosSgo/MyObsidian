`watch` во Vue — это механизм реактивного наблюдения за изменениями состояния. Он используется, когда нужно выполнить побочный эффект в ответ на изменение данных, а не просто пересчитать значение для шаблона.

Рассмотрим по порядку.

`watch` применяется, когда:  
– нужно реагировать на изменение значения (запрос к API, запись в storage, логика с таймерами);  
– важны старое и новое значения;  
– требуется асинхронная логика;  
– нужно отслеживать несколько источников.

Базовый пример (Composition API):

```ts
import { ref, watch } from 'vue';

const count = ref(0);

watch(count, (newValue, oldValue) => {
  console.log(newValue, oldValue);
});
```

`watch(source, callback)`  
source — `ref`, `computed`, геттер-функция или массив источников.  
callback вызывается при изменении.

Наблюдение за вычисляемым значением:

```ts
watch(
  () => props.userId,
  (id) => {
    loadUser(id);
  }
);
```

Важно: при передаче `ref` — передаём сам `ref`, при сложном выражении — функцию.

Отслеживание нескольких источников:

```ts
watch(
  [page, pageSize],
  ([newPage, newSize], [oldPage, oldSize]) => {
    fetchData();
  }
);
```

Глубокое наблюдение (`deep`):

По умолчанию `watch` не отслеживает вложенные изменения объектов.

```ts
watch(
  form,
  () => {
    validateForm();
  },
  { deep: true }
);
```

⚠️ Использовать `deep` аккуратно — это дорогая операция.

Запуск сразу при инициализации (`immediate`):

```ts
watch(
  query,
  (value) => {
    search(value);
  },
  { immediate: true }
);
```

Очистка побочных эффектов:

```ts
watch(source, (value, _, onCleanup) => {
  const controller = new AbortController();

  fetch(url, { signal: controller.signal });

  onCleanup(() => {
    controller.abort();
  });
});
```

Это критично для запросов, подписок, таймеров.

`watchEffect` — упрощённый вариант:

```ts
import { watchEffect } from 'vue';

watchEffect(() => {
  console.log(count.value);
});
```

Отличия от `watch`:  
– зависимости определяются автоматически;  
– нет старого значения;  
– выполняется сразу;  
– сложнее контролировать.

Когда использовать:  
– `computed` — для производных данных;  
– `watch` — для сайд-эффектов;  
– `watchEffect` — для простых реакций без сложных условий.

Типичная ошибка: использовать `watch` там, где нужен `computed`.

Плохо:

```ts
watch(a, () => {
  b.value = a.value * 2;
});
```
z
Хорошо:

```ts
const b = computed(() => a.value * 2);
```