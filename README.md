## Notes

1. [Логарифмы java](#logarithm)
2. [Unit тест приватного конструктора](#unitTestPrivateConstructor)
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
