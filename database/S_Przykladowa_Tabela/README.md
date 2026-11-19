## Używamy polskich nazw

! Używamy składni pod MySQL

## Schemat początku nazwy:

- `S_`    – tabele słownikowe.
- `X_`    – rozszerzenia tabel słownikowych.
- `N_`    – pierwszy poziom tabel relacyjnych.
- `N2_`    – drugi poziom tabel relacyjnych.
- `N3_`    – kolejny poziom...

Przykład:

    Nazwa tabeli:
    - S_Przykladowa_Tabela                #tabela słownikowa
    Nazwa pliku:
    - S_Przykladowa_Tabela.sql            #tabela słownikowa

```text
└── database/                             # nasz główny katalog plików do bazy danych
    └── S_Przykladowa_Tabela/             # katalog
        └── S_Przykladowa_Tabela.sql      # plik z definicją tabeli
```

## Przykładowy kod tabeli

```text
CREATE TABLE `S_Przykladowa_Tabela` (
    `Id` INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    `Nazwa` VARCHAR(100) NOT NULL,
    `Opis` TEXT NULL
);
```
