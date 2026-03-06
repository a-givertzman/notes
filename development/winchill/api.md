### 1. Метод «Быстрый старт»: Info*Engine

Это встроенная в Windchill технология для обмена данными. Она использует **Webjects** (XML-подобные инструкции) для чтения данных.

* **Как это работает:** Ты создаешь задачу (Task), которая делает запрос к базе Windchill, формирует XML или JSON с твоей номенклатурой и отдает его внешней системе.
* **Плюс:** Не нужно писать сложный код на Java, достаточно сконфигурировать XML-шаблоны.
* **Для чего:** Идеально для разовых или регламентных выгрузок состава изделия (BOM).

### 2. Метод «Современный»: Windchill REST Services (WRS)

В версии 11.0 компания PTC начала активно развивать REST API.

* **Как это работает:** Соседняя система отправляет стандартный HTTP-запрос (GET) к Windchill и получает структуру проекта в формате JSON.
* **Плюс:** Это самый легкий способ для интеграции с веб-сервисами и современными базами данных.
* **Минус:** В версии 11.0 возможности REST API еще были ограничены по сравнению с версией 12.0+, но базовые вещи (получить атрибуты детали или структуру) сделать можно.

### 3. Метод «Событийный»: Слушатели (Listeners)

Чтобы получить «сигнал» о создании документа, тебе нужен **Service Listener** на стороне Windchill.

* **Как это работает:** Ты пишешь небольшой Java-класс, который «подписывается» на событие `POST_STORE` или `CHECKIN`. Как только документ создан, Windchill дергает твой код, а тот отправляет уведомление (Webhook) в соседнюю систему.
* **Для чего:** Это единственный надежный способ реализовать твой запрос «получить сигнал о создании документа» в реальном времени.

---

### Сравнение подходов для твоих задач

| Задача | Рекомендуемый инструмент | Сложность реализации |
| --- | --- | --- |
| **Выгрузка номенклатуры** | Info*Engine или SQL-представления | Низкая |
| **Дерево проекта (BOM)** | Windchill REST Services | Средняя |
| **Сигнал о новом документе** | Java Event Listener + Webhook | Выше среднего |

### С чего начать?

Самое простое для выгрузки дерева проекта — использовать **Report Builder** внутри самого Windchill.

1. Создаешь отчет (Query Builder), который собирает нужные данные (Имя, Номер, Версия, Ссылки).
2. Настраиваешь его экспорт.
3. Если нужно автоматизировать — вызываешь этот отчет через Info*Engine.


***
***

## API Request - Запрос номенклатуры и BOM

Открытый перечень API для Windchill 11.0 — вещь специфическая. В отличие от современных SaaS-сервисов, у которых документация лежит в открытом доступе (как у Stripe или GitHub), документация PTC официально доступна только пользователям с аккаунтом в **PTC Support Center**.

Однако, в самой системе Windchill 11.0 есть встроенный инструмент:

* Если зайти по адресу `http://<ваш-сервер-windchill>/Windchill/servlet/odata/$metadata`, вы увидите XML-описание доступных сущностей через REST.
* Для более новых подзаголовков 11-й версии есть Swagger-интерфейс: `http://<ваш-сервер-windchill>/Windchill/netmarkets/html/rest/index.html`.

Ниже приведены примеры того, как выглядят запросы и логика для ваших задач.

---

### 1. Получение номенклатуры (через REST API)

Чтобы получить список деталей (WTPart) с их атрибутами, используется GET-запрос.

**Запрос:**
`GET http://<server>/Windchill/servlet/odata/ProdMgmt/Parts?$filter=State/Value eq 'RELEASED'&$top=100`

**Пример ответа (JSON):**

```json
{
  "value": [
    {
      "Number": "0000001234",
      "Name": "Вал приводной",
      "State": "Выпущено",
      "Version": "A.1",
      "Identity": "OR:wt.part.WTPart:123456"
    }
  ]
}

```

---

### 2. Получение дерева проекта (BOM / Structure)

Для получения структуры изделия (кто во что входит) используется навигация по связям.

**Запрос (получить состав конкретной детали по её ID):**
`GET http://<server>/Windchill/servlet/odata/ProdMgmt/Parts('OR:wt.part.WTPart:123456')/Uses`

Этот запрос вернет список всех дочерних компонентов первого уровня. Если нужно всё дерево целиком, обычно пишут рекурсивный скрипт на стороне "соседней системы" или используют Info*Engine задачу `Expand-Structure`.

