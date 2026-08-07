**AJAX** — подход к обмену данными между браузером и сервером без полной перезагрузки страницы. JavaScript отправляет HTTP-запрос в фоне, получает ответ и обновляет необходимую часть [[Понятие DOM|DOM]].

Название расшифровывается как **Asynchronous JavaScript and XML**, но XML не является обязательным. Современные приложения чаще передают JSON, а также текст, HTML, `FormData`, файлы и двоичные данные.

AJAX — не отдельный протокол и не библиотека. Это сочетание:
- JavaScript;
- HTTP;
- браузерного API для выполнения запросов;
- асинхронной обработки результата;
- изменения интерфейса через DOM.

Общая последовательность:

```text
действие пользователя
        ↓
JavaScript создаёт HTTP-запрос
        ↓
браузер отправляет запрос, не перезагружая документ
        ↓
сервер обрабатывает запрос и возвращает ответ
        ↓
JavaScript получает результат
        ↓
обновляется нужная часть DOM
```

Пока запрос выполняется, основной поток JavaScript не должен блокироваться ожиданием сети. Браузер выполняет сетевую операцию вне стека JavaScript, а продолжение, связанное с `Promise`, планируется после получения результата.

AJAX позволяет реализовать:
- поиск с подсказками;
- подгрузку ленты и пагинацию;
- отправку формы без перехода на новую страницу;
- обновление корзины;
- получение уведомлений;
- сохранение черновика;
- загрузку и отправку файлов;
- обновление отдельного компонента интерфейса.

### HTTP-запрос и ответ

AJAX использует обычный HTTP. Запрос может содержать:
- URL;
- метод;
- заголовки;
- параметры строки запроса;
- тело;
- cookies и другие учётные данные, если это разрешено настройками запроса.

Основные методы:
- `GET` — получить ресурс;
- `POST` — создать ресурс или выполнить операцию;
- `PUT` — полностью заменить представление ресурса;
- `PATCH` — частично изменить ресурс;
- `DELETE` — удалить ресурс.

Ответ содержит:
- код состояния, например `200`, `201`, `204`, `400`, `401`, `404`, `500`;
- заголовки;
- необязательное тело ответа.

Метод и URL сами по себе не определяют формат данных. О формате тела обычно сообщают заголовки:

```http
Content-Type: application/json
Accept: application/json
```

### Fetch API

Современный основной API для AJAX-запросов — `fetch()`. Он возвращает `Promise<Response>`.

Простой GET-запрос:

```javascript
async function loadUsers() {
  const response = await fetch('/api/users');

  if (!response.ok) {
    throw new Error(`HTTP error: ${response.status}`);
  }

  const users = await response.json();
  return users;
}
```

`await fetch()` завершается после получения статуса и заголовков ответа. Чтение тела выполняется отдельно и тоже асинхронно:

```javascript
const data = await response.json();
```

Тело можно прочитать разными способами:
- `response.json()` — JSON;
- `response.text()` — текст или HTML;
- `response.blob()` — файл или другой двоичный объект;
- `response.arrayBuffer()` — необработанные байты;
- `response.formData()` — данные формы.

Обычно тело ответа можно прочитать только один раз, поскольку оно представлено потоком. Для повторного чтения необходимо заранее клонировать `Response` или сохранить уже разобранные данные.

GET-запрос с параметрами:

```javascript
const params = new URLSearchParams({
  query: 'javascript',
  page: '2'
});

const response = await fetch(`/api/search?${params}`);
```

Параметры GET передаются в URL. Тело для обычного GET-запроса не используется.

POST-запрос с JSON:

```javascript
async function createUser(user) {
  const response = await fetch('/api/users', {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
      'Accept': 'application/json'
    },
    body: JSON.stringify(user)
  });

  if (!response.ok) {
    throw new Error(`HTTP error: ${response.status}`);
  }

  return response.json();
}
```

`fetch` не преобразует объект JavaScript в JSON автоматически. Для этого используется `JSON.stringify()`. Ответ также необходимо явно разобрать через `response.json()`.

Отправка формы и файла:

```javascript
const form = document.querySelector('#profile-form');
const formData = new FormData(form);

const response = await fetch('/api/profile', {
  method: 'POST',
  body: formData
});
```

