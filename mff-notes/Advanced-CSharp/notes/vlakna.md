### Vlákna v C#

Řeší to třída `System.Threding`, vlakno = instance tridy `Thread`. Staticka vlastnosti `Thread.CurrentThread`. Instance ma vlastnost `ManagedThreadId`

```c#
var t = new Thread(delegate ThreadStart());
// to ze vznikne t negarantuje, ze vznikne to actual vlakno, je to spis builder
// t muzeme jeste nastavit nejako vlastnosti
t.Priority = ...
t.IsBackground = true;

t.Start();    // nepusti to vlakno rovnou, jen ho zaradi do planovaciho procesu
```

Proces té C# aplikace skončí až když doběhnou všechna foreground vlákna. Takže pokud nějaké vlákno nemá brzdit konec aplikace, tak ho nastavim na Background.

Pozor! Context switch je hodně drahý - tisíce taktů procesoru. Pokud v tom jednom bloku co běžím toho počítám málo, tak to může být neefektivní.

Instance .NET vlákna má vlastnosti .Unstarted, .Running a .IsActive. Když už ho OS terminatne, tak má .IsActive == false.

To, že to vlákno je Running neznamená, že něco actually dělá, může čekat na nějaké jiné vlákno. OS přepíná mezi Running a ReadyToRun. Také můžeme udělat thread.Sleep(ms), čímž přejde do stavu Waiting.

thread.Yield() přenese to vlákno do ReadyToRun

Kooperativní plánování - vlákna spolu spolupracují, když se vlákno chce vzdát procesoru, tak zavolá Yeild.

Preemptivní plánování - přepínání řeší OS pomocí nějakého plánovače. Tohle může vést na race condition. Navíc se třeba nějaká datová struktura může rozbít během nějaké interní operace a nechci, aby k tomu v tu dobu mohla přistupovat ostatní vlákna. Pokud je bezpečné mít sdílenou nějakou instanci z různých vláken, tak je thread-safe. Všechny klasické .NET datové struktury (List) NEJSOU thread-safe.

Cekame az dobehne nejake vlakno `t`, nechci aktivne cekat pomoci `t.IsActive` ani semiaktivne pomoci `Thread.Sleep(0)` respektive `Thread.Yeild`. Spravne reseni je `t.Join()`. Moje vlako se rozbehne, az skonci vlakno `t`.

Vlakna bezi v jednom procesu. Kazde vlakno ma vlastni volaci zasobnik, ale vsechny sdili stejnou haldu.
