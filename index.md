---
marp: true
theme: gaia
size: 4K
class: default
paginate: true
footer: @asm0di0 at Twitter&emsp13;&emsp13;@asm0dey at Telegram&emsp13;&emsp13;#MoscowPythonConf
---
<!--
_backgroundImage: "linear-gradient(to bottom, #000 0%, #1a2028 50%, #293845 100%)"
_class: lead
_color: white
_paginate: false
_footer: ""
-->

<style>
footer {
    display: table
}
</style>

# Как Java-роботы видят Python

Паша Финкельштейн, Lamoda

---
<!--
_backgroundImage: "linear-gradient(to bottom, #000 0%, #1a2028 50%, #293845 100%)"
_class: lead invert
_color: white
-->

# Кто я

- 13 лет в IT
- 11 лет в разработке
  - Почти всё время на JVM
- Полгода :scream: в Python

---
<!-- _class:  -->

![bg](https://source.unsplash.com/XECZHb6NoFo)

---
<!--
_backgroundImage: "linear-gradient(to bottom, #000 0%, #1a2028 50%, #293845 100%)"
_class: lead invert
_color: white
-->

# Я :heart: Python

---
<!--
_backgroundImage: "linear-gradient(to bottom, #000 0%, #1a2028 50%, #293845 100%)"
_class: lead invert
_color: white
-->


# <!-- fit --> Python :snake::heartpulse: меня

---

![bg left:33% fit](images/MVC.png)

# <!-- fit --> Как всё устроено в Java

- Скучно
- Все всё понимают

---
<!--
_class:
-->

![bg right:40% fit](images/MVC2.png)

# Сложная логика?

- Всё ещё скучно
- Слишком просто

---
<!-- _class: lead -->

![bg right:40% fit](images/MVC2.png)

# Фичефлаги?

* Ну вы поняли

---

<style scoped>
section img{
    position: absolute;
    height: 80%;
    right: 15px;
    bottom: 0;
}
</style>

![bg left:30% fit](images/VM.png)

# Django

- А где логика?

![drop-shadow](images/7VE.gif)

---
<!-- _class: lead -->

![bg fit](https://cdn.sstatic.net/Sites/stackoverflow/company/img/logos/so/so-logo.svg?v=a010291124bf)


---
<!-- _class: lead -->

![drop-shadow height:250px](images/forms.png)

![drop-shadow height:250px](images/template.png)

---

![bg left:33%](https://source.unsplash.com/71CjSSB83Wo)

# Звонок другу!

https://phalt.github.io/django-api-domains/

```python
def get_book(*, id: uuid.UUID) -> Book:
    book = Book.objects.get(id=id)
    author = AuthorInterface.get_author(id=book.author_id)
    return {
        'name': book.name,
        'author_name': author.name,
    }
```

[django-best-practices/Make'em Fat](https://django-best-practices.readthedocs.io/en/latest/applications.html#make-em-fat)

---

![bg](https://source.unsplash.com/r-enAOPw8Rs)

---
<!-- _class: lead -->

![bg left:33% fit](images/VM2.png)

# Где логика, Джанго?

![height:400px drop-shadow](https://vignette.wikia.nocookie.net/djangounchained/images/a/a4/Django.jpg/revision/latest?cb=20120601224011)

---

# <!-- fit --> Серьёзные ребята знают как делать правильно!

[github.com/mdn/kuma/blob/master/kuma/authkeys/models.py](https://github.com/mdn/kuma/blob/master/kuma/authkeys/models.py)

```python
from django.utils.translation import ugettext_lazy as _

def generate_key():
    """Generate a random API key."""
    ...
```

---

## Продолжаем разбирать

```python
class Key(models.Model):
    """Authentication key"""
    ...

    
    def generate_secret(self):
        self.key = generate_key()
        secret = generate_key()
        self.hashed_secret = hash_secret(secret)
        return secret
```

---

# А что не так-то?

* А что если нам понадобится поменять алгоритм шифрования?
* Логгирование прямо в модели?
* Сложную логику создать невозможно
* Транзакциями управлять тоже невозможно

---

# В Spring

```java
// View
@Controller class MyController {
    @Inject MyService service;
    @GetRequest("/deal") UUID deal(@Valid Deal deal){
        service.saveDeal(deal);
```

---

# В Spring

```java
// Controller
@Transactional
@Service class MyService {
    @Inject Repo1 repo1; @Inject Repo2 repo2;
    @Inject Repo3 repo3; @Inject Repo4 repo4;
    Result<UUID> deal(Deal deal){
        if(/* check */) {}
        else(/* check */) {
            repo1.save(/* */);
            repo2.save(/* */);
        }
        return /**/;
```
---

# В Spring

```java
// Model
@Repository class MyService {
    @Inject Datasource ds;
    UUID save(Deal deal){
        try(var con = ds.getConnection()){
            var stmt = con.createStatement("INSERT INTO … RETURNING");
            stmt.fetchResult();
            return /* */;
```

---

# На что обратить внимание

- DI
- Разделение ответственности
- `Result<UUID>`

---

# Если вы уже :heart_eyes: DI

[dry-python/dependencies](https://github.com/dry-python/dependencies)

```python
class MyController(Injector):
    my_service = MyService

class MyService(Injector):
    repo1 = Repo1
    repo2 = Repo2

class Repo1(Injector):
    data_source = DataSource
```
---
<!-- _class: lead -->

# Если вы уже :heart_eyes: `Result<UUID>`

[dry-python/returns](https://github.com/dry-python/returns)

---
<!-- _class: lead -->

# <!-- fit --> Сложно управлять транзакциями?

![drop-shadow height:480px](https://www.plantuml.com/plantuml/svg/ZPBFIiD04CRl-nJZ0uZOzjI35Znw43nuBtGFXRMHDEBL9AANKfz1V84WMHjRNLzXvetyTi6aVuWra0spC_ERcMyoc2R3EBczDafTZVKT7PxGMJH9uiWO7VU9NxXaoojUSg4QntQOP5pnDqakJrn82lCB5yWXIMj0fOOc8Nc0Pu6BIXxPASApoRtKDz5ngEmGLuANoOnu9VD0-WWf8MYxY_6e1TVPmJLjILu3E_y2UfdwT76kj9bgahdSxnccY-glwu5G9UTtjiJMQxNH3XUdTJ_TdMsW_ID8QoKBGK7FjTGQ6BnjhNi0v9pnGfu9o_ZqF2-n1wVWkE35ju4xNXAE4Z6Etp29zMcmO1-4QbwDbh9-bjx-5VoEtMjPmn-h20rCHig_Qe0JF_GF)

---
<!--_class: lead -->
# Транзакции — это не пара `BEGIN`-`COMMIT` а бизнес-сущность

---
# В джаве стандартизировано ВСЁ

* Транзакции - JTA
  * JMS - работа с message-брокерами
  * JDBC - работа с БД
* Java Servlet API — работа с сетью
* JSP, JSF — стандарты бэкэнд-рендеринга страниц
* JAR — формат упаковки приложений ))

И это надёжная основа

---
# На слои поделили

Чтобы не сломать можно использовать [seddonym/import-linter](https://github.com/seddonym/import-linter)

```ini
[importlinter]
root_package = mypackage

[importlinter:contract:1]
name = My three-tier layers contract
type = layers
layers=
    mypackage.high
    mypackage.medium
    mypackage.low
```
---
# Но не будем о грустном

### Поговорим об исключениях

* Или это тоже грустное?

* В Python все исключения unchecked: хрен поймаешь.
* В Java можно поделить
  * Но множество «важных» исключений — checked: хрен откажешься ловить

---
И что?

```java
void someFun(){
    try {
        instance.call(payload);
    } catch (IOException e){
        logger.info("Can't write to FS");
    } catch (UnknownUserException e){
        logger.info("Who am I?")
    }
    // etc, etc…
}
```

---
<!-- _class: lead -->

![bg left:33%](https://source.unsplash.com/YRgPxwbvY0E)

# И только Kotlin :heart:

Всё unchecked, но если **очень надо™** — то есть аннотация `@Throws`

---
<!-- _class: lead invert -->
![bg](https://source.unsplash.com/p1SKuYXxqec)
# <!-- fit --> Экосистема

---
# Я долго ругал django

Но есть и хорошие части!

* Комьюнити огромно!
* Django ORM тупит? [django-perf-rec](https://pypi.org/project/django-perf-rec/)
    ```python
    def test_home(self):
        with django_perf_rec.record():
            self.client.get('/')
    ```
    записывает все запросы в `.yml` файл
* [django-stubs](https://pypi.org/project/django-stubs/) Добавляет типы в django! :heart_eyes:

---

![bg left:40%](https://conf.python.ru/uploads/5/df/a46de47948a30276501e77df56623-fit-265x265-0.jpg)

# Сегодня!

Виталий Брагилевский о типах :+1:

---

# Все понимают что типы нужны

- `mypy`
- [dry-python/returns](https://github.com/dry-python/returns)
- [wemake-python-styleguide](https://github.com/wemake-services/wemake-python-styleguide)

---
## Что не так с PyPI

* Зависимость добавляется так: ![](images/pypi.png)
* А куда она добавится?
* А что подтянется вместе с ней?
* А конфликтует ли она с моими зависимостями?
* Совместима ли она с моей версией Python? Как добавить старую версию?
* Безопасна ли она?

---
# Безопасна?

Если нам повезёт — мы будем устанавливать приложение глобально
Если нам повезёт — в setup.py будет аналог `rm -rf /*`

`sudo python setup.py install`

А даже если не `sudo` — эта штука может сотворить всё что угодно!

**И от этого не спастись!**

---
# Типы упаковки

* Egg. Умер, был нестандартизирован, но хато сравнительно юеопасен
* Wheel. Жив и опасен

Хочется лучшего из обоих миров…

---
# Или выход есть?

`pip install safety` :japanese_ogre::ghost:

Эта штука может проверить уже добавленные библиотеки  :smile:

Но в целом Safety - это линтер. А линтеры — это хорошо.

---
# Какие ещё есть?

* bandit - статический анализ security
* dlint - ещё сколько-то проверок

---
<!--
_backgroundImage: "linear-gradient(to bottom, #000 0%, #1a2028 50%, #293845 100%)"
_color: white
-->
# О чём я говорил

1. Python прекрасен!
2. Экосистема огромна
3. Уже очень нмого чего есть — надо только уметь искать :)
4. То чего не хватает — можете добавить вы, комьюнити!
5. Творите добро!

---
<!--
_backgroundImage: "linear-gradient(to bottom, #000 0%, #1a2028 50%, #293845 100%)"
_class: lead
_color: white
_paginate: false
_footer: ""
-->

# Спасибо!

## Вопросы?

asm0dey @ Facebook, Telegram
asm0di0 @ Twitter
asm0dey@asm0dey.ru