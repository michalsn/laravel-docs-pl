# Pamięć podręczna

- [Konfiguracja](#configuration)
- [Używanie pamięci podręcznej](#cache-usage)
- [Inkrementacja i dekrementacja](#increments-and-decrements)
- [Sekcje pamięci podręcznej](#cache-sections)
- [Pamięć podręczna dla sterownika database](#database-cache)

<a name="configuration"></a>
## Konfiguracja

Laravel dostarcza uniwersalne API dla wielu systemów pamięci podręcznej. Konfiguracja pamięci podręcznej znajduje się w pliku `app/config/cache.php`. W tym pliku, możesz określić, który sterownik pamięci podręcznej chciałbyć używać domyślnie w swojej aplikacji. Laravel wspiera popularne systemy pamięci podręcznej, takie jak [Memcached](http://memcached.org) i [Redis](http://redis.io) bez konieczności doinstalowywania dodatkowych komponentów.

Plik konfiguracyjny pamięci podręcznej zawiera również wiele innych opcji, których dokumentacja znajduje się w pliku - upewnij się więc, że zapoznasz się z nimi. Domyślnie, Laravel jest skonfigurowany tak, aby używać sterownika `file`, który przechowuje zserializowane i zcache'owane obiekty w systemie plików. Przy dużych aplikacjach, zalecane jest aby stosować sterowniki, które składują elementy w pamięci, takie jak Memcached lub APC.

<a name="cache-usage"></a>
## Używanie pamięci podręcznej

**Przechowywanie elementu w pamięci podręcznej**

	Cache::put('key', 'value', $minutes);

**Przechowywanie elementu w pamięci podręcznej, jeśli dany element nie isnieje**

	Cache::add('key', 'value', $minutes);

**Sprawdzanie, czy element istanieje w pamięci podręcznej**

	if (Cache::has('key'))
	{
		//
	}

**Zwracanie elementu z pamięci podręcznej**

	$value = Cache::get('key');

**Zwracanie elementu z pamięci podręcznej lub domyślnej wartości**

	$value = Cache::get('key', 'default');

	$value = Cache::get('key', function() { return 'default'; });

**Przechowywanie elementu w pamięci podręcznej na stałe**

	Cache::forever('key', 'value');

Czasami możesz zechcieć zwrócić element z pamięci podręcznej, ale również przechować domyślną wartość, jeśli dany element nie istnieje. Możesz to zrobić za pomocą metody `Cache::remember`:

	$value = Cache::remember('users', $minutes, function()
	{
		return DB::table('users')->get();
	});

Możesz również połączyć działanie metod `remember` i `forever`:

	$value = Cache::rememberForever('users', function()
	{
		return DB::table('users')->get();
	});

Zwróć uwagę, że wszystkie elementy przechowywane w pamięci podręcznej są zserializowane, możesz więc przechowywać każdy typ danych.

**Usuwanie elementu z pamięci podręcznej**

	Cache::forget('key');

<a name="increments-and-decrements"></a>
## Inkrementacja i dekrementacja

Wszystkie sterowniki za wyjątkiem `file` i `database`, wspierają operacje inkrementacji i dekrementacji:

**Inkrementacja wartości**

	Cache::increment('key');

	Cache::increment('key', $amount);

**Dekrementacja wartości**

	Cache::decrement('key');

	Cache::decrement('key', $amount);

<a name="cache-sections"></a>
## Sekcje pamięci podręcznej

> **Uwaga:** Sekcje pamięci podręcznej nie są wspierane podczas korzystania ze sterownika `file` lub `database`.

Sekcje pamięci podręcznej pozwalają na grupowanie powiązanych elementów i późniejsze ich zwracanie w całości. Aby mieć dostęp do sekcji, użyj metody `section`:

**Dostęp do sekcji pamięci podręcznej**

	Cache::section('people')->put('John', $john);

	Cache::section('people')->put('Anne', $anne);

Możesz uzyskać dostęp do elementów z pamięci podręcznej oraz używać innych metod, takich jak `increment` i `decrement`:

**Dostęp do elementów z sekcji pamięci podręcznej**

	$anne = Cache::section('people')->get('Anne');

W ten sposób możesz zwrócić wszystkie elementy sekcji:

	Cache::section('people')->flush();

<a name="database-cache"></a>
## Pamięć podręczna dla sterownika database

Jeśli używasz sterownika `database` do przechowywania pamięci podręcznej, to musisz utworzyć tabelę, na przechowywane elementy. Poniżej znajduje się przykładowa deklaracja `schematu` dla tabeli:

	Schema::create('cache', function($table)
	{
		$table->string('key')->unique();
		$table->text('value');
		$table->integer('expiration');
	});
