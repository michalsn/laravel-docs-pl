# Konfiguracja

- [Wprowadzenie](#introduction)
- [Konfiguracja środowiska](#environment-configuration)
- [Tryb konserwacji](#maintenance-mode)

<a name="introduction"></a>
## Wprowadzenie

Wszystkie pliki konfiguracyjne dla frameworka Laravel znajdują się w katalogu `app/config`. Każda z opcji, która znajduje się w plikach jest opisana, więc ze spokojem możesz przejrzeć te pliki i zobaczyć jakie opcje masz do dyspozycji.

Czasami możesz chcieć mieć dostęp do wartości konfiguracyjnych podczas działania aplikacji. Możesz to zrobić za pośrednictwem klasy `Config`:

**Dostęp do zmiennej konfiguracyjnej**

	Config::get('app.timezone');

Możesz również określić domyślną wartość jaka ma zostać zwrócona, jeśli dana zmienna konfiguracyjna nie istanieje:

	$timezone = Config::get('app.timezone', 'UTC');

Zauważ, ze posługujemy się tutaj "kropkową" składnią aby dotrzeć do wartości z różnych plików. Możesz również ustawić zmienną konfiguracyjną podczas działania aplikacji:

**Ustawianie zmiennej konfiguracyjnej**

	Config::set('database.default', 'sqlite');

Zmienne konfiguracyjne, które są ustawiane podczas działania aplikacji, są ustawiane tylko na okres danego żądania i nie będą zapisywane, a tym samym przenoszone do kolejnych żądań.

<a name="environment-configuration"></a>
## Konfiguracja środowiska

Często wygodnie jest posiadać różne wartości konfiguracyjne, zależne od środowiska, w którym uruchomiona jest aplikacja. Dla przykładu, możesz chcieć stosować różne sterowniki cache'owania, w zależności od tego, czy działasz na maszynie developerskiej, czy środowisku produkcyjnym. Takie podejście można łatwo osiągnąć stosując konfigurację opartą o dane środowisko.

Wystarczy, że stworzysz w katalogu `config` folder, który będzie odpowiadał nazwie Twojego środowiska, np. `local`. Następnie utworzysz pliki konfiguracyjne, które chcesz przesłonić i określisz opcje dla tego środowiska. Dla przykładu, aby przesłonić konfigurację sterownika cache'u dla lokalnej maszyny, utworzylibyśmy plik `cache.php` w folderze `app/config/local` o następującej treści:

	<?php

	return array(

		'driver' => 'file',

	);

> **Uwaga:** Dla nazwy środowiska nie używaj nazwy `testing`. Jest ona zarezerwowana dla testów jednostkowych.

Zauważ, że nie musisz określać _każdej_ opcji, która znajduje się w bazowym pliku konfiguracyjnym, lecz tylko te opcje, które chcesz zastąpić.

Następnie musimy poinformować framework w jakim środowisku działa. Domyślnym środowiskiem jest zawsze `production`. Możesz jednak ustawić inne środowisko w pliku `bootstrap/start.php` w głównym pliku Twojej instalacji. W tym pliku znajdziesz wywołanie `$app->detectEnvironment`. Tablica przypisana do tej metody jest używana do określenia aktualnego środowiska. Jeśli potrzebujesz, możesz dodać inne środowiska i nazwy maszyn to tej tablicy.

    <?php

    $env = $app->detectEnvironment(array(

        'local' => array('your-machine-name'),

    ));

Możesz również przypisać funkcję anonimową (`Closure`) do metody `detectEnvironment`, co pozwoli na implementację własnego sposobu na ustawianie środowiska:

	$env = $app->detectEnvironment(function()
	{
		return $_SERVER['MY_LARAVEL_ENV'];
	});

Do aktualnego środowiska aplikacji, możesz się dostać za pomoca metody:

**Dostęp do aktualnego środowiska aplikacji**

	$environment = App::environment();

<a name="maintenance-mode"></a>
## Tryb konserwacji

Kiedy Twoja aplikacja jest w trybie konserwacji, zwyczajne widoki będą wyłączone dla wszystkich reguł routingu. W ten sposób mozesz łatwo "wyłączyć"" swoją aplikację podczas dokonywania aktualizacji. Odwołanie do metody `App::down` jest w pliku `app/start/global.php`. Odpowiedź z tej metody zostanie zwrócona do użytkownika kiedy aplikacja będzie w trybie konserwacji.

Aby aktywować tryb konserwacji, należy wykonać komendę `down` dla Artisan:

	php artisan down

Aby wyłączyć tryb konserwacji, użyj komendy `up`:

	php artisan up

Aby wyświetlić określony widok, kiedy aplikacja jest w trybie konserwacji, możesz dodać do pliku `app/start/global.php` kod podobny do tego:

	App::down(function()
	{
		return Response::view('maintenance', array(), 503);
	});
