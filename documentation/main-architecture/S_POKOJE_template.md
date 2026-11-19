
# Tabela słownikowa S_POKOJE

**Nazwa tabeli:** S_POKOJE

**Rodzaj:** Tabela słownikowa

**Opis:** Tabela przechowuje informacje o pokojach dostępnych w salonie masażu.

## Struktura tabeli

| Nazwa kolumny | Typ danych | Klucz | Wymagane | Opis |
|---|---|---|---|---|
| ID_POKOJU | INT | PK | TAK | Unikalny identyfikator pokoju |
| NAZWA_POKOJU | VARCHAR(50) | - | TAK | Nazwa pokoju |
| TYP_POKOJU | VARCHAR(30) | - | TAK | Rodzaj pomieszczenia |
| NUMER_POKOJU | VARCHAR(10) | - | TAK | Numer pokoju |
| POJEMNOSC | INT | - | TAK | Maksymalna liczba osób w pokoju |
| STATUS | VARCHAR(20) | - | TAK | Aktualny status pokoju |
| OPIS | VARCHAR(255) | - | NIE | Dodatkowe informacje o pokoju |

## Przykładowe dane

| ID_POKOJU | NAZWA_POKOJU | TYP_POKOJU | NUMER_POKOJU | POJEMNOSC | STATUS | OPIS |
|---|---|---|---|---|---|---|
| 1 | Pokój relaksacyjny | STANDARD | P01 | 1 | AKTYWNY | Pokój do masażu relaksacyjnego |
| 2 | Pokój terapeutyczny | TERAPEUTYCZNY | P02 | 1 | AKTYWNY | Pokój do masażu leczniczego |
| 3 | Pokój dla par | DLA_PAR | P03 | 2 | AKTYWNY | Pokój z dwoma stanowiskami masażu |
| 4 | Pokój VIP | VIP | P04 | 1 | AKTYWNY | Pokój do masażu premium |
| 5 | Pokój rehabilitacyjny | REHABILITACYJNY | P05 | 1 | NIEAKTYWNY | Pokój tymczasowo wyłączony z użytku |

## Reguły biznesowe

- ID_POKOJU jest kluczem głównym (PK).
- NUMER_POKOJU musi być unikalny.
- POJEMNOSC musi być większa od 0.
- STATUS może przyjmować wartości: AKTYWNY, NIEAKTYWNY, W_REMONT.
- TYP_POKOJU określa przeznaczenie pomieszczenia.
