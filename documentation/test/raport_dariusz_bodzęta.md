# Raport z laboratorium — bezpieczny eksport pliku

Autorzy i grupa: System, wersja kompilatora, UID:

Autorzy:

- Dariusz Bodzeta

- Maciej Krysiński

System:

- Kubuntu 26.04 LTS

Wersja kompilatora:

- gcc (Ubuntu 15.2.0-16ubuntu1) 15.2.0

UID:

- uid=1000(dariusz) gid=1000(dariusz) groups=1000(dariusz)

## 0. Konfiguracja wstępna środowiska

Polecenia:

- id -u - sprawdzenie czy nie działamy na root
- PS1='\$ ' - skrócenie wiersza poleceń
- gcc -std=c17 -Wall -Wextra -Werror eksport.c -o eksport
- zamknięcie aby wyjść z root
- LAB=$PWD
- mkdir proby
- umask = 0002 – domyślna maska uprawnień stosowana przy tworzeniu nowych plików i katalogów przez proces użytkownika.

## 1. Hipoteza

Podejrzewamy, że przyczyną nadpisania wzorca jest użycie flagi `O_TRUNC` w funkcji `open()` bez zastosowania `O_EXCL`.

Jeżeli nazwa `raport.txt` jest już zajęta przez dowiązanie symboliczne wskazujące na `wzorzec.conf`, program podąży za dowiązaniem i nadpisze plik docelowy.

Hipotezę sprawdzimy, tworząc dowiązanie symboliczne, a następnie porównując sumę kontrolną SHA-256 i zawartość wzorca przed uruchomieniem eksportera i po nim.


## 2. Prawa dostępu

- cd "$LAB/proby"
  - Utowrzono folder proby z uprawnieniami:
    - 0775/drwxrwxr-x

- mkdir prawa && cd prawa
  - Utowrzono folder proby z uprawnieniami:
    - 0775/drwxrwxr-x

Foldery nie są badane, ale można zauważyć że ich uprawnienia powstają na skutek nakładania się maski użytkownika 0002 na możliwe uprawnienia dla:

Folder:
- 0777

Plik:
- 0666

| Próba | Przewidywanie | Wynik (polecenie + obserwacja) | Wyjaśnienie |
| - | - | - | - |
| Nowy plik, umask 022 | 644 | 644 -rw-r--r-- a.txt | Domyślne uprawnienia 0666 z maską 022 dają 0644. Tylko właściciel ma prawo zapisu, reszta tylko odczyt |
| Nowy plik, umask 077 | 600 | 600 -rw------- b.txt | Domyślne 0666 z maską 077 daje 0600. Nikt poza właścicielem nie ma dostępu. |
| Istniejący plik po zmianie maski | 644 | 644 a.txt | Maska umask nie zmienia uprawnień już istniejącego pliku. Nawet zastosowanie maski 0777, która przy tworzeniu nowego pliku wyeliminowałaby wszystkie standardowe uprawnienia, nie blokuje zapisu do pliku utworzonego wcześniej, jeżeli proces ma odpowiednie uprawnienia do tego pliku. |
| Istniejący plik z maską pp 777 | 644 | 644 a.txt | Nawet blokująca maska wszystkie uprawnienia w pod powłoce nie zapobiegła zapisowi do pliku a.txt |
| Nowa nazwa w katalogu 500 | Błąd, kod 1 | touch: cannot touch 'nowy': Permission denied kod=1 | Tworzymy katalog z uprawnieniami 500, poprzez polecenie chmod, czyli nikt nie ma prawa zapisu r-x	---	--- |


## 3. Rekonstrukcja

Wklej wynik ls -li, stat albo sha256sum sprzed i po eksporcie do podstawionej nazwy.

### Wynik poleceń przed exportem

```t
ls -li raport.txt wzorzec.conf:
1706017 lrwxrwxrwx 1 dariusz dariusz 12 Nov 24 03:51     raport.txt -> wzorzec.conf
1706006 -rw-rw-r-- 1 dariusz dariusz 10 Nov 24 03:50 wzorzec.conf
```

```t
sha256sum wzorzec.conf:
35d90425a8cd96dfeadebfdabe3c6ee5a31265ad13dc75b08242497d996df169  wzorzec.conf
```

### Wykonanie exportu:

```text
"$LAB/eksport"; echo "kod=$?"
kod=0
```

### Wynik poleceń po exporcie

```text
ls -li raport.txt wzorzec.conf:
1706017 lrwxrwxrwx 1 dariusz dariusz 12 Nov 24 03:51 raport.txt -> wzorzec.conf
1706006 -rw-rw-r-- 1 dariusz dariusz 31 Nov 24 04:11 wzorzec.conf
```

```text
sha256sum wzorzec.conf:
6bf39365fa83bca5fa8b963eafcc1ad64fb5e73c550659e79d626f8e317088ef  wzorzec.conf
```

```t
cat wzorzec.conf:
RAPORT-LAB: eksport zakonczony
```

### Odpowiedzi na pytania

**1. Która operacja zmieniła wzorzec?**

Operacja `open()` z flagą `O_TRUNC` opróżniła istniejący plik. Ponieważ `raport.txt` było dowiązaniem symbolicznym wskazującym na `wzorzec.conf`, system podążył za dowiązaniem i zmodyfikował plik docelowy.

**2. Jakie prawa musiał mieć proces eksportera?**

Proces musiał mieć prawo zapisu do pliku docelowego `wzorzec.conf`. W przeprowadzonej symulacji działał jako właściciel tego pliku, który posiadał uprawnienia odczytu i zapisu.

**3. Które działania w ćwiczeniu symulują osobę mogącą zmieniać wpisy w katalogu?**

Utworzenie dowiązania symbolicznego poleceniem `ln -s wzorzec.conf raport.txt` symuluje działanie osoby mogącej tworzyć wpisy w katalogu. Wymaga to posiadania uprawnień zapisu (`w`) i przechodzenia (`x`) do tego katalogu.



## 4. Poprawka

### Różnica między wersją pierwotną a poprawioną

Wynik polecenia `diff -u eksport.c eksport_poprawiony.c`:

```diff
--- eksport.c
+++ eksport_poprawiony.c
@@ -9,7 +9,7 @@
 
     /* Tu zapada decyzja o utworzeniu lub otwarciu pliku. 
        niskopoziomowe otwarcie/utworzenie pliku */
-    int fd = open(nazwa, O_WRONLY | O_CREAT | O_TRUNC, 0666 );
+    int fd = open(nazwa, O_WRONLY | O_CREAT | O_EXCL, 0600);
```

### Wyjaśnienie zmienionych flag i trybu tworzenia

W funkcji `open()` wprowadzono następujące zmiany:

- Usunięto `O_TRUNC`, ponieważ powodowała opróżnienie istniejącego pliku.
- Dodano `O_EXCL`, która w połączeniu z `O_CREAT` wymaga, aby nazwa pliku była wolna. Jeżeli jest już zajęta, operacja kończy się błędem.
- Zmieniono tryb tworzenia z `0666` na `0600`. Właściciel otrzymuje prawo odczytu i zapisu, natomiast grupa i pozostali użytkownicy nie otrzymują żadnych uprawnień.

Połączenie `O_CREAT | O_EXCL` zapewnia, że sprawdzenie dostępności nazwy i utworzenie pliku odbywają się atomowo, jako jedna operacja.

### Dlaczego osobne sprawdzenie istnienia nazwy przed `open()` pozostawia lukę czasową?

Osobne sprawdzenie istnienia pliku, a następnie jego otwarcie, pozostawia lukę czasową określaną jako TOCTOU (Time Of Check to Time Of Use).

Pomiędzy sprawdzeniem, czy nazwa jest wolna, a wykonaniem `open()` inny użytkownik może utworzyć plik lub dowiązanie symboliczne o tej samej nazwie. W rezultacie program może otworzyć niewłaściwy obiekt i zmodyfikować jego zawartość.

Zastosowanie `O_CREAT | O_EXCL` eliminuje tę lukę w procesie sprawdzania wolnej nazwy i tworzenia pliku, ponieważ obie czynności są wykonywane atomowo.

## 5. Weryfikacja

Dołącz wyniki pięciu testów przed poprawką i po niej. Opisz jeden dodatkowy test: przygotowanie, przewidywanie, polecenie, wynik. Jakie ryzyko pozostaje, jeżeli inny użytkownik może usuwać wpisy w katalogu już po eksporcie?

### Przed poprawką

