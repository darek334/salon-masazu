## Struktura endpointów

- Każdy endpoint posiada własny katalog.
- Nazwa katalogu odpowiada nazwie endpointu.

**Przykładowa ścieżka endpointu:**
`https://edu.simplefilter.eu/vistula/semestr_5/it_pdw_iv/salon_masazu/endpoint_xyz`

Czyli:

Przykład:

    Nazwa endpointa:
    - endpoint_xyz

```text
└── backend/                             # nasz główny katalog endpointów
    └── endpoint_xyz/                    # endpoint
        ├── index.php                    # index.php kod endpointa w PHP
        └── inne.rozsz                   # inne pliki jeśli potrzebne
```

## Przykładowy kod index.php

❗Używamy schematu try catch cały kod w tym bloku na górze te nagłówki ze schematu

```text
<?php

	try{
		
		header('Content-type: application/json; charset=utf-8' );
		header('X-Content-Type-Options: nosniff' );
		header('Cache-Control: no-store' );
		header('SimpleFilter-API-Name: salon_masazu' );
	
		error_reporting(E_ALL );
		
		//...
	}
	catch(Throwable $Error ){

		//...
	}
?>
```
