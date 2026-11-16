# S_POKOJE

## Tabela słownikowa

  -----------------------------------------------------------------------
  ID_POKOJU               NAZWA_POKOJU            OPIS
  ----------------------- ----------------------- -----------------------
  1                       Gabinet masażu          Pomieszczenie
                          klasycznego             przeznaczone do
                                                  wykonywania masażu
                                                  klasycznego

  2                       Gabinet masażu          Pomieszczenie do
                          relaksacyjnego          wykonywania masaży
                                                  relaksacyjnych

  3                       Gabinet masażu          Pomieszczenie
                          leczniczego             przeznaczone do masaży
                                                  terapeutycznych

  4                       Gabinet masażu          Pomieszczenie do masaży
                          sportowego              dla osób aktywnych
                                                  fizycznie

  5                       Gabinet masażu          Pomieszczenie
                          tajskiego               przystosowane do
                                                  wykonywania masażu
                                                  tajskiego

  6                       Poczekalnia             Pomieszczenie dla
                                                  klientów oczekujących
                                                  na wizytę

  7                       Recepcja                Pomieszczenie obsługi
                                                  klientów i rezerwacji
                                                  wizyt

  8                       Zaplecze pracownicze    Pomieszczenie
                                                  przeznaczone dla
                                                  pracowników

  9                       Magazyn                 Pomieszczenie do
                                                  przechowywania
                                                  ręczników, olejków i
                                                  wyposażenia
  -----------------------------------------------------------------------

## Struktura tabeli

  Pole           Typ danych     Klucz   Opis
  -------------- -------------- ------- ----------------------------------------------
  ID_POKOJU      INT            PK      Unikalny identyfikator rodzaju pomieszczenia
  NAZWA_POKOJU   VARCHAR(50)    \-      Nazwa rodzaju pomieszczenia
  OPIS           VARCHAR(255)   \-      Opis przeznaczenia pomieszczenia

## Szablon do kolejnych tabel słownikowych

  Pole               Typ danych     Klucz   Opis
  ------------------ -------------- ------- -------------------------
  ID\_\[NAZWA\]      INT            PK      Unikalny identyfikator
  NAZWA\_\[NAZWA\]   VARCHAR(50)    \-      Nazwa elementu słownika
  OPIS               VARCHAR(255)   \-      Opis elementu
