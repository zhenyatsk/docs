# Рекомендации по использованию {{ yagpt-name }}

Модель {{ yagpt-name }} позволяет эффективно решать различные задачи, связанные с обработкой текстов. В этом разделе приведены некоторые рекомендации, которые помогут вам максимально эффективно использовать возможности {{ yagpt-name }}.

## Разделение сложных задач {#tasks-division}

Сложные задачи лучше разбивать на несколько простых, если это позволяет допустимое время обработки запроса и бюджет.

Например, вы решаете задачу переписывания неформального текста в более формальный. Ваше понимание «формального текста» и понимание модели отличаются. Чтобы получать текст в ожидаемом виде, вы передаете в промт длинный список критериев того, что такое «формальный текст». С увеличением критериев модель будет придавать все меньше значимости каждому из них, и точность результатов будет падать.

Однако как большинство задач реального мира, так и вашу текущую тоже, можно разложить на несколько простых:

1. Продумайте последовательные шаги решения вашей задачи.
1. Составьте промты для каждого шага.
1. Отправляйте их в модель последовательно.

Например, вы можете составить шесть критериев формального текста, трансформировать текст с вниманием к первым трем критериям, а затем повторить операцию с вниманием к оставшимся. Чем короче и лаконичнее промт, тем выше точность работы модели и меньше различий в ответах на похожие запросы.

## Приведение ответа к конкретным форматам {#formatting}

Если вам нужна высокая стабильность генерации текста в определенном формате, вы можете вынести переформатирование текста в дополнительный шаг. Например, если вам нужно преобразовать текст из [Markdown](https://ru.wikipedia.org/wiki/Markdown), можно действовать следующим образом:

1. Сначала переписать ответ модели еще раз.

   ```text
   Перепиши json ниже в правильный формат, если есть ошибки (например использование ```json или ``` — это нужно удалить). Не выводи ничего лишнего.
   ```

1. Очистить текст от артефактов программными методами.
Например, следующий скрипт на Python использует библиотеку `markdown2` и регулярное выражение для постобработки ответа {{ yagpt-name }}:

   ```python
   import markdown2
   import re

   markdown_text = """
   # Заголовок
   **Жирный текст**
   *Курсивный текст*
   [Ссылка](http://example.com)
   ![Изображение](http://example.com/image.jpg)
   `Код`
   - Список
   """
   html = markdown2.markdown(markdown_text)
   plain_text = re.sub(r'<[^>]+>', '', html)  # Удаление HTML-тегов
   print(plain_text)
   ```

## Указание длины выходного текста {#result-length}

Для задания длины выходного текста укажите примерную длину ответа в промте. Например:

```text
"Перепиши текст так, чтобы он был длиной около 1000 символов."
```

Также вы можете ограничить длину текста в [токенах](../concepts/yandexgpt/tokens.md). Для этого укажите параметр `max_tokens` в запросе к {{ yagpt-name }}.

## Использование классификаторов для улучшения алгоритма {#classifiers}

Если вам нужно добавить логику обработки запроса, похожую на маршрутизацию или отделение "правильных" запросов от "плохих", в этом случае можно использовать классификатор на базе {{ yagpt-name }}. Вы можете применять его на любом участке работы вашего алгоритма обработки запроса.

## Дообучение модели {#fine-tuning}

Для повышения точности работы в некоторых задачах можно [дообучить](../../datasphere/concepts/models/foundation-models.md) модель под конкретную задачу. Для этого вам будут необходимы данные. Вы можете создать их с помощью писателей, найти в готовые в интернете, создать синтетически или собрать с пользователей в процессе их взаимодействия с вашим сервисом.

Так, модель можно обучить:
* пересказывать и изменять формулировки текстов;
* генерировать вопросы к тексту и ответы на них;
* форматировать ответы в определенной стилистике или формате;
* классифицировать тексты, обращения и диалоги;
* извлекать сущности из текста.

Дообучение не поможет или несущественно увеличит точность результатов, если вы хотите:
* заложить в модель новые знания;
* улучшить или изменить знания модели;
* заложить в модель новую доменную область;
* заложить в модель понимание новых терминов;
* научить модель анализировать сложные технические данные.

Дообучение {{ yagpt-name }} изменяет только последний слой модели. О том, как дообучить модель, см. руководство [{#T}](../tutorials/yagpt-tuning.md).