# Golden selenide. My best testing practices.

### ДАННАЯ СТАТЬЯ СЫРОВАТА К ПРОЧТЕНИЮ, ЕСЛИ У ВАС ЕСТЬ ПРЕДЛОЖЕНИЯ, 
### PLEASE CONTACT ME ON <a href="https://t.me/dark_tulip">TELEGRAM</a>
## in progress...

Золотой селенид. Мои лучшие практики по тестированию. (в процессе)

!!! Внимание, автор не разделяет понятий чистый автоматизатор или мануальщик, он является Инженером, инженером по тестированию который отвечает за то и другое, являясь истинным QA !!!
Но упор пойдет по практикам автоматизации

> <b><i>Тестирование для меня как целая наука, имеющая множество направлений.</i></b>

### Что такое тест? 
Тест - это проверка сценария, expected === actual

### Какие тесты бывают?
- Юнит тесты (их пишут разработчики) 
- UI тесты (их пишу я, и мы остановимся на этом)

### Как я разделяю типы UI тестов
- Пользовательские сценарии (шаги в системе, приводящие к определенному резульату)
- Сочетания настроек (уместно применить - техники тест дизайна)

> <b><i>Тестировщик, программист или аналитик, вопрос, кто живет в доме? - я скажу тестировщик - любому автоматизатору придется там жить, и далее наша речь пойдет о локаторах...</i></b>

### Распределение локаторов
<b>Локатор</b> - адрес/путь веб элемента на странице
- храните в отдельном файле, так и назовите, Локаторы 
- отдельный файл с локаторами для каждой страницы - забудьте про это. Потому что вам запарится разделять локаторы по страницам, веб элемнеты состоят из идентичных форм, CSS классов, и один локатор часто будет повторяться на разных страницах приложения. Думаете что файл взорвется? Нет, благо в современном мире есть мощные IDE, упрощающих поиск. ~500 локаторов достаточно покрывают все потребности по нашему проекту

### Интуитивно понятные локаторы
Лучше всего использовать идентификаторы для каждого веб элемента. Либо писать интуитивно понятные локаторы. Прощупайте разницу если написать:
``` css
.top-header  # Какой то класс
div.top-header  # Блок, но таких блоков может быть много
input.top-header  # Ага, поле ввода
div.top-header.logo  # Ага, сразу понятно, логотип на вверхем хэдере
```

Длинный локатор не проблема когда они вынесены в отдельный файл с локаторами:
``` Java 
// WebElementLocators.java

public class ElementLocators {
  
  public static final String HEADER = "div.top-header-block";
  public static final String RED_COLOR = "rgba(248, 248, 248, 1)";
  ...
}
```
### Палитра на любой вкус. Проверка по цвету - check webElement Color
``` Java
$$(DANGER_BTN).shouldHave(size(1)).first()
        .shouldBe(visible)
        .shouldHave(cssValue("background-color", RED_COLOR), cssClass("danger-btn"));
```
тут
``` Java
Обратиться к:
$ - is SelenideElement (one WebElement) 
$$ - is ElementsCollection (many)
```
### Паттерны? не, не слыхал
Но возникает еще одна проблема, разгромождение локаторов в самих тест кейсах. Решение: POM, POM, POM...
Page Object Model - standart test automation pattern. Паттерн это набор практик, проверенных временем, и прошедших множество костылей. <b>Нельзя просто так взять и понять POM</b>. Напишите свою первую, вторую, третью сотку тестов (я внедрила после четырехсотки), и вы поймете что в них что то не так, придя к моменту изучите POM.

> <b><i>P.S. сначала нужно наступить на грабли, перед тем как поДнять их</i></b>

### Мне-тебе не до душе. Какой ЯП выбрать для автотестов?
Есть три популярнейших ЯП упрощающих жизнь автоматизаторам своимим фреймворками и широким комьюнити, 
- Python (Selenium, Unittest/Pytest), 
- Java (Selenide, testNG/JUnit), 
- C# (тут я незнаю что нужно для Дот Нетщиков)

Да, еще можно писать на чистом JS. Хотя считаю это прошлым веком, но понимать его особо необходимо.
На конференции QA от Kolesa Group узнала что там используют PHP :D

