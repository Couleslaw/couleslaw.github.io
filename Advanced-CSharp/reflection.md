## Co se deje pri prekladu

- C# zdrojaky s priponou .cs
- prelozi se do CIL kodu, pripona .dll, ulozi se metadata o typech a metodach
  - nastroje jako ildasm nebo ILSpy umi pomoci tech metadat zrekonstruovat (ekvivalnetni) zdrojaky
- JIT a CLR to pak prelozi do strojoveho kodu cilove platformy

V .NET je nejaka sada trid, ktera umoznuje zkoumat metadata assemblies, ktere jsou nahrade v te CLR, ve ktere bezim.

### Typ Type

- instance tridy type odpovidajici danenu typu ziskame pomoci `typeof(T)`, kde `T` je trida, nebo `x.GetType()`, kde `x` je instance
- objekty na heapu maji v sobe ukazatel na tuto instanci tridy type

### Typ Assembly

Ma staticke metody, vsechny vraci nejakou instanci tridy Assembly

- `Assembly.GetExecutingAssembly()` - vrati assembly, ve ktere je tento radek kodu realne. Pokud mam metodu v assembly A, ktera vola metodu v assembly B, ktera obsahuje tento radek, tak vrati B
- `Assembly.GetCallingAssembly()` - vrati assembly, ktera se na to realne pta --> A
- `Assembly.GetEntryAssembly()` - vrati entry-point toho celeho programu

```c#
// Get types implementing ISimplisticRoutesHandler from the calling assembly
Type[] types = Assembly.GetCallingAssembly().GetTypes()
    .Where(t => t.GetInterfaces().Contains(typeof(ISimplisticRoutesHandler)))
    .ToArray();
```

Metody na instanci typu Assembly:

- `Type[] .GetTypes()` - vrati vsechny typy v assembly
- `Type? .GetType(string name)` - vrati typ podle jmena

Metody na instanci typu Type vraci instance potomku abstraktni tridy `Memberinfo`

- `.GetFields()` - vrati pole `FieldInfo`
- `.GetProperties()` - vrati pole `PropertyInfo` atd
- `.GetMethods()`
- `.GetConstructors()`
- `.GetEvents()`
- `Type[] .GetInterfaces()`

A obdobne veci jako

- `MethodInfo? .GetMethod(string name)`

## Co s tim?

Muzeme si zaridit python-like duck typing.

```c#
void RunMethod(object obj, object[] args, string methodName) {
    MethodInfo? method = obj.GetType().GetMethod(methodName);
    if (method != null) {
        method.Invoke(obj, args);
    }
}
```

Zajimave metody

- `MethodInfo? Type.GetMethod(string name, Type[] types)` - najde konkretni overload podle typu parametru
- `object MethodInfo.Invoke(object this, object[] args)` - zavola metodu na objektu s parametry a vrati vysledek. Kdyby to byla staticka metoda, tak to `this` je `null`. Hodnotove argumenty musi boxovat.
- rekneme ze mame Typ `A`, ktery ma nestatickou metodu `f`, kterou chceme zavolat, ale nemame instanci

```c#
var a = Activator.CreateInstance(typeof(A));
var m = aType.GetMethod(nameof(A.f));
m.Invoke(a, null);
```

### Problemy reflection

- je strase pomala
- neni type-safe, snadny zdroj behovych chyb
- potencialne nebezpecna - da se pristupovat k private metodam a fieldum
  - to neni spatne! Private veci nejsou nejak tajne, jen by je
  - proto ty petody `.GetMethods`, `.GetFields` atd maji overloady s parametrem `BindingFlags`, kde se da nastavit, co vsechno chci videt
- uzivatele toho API nemeli pouzivati
