## LINQ (Language Integrated Query)

V C# je LINQ pouze syntaxe, nedefinuje zadnou semantiku.

```c#
var collection : IEnumerable<int>;

// do klauzule pisu pouze telo lambda funkce
var data = from x in collection where x < 5 orderby x select x*2;
// prelozi se to na tohle
data == collection.Where(x => x<5).Orderby(x => x).Select(x => x*2);
```

To prvni se prelozi na to druhe. Standardne ty metody Where, Select, Orderby atd implementuje modul LinqToObjects. Ale muzu si napsat vlastni implementaci - jsou to proste extension metody IEnumerable, potom dam using a bude se pouzivat to moje.

Pokud se to spodni prelozi, tak to horni je syntakticky spravne. Dotaz musi zacinat `from`, musi tam byt `in` a musi koncit `select`. Select funguje stejne jako ostatni klauzule, az na pripad, kdy vracim proste `x`, pak se ten posledni `Select(x => x)` vynecha.

Navrat LINQ vyrazu urcuje posledni klauzule, muze to byt cokoliv, takze se pouziva duck typing.

To `x` nemusi byt collection, ale uplne cokoliv. Na tom typu jen musi existovat public metody odpovidajici tem klauzulim, co pouzivam.

To LinqToObjects implementuje extension metody pro IEnumerable ve staticke tride `Enumerable`.

Pokud budu mit nejaky vlastni typ, ktery implementuje IEnumerable a definuju si na nem vlastni metodu `Where`, tak se misto te LINQ metody pouzije ta moje.

## Jak funguje LinqToObjects

- tato sekce je okopirovana z [vitkolos.cz](https://www.vitkolos.cz/node/view/notes-ipp/main/semestr4/pokrocile-programovani-c-sharp/prednaska.md)
- myšlenka LINQ to Objects -- zavolání metod má jenom připravit
  dotaz, nemá se to reálně dotazovat
  - když zavolám Where, tak z ní vypadne krabička, kde je
    delegát a datový zdroj
  - ta krabička se jmenuje třeba WhereEnumerable
  - WhereEnumerable taky implementuje všechny metody z LINQu
    - ale tady už se nepoužívají extension metody
  - pokud zavolám Where na WhereEnumerable, tak si to nové
    WhereEnumerable bude jako zdroj pamatovat to původní
    WhereEnumerable
- výsledek LINQ to Objects dotazu vytvoří vázaný seznam těch
  krabiček
- všechny ty krabičky implementují rozhraní IEnumerable
- můžeme na tom udělat foreach cyklus
  - při inicializaci dostaneme enumerátor poslední krabičky
  - první volání MoveNext
    - vygeneruje enumerátory všech krabiček v tom spojáku
    - zkontroluje to platnost predikátů u Where klauzulí
    - volá to MoveNext na enumerátorech, dokud nejsou
      predikáty splněny
- LINQ to Objects provádí líné vyhodnocení (lazy evaluation)
  dotazu
- ten dotaz nemusím enumerovat celý
- jeden dotaz můžu spouštět vícekrát (od začátku)
- dotazy můžu skládat
- pozor, když mám proměnnou se seznamem, nad kterou postavím LINQ
  krabičky, a do této proměnné uložím jiný seznam, tak se dotaz
  nepřegeneruje -- má v sobě uložený odkaz na entitu, ne na
  proměnnou
- lazy evaluace se používá, pokud je to možné
  - pro některé operátory se dělá eager evaluace
  - Where ... lazy
  - OrderBy ... eager
  - ale to se týká jenom LINQ to Objects, neplatí to univerzálně
  - u eager evaluace se data musí někam uložit
- C# překladač nedělá žádné optimalizace -- je potřeba přemýšlet
  nad pořadím klauzulí
  - where → orderBy vs. orderBy → where
  - nejspíš bude efektivnější ta první varianta (pokud where
    a orderBy fungují tak, jak bychom čekali)
- poznámka -- LINQ to Objects dělá nějaké optimalizace
  - pokud máme dvě Where za sebou, tak se to sloučí do jedné
    krabičky, která má seznam podmínek
  - podobně Where a Select krabičky se můžou sloučit
  - ale nemění to fungování