<b>Итог</b>: выбирайте язык на котором пишут разработчики. Будет у кого спросить в тупиковой ситуации. (Возможно язык на котором вы сейчас пишете содержит скелеты в шкафу)

### Из чего состоит проверка сценария?
Каждый тестируемый случай в моих автотестах состоит из
```
1. TEST ID (идентификатор теста)
2. Name (название)
3. Description (описание)
4. Steps to reproduce (шаги воспроизведения)
5. Expected result (ожидаемый результат)
6. Actual result (фактический результат)
```

Тут
- <b>Ожидаемый результат</b> - версия правильной работы, как и что должно быть
- <b>Фактический результат</b> - что случилось, на каком моменте сломалось, что произошло
- <b>Шаги воспроизведения</b> - самая нужная часть тест кейса, приводящая к конечному тестируемому случаю. Мастерство тестировщика в ясности и четкости (краткости) этих шагов. Не пренерегайте этим пунктом. Без этого тест кейс не тест кейс.
- <b>Описание</b> - слово само за себя, сюда можно уместить сжатую композицию шагов воспроизведения
- <b>Название</b> - название функции автотеста, упрощает поиск. Рекомендую использовать или заканчивать название глаголом. Something<b>Should</b>Exist. Something<b>Of</b>TestingCase. 
- <b>TEST ID</b> - использую для подсчета кол-ва написанных тестов

> ЛайфХак. Когда падает автотест, просто копируете тест кейс и вставляете в bug-тикет. Хорошо когда у разработчиков есть локальный проект тестирования, где они сами могут найти (по айдишке или названию функции) и запустить тест. Рядом пахнет STDD - selenide test driven development. В этом случае вы пишете тест до момента UI ошибки, передаете разработчикам. На самом конце можно поставить слип, и дальше тыкать браузер. Думаю это удобно. После фикса дописываете автотест и закрываете кейс.

TDD - это когда тесты написаны до реализации чего либо (функции, модуля, класса, объекта). Таким образом помогает собрать мысли и сконцентрироваться на задачи, понимая, что должен делать и какой результат должен давать код.

### Борьба с флоки тестами
Must have
1) Перезапустить тест. Самый оптимальный вариант при крайних сроках и жестких дедлайнах.
2) Стереть и переписать тест. Вероятность написать шаги воспроизделения теста немного иным способом высока. Следовательно тест может стать стабильнее. Также подумайте о лишних шагах.
3) Test retrier - повторитель тестов. Must have штука. Обязательно научитесь RetryAnalyzer-у (ссылочку позднее). Очень полезная штука, проигрывается N раз, пока тест не загорит зеленым или останется красным. Минусы: действительно упавший тест будет перезагружаться N раз (cуммарно займет N * times времени)
4) UI Trigger - некий UI webelement, настоящий delay триггер (триггер задержки) на странице, явно дающий понять что событие произошло. Наверняка большинство тестов имеют особенность падать при клике на определенный элемент. Проблема в том что не всегда ясно, завершило ли событие свое действие или имеет не тот атрибут. Например, загрузка файлов, с помощью всемогущего Селенида скачали файл, но как понять что файл был полностью передан с сервера? Поставить слипы на 10 сек - не вариант. Нужно явно настоять программистам установить спиннер или индикатор загрузки на web странице.
5) JS Injection - об этом напишу словечко. На практике не использовала. Можно внедрить веб элементу css-класс или изменить значение атрибута. Для особо отчаянных случаев.


Что такое веб элемнет? Это все блоки, точки, кнопки, div-ки из которых состоит веб страница. Откройте devtools (F12 для Chrome браузера) Все что имеет визуально-функциональный блок. Все это WEB ELEMENT-ы. Других названий не поощряю.


