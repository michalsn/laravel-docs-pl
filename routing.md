# Routing

- [Podstawowy routing](#basic-routing)
- [Parametry routingu](#route-parameters)
- [Filtry routingu](#route-filters)
- [Nazwane reguły routingu](#named-routes)
- [Grupowanie reguł routingu](#route-groups)
- [Routing subdomeny](#sub-domain-routing)
- [Prefiksy dla reguł routingu](#route-prefixing)
- [Routowanie połączone z bindowaniem modelu](#route-model-binding)
- [Zwracanie błędów 404](#throwing-404-errors)
- [Routing do kontrolerów](#routing-to-controllers)

<a name="basic-routing"></a>
## Podstawowy routing

Większość reguł routingu w Twojej aplikacji będzie zdefiniowana w pliku `app/routes.php`. Najprostrze reguły routingu składają się z adresu URI oraz anonimowej funkcji (Closure).

**Podstawowy routing GET**

	Route::get('/', function()
	{
		return 'Hello World';
	});

**Podstawowy routing POST**

	Route::post('foo/bar', function()
	{
		return 'Hello World';
	});

**Rejestracja routingu odpowiadającego na każde żądanie HTTP**

	Route::any('foo', function()
	{
		return 'Hello World';
	});

**Wymuszenie, aby routing był obsługiwany po HTTPS**

	Route::get('foo', array('https', function()
	{
		return 'Must be over HTTPS';
	}));

Często będziesz chciał generować adresy URL, dla swoich reguł routingu. Możesz to zrobić za pomocą metody `URL::to`:

	$url = URL::to('foo');

<a name="route-parameters"></a>
## Parametry routingu

	Route::get('user/{id}', function($id)
	{
		return 'User '.$id;
	});

**Opcjonalne parametry routingu**

	Route::get('user/{name?}', function($name = null)
	{
		return $name;
	});

**Opcjonalne parametry routingu, z domyślną wartością**

	Route::get('user/{name?}', function($name = 'John')
	{
		return $name;
	});

**Reguły routingu ograniczone za pomocą wyrażenia regularnego**

	Route::get('user/{name}', function($name)
	{
		//
	})
	->where('name', '[A-Za-z]+');

	Route::get('user/{id}', function($id)
	{
		//
	})
	->where('id', '[0-9]+');

Oczywiście, jeśli tylko potrzebujesz, możesz przypisać całą tablicę ograniczeń:

	Route::get('user/{id}/{name}', function($id, $name)
	{
		//
	})
	->where(array('id' => '[0-9]+', 'name' => '[a-z]+'))

<a name="route-filters"></a>
## Filtry routingu

Filtry routingu pozwalają w wygodny sposób ograniczyć dostęp do określonego adresu, co jest przydatne jeśli Twoja strona posiada miejsca, które wymagają uwierzytelnienia. Laravel posiada kilka wbudowanych filtrów: `auth`, `auth.basic`, `guest` oraz `csrf`. Znajdziesz je w pliku `app/filters.php`.

**Definiowanie filtra routingu**

	Route::filter('old', function()
	{
		if (Input::get('age') < 200)
		{
			return Redirect::to('home');
		}
	});

Jeśli z filtra zwrócimy odpowiedź, to ta odpowiedź będzie traktowana jako odpowiedź dla danego adresu i reguła routingu nie zostanie wykonana. Jeśli używamy filtrów typu `after`, to one również nie zostaną wykonane.

**Używanie filta z regułą routingu**

	Route::get('user', array('before' => 'old', function()
	{
		return 'You are over 200 years old!';
	}));

**Używanie wielu filtrów z regułą routingu**

	Route::get('user', array('before' => 'auth|old', function()
	{
		return 'You are authenticated and over 200 years old!';
	}));

**Określanie parametrów dla filtra**

	Route::filter('age', function($route, $request, $value)
	{
		//
	});

	Route::get('user', array('before' => 'age:200', function()
	{
		return 'Hello World';
	}));

Filtry typu `after` otrzymują zmienną `$response` jako trzeci argument przypisany do filtra:

	Route::filter('log', function($route, $request, $response, $value)
	{
		//
	});

**Filtry oparte na wzorze**

Możesz określić, aby filtr był stosowany dla całego zestawu reguł routingu, na podstawie ich adresu URI.

	Route::filter('admin', function()
	{
		//
	});

	Route::when('admin/*', 'admin');

W powyższym przykładzie filtr `admin` zostanie zastosowany do wszystkich reguł zaczynających się od `admin/`. Gwiazdka jest tu stosowana jako wildcard i będzie brana jako dowolny ciąg znaków.

Możesz również ograniczyć wzór filtra poprzez zastosowanie określonego żądania HTTP:

	Route::when('admin/*', 'admin', array('post'));

**Klasy filtrowania**

Do zaawansowanego filtrowania, możesz skorzystać z klasy, zamiast anonimowych funkcji. Ponieważ klasy filtrów są obsługiwane za pomocą [kontenera IoC](/ioc), będzie można w nich skorzystać ze wstrzykiwania zależności, co pozwoli na dokładniejsze testowanie. 

**Definiowanie klasy filtra**

	class FooFilter {

		public function filter()
		{
			// Filter logic...
		}

	}

**Rejestrowanie filtra opartego o klasę**

	Route::filter('foo', 'FooFilter');

<a name="named-routes"></a>
## Nazwane reguły routingu

Nazwane reguły routingu tworzą referencję, co można wykorzystać przy tworzeniu przekierowań lub adresów URL. Dzięki temu możesz określić nazwę dla reguły routingu, w ten sposób:

	Route::get('user/profile', array('as' => 'profile', function()
	{
		//
	}));

Możesz równiez określić nazwę routingu dla metody kontrolera:

	Route::get('user/profile', array('as' => 'profile', 'uses' => 'UserController@showProfile'));

Teraz, możesz używać nazwy routingu przy tworzeniu adresów URL lub przekierowań:

	$url = URL::route('profile');

	$redirect = Redirect::route('profile');

Dostęp do aktualnej nazwy routingu możemy uzyskać za pośrednictwem metody `currentRouteName`:

	$name = Route::currentRouteName();

<a name="route-groups"></a>
## Grupowanie reguł routingu

Czasami możesz potrzebować określić filtry dla całej grupy reguł routingu. Zamiast pojedynczo określać filtry dla każdej reguły, możesz je zgrupować w ten sposób:

	Route::group(array('before' => 'auth'), function()
	{
		Route::get('/', function()
		{
			// Has Auth Filter
		});

		Route::get('user/profile', function()
		{
			// Has Auth Filter
		});
	});

<a name="sub-domain-routing"></a>
## Routing subdomeny

Routing w Laravel jest również w stanie obsłużyć subdomeny wildcard i przypisać Twoje parametry wildcard z domeny:

**Rejestracja routingu subdomeny**

	Route::group(array('domain' => '{account}.myapp.com'), function()
	{

		Route::get('user/{id}', function($account, $id)
		{
			//
		});

	});
<a name="route-prefixing"></a>
## Prefiksy dla reguł routingu

Grupa reguł routingu może posiadać prefiks, dzięki opcji `prefix` w tablicy atrybutów:

**Dodawanie prefiksu dla grupowanych reguł routingu**

	Route::group(array('prefix' => 'admin'), function()
	{

		Route::get('user', function()
		{
			//
		});

	});

<a name="route-model-binding"></a>
## Routowanie połączone z bindowaniem modelu

Routowanie do modelu pozwala w wygodny sposób na przekazanie instancji modelu do routingu. Dla przykładu, zamiast przekazywać numer ID użytkownika, możesz przekazać całą instancję modelu User, która odpowiada podanemu numerowi ID. Na początek, przy pomocy metody `Route::model`, określ model, który powinien być użyty dla podanego parametru:

**Bindowanie parametru do modelu**

	Route::model('user', 'User');

Następnie, zdefiniuj routing, który zawiera parametr `{user}`:

	Route::get('profile/{user}', function(User $user)
	{
		//
	});

Ponieważ mamy powiązanie między parametrem `{user}` i modelem `User`, instancja `User` zostanie wstrzyknięta do routingu. Dla przykładu, żądanie `profile/1`, wstrzyknie instancję `User`, która ma wartość ID równą 1.

> **Uwaga:** Jeśli odpowiednia instancja modelu nie zostanie znaleziona w bazie danych, to zostanie zwrócony błąd 404.

Jeśli chcesz określić własne zachowanie w przypadku nie znalezienia modelu, to dla metody `model` możesz dodać jako trzeci parametr anonimową funkcję: 

	Route::model('user', 'User', function()
	{
		throw new NotFoundException;
	});

Czasami możesz chcieć samodzielnie określić parametry routingu. Wystarczy, że użyjesz metody `Route::bind`:

	Route::bind('user', function($value, $route)
	{
		return User::where('name', $value)->first();
	});

<a name="throwing-404-errors"></a>
## Zwracanie błędów 404

Są dwa sposoby na ręczne wywołanie błędu 404 z routingu. Można użyć metody `App::abort`:

	App::abort(404);

Można też zwrócić instancję `Symfony\Component\HttpKernel\Exception\NotFoundHttpException`.

Więcej informacji na temam obsługi wyjątków 404 i używania własnych odpowiedzi dla tych błędów, możesz znaleźć w rozdziale o [błędach](/errors#handling-404-errors).

<a name="routing-to-controllers"></a>
## Routing do kontrolerów

Laravel pozwala nie tylko na routing za pomocą anonimowych funkcji, ale również do klas kontrolera. Pozwala nawet na tworzenie [kontrolerów zasobów](/controllers#resource-controllers).

Po więcej informacji, zajrzyj do rozdziału o [kontrolerach](/controllers).
