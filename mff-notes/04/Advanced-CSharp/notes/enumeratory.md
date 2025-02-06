## Hierarchie list-like interfaců

```c#
interface IEnumerator<out T> {
    T Current { get; private set; }
    bool MoveNext();
    void Dispose();
    void Reset();   // nastavi Current na prvni prvek, realne to je k nicemu
    // pro nejake slozitejsi typy muze byt obtizne zjistit puvodni start
    // takze casto Reset() vyhodi NotImplementedException
}

interface IEnumerable<out T> {
    IEnumerator<T> GetEnumerator();
}

interface IReadOnlyCollection<out T> : IEnumerable<T> {...}
interface IReadOnlyList<out T> : IReadOnlyCollection<T> {...}

interface ICollection<T> : IEnumerable<T> { int Count; ...}
interface IList<T> : ICollection<T> {...}

class T[] : IReadOnlyList<T>, IList<T> {...}
class List[] : IReadOnlyList<T>, IList<T> {...}

// z nejakych dobrych duvodu jsou oba parametry invariant
interface IReadOnlyDictionary<TKey, TValue> {...}
interface IDictionary<TKey, TValue> {...}
```

Z historických důvodů existují negenerické IEnumerator a IEnumerable od kterých dědí ty generické, takže musím Current nebo GetEnumerator implementovat genericky i negenericky.

## Closer look at IEnumerable

```c#
interface IEnumerable<T> : IEnumerable {
    IEnumerator<T> GetEnumerator();
    // explicitne implementuju interface IEnumerable
    IEnumerator IEnumerable.GetEnumerator() => GetEnumerator();
}

interface IEnumerator<T> : IEnumerator, IDisposable {
    T Current { get; }
    bool MoveNext();
    void Reset();  // casto k nicemu --> NotImplementedException
    void Dispose();
}

var a = new List<int> {1, 2, 3};
var e = a.GetEnumerator();
// (pred prvnim MoveNext) --> abychom mohli mit prazdne kolekce
e.Current;  --> InvalidOperationException
while (e.MoveNext()) {
    e.Current;  // 1, 2, 3
}
```

It is possible to make infinite collections

```c#
public class FactorialEnumerable : IEnumerable<int> {
    public IEnumerator<int> GetEnumerator() {
        return new Enumerator();
    }
    IEnumerator IEnumerable.GetEnumerator() => GetEnumerator();

    // tim ze je to private vnoreny typ, tak zvenci neni pristupny
    private class Enumerator : IEnumerator<int> {
        private int _counter = 0;

        public int Current { get; private set; }
        object IEnumerator.Current => Current;

        public bool MoveNext() {
            if (_counter == 0) {
                Current = 1;
            } else {
                Current *= _counter;
            }
            _counter++;
            return true;
        }

        public void Reset() {
            _counter = 0;
        }

        public void Dispose() {}
    }
}
```

## Concurrent modification

Když enumeruju list a současně se snažím odstraňovat nějaké prvky. Povede to na `InvalidOperationException`. Řekněme že mám [1,2,3,4,5], enumerátor je na 2, já odstraním 2 a tím se posune 3 o jeden index zpět. Zavolám `MoveNext()`, pak `Current` a dostal bych 4. Takže jsem vyrobil race condition bez vláken. Podporovat concurrent modification by bylo drahé na paměť, proto to kolekce standardně nedělají, ale měly by to nějak detekovat a vyhodit `InvalidOperationException`.

```c#
var a = new List<int> {1, 2, 3, 4, 5};
var e = a.GetEnumerator();

int index = 0;
while (e.MoveNext()) {
    if (e.Current % 2 == 0) {
        a.RemoveAt(index);
    }
    index ++;
}
```

## Jak funguje foreach

