---
layout: default
title: Dějiny Matematiky
---

[..](./index.md)

# Algebra

- Diophantus (3. stol.)

  - Řecký matematik (Arithmetica) — metoda řešení diofantických rovnic
  - Vliv na pozdější generace, včetně Fermata

- Al-Chorezmi (9. stol.)

  - kniha o řešení rovnic, pojem al‑jabr (odtud „algebra“)
  - Zavedení desítkové poziční soustavy a význam nuly
  - Převzal to z Indie, říkáme tomu arabské číslice protože se to k nám dostalo z arabského světa
  - Základní principy řešení rovnic: přičítání k obou stranám, dělení obou stran, práce s neznámou (x)

- Evropa středověk a renesance

  - Leonardo Pisano (Fibonacci) — šíření poziční desítkové soustavy v Evropě (Liber Abaci)
  - Postupné rozšiřování metod řešení rovnic a praktických aplikací

### Příběh o vyřešení kubické rovnice a objevení komplexních čísel

- Pozadí a hlavní aktéři

  - Scipione del Ferro (počátek 16. stol.) – jako první vyřešil určité typy kubických rovnic (tzv. „depresovanou“ kubickou) a své výsledky učil jen vybraným žákům.
  - Niccolò Tartaglia – objevil nezávisle metodu řešení pro určité kubické rovnice (získal ji mimo jiné při matematickém souboji) a byl známý jako vynikající řešitel soutěžních výzev.
  - Gerolamo Cardano – lékař a matematik, který Tartagliovi vynutil slib mlčenlivosti, Tartaglia mu metodu později prozradil pod podmínkou, že ji nezveřejní. Cardano však po zjištění, že del Ferro řešení zná dříve, publikoval metodu v knize Ars Magna (1545) — to vyvolalo spor s Tartagliou.
  - Lodovico Ferrari – žák Cardana, rozšířil metody a našel řešení pro kvadratické i kvadraturové případy (řešení kvartické rovnice).

- Jak řešení vzniklo (stručně a s příkladem)

  - Historicky šlo o postupné rozvíjení technik pro zvláštní tvary kubických rovnic a následné „unifikování“ do obecnějších vzorců. Del Ferro objevil metodu pro jeden speciální typ; Tartaglia ji znovu objevil a použil v soutěži; Cardano metodu publikoval a doplnil ji o další systematizaci.
  - Cardanova metoda (zjednodušeně): kubickou rovnici lze vhodnou substitucí zredukovat na „depresovanou“ formu a pak rozložit pomocí vztahu mezi dvěma třetími mocninami; z toho vznikají vzorce obsahující výraz pod třetí odmocninou.

- Casus irreducibilis a vstup imaginárních čísel

  - Pokud má rovnice jeden reálný kořen, tak Cardanuv postup ho přímo dá bez komplexních čísel.
  - Ale právě tehdy když má rovnice tři reálné kořeny, vede Cardanova formule k mezikrokům zahrnujícím druhé odmocniny záporných čísel (komplexní čísla).
  - Tento fenomén dostal později jméno casus irreducibilis (nerozložitelný příklad)

- Bombelli a vytvoření pravidel pro „imaginární“ čísla

  - Rafael Bombelli (polovina 16. stol.) systematicky pracoval s těmito „výbryky“ Cardanovy formule. V knize L'Algebra (přelom 16. a 17. století) popsal, jak s výrazy typu $a + b\sqrt{-1}$ operovat (sčítat, násobit, dělit) a ukázal, že takové postupy mají smysl: příklady, které formálně používaly odmocniny záporných čísel, vedly správně k reálným výsledkům.
  - Bombelli demonstroval konkrétně, jak Cardanova formule pro příklad $x^3 = 15x + 4$ dává po mezikrocích dva komplexní členy, jejichž součet je reálný kořen 4 — čímž ukázal, že „imaginární“ mezivýrazy lze smysluplně používat.

