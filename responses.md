# Widoki i dane wyjściowe

- [Podstawowe dane wyjściowe](#basic-responses)
- [Przekierowania](#redirects)
- [Widoki](#views)
- [Kompozytorzy widoku](#view-composers)
- [Specjalne dane wyjściowe](#special-responses)

<a name="basic-responses"></a>
## Podstawowe dane wyjściowe

**Zwracanie ciągów za pomocą reguł routingu**

	Route::get('/', function()
	{
		return 'Hello World';
	});

**Tworzenie własnych danych wyjściowych**

Instancja `Response` dziedziczy z klasy `Symfony\Component\HttpFoundation\Response` i dostarcza wielu metod do budowy odpowiedzi HTTP.

	$response = Response::make($contents, $statusCode);

	$response->header('Content-Type', $value);

	return $response;

**Dołączanie ciasteczka do danych wyjściowych**

	$cookie = Cookie::make('name', 'value');

	return Response::make($content)->withCookie($cookie);

<a name="redirects"></a>
## Przekierowania

**Zwracanie przekirowania**

	return Redirect::to('user/login');

**Zwracanie przekierowania z danymi sesyjnymi (typu flash)**
	
	return Redirect::to('user/login')->with('message', 'Login Failed');

**Zwracanie przekierowania do nazwanej reguły routingu**

	return Redirect::route('login');

**Zwracanie przekierowania do nazwanej reguły routingu z parametrami**

	return Redirect::route('profile', array(1));

**Zwracanie przekierowania do nazwanej reguły routingu z nazwanymi parametrami**

	return Redirect::route('profile', array('user' => 1));

**Zwracanie przekierowania do akcji kontrolera**

	return Redirect::action('HomeController@index');

**Zwracanie przekierowania do akcji kontrolera z parametrami**

	return Redirect::action('UserController@profile', array(1));

**Zwracanie przekierowania do akcji kontrolera z nazwanymi parametrami**

	return Redirect::action('UserController@profile', array('user' => 1));

<a name="views"></a>
## Widoki

Widoki zazwyczaj zawierają kod HTML Twojej aplikacji i są wygodnym sposobem na odseparowanie kontrolerów i głównej logiki aplikacji od wartwy prezentacji. Widoki znajdują się w katalogu `app/views`.

Prosty widok może wyglądać w ten sposób:

	<!-- View stored in app/views/greeting.php -->

	<html>
		<body>
			<h1>Hello, <?php echo $name; ?></h1>
		</body>
	</html>

Ten widok może zostać zwrócony do przeglądarki w ten sposób:

	Route::get('/', function()
	{
		return View::make('greeting', array('name' => 'Taylor'));
	});

Drugi argument przypisany do `View::make` jest tablicą z danymi, które powinny być dostępne w widoku.

**Przekazywanie danych do widoku**

	$view = View::make('greeting', $data);

	$view = View::make('greeting')->with('name', 'Steve');

W powyższym przykładzie, zmienna `$name` będzie dostępna w widoku i zawierać będzie wartość `Steve`.

Możesz również udostępniać dane dla wszystkich widoków jednocześnie:

	View::share('name', 'Steve');

**Przekazywanie widoku do widoku**

Czasami możesz zechcieć przekazać widok, do drugiego widoku. Dla przykładu, mając widok w katalogu `app/views/child/view.php`, moglibyśmy go przekazać do innego widoku w ten sposób:

	$view = View::make('greeting')->nest('child', 'child.view');

	$view = View::make('greeting')->nest('child', 'child.view', $data);

"Podwidok" może być wtedy wyświetlony z głównego widoku w ten sposób:

	<html>
		<body>
			<h1>Hello!</h1>
			<?php echo $child; ?>
		</body>
	</html>

<a name="view-composers"></a>
## Kompozytorzy widoku

Kompozytorzy widoku, to żądania zwrotne lub metody klasy, które są wywoływane, kiedy tworzony jest widok. Jeśli masz dane, które chcesz powiązać z danym widokiem, za każdym razem kiedy jest on tworzony przez Twoją aplikację, to za pomocą kompozytora widoku możesz umieścić kod w jednym miejscu. Kompozytorzy widoku mogą funkcjonować jak "widoki modelów" lub "prezenterzy".

**Definiowanie kompozytora widoku**

	View::composer('profile', function($view)
	{
		$view->with('count', User::count());
	});

Teraz, za każdym razem kiedy tworzony jest widok `profile`, wartość `count` będzie powiązywana z widokiem.

Możesz dołączyć kompozytora widoku do wielu widoków jednocześnie:

    View::composer(array('profile','dashboard'), function($view)
    {
        $view->with('count', User::count());
    });

Jeśli wolisz używać kompozytora opartego o klasę, co będzie miało swoje zalety poprzez dostęp za pomocą [kontenera IoC](/ioc), możesz zrobić tak:

	View::composer('profile', 'ProfileComposer');

Klasa kompozytora widoku powinna być zdefiniowana w ten sposób:

	class ProfileComposer {

		public function compose($view)
		{
			$view->with('count', User::count());
		}

	}

Zwróć uwagę, że nie ma żadnej przyjętej konwencji dla lokalizacji plików kompozytora widoku. Możesz je umieszczać gdzie chcesz, jeśli tylko będą mogły być automatycznie ładowane za pomocą dyrektyw w pliku `composer.json`.

<a name="special-responses"></a>
## Specjalne dane wyjściowe

**Tworzenie odpowiedzi JSON**

	return Response::json(array('name' => 'Steve', 'state' => 'CA'));

**Tworzenie odpowiedzi JSONP**

	return Response::json(array('name' => 'Steve', 'state' => 'CA'))->setCallback(Input::get('callback'));

**Tworzenie odpowiedzi zwracającej plik**

	return Response::download($pathToFile);

	return Response::download($pathToFile, $name, $headers);