При передаче `FormData` не нужно самостоятельно задавать `Content-Type`: браузер добавит `multipart/form-data` вместе с необходимой границей частей.

### Обработка результата в интерфейсе

Запрос должен иметь состояния загрузки, успешного результата и ошибки:

```javascript
const status = document.querySelector('#status');
const list = document.querySelector('#users');

async function renderUsers() {
  status.textContent = 'Загрузка...';

  try {
    const users = await loadUsers();

    list.replaceChildren(
      ...users.map((user) => {
        const item = document.createElement('li');
        item.textContent = user.name;
        return item;
      })
    );

    status.textContent = '';
  } catch (error) {
    status.textContent = 'Не удалось загрузить пользователей';
    console.error(error);
  }
}
```

Для данных с сервера предпочтительно создавать элементы и задавать `textContent`. Вставка непроверенных данных через `innerHTML` может привести к XSS.

### Ошибки fetch

`fetch()` отклоняет `Promise` при сетевой ошибке, отмене запроса или невозможности выполнить запрос. HTTP-ответы `404` и `500` сами по себе не отклоняют `Promise`.

Поэтому нужны две проверки:
- `try/catch` — сетевые ошибки и исключения обработки;
- `response.ok` или `response.status` — HTTP-ошибки.

```javascript
try {
  const response = await fetch('/api/data');

  if (!response.ok) {
    const message = await response.text();
    throw new Error(`${response.status}: ${message}`);
  }

  const data = await response.json();
} catch (error) {
  console.error('Request failed:', error);
}
```

`response.ok` равен `true` для кодов от `200` до `299`.

В реальном приложении полезно различать:
- ошибку валидации `400` или `422`;
- отсутствие аутентификации `401`;
- недостаток прав `403`;
- отсутствие ресурса `404`;
- конфликт состояния `409`;
- ограничение частоты запросов `429`;
- серверную ошибку `5xx`;
- сетевую ошибку и отсутствие соединения.

### Отмена запроса

Запрос можно отменить через `AbortController`:

```javascript
const controller = new AbortController();

async function loadData() {
  try {
    const response = await fetch('/api/data', {
      signal: controller.signal
    });

    return await response.json();
  } catch (error) {
    if (error.name === 'AbortError') {
      console.log('Запрос отменён');
      return;
    }

    throw error;
  }
}

controller.abort();
```

Отмена полезна, когда:
- пользователь покинул компонент;
- отправлен новый поисковый запрос, делающий старый ненужным;
- превышено допустимое время ожидания;
- закрыто окно или диалог.

Тайм-аут можно реализовать сигналом отмены:

```javascript
const response = await fetch('/api/data', {
  signal: AbortSignal.timeout(5000)
});
```

Даже если клиент отменил ожидание, сервер мог уже получить запрос и выполнить изменение. Отмена на клиенте не является откатом серверной операции.

### Гонки запросов

Асинхронные ответы могут прийти не в порядке отправки. Например, пользователь вводит `java`, затем `javascript`, но ответ для первого запроса приходит позже и перезаписывает новый результат.

Один способ решения — отменять предыдущий запрос:

```javascript
let activeController;

async function search(query) {
  activeController?.abort();
  activeController = new AbortController();

  const response = await fetch(
    `/api/search?query=${encodeURIComponent(query)}`,
    { signal: activeController.signal }
  );

  if (!response.ok) {
    throw new Error(`HTTP error: ${response.status}`);
  }

  return response.json();
}
```

Также применяют:
- номер последнего запроса;
- проверку актуальности параметров перед обновлением DOM;
- debounce для уменьшения числа запросов при вводе;
- библиотеки управления серверным состоянием и кэшем.

### XMLHttpRequest

До `fetch` основным AJAX API был `XMLHttpRequest`, или XHR:

```javascript
const xhr = new XMLHttpRequest();
xhr.open('GET', '/api/users');
xhr.responseType = 'json';

xhr.addEventListener('load', () => {
  if (xhr.status >= 200 && xhr.status < 300) {
    console.log(xhr.response);
  } else {
    console.error(`HTTP error: ${xhr.status}`);
  }
});

xhr.addEventListener('error', () => {
  console.error('Network error');
});

xhr.send();
```

