# Notes

1. [Логарифмы java](#logarithm)
2. [Unit тест приватного конструктора](#unitTestPrivateConstructor)
3. [Создание portable версии браузера linux](#portBrowserLinux)
4. [Получение одного файла/папки из git](#oneFileFromGit)
---

<a name="logarithm"></a>
# *Логарифмы java*
Логарифм это показатель степени в которую нужно возвести основание а для получения числа б
x = LOGa(b) -> логарифм б по основанию а
Такая запись равносильна решению уравнения a^x = b

Java code:

```
public double log(double b, double a){
  return Math.log(b) / Math.log(a);
}
```

Вызов `log(256,2)` равносилен записи log2(256) и равен 8

2^8 = 256

---
<a name="unitTestPrivateConstructor"></a>
# *Юнит тест приватного класса конструктора java*
Допустим есть класс MyClass, который имеет только статичные методы, и не имеет конструктор
По правилам диктуемыми sonar у него должен быть приватный конструктор, который должен выдавать ошибку `IllegalStageException`

По тем же правилам sonar всё нужно покрывать юнит тестами
Но конструктор мы вызвать не можем, а проверить нужно

Для этого будем танцевать в присядку:

MyClass:
```
public class MyClass {

  private MyClass(){
    throw new IllegalStageException("utility class");
  }

}
```
MyClassTest:
```
class MyClassTest{

  @Test
  void constructor(){
    try {
      var const = MyClass.class.getDeclaredConstructor();
      const.setAccessible(true);
      const.newInstance();
    } catch (ReflectiveOperationException e){
      return;
    }
    fail("Must throw exception");
  }
}
```
---
<a name="portBrowserLinux"></a>
# *Создание portable версии браузера linux*

Для работы нам понадобится:

- deb пакет с необходимым браузером (Или любым другим приложением)
- утилита ar
- немного свободного времени

Работать будем с пакетом browser.deb

Сначала разархивируем пакет:
```
$ mkdir browser
$ ar x browser.deb
```

После переходим в папку browser. Там нам нужен только архив data:
```
$ cd browser
$ mkdir data
$ tar -xf data.tar.xz -C data
```

Далее нам нужна папка opt, в которой и лежит бинарный файл для запуска + необходимая обвязка
В файле `data/usr/share/menu/browser.menu` можно найти путь до бинаря, который следует запускать. Он написан в блоке `command`
Сохраняем в удобное место, и пробуем запустить:
```
$ mv data/opt ~/my
$ cd ~/my
$ ./browser/browser
```
---
<a name="oneFileFromGit"></a>
# *Получение одного файла/папки из git*

Для получения одного файла из репозитория можно использовать команду `git archive`

Для MacOs нужно предварительно создать папку, и инициализировать там git коммандой `git init`

## Примеры
### Получить файл README.md из корневой ветки репозитория
`git archive --remote=ssh://git@repo.ru/repo.git HEAD README.md | tar xO`
Внимание! В команде tar используются только буквы, т.е. tar икс + О
Таким образом в консоль будет выведено содержимое файла README.md
Та уже можно перенаправить вывод в файл, добавив в конце `>> file.name`

### получить папку
`git archive --remote=ssh://git@repo.ru/repo.git HEAD path/to/holder | tar xO >> holder && tar xf holder`