---

### 3. Как «слушать» события (Java Event Listener)

В Windchill нет встроенных Webhooks (как в GitHub), которые можно настроить через админку. Чтобы получить сигнал о создании документа, нужно написать небольшой Java-класс и зарегистрировать его в системе.

**Логика кода на Java:**

```java
public class MyDocumentListener extends ServiceEventListenerAdapter {
    @Override
    public void notifyVetoableEvent(Object event) throws Exception {
        if (event instanceof KeyedEvent) {
            KeyedEvent keyedEvent = (KeyedEvent) event;
            Object target = keyedEvent.getEventTarget();
            String type = keyedEvent.getEventType();

            // Проверяем, что это создание документа (WTDocument)
            if (target instanceof WTDocument && type.equals(PersistenceManagerEvent.POST_STORE)) {
                WTDocument doc = (WTDocument) target;
                
                // Здесь вызываем ваш внешний API
                sendSignalToExternalSystem(doc.getNumber(), doc.getName());
            }
        }
    }
}

```

**Как это работает:**

1. Вы компилируете этот код в `.class`.
2. Регистрируете его в файле `service.properties`.
3. После перезагрузки Windchill, при каждом создании документа ваш код будет срабатывать и «пинговать» вашу соседнюю систему.

---

### Что важно учитывать:

1. **Авторизация:** Windchill использует Basic Auth (логин:пароль в base64) или форму сессии.
2. **Формат ID:** В запросах часто используются строки вида `OR:wt.part.WTPart:XXXXX`. Это внутренние идентификаторы объектов.
3. **Версии:** В Windchill 11.0 REST API (OData) был ограниченным. Если нужных полей нет в стандартном `ProdMgmt`, придется использовать **Info*Engine** (через SOAP/XML запросы), который умеет вытаскивать абсолютно всё.

***
***

Работа с **Info*Engine** в Windchill 11.0 — это «путь воина», но он самый надежный, когда стандартного REST API не хватает. Info*Engine использует так называемые **Webjects** (XML-теги), которые сервер интерпретирует и превращает в SQL-запросы к базе данных или вызовы Java API.

### 1. Как слушать события через Info*Engine?

Короткий ответ: **Напрямую — нет.** Info*Engine работает по модели «запрос-ответ» (Pull). Он не умеет сам «просыпаться», когда в системе что-то происходит. Для получения сигнала о создании документа вам всё равно понадобится **Java Event Listener** (как я описывал выше).

**Однако есть хитрость:** Ваш Java Listener может не содержать сложной логики, а просто вызывать конкретную задачу Info*Engine (Task), которая уже передаст данные в вашу систему. Либо вы можете раз в 5 минут делать запрос через Info*Engine: «Дай мне все документы, созданные за последние 5 минут».

---

### 2. Пример Info*Engine задачи (Task)

Задачи Info*Engine обычно хранятся в файлах с расширением `.xml` или `.jsp` в директории `tasks` вашего сервера Windchill.

#### А) Выгрузка всей номенклатуры (WTPart)

Этот вебджект найдет все детали определенного типа.

```xml
<%@page language="java" session="false" errorPage="error.jsp"%>
<%@taglib uri="http://www.ptc.com/infoengine/taglib/core" prefix="ie"%>

<ie:webject name="Query-Objects" type="OBJ">
    <ie:param name="INSTANCE" data="windchill.server.name"/> <ie:param name="TYPE" data="wt.part.WTPart"/>           <ie:param name="ATTRIBUTE" data="number,name,state,iterationInfo.identifier.iterationId"/> 
    <ie:param name="GROUP_OUT" data="allParts"/>            </ie:webject>

<ie:webject name="Display-Resource" type="GRP">
    <ie:param name="GROUP_IN" data="allParts"/>
</ie:webject>

```

#### Б) Выгрузка дерева проекта (BOM)

Для получения дерева используется вебджект `Expand-Structure`. Он умеет «раскрывать» сборку на нужную глубину.

```xml
<ie:webject name="Expand-Structure" type="OBJ">
    <ie:param name="INSTANCE" data="windchill.server.name"/>
    <ie:param name="OBID" data="OR:wt.part.WTPart:123456"/> <ie:param name="RELATIONSHIP" data="wt.part.WTPartUsageLink"/> <ie:param name="DIRECTION" data="uses"/>                <ie:param name="LEVELS" data="0"/>                      <ie:param name="ATTRIBUTE" data="number,name"/>
    <ie:param name="GROUP_OUT" data="bomStructure"/>
</ie:webject>

```

