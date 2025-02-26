[![Build status](https://ci.appveyor.com/api/projects/status/4lnnixn97gvsjnyo?svg=true)](https://ci.appveyor.com/project/safaleks/homework-selenium)

# Домашнее задание к занятию «2.1. Тестирование веб-интерфейсов»
## Настройка
    
### 1. Целевой сервис

Ваш целевой сервис (SUT — System under test) расположен в файле `app-order.jar` в этом репозитории. Вам нужно его скачать и положить в каталог `artifacts` вашего проекта.

Поскольку файлы с расширением `.jar` находят в списках `.gitignore`, вам нужно принудительно заставить Git следить за ним: `git add -f artifacts/app-order.jar`.

После чего сделать `git push`. Обязательно удостоверьтесь, что файл попал в репозиторий.

### 2. `build.gradle`

Ваш `build.gradle` должен выглядеть следующим образом:

```groovy
plugins {
    id 'java'
}

group 'ru.netology'
version '1.0-SNAPSHOT'

sourceCompatibility = 11

// кодировка файлов (если используете русский язык в файлах)
compileJava.options.encoding = "UTF-8"
compileTestJava.options.encoding = "UTF-8"

repositories {
    mavenCentral()
}

dependencies {
    testImplementation 'org.junit.jupiter:junit-jupiter:5.6.1'
    testImplementation 'com.codeborne:selenide:5.19.0'
}

test {
    useJUnitPlatform()
    // в тестах, вызывая `gradlew test -Dselenide.headless=true` будем передавать этот параметр в JVM (где его подтянет Selenide)
    systemProperty 'selenide.headless', System.getProperty('selenide.headless')
}
```

#### **Headless-режим браузера**

На серверах сборки чаще всего нет графического интерфейса, поэтому, запуская браузер в режиме **headless**, мы отключаем графический интерфейс. При этом все процессы браузера продолжают работать так же. 
При использовании **Selenium** этот режим настраивается непосредственно в коде во время инициализации драйвера, примеры инициализации ниже.

Детальнее можете почитать про headless:
- [Chrome](https://www.chromestatus.com/features/5678767817097216),
- [Gecko (Firefox)](https://developer.mozilla.org/en-US/docs/Mozilla/Firefox/Headless_mode).

Включение **headless**-режима при использовании **Selenium** необходимо реализовать в коде во время создания экземпляра WebDriver:  

```java
ChromeOptions options = new ChromeOptions();
options.addArguments("--disable-dev-shm-usage");
options.addArguments("--no-sandbox");
options.addArguments("--headless");
driver = new ChromeDriver(options);
```

Для **Selenide** **headless**-режим активируется при запуске тестов с определённым параметром:  
```
gradlew test -Dselenide.headless=true
```

#### **WebDriver для разных операционных систем**

Если вы выполняете работу с использованием **Selenium**, то будьте готовы, что ваша сборка может упасть из-за того, что у вас в репозитории WebDriver для одной ОС, например, для Windows, а в CI используется Linux. Для решения этой проблемы можно использовать библиотеку [Webdriver Manager](https://github.com/bonigarcia/webdrivermanager). Она автоматически определяет ОС и версию браузера, скачивает и устанавливает подходящий файл драйвера. Кстати, в Selenide используется именно эта библиотека.

### 3. `.appveyor.yml`

AppVeyor настраивается аналогично предыдущей лекции, за исключением того, что тесты нужно запускать так, чтобы **Selenide** запускался в headless-режиме. Читайте выше о передаче специального флага при вводе команды запуска. Если тесты написаны с использованием **Selenium**, то после включения headless-режима в коде никаких дополнительных флагов в командной строке передавать не нужно.

## Задача №1: заказ карты

Вам необходимо автоматизировать тестирование формы заказа карты:

![](pic/order.png)

Требования к содержимому полей:
1. В поле фамилии и имени разрешены только русские буквы, дефисы и пробелы.
2. В поле телефона — только 11 цифр, символ + на первом месте.
3. Флажок согласия должен быть выставлен.

Тестируемая функциональность: отправка формы.

Условия: если все поля заполнены корректно, то вы получаете сообщение об успешно отправленной заявке:

![](pic/success.jpg)

Вам необходимо самостоятельно изучить элементы на странице, чтобы подобрать правильные селекторы.

<details>
    <summary>Подсказка</summary>

    Смотрите на `data-test-id` и внутри него ищите нужный вам `input` — используйте вложенность для селекторов.
</details>

Проект с автотестами должен быть выполнен на базе Gradle с использованием Selenide или Selenium по выбору студента.

Для запуска тестируемого приложения скачайте JAR-файл из текущего каталога и запускайте его командой:
`java -jar app-order.jar`.

Приложение будет запущено на порту 9999.

Если по каким-то причинам порт 9999 на вашей машине используется другим приложением, используйте:

`java -jar app-order.jar -port=7777`

Убедиться, что приложение работает, вы можете, открыв в браузере страницу: http://localhost:9999.
///
