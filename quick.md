# Laravel szybki start

- [Instalacja](#installation)
- [Routing](#routing)
- [Tworzenie widoku](#creating-a-view)
- [Tworzenie migracji](#creating-a-migration)
- [Eloquent ORM](#eloquent-orm)
- [Wyświetlanie danych](#displaying-data)

<a name="installation"></a>
## Instalacja

Aby zainstalować framework Laravel, możesz wykonać następujące polecenie z poziomu linii komend:

	composer create-project laravel/laravel your-project-name --prefer-dist

Możesz również ściągnąć kopię z [repozytorium GitHub](https://github.com/laravel/laravel/archive/master.zip). Następnie po [zainstalowaniu Composera](http://getcomposer.org), wykonaj komendę `composer install` w głównym katalogu Twojego projektu. Ta komenda ściągnie i zainstaluje potrzebne zależności dla frameworka.

Po zainstalowaniu frameworka, rozejrzyj się po projekcie, aby zapoznać się ze strukturą katalogów. Katalog `app` zawiera takie foldery jak `views`, `controllers` i `models`. Większość kodu Twoich aplikacji będzie się znajdowała właśnie w tym katalogu. Możesz również zajrzeć do folderu `app/config` i zapoznać się z dostępnymi opcjami konfiguracyjnymi.

<a name="routing"></a>
## Routing

Aby zacząć, stwórzmy naszą pierwszą regułę routingu. Otwórz plik `app/routes.php` i dodaj następującą regułę na końcu pliku:

	Route::get('users', function()
	{
		return 'Users!';
	});

Jeśli przejdziesz pod adres `/users` w swojej przeglądarce, powinieneś zobaczyć napis `Users!`. Świetnie! Stworzyłeś swoją pierwszą regułę routingu.

Reguły routingu mogą być również przypisane do klasy kontrolera, np:

	Route::get('users', 'UserController@getIndex');

Ta reguła informuje framework, że adres `/users` powinien wywołać metodę `getIndex` z klasy `UserController`. Po więcej informacji z tego zakresu, zajrzyj do [dokumentacji kontrolera](/controllers).

<a name="creating-a-view"></a>
## Tworzenie widoku

Teraz stworzymy prosty widok, aby wyświetlać nasze dane. Widoki znajdują się w katalogu `app/view` i zawierają kod HTML Twojej aplikacji. Utworzymy dwa nowe widoki w tym folderze: `layout.blade.php` i `users.blade.php`. Najpierw stworzymy plik `layout.blade.php`:

	<html>
		<body>
			<h1>Laravel Quickstart</h1>

			@yield('content')
		</body>
	</html>

Następnie plik `users.blade.php`:

	@extends('layout')

	@section('content')
		Users!
	@stop

Część składni zapewne wygląda dla Ciebie dość dziwnie. To dlatego, że używamy systemu szablonów dostarczonego przez Laravel: Blade. Blade jest bardzo szybki, ponieważ to kilka wyrażeń regularnych, które kompilują szablon do czystego PHP. Blade dostarcza potężnej funkcjonalności, np. umożliwia dziedziczenie szablonów oraz dodaje usprawnienia składni dla typowych strukturach kontroli w PHP, takich jak: `if` i `for`. Po więcej informacji zajrzyj do [dokumentacji Blade](/templates).

Teraz, kiedy mamy już nasze widoki, zastosujmy je dla reguły routingu  `/users`. Zamiast zwracać ciąg `Users!`, zwrócimy nasz widok:

	Route::get('users', function()
	{
		return View::make('users');
	});

Wspaniale! Utworzyliśmy prosty widok, który rozszerza layout. Teraz zacznijmy pracować nad warstwą bazy danych.

<a name="creating-a-migration"></a>
## Tworzenie migracji

Aby utworzyć tabelę, która będzie przechowywała nasze dane, użyjemy systemu migracji dostarczonego przez Laravel. Migracje pozwalają w ekspresyjny sposób zdefiniować zmiany dla bazy danych i w prosty sposób podzielić się nimi z innymi członkami Twojego zespołu.

Najpierw skonfigurujmy połączenie dla bazy danych. Wszystkie połączenia z bazą danych możesz skonfigurować w pliku `app/config/database.php`. Domyślnie Laravel jest skonfigurowany tak, aby używać bazy MySQL, dlatego musisz skonfigurować dane dostępowe dla połączenia w pliku konfiguracyjnym. Jeśli chcesz, możesz zmienić opcję `driver` na `sqlite`, co spowoduje użycie bazy SQLite znajdującej się w katalogu `app/database`.

Aby stworzyć migrację, użyjemy [Artisan CLI](/artisan). W głównym katalogu Twojego projektu, wykonaj następujące polecenie z poziomu linii komend:

	php artisan migrate:make create_users_table

Wygenerowany plik z migracją, znajdziesz w katalogu `app/database/migrations`. Ten plik zawiera klasę z dwoma metodami `up` i `down`. W metodzie `up` dokonujemy porządanych przez nas zmian w bazie danych, a w metodzie `down` po prostu je cofamy.

Zdefiniujmy migrację, która wygląda w ten posób:

	public function up()
	{
		Schema::create('users', function($table)
		{
			$table->increments('id');
			$table->string('email')->unique();
			$table->string('name');
			$table->timestamps();
		});
	}

	public function down()
	{
		Schema::drop('users');
	}

Teraz możemy uruchomić naszą migrację z poziomu linii komend przy użyciu komendy `migrate`. Wystarczy, że wywołasz tę komendę z poziomu głównego katalogu projektu:

	php artisan migrate

Jeśli chcesz cofnąć migrację, możesz użyć komendy `migrate:rollback`. Teraz kiedy mamy już tabelę w bazie danych, możemy zacząć pobierać dane!

<a name="eloquent-orm"></a>
## Eloquent ORM

Laravel posiada świetny ORM: Eloquent. Jeśli używałeś frameworka Ruby on Rails, to Eloquent wyda Ci się znajomy, ponieważ do interakcji z bazą danych wykorzystuje on wzorzec ORM Active Record.

Na początek zdefiniujmy model. Model Eloquent, może być użyty, aby skojarzyć tabelę z bazy danych lub konkretny wiersz z tabeli. Nie mart się, wkrótce wszystko nabierze sensu! Modele są zazwyczaj przechowywane w katalogu `app/models`. Zdefiniujmy więc model `User.php` w tym folderze:

	class User extends Eloquent {}

Zauważ, że nie musimy mówić modelowi Eloquent, której tabeli ma użyć. Eloquent ma wiele gotowych konwencji, jedną z nich jest używanie dla nazwy tabeli liczby mnogiej od nazwy modelu. Bardzo wygodnie!

Korzystając z preferowanych przez Ciebie narzędzi do administrowania bazą danych, dodaj trochę danych do tabeli `users`. Teraz możemy użyć Eloquent, aby zwrócić dane do naszego widoku.

Zmodyfikujmy naszą regułę routingu `/users`, aby wyglądała w ten sposób:

	Route::get('users', function()
	{
		$users = User::all();

		return View::make('users')->with('users', $users);
	});

Prześledźmy to po kolei. Najpierw metoda `all` z modelu `User` zwróci wszystkie wiersze z tabeli `users`. Dalej, przypiszemy te dane do widoku za pośrednictwem metody `with`. Metoda `with` przyjmuje jako parametr klucz i wartość. W ten sposób przekazywane dane są dostępne w widoku.

Świetnie. Teraz jesteśmy gotowi do wyświetlenia użytkowników w naszym widoku!

<a name="displaying-data"></a>
## Wyświetlanie danych

Zmienna `users` jest teraz dostępna w naszym widoku. Możemy więc wyświetlić użytkowników w ten sposób:

	@extends('layout')

	@section('content')
		@foreach($users as $user)
			<p>{{ $user->name }}</p>
		@endforeach
	@stop

Możesz się zastanawiać gdzie podziała się instrukcja `echo`. Kiedy korzystamy z szablonów Blade, możemy wyświetlić dane otaczając je podwójnymi nawiasami klamrowymi. Łatwizna. Teraz, kiedy przejdziesz pod adres `/users`, powinieneś zobaczyć listę wyświetlonych użytkowników.

To dopiero początek. W tym tutorialu, zapoznałeś się z podstawami podstaw frameworka Laravel. Jest jeszcze wiele ekscytujących rzeczy do nauki. Kontynuuj czytanie dokumentacji i zagłębiaj się w potężne możliwości jakie oferuje [Eloquent](/eloquent) i system szablonów [Blade](/templates). Być może jesteś bardziej zainteresowany poznaniem możliwości zwykłych zapytań [Queues](/queues) i tym jak stosować [Testy jednostkowe](/testing). Później możesz być też zainteresowany zmaganiem z architekturą Laravel i [kontenerem IoC](/ioc). Wybór należy do Ciebie!
