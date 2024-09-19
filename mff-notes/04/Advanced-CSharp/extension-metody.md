### [Sharplab](https://sharplab.io/)

Stránka na kompilaci kodu - zobrazí CIL / strojový kód

### Extension metody

```c#
class A {
    public void m(int b) {...}
}
static class AExtensions {
    static public void f(this A a, int b) {...}
}

A a = new A();
// ekvivalentni
AExtension.f(a,5);
a.f(5)
```

Pokud volam `a.f`, tak se prohledava postupne

- vhodna metoda na typu promenne `a`, takze `A`
- pak extension metody v namespace, kde je promenna `a` deklarovana
- pak extension metody v dalsich namespacech

Extension metody se daji volat i na potomcich `B : A`, takze se hledaji tranzitivne extension metody vsech predku.

### Implicit conversions

`Double` --> `Fraction` ani `Fraction` --> `Double` nedávají smysl (ztrácíme přesnost).

Nezáleží na tom ve které z těch dvou tříd je ten operátor definovaný.

Podobně jako `implicit` můžu definovat i `explicit` conversion.

### Přetěžování operátorů

- Aritmetické: `+-,*,/,%,++,--`
- Logické: `==, !=, <,>,<=,>=`
- Binární: `&, |, ^, <<, >>, ...`