XHR основан на событиях и изменении состояния объекта. Он поддерживает:
- отслеживание прогресса загрузки;
- прогресс отправки через `xhr.upload`;
- отмену через `xhr.abort()`;
- настройку формата `responseType`;
- как асинхронный, так и устаревший синхронный режим.

Синхронные сетевые запросы в основном потоке блокируют интерфейс и не должны использоваться. Для нового кода обычно выбирают `fetch`, а XHR сохраняется в старых проектах и сценариях, где необходим привычный API прогресса отправки.

### Same-origin policy и CORS

Браузер применяет **политику одинакового источника**. Источник определяется тройкой:

```text
схема + хост + порт
```

Например, источники различаются:
- `https://example.com` и `http://example.com` — разные схемы;
- `https://api.example.com` и `https://example.com` — разные хосты;
- `https://example.com:443` и `https://example.com:8080` — разные порты.

Чтение ответа от другого источника разрешается механизмом **CORS**, если сервер отправляет подходящие заголовки:

```http
Access-Control-Allow-Origin: https://client.example
```

Для некоторых межсайтовых запросов браузер сначала отправляет предварительный запрос `OPTIONS`, или **preflight**. Он проверяет, разрешает ли сервер:
- исходный origin;
- HTTP-метод;
- нестандартные заголовки.

CORS настраивается сервером. Клиент не может исправить отсутствие разрешающих заголовков установкой `mode: 'no-cors'`. В режиме `no-cors` ответ становится непрозрачным: JavaScript не может прочитать его статус, заголовки и тело.

### Cookies, credentials и безопасность

Для запросов к своему origin cookies обычно учитываются автоматически. Для передачи учётных данных в CORS-запросе может потребоваться:

```javascript
fetch('https://api.example.com/profile', {
  credentials: 'include'
});
```

Сервер при этом должен явно разрешить origin и credentials. Значение `Access-Control-Allow-Origin: *` нельзя использовать для CORS-ответа с учётными данными.

AJAX не отменяет обычные требования безопасности:
- сервер должен проверять аутентификацию и права;
- входные данные нельзя считать доверенными;
- изменение данных необходимо защищать от CSRF, если авторизация основана на cookies;
- токены и секретные ключи нельзя безопасно скрыть в клиентском JavaScript;
- передаваемые данные следует защищать HTTPS;
- содержимое ответа нельзя без проверки вставлять через `innerHTML`;
- CORS не является механизмом аутентификации и не запрещает сервер-серверные запросы.

### AJAX и связанные понятия

**AJAX и REST** — разные понятия:
- AJAX описывает способ фонового обмена данными в клиенте;
- REST описывает архитектурный стиль серверного API;
- AJAX может работать с REST, GraphQL, RPC и обычными серверными обработчиками.

**AJAX и SPA** также не равны:
- AJAX можно использовать на обычной многостраничной странице;
- SPA широко применяет фоновые запросы, но дополнительно управляет маршрутизацией и состоянием интерфейса;
- страница может обновлять отдельный блок через AJAX, оставаясь многостраничным приложением.

AJAX основан на модели «запрос — ответ». Для постоянного двустороннего соединения используют WebSocket, для потока событий от сервера — Server-Sent Events. Периодические AJAX-запросы к серверу называются polling.

### Практические правила

- Всегда проверять `response.ok` или конкретный статус.
- Показывать состояния загрузки, ошибки и пустого результата.
- Отменять устаревшие запросы.
- Защищаться от гонок ответов.
- Не отправлять запрос на каждый вводимый символ без debounce.
- Использовать подходящий HTTP-метод.
- Не задавать `Content-Type` вручную для `FormData`.
- Не вставлять недоверенный ответ через `innerHTML`.
- Обрабатывать повторную отправку формы и двойные клики.
- Учитывать кэширование HTTP вместо бессистемного добавления случайных параметров к URL.
- Разделять сетевой слой, состояние приложения и обновление DOM.
- Не считать отменённый клиентом запрос отменённой серверной операцией.
- Тестировать медленную сеть, отсутствие соединения, тайм-ауты и ответы `4xx`/`5xx`.

См. также [[Понятие DOM]], [[DOM навигация]] и [[Frontend]].
