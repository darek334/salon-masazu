# Katalog klas PHP

## Struktura folderów z klasami

- Każda klasa posiada własny katalog.
- Nazwa katalogu odpowiada nazwie klasy ale w formacie **nazwa_klasy**

Przykład:

    Nazwa klasy:
    - NazwaKlasy                 # nazwa klasy
    - nazwa_klasy                # nazwa folderu
    - NazwaKlasy.php             # nazwa pliku

Drzewo:

```text
└── backend/                     # nasz główny katalog endpointów
    └── php/                     # endpoint
        └── nazwa_klasy/         # endpoint
            └── NazwaKlasy.php   # index.php kod endpointa w PHP
```

## Przykładowy kod NazwaKlasy.php

```text
<?php
  	final class NazwaKlasy{
		
		public static function jakasFunkcja1(Klasa $argument ): string {

			//kod funkcji
		}
		
		public static function jakasFunkcja2(int $severity ): string{
			
			//kod funkcji
		}
	}
?>
```