```c#
foreach (ElemT item in collection) statement;

// se prelozi zhruba na

IEnumerator enumerator = ((IEnumerable)collection).GetEnumerator();
try {
    ElemT element;
    while (enumerator.MoveNext()) {
        element = (ElemT)enumerator.Current;
        statement;
    }
} finally {
    IDisposable disposable = enumerator as IDisposable;
    // kdyby to nebylo IEnumerable<T>, tak tohle Dispose() nezavola
    // protože negenericky IEnumerator neimplementuje IDisposable
    if (disposable != null) disposable.Dispose();
}
```

Reálně nejprve zkusí zavolat prostě `collection.GetEnumerator`, až pak na `IEnumerable<T>.GetEnumerator` a nakonec `IEnumerable.GetEnumerator`. Takže collection nemusí implementovat `IEnumerable`. Ta metoda `GetEnumerator` může být klidně extension metoda.

Opravdu tam je ta explicitni konverze, takže lze dělat třeba

```c#
foreach (byte b in new List<int> {1, 2, 3})
```

Nebo pokud neexistuje implicitni ani explicitni C# konverze, tak to hleda user defined konverzi.

## Iterátorové metody

Nejprve motivacni priklad, ze psat enumeratory rucne je dost otravne. Mame tridu `A`, ktera ma dve pole a chceme je enumerovat za sebou. Misto

```c#
A a = new A();
foreach (int x in a.x1) Console.WriteLine(x);
foreach (int x in a.x2) Console.WriteLine(x);
// cheme
foreach (int x in a) Console.WriteLine(x);
```

```c#
class A : IEnumerable<int> {
    private int[] x1 = { 1, 2 };
    private int[] x2 = { 3, 4, 5 };

    public IEnumerator<int> GetEnumerator() => new Enumerator(this);

    private class Enumerator : IEnumerator<int> {
        private A a;
        private int index = -1;
        private bool firstArray = true;

        public Enumerator(A a) => this.a = a;

        public int Current {
            get {
                if (firstArray) {
                    if (index < 0) {
                        throw new InvalidOperationException("Enumeration has not started. Call MoveNext.");
                    } else {
                        return a.x1[index];
                    }
                } else {
                    if (index >= a.x2.Length) {
                        throw new InvalidOperationException("Enumeration already finished.");
                    } else {
                        return a.x2[index];
                    }
                }
            }
        }

        public bool MoveNext() {
            if (firstArray) {
                if (index < a.x1.Length - 1) {
                    index++;
                    return true;
                } else {
                    index = -1;
                    firstArray = false;
                }
            }

            if (index < a.x2.Length - 1) {
                index++;
                return true;
            } else {
                if (index < a.x2.Length) index++;
                return false;
            }
        }

        public void Reset() { }
        public void Dispose() { }
    }
}
```

V podstate musim udelat nejaky jednoduchy stavovy automat. Proto ma C# iteratorove metody, ktere tohle delaji za me. Ale porad se to preklada do nejakeho konecneho automatu co uvnitr pouziva switch a goto. Tu stejnou vec bych napsal jako

```c#
public IEnumerator<int> GetEnumerator() {
    for (int i = 0; i < x1.Length; i++) {
        yield return x1[i];
    }
    for (int i = 0; i < x2.Length; i++) {
        yield return x2[i];
    }
}
```

Musí vracet nějaký `IEnumerator` a obsahovat klíčové slovo `yield return`.

```c#
IEnumerator<int> GetData(...) {
    // kod alpha
    yield return 1 + f(1);
    // kod beta
    yield return 2 + f(2);
    // kod gamma
}
```

Když poprvé volám `MoveNext()`, tak se provede alpha a spočítá se `1+f(1)`. Podruhé se provede beta a spočítá se `2+f(2)` a napotřetí se provede gamma a vrátím `false`, protože enumerátor už nemá co vracet.

To co vrací `yield return` se zapamatuje do nějakého vnitřního stavu, takže `GetCurrent()` vrátí ten uložený stav. `GetCurrent()` před alpha a po gamma by měl vyhazovat `InvalidOperationException`, ale kvůli historickým důvodům to tak není. Před alpha to vrátí nějakou defaultní hodnotu toho `T` a po gamma to vrátí poslední uloženou hodnotu.