---

### 3. Как это вызвать из «соседней системы»?

Вам не нужно лезть в консоль сервера. Info*Engine задачи доступны по URL. Вы просто делаете HTTP POST запрос:

**URL:** `http://your-windchill/Windchill/servlet/RPCAdapter`
**Body:** В теле передается XML-запрос или параметры вызова конкретного `.xml` файла задачи.

### 4. Плюсы и минусы Info*Engine для вашей задачи

| Характеристика | Оценка | Комментарий |
| --- | --- | --- |
| **Гибкость** | ⭐⭐⭐⭐⭐ | Можно вытащить любой атрибут, даже самописный (IBA). |
| **Производительность** | ⭐⭐⭐⭐ | Работает быстрее, чем тяжелые Java-сервисы. |
| **Сложность** | ⭐⭐⭐ | Нужно разобраться в синтаксисе Webjects и схеме данных Windchill. |
| **Real-time** | ❌ | Только опрос (polling) по расписанию. |


***
***

## Info*Engine - Запрос «новых документов за период»

Самый «ленивый» (в хорошем смысле) и безопасный способ интеграции: вместо того чтобы внедряться в ядро Windchill с помощью Java, мы просто будем регулярно «опрашивать» его через **Info*Engine**.

Для этого нам понадобится вебджект `Query-Objects` с правильным фильтром по атрибуту `thePersistInfo.createStamp`.

### 1. Текст задачи Info*Engine (файл `get_recent_docs.xml`)

Создай файл (или попроси админа) в директории `<Windchill>/tasks/` со следующим содержимым:

```xml
<%@page language="java" session="false" errorPage="error.jsp"%>
<%@taglib uri="http://www.ptc.com/infoengine/taglib/core" prefix="ie"%>

<ie:webject name="Query-Objects" type="OBJ">
    <ie:param name="INSTANCE" data="windchill.server.name"/> 
    <ie:param name="TYPE" data="wt.doc.WTDocument"/>
    
    <ie:param name="WHERE" data="thePersistInfo.createStamp &gt;= '${@FORMBASE[0]fromDate[0]}'"/>
    
    <ie:param name="ATTRIBUTE" data="number,name,creator,thePersistInfo.createStamp,format.formatName"/>
    <ie:param name="GROUP_OUT" data="newDocuments"/>
</ie:webject>

<ie:webject name="Display-Resource" type="GRP">
    <ie:param name="GROUP_IN" data="newDocuments"/>
</ie:webject>

```

---

### 2. Как это вызвать из твоей системы (Python/JS/C#)

Твоя «соседняя система» должна раз в 5–10 минут делать обычный HTTP-запрос.

**Пример на Python (библиотека `requests`):**

```python
import requests

url = "http://your-windchill-server/Windchill/servlet/RPCAdapter"
auth = ('username', 'password')

# Дата, с которой мы хотим получить обновления
data = {
    'task': '/tasks/get_recent_docs.xml', # путь к файлу выше
    'fromDate': '2023-10-27 10:00:00'     # время последнего успешного опроса
}

response = requests.post(url, data=data, auth=auth)

if response.status_code == 200:
    print("Найдены новые документы:", response.text)

```

---

### 3. Нюансы, которые сэкономят тебе время

* **Часовые пояса:** Windchill хранит даты в БД в формате UTC. Убедись, что твоя внешняя система отправляет время, скорректированное под серверное, иначе рискуешь получить пустой список или дубли.
* **Формат вывода:** По умолчанию Info*Engine отдает XML. Если твоей системе удобнее JSON, в URL запроса добавь параметр `&accept=application/json`.
* **Идентификатор (OBID):** Чтобы построить прямую ссылку на документ в соседней системе, обязательно добавь в список атрибутов `obid`. Ссылка на документ в Windchill обычно выглядит так:
`http://server/Windchill/app/#ptc1/tcomp/infoPage?oid=<OBID>`

### 4. Что если нужно «Дерево + Новые детали» сразу?

Если ты хочешь за один запрос получать и структуру, и понимать, что в ней изменилось, можно объединить два вебджекта (`Expand-Structure` и `Query-Objects`) в один `.xml` файл. Тогда Windchill вернет тебе один большой пакет данных со всеми связями.
