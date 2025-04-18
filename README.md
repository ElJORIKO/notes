# Notes

1.  [Логарифмы java](#logarithm)
2.  [Unit тест приватного конструктора](#unitTestPrivateConstructor)
3.  [Создание portable версии браузера linux](#portBrowserLinux)
4.  [Получение одного файла/папки из git](#oneFileFromGit)
5.  [Вставить строку в файл sed](#pastLineSed)
6.  [Рекурсивно выкачать сайт](#downloadSiteReqursive)
7.  [Java получить имя метода, вызвавшего метод](#getMethodWhoCall)
8.  [Справка по scp](#scpInfo)
9.  [Vim удалить строки, не содеражащие regex](#vimDeleteLinesNotContains)
10. [Openssl der в p12](№derToP12)
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

---
<a name="pastLineSed"></a>
# *Вставить строку в файл sed*

Вставить строку в файл с помощью утилиты sed можно двумя способами:
## Вставка после определенной строки
Вставится на 5 строку (т.е. после 4-й)
```
sed '4a\
Some message ' file.txt > result.txt
```
## Вставка перед определенной строкой
Вставится на 3 строку (т.е. перед 4-й)
```
sed '4b\
Some message ' file.txt > result.txt
```

---
<a name="downloadSiteReqursive"></a>
# *Рекурсивно выкачать сайт*

**Чтобы скачать сайт целиком с помощью wget нужно выполнить команду:**  
`wget -r -k -l 7 -p -E -nc http://site.com/`

После выполнения данной команды в директорию site.com будет загружена локальная копия сайта site.com. Чтобы открыть главную страницу сайта нужно открыть файл index.html.  
Рассмотрим используемые параметры:  
\-r — указывает на то, что нужно рекурсивно переходить по ссылкам на сайте, чтобы скачивать страницы.  
\-k — используется для того, чтобы wget преобразовал все ссылки в скаченных файлах таким образом, чтобы по ним можно было переходить на локальном компьютере (в автономном режиме).  
\-p — указывает на то, что нужно загрузить все файлы, которые требуются для отображения страниц (изображения, css и т.д.).  
\-l — определяет максимальную глубину вложенности страниц, которые wget должен скачать (по умолчанию значение равно 5, в примере мы установили 7). В большинстве случаев сайты имеют страницы с большой степенью вложенности и wget может просто «закопаться», скачивая новые страницы. Чтобы этого не произошло можно использовать параметр -l.  
\-E — добавлять к загруженным файлам расширение .html.  
\-nc — при использовании данного параметра существующие файлы не будут перезаписаны. Это удобно, когда нужно продолжить загрузку сайта, прерванную в предыдущий раз.

---
<a name="getMethodWhoCall"></a>
# *Java получить имя метода, вызвавшего метод*

Для получения имени метода, который вызвал наш метод можно использовать конструкцию:
`Thread.currentThread().getStackTrace()[1].getMethodName()`

Пример использования:
```
public class Test {
  public static void main(String args[]) {
    new Test().doit();
  }
  public void doit() {
    System.out.println(
       Thread.currentThread().getStackTrace()[1].getMethodName()
    );
   }
```

---
<a name="scpInfo" ></a>
# *Scp справка*

Использование: 
`scp [from] server@name:[to]` - отправить из папки from на сервер server под именем name в папку to
`scp server@name:[from] [to]` - получить с удлённой машины на локальную
Например:
`scp ~/my/path 192.168.0.1@user:/full/path/to/target`
`scp 192.168.0.1@user:/path/to/file ~/target`

Рекурсивное кописрование (вложенные папки и файлы):
`scp -rp [from] server@name:[to]`

---
<a name="vimDeleteLinesNotContains"></a>
# *Vim удалить строки, не содеражащие regex*

Использование:
Команда `:g!/text/d` удаляет все строки НЕ содержащие text

---
<a name="derToP12"></a>
# *Openssl der в p12*

Для перевода сертификата pem и key в p12 нужно выполнить следующие команды:

`openssl x509 -in app.cer -inform DER -out app.pem -outform PEM`
Эта команда преобразует Der сертификат в pem формат

`openssl pkcs12 -export -out app.p12 -inkey app.key -in app.pem` 
Далее нужно два раза ввести пароль для p12 сертификата
И на выходе будет сертификат p12
