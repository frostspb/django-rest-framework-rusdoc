# Ответы

> В отличии от обычных объектов HttpResponse, объекты TemplateResponse передают детали контекста представления для того чтобы софрмировать ответ. Конечный результат высчитывается позже в процессе ответа, лишь когда это необходимо. 

— Документация Django

REST framework поддерживает согласование содержимого с помощью класса `Response`, благодарая которому возможно возрвращать по-разному генерируемая сущность, в зависимости от запросов клиентов.

Класс `Response` является производным от `SimpleTemplateResponse`. Объекты `Response` инициализируются с данными, которые должны состоять из нативных примитивов Python. Затем REST framework использует стандатрное  согласование содержимого для того, чтобы определить, каким образом сгенерировать содержание конечного ответа.

Совсем не обязательно использовать класс `Response` - если требуется, вы можете возвращать обычные HttpResponse или StreamingHttpResponse объекты из ваших представлений. Использование класса `Response` всего лишь предоставляет лучший интерфейс для возвращения ответов Web API, связяанных с согласованием содержимого, которое может быть сгенерированно в разные форматы.

Если только вы не собрались основательно изменить REST framework, всегда следует использовать класс `APIView` или функцию `@api_view` для представлений, которые возвращают объекты `Response`. Таким образом представление сможет согласовывать содержимое и генерировать нужную информацию для ответа перед тем, как он вернулся от представления.

# Создание запросов
## Response()
Сигнатура: Response(data, status=None, template_name=None, headers=None, content_type=None)

В отличие от обычных объектов `HttpResponse`, вы не создаете экземпляр `Response` объекта с генерируемой сущностью. Вместо этого вы передаете ему неотрендеренные данные, который могут состоять из примитивов Python.
Рендеры, которые используюся классом `Response` не могут нативно обрабатывать сложные типы данных, такие как экземпляры моделей Django, поэтому перед созданием объекта `Response` вам следует преобразовать данные в более простой тип. 
Для этих целей вы можете воспользоваться классами `Serializer`REST framework, или своими собственным преобразователем.

Аргументы:

* `data`: Сериализированные данные для ответа.
* `status`: Код состояния для ответа. По умолчанию 200. См также коды состояния.
* `template_name`: Имя шаблона, которое используется в случае, если выбран `HTMLRenderer`.
* `headers`: Словарь HTTP заголовков, использующихся в ответах.
* `content_type`: Тип содержания ответа. Как правило автоматически устанавливается через согласование содержимого, но могут возникнуть случаи, когда потребуется особо уточнить тип сущности.
# Атрибуты
## .data
Неотрендеренные, непреобразованные данные ответа.

## .status_code
Цифровой код состояния HTTP ответа.

## .content
Отрендеренная сущность ответа. Метод `.render()` должен быть вызван до обращения к `.content`.


## .template_name
Имя шаблона, при наличии такового.Требуется только если HTMLRenderer, или другой пользовательский рендер шаблонов, отвечает за рендер ответа.

## .accepted_renderer
Экземпляр рендера, который будет использоваться для рендера ответа.
Устанавливается автоматически через `APIView` или `@api_view` сразу после получения ответа от представления. 


## .accepted_media_type
Тип данных, выбранный во вреся согласования содержимого.
Устанавливается автоматически через `APIView` или `@api_view` сразу после получения ответа от представления. 


## .renderer_context
Словарь дополнительной контекстной информации, который передается методу рендера `.render()`.
Устанавливается автоматически через `APIView` или `@api_view` сразу после получения ответа от представления. 

# Стандартные атрибуты HttpResponse
Класс `Response` расширяет `SimpleTemplateResponse`, при этом все обычные атрибуты и методы также доступны. Например можно оформить заголовки ответа стандартным способом:

```python
response = Response()
response['Cache-Control'] = 'no-cache'
```
## .render()
**Сигнатруа**: `.render()`
Как и в случае с другими методами TemplateResponse, этот метод вызывается для того, чтобы срендерить преобразованные данные ответа в конечную сущность. Когда вызывается `.render()`, сущность ответа будет сводиться к результату вызова метода `.render(data, accepted_media_type, renderer_context)` экземпляра `accepted_renderer`.
Как правило вам не неужно самим обращаться к `.render()`, так как он вшит в цикл ответов Django по умолчанию.