```przed
$ bash testy.sh ./eksport
OK    Nowy raport zawiera oczekiwany wiersz
BŁĄD  Nowy raport ma tryb 600 przy umask 000
BŁĄD  Istniejący raport pozostaje bez zmian, program zwraca błąd
BŁĄD  Dowiązanie pozostaje na miejscu, cel zachowuje treść, program zwraca błąd
open raport.txt: Permission denied
OK    Katalog bez prawa zapisu pozostaje pusty, program zwraca błąd
Liczba niezaliczonych testów: 3 z 5
```

### Po poprawce

```po
$ bash testy.sh ./poprawiony
OK    Nowy raport zawiera oczekiwany wiersz
OK    Nowy raport ma tryb 600 przy umask 000
open raport.txt: File exists
OK    Istniejący raport pozostaje bez zmian, program zwraca błąd
open raport.txt: File exists
OK    Dowiązanie pozostaje na miejscu, cel zachowuje treść, program zwraca błąd
open raport.txt: Permission denied
OK    Katalog bez prawa zapisu pozostaje pusty, program zwraca błąd
Liczba niezaliczonych testów: 0 z 5
```

### Szósty test – dowiązanie do nieistniejącego celu

**Przygotowanie:**

Utworzenie dowiązania symbolicznego `raport.txt`, wskazującego na nieistniejący plik `brakujacy.conf`.

**Przewidywanie:**

Program powinien zakończyć się błędem, ponieważ nazwa `raport.txt` jest już zajęta przez dowiązanie symboliczne. Dowiązanie powinno pozostać na miejscu, a plik docelowy nie powinien zostać utworzony.

**Polecenia:**
```bash
cd "$LAB/proby"
mkdir test6 && cd test6
ln -s brakujacy.conf raport.txt
ls -l raport.txt
"$LAB/poprawiony"; echo "kod=$?"
ls -l raport.txt
test -L raport.txt && echo "Dowiazanie pozostalo"
test ! -e brakujacy.conf && echo "Cel nadal nie istnieje"
```

**Wynik:**

```test6
$ pwd
/home/dariusz/Dokumenty/Materialy_studenckie_Linux_bezpieczny_zapis
$ LAB=$PWD
$ cd "$LAB/proby"
$ ls
incydent  prawa
$ mkdir test6 && cd test6
$ ln -s brakujacy.conf raport.txt
$ ls -l raport.txt
lrwxrwxrwx 1 dariusz dariusz 14 Nov 26 01:25 raport.txt -> brakujacy.conf
$ "$LAB/poprawiony"; echo "kod=$?"
open raport.txt: File exists
kod=1
$ ls -l raport.txt
lrwxrwxrwx 1 dariusz dariusz 14 Nov 26 01:25 raport.txt -> brakujacy.conf
$ test -L raport.txt && echo "Dowiazanie pozostalo"
Dowiazanie pozostalo
$ test ! -e brakujacy.conf && echo "Cel nadal nie istnieje"
Cel nadal nie istnieje
```

### Dowód utraty i zachowania integralności

Utratę integralności wykazano podczas rekonstrukcji incydentu. Po uruchomieniu pierwotnego eksportera zmieniła się suma kontrolna SHA-256 pliku `wzorzec.conf`, a jego zawartość została zastąpiona komunikatem eksportera.

Zachowanie integralności potwierdziły testy poprawionej wersji. Próby wykorzystania istniejącego pliku oraz dowiązania symbolicznego zakończyły się błędem, a dotychczasowe dane pozostały bez zmian.

### Jakie ryzyko pozostaje, jeżeli inny użytkownik może usuwać wpisy w katalogu już po eksporcie?

Uprawnienia pliku 600 chronią jego zawartość przed odczytem i zapisem przez innych użytkowników, ale nie chronią samego wpisu w katalogu przed usunięciem lub zmianą.

Jeżeli inny użytkownik posiada prawa zapisu (`w`) i przechodzenia (`x`) do katalogu, może usunąć lub podmienić wpis `raport.txt`, mimo że nie ma prawa zapisu do samego pliku.

Aby ograniczyć to ryzyko, katalog roboczy usługi powinien należeć do dedykowanego użytkownika, na którym działa usługa, i mieć uprawnienia 700. Wówczas inni zwykli użytkownicy nie będą mogli zmieniać jego wpisów.