C# překladač z toho vyrobí enumerátor, který má ten náš kód alpha, beta a gamma uvnitř `MoveNext()` (implementace pomocí state machine) a z té metody `GetData()` vygeneruje metodu, která jen vyrobí a vrátí ten enumerátor.

- Lokální proměnné z GetData() se převedou na fieldy toho enumerátoru.
- V Release režimu si překladač všimne, že některé lokální proměnné není třeba držet globálně a přeloží je jako lokální (ne jako fieldy).
- Parametry iter. metody se taky ulozi do enumeratoru jako private fieldy - capture by value.
- stavovy automat pres switch a goto, ma stav `_state`
  - na zacatku je `_state = 0`
  - koncovy stav je `_state = -1`
  - jinak podle toho co se v te metode deje jsou tam dalsi stavy
  - kdyz zavolam `MoveNext()`, tak se `_state` nejprve nastavi na -1 a az kdyz to dobehne, tak zase na neco korektniho
  - duvod: kdyby se nejak pokazil vnitrni stav toho enumeratoru, tak budu v koncovem stavu, coz ma deterministicke chovani

POZOR: veskery kod te metody skonci v `MoveNext()`, takze pokud tam delam treba nejakou kontrolu parametru a vyhazuju vyjimku, tak se to provede az pri prvnim volani `MoveNext()`, coz bude typicky v nejakem foreach cyklu. NE PRI VYTVARENI ITERATORU.

POZOR: foreach cyklus je pro `IEnumerable`, ne pro `IEnumerator`, takže reálně musím používat `MoveNext()`, pokud s ním chci pracovat

Klíčové slovo `yield break` enumerátor rovnou přenese do koncového stavu, takže enumerování skončí a `MoveNext()` bude vracet false.

## Prevod lazy evaluation na eager evaluation

System.LINQ ma metody `IEnumerable.ToList` a `.ToArray`. Muze se hodit. POZOR: `ToArray` je trochu pomalejsi, protoze nevime pocet prvku v kolekci. Takze se to stejne musi kopirovat do nejakeho nafukovaciho pole a navic nakonec prekopirovat do toho finalniho pole.

- pri lazy evaluaci je potreba dat si pozor na concurrent modification

## Iteratorove metody II

Standardne iteratorove metody vraci `IEnumerator<T>`, ale mohou vracet i `IEnumerable<T>`.

```c#
class A {
    public int x = 0;

    IEnumerable<int> Range(int from, int to) {
        for (int i = from; i < to; i++) {
            yield return x + i;
        }
        x += 100;
    }
}
```

Tohle se pri prekladu rozpadne na dve tridy.

```c#
class A {
    public int x = 0;

    IEnumerable<int> Range(int from, int to) {
        var alpha = new Alpha();
        alpha._from = from;
        alpha._to = to;
        alpha._this = this; // protoze dochazi ke zmene this.x
        return alpha;
    }

    private class Alpha : IEnumerable<int> {
        // capture params by value
        public int _from;
        public int _to;
        public A _this;

        public IEnumerator<int> GetEnumerator() {
            var beta = new Beta();
            beta._from = _from;
            beta._to = _to;
            beta._this = _this;
            return beta;
        }
    }

    private class Beta : IEnumerator<int> {
        // captured local vars by move -- "presunou" se sem
        // recapture params by value
        public int _from;
        public int _to;
        public A _this;

        int _state = 0;
        public bool MoveNext() { state machine }
    }
}
```

Proc se te Bete nepreda odkaz na Alpha, ale deje se znovu capture params? Protoze v te Alpha to je nejaky sdileny stav pro vsechny enumeratory a nechci ho menit z nejakeho konretniho enumeratoru... V te iter. metode jsou `from` a `to` jsou nejake lokalni promenne, ktere teoreticky muzu menit.

Ve skutecnosti to vsechno dela nejaka jedna trida ktera implementuje `IEnumerable` i `IEnumerator`. Ale semanticky to je takhle.