- Důsledky a význam

  - Přijetí komplexních čísel bylo postupné: zpočátku aretované jako jen „nástroj při výpočtu“, později jako plnohodnotné matematické objekty. Řešení kubické a Bombelliho práce byly klíčové pro tento přechod.
  - Vývoj vedl k rozšíření algebraických metod (symbolika, systematické řešení vyšších rovnic) a nakonec i k formování moderní teorie komplexních čísel a komplexní analýzy.
  - Konflikt mezi Tartagliou a Cardanem ilustruje, že v matematice bývá objev spojený s autorskými spory; podobné situace se opakovaly (například L'Hôpitalovo pravidlo objevili bratři Bernoulliovi).

- Stručné shrnutí

  - Řešení kubické vzniklo postupným sdílením a znovuobjevováním metod (del Ferro → Tartaglia → Cardano).
  - Cardanova publikace formalizovala vzorce, ale vyvolala spor kvůli slibům mlčenlivosti.
  - Casus irreducibilis přiměl matematiky k používání „imaginárních“ čísel; Bombelli položil pravidla pro jejich manipulaci.

### Leibniz - 17. století

- zavede značení $i$ pro imaginární jednotku
- ale pořád je z toho cítit takové mystično

### Gauss - 19. století

- komplexní čísla jako body v rovině (Gaussova rovina)
- Základní věta algebry (každá polynomická rovnice má kořen v komplexních číslech)

### 1843 Hamilton - nejslavnější Irský matematik

- výjimečně nadaný, ve 13 letech uměl 13 jazyků
- komplexní čísla jsou deeply linked s geometrií
- celou geometrii v rovině lze popsat pomocí komplexních čísel
- komplexní čísla $u$ a $v$, pak

  $$
  \overline{u}v = (u_1-u_2i)(v_1+v_2i) = (u_1v_1 + u_2v_2) + (u_1v_2 - u_2v_1)i
  $$

- reálná část je skalární součin, imaginární část je determinant (orientovaný obsah rovnoběžníku)
- zkusil hyperkomplexní čísla - 3 dimenze
  - $u=u_1 + u_2i+u_3j$ a $i^2 = j^2 = -1$
  - nefunguvalo mu násobení dlouhá léta
  - pak šel s manželkou na procházku po mostě v Dublinu a najednou ho to napadlo
    - 4 dimenze, čtyři jednotky $1, i, j, k$ a pravidla:
      - $i^2 = j^2 = k^2 = ijk = -1$
      - $ij = k, ji = -k$ (nekomutativní násobení)
    - nazval to kvaterniony
- k čemu je to dobré?
  - reprezentace rotací ve 3D prostoru když se zapomene reálná část
  - dnes se používají v počítačové grafice, robotice, fyzice

### 1843 H. Grassmann - lineární algebra

- do jisté míry otec lineární algebry
- vnější součin, něco mezi vektorovou algebrou a kvaterniony
- ale neměl pořádné matematické vzdělání - i když tam byly zásadní myšlenky, nedokázal je pořádně prezentovat a všechno to formuloval filozoficky
- univerzita mu to odmítla, on to nějak vylepšil, ale pořád to bylo těžkopádné - odmítli ho znovu
- takže se vykašlal na matematiku a šel dělat lingvistiku, kde úspěšně pracoval na sanskrtu - prastarý indický jazyk, podobný češtině, protože to jsou indoevropské jazyky

### W.K. Clifford

- pokusil se spojit Grassmanna a Hamiltona
- pomocí vektorů formuluje kvaterniony
- pak umře mladý na tuberkulózu

### Gibbs a vektorový počet (19. století) - navazuje na Clifforda

- skalární a vektorový součin, všechno pomaličku
- bázové vektory i, j, k (původně založené na kvaternionech, které pak zmizí)
- zapsal to jeho asistent Wilson - Wilsonova kniha
- fyzikům se to líbí - Maxwellovy rovnice najednou začínají vypadat jednoduše
- pomocí kvaternionů lze dokonce zapsat jednu jedinou Maxwellovu rovnici obsahující vše
- vektorová algebra začne vládnout světu fyziky i matematiky
