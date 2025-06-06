## Lambda funkce

Taky anonymni metody, jmeno totiz vygeneruje C# prekladac.

### Staticke lambda funkce

Klicove slovo static rika, ze lambda funkce nema stav.

```c#
static (params) => { body }

static (int x) => { return x * x; }  // statement body
static (int x) => x * x   // expression body
static x => x * x         // implicitly typed, prekladac cela type inference

static (int x, int y) => x + y
static (x, y) => x + y

static () => Console.WriteLine()
```

Lambda vyrazy nejsou "first class entities", neexistuje typ "lambda funkce". Ale mohou byt implicitne prevedena na typ delegate. Type inference parametru se dela podle delegata, kam to prirazuju.

```c#
Func<int, int> f1 = static x => x + 1;   // bere int vraci int
Func<int, double> f2 = static x => x + 1;  // bere int vraci double
Func<double, int> f3 = static x => x + 1;  // error, neexistuje implicitni konverze z double na int
```

Pro type inference prikladac resi nejakou rovnici, pokud je to nejaka slozita situace bez jednoznacneho reseni, tak je to prekladova chyba.

## Lambda funkce s vnitrnim stavem

- tato sekce je okopirovana z [vitkolos.cz](https://www.vitkolos.cz/node/view/notes-ipp/main/semestr4/pokrocile-programovani-c-sharp/prednaska.md)
- dosud nám k vyhodnocení funkce stačily její parametry
- co kdybychom uvnitř funkce chtěli používat nějaké další
  proměnné?
- když nepoužívám lambda funkce, ale předávám delegáta na
  instanční metodu -- uvnitř delegáta se uloží (odkaz na) this
- lambda funkci si můžeme představit jako funkci, která žije
  v nějakém kontextu
  - parametry -- deklarace lokálních proměnných
  - tělo -- deklarace nějakých proměnných a použití nějakých
    proměnných
    - použité proměnné můžou být 1. vázané (tzn. někde uvnitř
      funkce jsou deklarované) nebo 2. volné (nejsou
      deklarované uvnitř funkce)
    - některé jazyky umožňují mít funkci s volnými proměnnými
      jako first-class entity -- ty se pak dodefinují před
      použitím
      - tohle v C# nelze
    - `static` zakazuje volné proměnné
    - za překladu musí být jasné, co ty volné proměnné
      znamenají (v daném kontextu) -- převádějí se na vázané
      - lambda funkce bez volných proměnných = closure
      - v C# vlastně lambda funkce neexistují, jsou tam jen
        lambda výrazy a do delegátů se vždy přiřazují
        closures
- potřebovali bychom nějakým trikem dostat do lambda funkce
  přiřazené hodnoty těch volných proměnných
  - vezmeme nějaký její scope
  - v tom scopu zachytíme volné proměnné (Scope si můžeme
    představit jako nějakou třídu/objekt, kde ta lambda funkce
    je jeho instanční metoda)
  - podobný princip jako u iterátorových metod
  - v C++ to takhle funguje, ale v C# ne
    - syntax lambda funkcí v C++
      - `[](parametry) {tělo}`
    - do hranatých závorek se dá napsat `=`, to znamená
      capture by value
  - capture by value má zjevně nevýhody v mnoha situacích
    - pokud v lambda funkci něco spočítáme, nemůžeme to dostat
      ven
  - C++ umožňuje capture by reference, kdy se do hranatých
    závorek dá `&`
    - všechny fieldy ve scopu budou reference na nějaké místo
    - tohle v C# nejde, protože tracking reference mají
      omezení kvůli živnotnosti
  - v C# se neprovádí capture by value ani by reference
  - budeme tomu říkat „capture by move"
  - proměnná se „přesune" do toho scopu
  - když lambda funkce použije nějakou proměnnou z kontextu,
    změní se způsob překladu veškerého kódu za lambda funkcí
    (v daném kontextu)
    - veškerá další práce s danou proměnnou se přepíše na
      přístup k proměnné uvnitř scopu -- jako by to byla
      veřejná vlastnost (nebo field) nějakého objektu Scope
  - C# překladač opravdu vyrábí instanci nějaké třídy Scope
  - všechny lambda funkce v daném kontextu sdílejí stejnou
    instanci scopu
    - jedna instance scopu tedy vlastně odpovídá jednomu
      volání rodičovské funkce
  - do delegátu se předává „this" ukazující na konkrétní
    instanci scopu
- ty pomocné třídy se v C# nejmenují Scope, ale DisplayClass
- tohle se generuje C# překladačem na úrovni CIL kódu, takže JIT
  o lambda funkcích ani closure nic neví

- scope
  - vzniká ne kvůli lambda funkci, ale kvůli proměnným v ní
  - třídě scopu se říká DisplayClass
  - jeden scope odpovídá jedné úrovni životnosti proměnných
    - mějme proměnné a, i, j ve vnějším kontextu
    - mějme proměnnou k ve vnitřním kontextu
    - mějme lambda funkci ve vnitřním kontextu, ta používá
      proměnné i, j, k
    - vznikne jedna instance DisplayClass, která bude obsahovat
      proměnné i, j, a druhá instance, která bude obsahovat k
  - proměnné se neukládají podle názvu -- takže pokud mám uvnitř for
    cyklu nějakou proměnnou `int a`, tak ta se bere v každé iteraci
    samostatně
  - DisplayClass s nejkratší životností
    - ukazuje na ty nadřazené s delší životností
    - jako metodu obsahuje tělo lambda funkce (?)
  - víc lambda funkcí
    - na generování DisplayClasses se nic nemění
  - pozor na zachytávání proměnných v lambda funkci -- abychom něco
    nezachytili omylem
    - když ve VS najedu na šipečku lambda funkce, tak se zobrazí
      zachycené proměnné
    - častý bug -- myslím si, že se používá capture by value (ale
      hodnota se před voláním lambda funkce změní)
    - „capture by accident"

```c#
List<Action> actions = new List<Action>();

for (int x = 0; x < 2; x++) {
    int i = 0;
    for (int y = 0; y < 3; y++) {
        int j = 0;
        actions.Add(() => Console.WriteLine(
            "lambda: j:{0}, i:{1}, y:{2}, x:{3}",
            ++j, ++i, ++y, ++x
        ));
    }
}

foreach (Action a in actions) a();
```

```
lambda: j:1, i:1, y:4, x:3
lambda: j:1, i:2, y:5, x:4
lambda: j:1, i:3, y:6, x:5
lambda: j:1, i:1, y:4, x:6
lambda: j:1, i:2, y:5, x:7
lambda: j:1, i:3, y:6, x:8
```
