## Delegates

Klicove slovo `delegate`, vzdy to je referencni typ, co ktery odpovida hlavicce metode. Musime specifikovat navratovy typ a parametry.

```c#
delegate int MathDelegate(int a, int b);

class A {
    public int Add(int a, int b) => a + b;
}

class Math {
    public static int PerformOperation(int a, int b, MathDelegate operation) => operation(a, b);
}

A a = new A();
MathDelegate add = new(a.Add);
int result = Math.PerformOperation(1, 2, add);
```

Dedicnost: `deleates : System.MulticastDelegate : System.Delegate : System.Object`

Syntaxe

- `MathDelegate add = new(a.Add)` vyrobi noveho delegata
- `MathDelegate add = a.Add` je skoro to same, ale saha do cache, takze se novy delegat nevytvori. Vetsinou chci pouzit tohle, leda bych ty delegaty pouzival treba jako klice do slovniku.
- `operation(a,b)` je zkratka za `operation.Invoke(a,b)`
  - TOHLE NENI REFLECTION INVOKE `MethodInfo.Invoke`
  - pri reflection prekladac nevi, co dostane za paramerty a musi se delat runtime check
  - tady to je silne typovane, takze se to normalne prelozi do CIL kodu
- instance delegatu je immutable, podobne jako stringy
- Ruzni delegati co maji stejny signature se do sebe NEDAJI prirazovat, nijak od sebe totiz nededi.

Jak to funguje?

- delegat si pamatuje nejaky pointer na metodu
- pokud je to instancni metoda, tak i nejaky `object this`, aby se to mohlo zavolat
- pokud to je staticka metoda, tak `this` je `null`
- tim, ze je delegat immutable, tak to konkretni `this` je tam navzdy, takze ruzne instancni metody se muzou chovat jinak
- pokud je `this` struktura, tak se boxuje

Existuji i genericti delegati, existuje tam covariance a contravariance.

### Delegati a viditelnost

```c#
delegate void Process(int a);

class A {
    private void privateMethod(int a);
    public Process UsefulMethod() => privateMethod;
}
```

Umoznuje to zvenci dostat pristup k nejake private metode. Ale muzu tu private metodu bezpecne smazat, naimplementovat to uplne jinak a v pristi verzi jen vracet delegata na jinou metodu. Zvenci se nic nezmeni.

### Preddefinovani delegati

```c#
delegate void Action<...>(...);   // muze mit rouzne parametry, ale vraci void
delegate TResult Func<...,TResult>(...);  // opet ruzne parametry, posledni TResult je navratovy typ
delegate bool Predicate<T>(T item);  // vraci bool, bere jeden parametr
```

Kdyz udelam `var d = <nejaka metoda>`, tak se to prelozi a vybere se nejaky preddefinovany delegat.

### Efektivita delegatu

Vyroba delegata neco stoji, volani vyjde prakticky nastejno.

Delegati maji vlastnost `.Method`, ktera vraci `MethodInfo`, pouziva se to pro reflection a je to docela drahe. Ale cachuje se to.

Podobne na instanci `MethodInfo` existuje genericka metoda

```c#
TD MethodInfo.CreateDelegate<TD>() where TD : Delegate
```

Takze kdyz mam `MethodInfo` a chci tu metodu casto volat, tak vyrobim delegata a misto `MethodInfo.Invoke` volam `delegat.Invoke`, coz je mnohem rychlejsi.
