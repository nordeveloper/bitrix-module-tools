#### [Главная страница](../README.md)

## Логирование информации
Основой функционала логирования является [Журнал событий](https://dev.1c-bitrix.ru/community/webdev/user/11948/blog/2647/). Он позволяет журналировать собщения различного характера.
Модуль WS\Tools упрощает данную процедуру.
Пример помещения текста в Журнал событий:

```php
<?php
$log = $toolsModule->getLog("MY_CUSTOM_TYPE");
$log->severity(\WS\Tools\Log::SEVERITY_INFO)
    ->itemId(15)
    ->description("Обработка добавления элемента каталога в корзину")
    ->put();
```

Таким образом в примере выше в Журнал событий была добавлена запись произвольного типа "MY_CUSTOM_TYPE", с описанием "Обработка добавления элемента каталога в корзину", идентификатор элемента 15.

Теперь по поядку использование методов и их параметров:

* метод `getLog($type)` фасада модуля, осуществляет доступ к новому экземпляру класса логирования, где параметр `$type` определяет тип сообщения, к примеру для работы с добавлением в корзину элемента можно указать тип "ELEMENT_BASKET_ADD", его название произвольно в приложении. Зарание подготовленные типы доступны в константах `WS\Tools\Log::AUDIT_TYPE_..`
* метод `severity($value)` экземпляра логирования, задание уровня сообщения. Все уровни доступны через константы `WS\Tools\Log::SEVERITY_..`
* существуют вспомогательные методы более простого определения уровня сообщения:
+ `severityAsSecurity()` определяет сообщение как сообщение уровня безопасности. 
+ `severityAsError()` определяет сообщение как сообщение уровня ошибки. 
+ `severityAsWarning()` определяет сообщение как сообщение уровня предупреждения. 
+ `severityAsInfo()` определяет сообщение как сообщение информационного уровня. 
+ `severityAsDebug()` определяет сообщение как сообщение уровня отладки.
* `itemId($value)` устанавлевает идентификатор логируемого объекта, необязательный. Удобно использовать когда необходимо в журнале событий найти объект по идентификатору.
* `description($value)` установка описания записи, `$value` может принимать значения скаляра, массива или объекта
* `put()` отправляет сформированный объект на запись в Журнал событий

Функционал логирования поддерживает типы событий уровня ядра, например:

```php
<?php
$log = $toolsModule->getLog(\WS\Tools\Log::AUDIT_TYPE_IBLOCK_EDIT);
$log->severity(\WS\Tools\Log::SEVERITY_INFO)
    ->itemId(15)
    ->description("Обработка редактирования элемента инфоблока при вызове некого компонента")
    ->put();
```

Это означает то, что в Журнале событий колонки ITEM_ID появится запись элемента `15` со ссылкой на редактирование, а вместо кода `IBLOCK_EDIT` , будет сообщение `Изменен инфоблок`

Для получения описания типа сообщения существует событие 1С-Битрикс "OnEventLogGetAuditTypes" модуля "main". Обработчик должен фернуть массив пар ключ значение, где ключом будет являться код типа, а значением его описание. Можно также воспользоваться менеджером событий `WS\Toools`

```php
<?php
$eventManager = $toolsModule->eventManager();
$eventType = \WS\Tools\Events\EventType::create(\WS\Tools\Events\EventType::MAIN_EVENT_LOG_GET_AUDIT_TYPES);
$eventManager->subscribe($eventType, function () {
    return array(
        'MY_CUSTOM_TYPE' => "Пользовательский тип собщения"
    );
});
```

Пример, добавление сообщения об ошибке:

```php
<?php
// объект журналирования
$log = $toolsModule->getLog("WS_PROJECT_DEBUG_ERRORS");
// уровень сообщения
$log->severity(\WS\Tools\Log::SEVERITY_ERROR)
    // описание сообщения
    ->description("Обработка обработки почтового отправления, код {$postCode}")
    // сохранение сообщения
    ->put();
```

#### [Главная страница](../README.md)
