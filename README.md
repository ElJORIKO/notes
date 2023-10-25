## Notes

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
