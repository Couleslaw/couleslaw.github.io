## Namespaces (jmenné prostory)

- můžu je vnořovat -- je to syntaktická zkratka
- tečka je v dotnetu validní součást identifikátoru
  - překladač vidí jenom názvy typů jako celek (i s tečkami)
- v názvu jmenného prostoru může být tečka
  - takže namespace A.C je stejný jako namespace C uvnitř
    namespace A
- jmenné prostory jsou z pohledu CLR ploché -- zanoření nemá žádný
  speciální význam
- když použijeme using, tak se aktivují extension metody daného
  jmenného prostoru

## Nested types (vnořené typy)

- tato sekce je okopirovana z [vitkolos.cz](https://www.vitkolos.cz/node/view/notes-ipp/main/semestr4/pokrocile-programovani-c-sharp/prednaska.md)
- CLR = common language runtime
- CLI = common language infrastructure
- z pohledu CLR jsou typy opravdu vnořené (na rozdíl od
  namespaces)
- mějme namespace X, v něm typ A, uvnitř vnořený typ B
- konvence CLI pro názvy vnořených typů (pro výpis)
  - X.A+B
- konvence C# pro názvy vnořených typů (pro použití v kódu)
  - X.A.B
- k čemu je to užitečné?
  - v Javě je souvislost mezi instancemi A a B
    - uděláme instanci A
    - v kontextu A vyrobíme B
    - to B má zpětně referenci na instanci A
    - v C# tohle neplatí
  - v C# můžu lépe upravovat viditelnost typů
    - normální typy můžou mít viditelnost internal, public
      nebo file
    - vnořené typy můžou být private, protected, public,
      internal, ...
    - docela častá je viditelnost private -- daný typ vidí
      jenom kód uvnitř A
    - kdyby A byl SortedDictionary implementovaný
      červeno-černým stromem, tak potřebuju nějaký typ, který
      bude reprezentovat vrchol toho stromu
      - je to implementační detail, takže je vhodné, aby typ
        Node byl vnořený -- takže nehrozí, aby ho začal
        používat někdo jiný
- když máme metodu v internal interface a nějaká třída ji
  implementuje jako interfacovou, tak se bez přístupu k interfacu
  ta metoda nedá zavolat
  - metoda by taky mohla být interní a výsledek by byl stejný,
    ale to teď není důležité
  - podobně můžeme mít v nějakém typu vnořený private interface
    a použít podobný efekt u vnořeného typu, který ten interface
    implementuje
- vnořený typ má přístup k private věcem nadřazeného typu
- vnořený typ může dědit od nadřazeného typu
