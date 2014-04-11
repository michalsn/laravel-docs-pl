# Basic Database Usage

- [Konfiguracja](#configuration)
- [Wykonywanie Zapytań](#running-queries)
- [Transakcje Bazy Danych](#database-transactions)
- [Dostęp do połączeń](#accessing-connections)
- [Logowanie zapytań](#query-logging)

<a name="configuration"></a>
## Konfiguracja

Z pomocą Laravel tworzenie połączeń z bazą danych i wykonywanie zapytań jest bardzo proste. Konfiguracja bazy danych znajduje się w pliku `app/config/database.php`. W pliku możesz zdefiniować wszystkie połączenia z bazami danych, jak również sprecyzować które połączenie powinno użyć jako domyślne. Przykłady dla wszystkich wspieranych baz danych znajdują się w tym pliku.

Aktualnie Laravel wspiera cztery systemy baz danych: MySQL, Postgres, SQLite, i SQL Server.

<a name="running-queries"></a>
## Wykonywanie zapytań

Kiedy masz już skonfigurowane połączenie z bazą możesz wykonywać zapytania przy pomocy klasy `DB`.

**Wykonywanie zapytania SELECT**

	$results = DB::select('select * from users where id = ?', array(1));

Metoda `select` zawsze zwraca wynik w `array`.

**Wykonywanie zapytania INSERT**

	DB::insert('insert into users (id, name) values (?, ?)', array(1, 'Dayle'));

**Wykonyanie zapytania UPDATE**

	DB::update('update users set votes = 100 where name = ?', array('John'));

**Wykonywanie zapytania DELETE**

	DB::delete('delete from users');

> **Note:** Metody `update` i `delete` zwracają ilość wierszy których dotyczyło zapytanie.

**Wykonywanie dowolnego zapytania**

	DB::statement('drop table users');

Możesz nasłuchiwać zapytań używając metody `DB::listen`:

**Nasłuchiwanie zapytań**

	DB::listen(function($sql, $bindings, $time)
	{
		//
	});

<a name="database-transactions"></a>
## Transakcje Bazy Danych

Aby uruchomić zbiór operacji w transakcji powinieneś użyć metody `transaction`:

	DB::transaction(function()
	{
		DB::table('users')->update(array('votes' => 1));

		DB::table('posts')->delete();
	});

<a name="accessing-connections"></a>
## Dostęp do połączeń

Kiedy używasz wielu połączeń możesz zdefiniować połączenie za pomocą metody `DB::connection`:

	$users = DB::connection('foo')->select(...);

Możesz również pobrać sam obiekt PDO:

	$pdo = DB::connection()->getPdo();

W momencie kiedy będziesz potrzebował wznowienia połączenia możesz użyć metody:

	DB::reconnect('foo');

<a name="query-logging"></a>
## Logowanie zapytań

Domyślnie Laravel trzyma w pamięci logi wszystkich zapytań które były uruchomione w aktualnym wywołaniu. Aczkolwiek, w pewnych przypadkach, kiedy dodaje dużą ilość wierszy, może to spowodować zużycie dużej ilości pamięci. Aby wyłączyć funkcję logowania powinieneś użyć metody `disableQueryLog`:

	DB::connection()->disableQueryLog();