### Явные (explicit waits) VS Неявные (implicit waits) ожидания
- Явные ожидания - ждем хардкодно, программа спит и ничего не делает. Это тот самый `sleep(1_000); // msec`
- Неявные ожидания - ждет в течение заданного промежутка, каждый раз пингуя элемент. Не будет ждать окончания таймера, сразу пойдет дальше при положительном условии, тем самым экономим время тестов. 
``` Java
$(HEADER).shouldBe(exist, enabled, visible);

тут:
Отдельно вынесенный локатор:
public static String final HEADER = "div.main-header"

Состояния:
enabled - активный, в основном применяю для проверки кликабельных элементов
visible - элемент виден на странице
exist - элемент сущетсвует в DOM-е
```


### Complex Conditions
Селенид очень удобен написанием однострочников, которые на голом селениуме задромождают тестовый код.
Под капотом селенид ждет 4 сек, каждый раз пингуя элемент, активен он или нет. Эту конфигурацию можно установить через `Configuration.timeout=10_000;`. Это то самое неявное ожидание. 
``` Java
$(HEADER).shouldBe(exist, enabled, visible).shouldHave(attribute("title", "myTitle"), text("TEXT"));
```
Разница между `should, shouldBe, shouldHave` - абсолютно никакой. Используйте для визуально приятного восприятия (читабельности)

### Отключить выполнение скриптов

F12
Ctrl+Shift+P (Cmd+Shift+P)
disable JavaScript

# TODO

- Написать пример как я пишу тест на POM
- добавить больше short-selenide-tips (плюшек селенида)
- Написать обычный тест (без POM)
- Мантра автотестера 


### Базовый помощник. Hot keys for IDE

``` Java
# Переход между вкладками
ALT + (->, <-)

# Чтобы открыть любой файл
CTRL + SHIFT + N

# Find in files
CTRL + SHIFT + F

# Format current page
CTRL + ALT + O
CTRL + ALT + L
```
## Difference between isBlank, isEmpty
разница в проверке пробела:
```Java
StringUtils.isBlank(" ") = true
StringUtils.isEmpty(" ") = false
```
#### Колекция элементов, не входящих в заданный класс 
``` Java
ElementsCollection notCheckedCheckboxes = $$("div.checkboxes").filterBy(not(cssClass("checked")));
```
Для начинающих, коллекцию элементов воспринимайте как обычный массив

#### Когда чекбокс включен, но выглядит как readonly
``` Java  
$("input:checked").should(exist)
```
#### Useful webElement Locator generators 
полезно и очень удобно использовать функции генераторы для составления локаторов, вот некоторые примеры
``` Java
  public static String insertIntoTitleStartsWith(String textInTitle) {
    return String.format("*[title^='%s']", textInTitle);  // ^ нужно для поиска элементов, которые содержат атрибут начинающийся с переданного текста
  }

  public static String insertIntoTitle(String textInTitle) {
    return String.format("*[title='%s']", textInTitle);
  }

  public static String insertIntoAttribute(String attribute, String value) {
    return String.format("*[%s='%s']", attribute, value);
  }
```

#### Настройка гитлаб раннера (касточный) на локальном сервере


# ИНСТРУКЦИЯ ПО ПЕРВОМУ КОММИТУ  
1) Сначала клонируем репозиторий (репо - это удаленный проект над которым мы все работаем)
```
git clone https://github.com/dark-tulip/aknbt
```
Дождитесь пока IDEA установит за нас все необходимые плагины и зависимости - 
справа нажмите на Gradle (cлоник) - это сборщик проектов, вам ничего не нужно будет устанавливать, IDEA все сделает за вас
(Reload All Projects)

2) Внесите изменения в РИДМИ файл, затем отправьте изменения, для этого
```
git pull (применить все изменения, когда кто-то что-то запушил)

git add . (точка чтобы подготовить (добавить) все файлы для коммита)
git commit -m "Что вы добавили"
git push (отправить изменнения)
```

можно запушить без никаких комманд
```
1) В правом верхнем углу зеленая галочка Commit
2) В окне выбираем файлы для публикации
3) Снизу пишем сообщение - какое изменение добавили в проект (Commit Message)
4) Commit and Push
5) Ничего не трогаем, еще раз нажимаем пуш
```

Merge - объединение проектов (например слить с dev на master ветку)
Сherry pick - вынуть изменения (некоторые коммиты из другой ветки)
Revert - сбросить все что вы написали до посленего pull-a
