# Nazywanie katalogów, plików i tabel

## Podstawa schemat:

- `S_`    – tabele słownikowe.
- `X_`    – rozszerzenia tabel słownikowych.
- `N_`    – pierwszy poziom tabel relacyjnych.
- `N2_`    – drugi poziom tabel relacyjnych.
- `N3_`    – kolejny poziom...

Dala każdej tabeli robisz oddzielny folder a potem wrzucasz do niego plik z definicją tabeli.
Przykład:

    Nazwa tabeli:
    - S_Pokoj
    Nazwa pliku:
    - S_Pokoj.sql

```text
└── database/             # nasz główny katalog plików do bazy danych
    └── S_Pokoj/          # katalog
        └── S_Pokoj.sql   # plik z definicją tabeli
```
