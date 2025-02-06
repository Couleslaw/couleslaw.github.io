## Variance generickych typu

Definice. Necht `B` je typove kompatibilni s `A`, typicky pokud B dedi od A. Muzu udelat `A a = new B();` Potom typ `C<T>` je

- covariantni podle `T` $\equiv$ `C<B>` je typove kompatibilni s `C<A>`
- contravariantni podle `T` $\equiv$ `C<A>` je typove kompatibilni s `C<B>`
- invariantni podle `T` $\equiv$ `C<A>` a `C<B>` nejsou typove kompatibilni

Trochu lidsky

- Covariant - muzu do pole objectů přiřadit pole Persons (neco lepsiho)
- Contravariant - můžu do neceho lepsiho přiřadit něco horšího
- Invariant - nemůžu přiřadit nic jiného

Priklady:

- pole referencí je covariant, ale pole hodnot není
  - vyhoda kovariance: muzu psat obecnejsi kod
  - nevyhoda: vetsinou kdyz pracuji s pole referenci, tak kovarianci nevyuziju, ale stejne za to musim zaplatit. Tim ze povoluju kovarianci, se musi vzdy pri zapisu do pole delat run-time check. Takze zapis do pole referenci je asi o 20% pomalejsi nez zapis do pole hodnot. Pri optimalizaci se muze vyplatit ty referencni typy zabalit do nejakeho hodnotoveho typu a definovat implicitni konverze.

```c#
string[] sa = new string[10];
object[] so = sa;  // ok --> covariant

oa[0] = "Hello";  // ok
oa[1] = 5;  // prelozi se, ale pri behu to spadne
```

- List je invariantni. Nemůžu udělat toto:

```c#
class A {...}
class B : A {...}

var a = new List<A>();
a = new List<B>();  // error
var o = new List<object>();
o = a;  // error
```

Ale bylo by to fajn. Možná nás zachrání interface `IList<T>`. Umí věci typu `Count` a indexovat.

Generické interfacy lze explicitně označit za covariant / contravariant. Ale jen pro refereční typy!!!

```c#
interface I1<T> {...}   // invariant
interface I2<out T> {   // covariant
    public T m();
    public void m(T a);  // error
}
interface I3<in T> {    // contravariant
    public T m();   // error
    public void m(T a);
}
interface I4<out T1, T2, in T3, in T4> {...}

I<A, B, C:G, D:H> = I<A/E:A, B, C/G, D/H>
```

Z toho plyne, že `IList<T>` musí být invariantní, protože má nějakou indexovací metodu, který bude mít setter (in T) a getter (out T).

Takže je potřeba nějaký interface, který je jen readonly... proto existuje `IReadOnlyList`, takže do `IReadOnlyList<object>` můžu předat cokoliv co ho implementuje... pole a list referenčních typů.

### K čemu je sakra contravariance?

Pozor, tohle neni `IComparable`! c# má interface `IComparer`, který umí comparovat.

```c#
interface IComparer<in T> {
    int compare(T i1, T, i2);
}

abstract class Animal { string Name; int weight; }
class Cat : Animal { int Fluffiness }
class Elephant : Animal { int TrunkLength }

class AnimalWeightComparer : IComparer<Animal> {...}
class CatFluffinessComparer : IComparer<Cat> {...}

static void OrganizeCatCompetition(Cat c1, Cat c2, IComparer<Cat> comparer) {...}
```

Protože `IComparer` je contravariant, tak do té soutěže můžu strčit `AnimalWeightComparer` a porovnávat kočky podle váhy. Sortu totiž můžu předat `IComparer`.
