# Kontrolery

- [Podstawowe kontrolery](#basic-controllers)
- [Filtry kontrolera](#controller-filters)
- [Kontrolery RESTful](#restful-controllers)
- [Kontrolery zasobów](#resource-controllers)
- [Obsługa nieistniejących metod](#handling-missing-methods)

<a name="basic-controllers"></a>
## Podstawowe kontrolery

Zamiast definiować logikę aplikacji w pojedynczym pliku `routes.php`, możesz skorzystać z klas kontrolera. Kontrolery mogą grupować część logiki aplikacji w klasę. Skorzystanie z kontrolerów, umożliwi również skorzystanie z bardziej zaawansowanych funkcji frameworka, takich jak automatyczne [wstrzykiwanie zależności](/ioc).

Kontrolery znajdują się zazwyczaj w katalogu `app/controllers`. Ten katalog jest zarejestrowany domyślnie w pliku `composer.json` w opcji `classmap`.

Oto przykład podstawowej klasy kontrolera:

	class UserController extends BaseController {

		/**
		 * Show the profile for the given user.
		 */
		public function showProfile($id)
		{
			$user = User::find($id);

			return View::make('user.profile', array('user' => $user));
		}

	}

Wszystkie kontrolery powinny rozszerzać klasę `BaseController`. `BaseController` również znajduje się w katalogu `app/controllers` i może być użyty do przechowywania współdzielonej logiki kontrolerów. `BaseController` rozszerza klasę `Controller` frameworka. Teraz, możemy zdefiniować routing do akcji kontrolera w ten sposób:

	Route::get('user/{id}', 'UserController@showProfile');

Jeśli będziesz chciał zagnieżdżać lub używać w swoich kontrolerach przestrzeni nazw, po prostu użyj pełnych nazw dla klasy w routingu:

	Route::get('foo', 'Namespace\FooController@method');

Możesz równiez określić nazwy dla routingu kontrolera:

	Route::get('foo', array('uses' => 'FooController@method',
											'as' => 'name'));

Aby wygenerować adres URL do akcji kontrolera, mozesz użyć metody `URL::action`:

	$url = URL::action('FooController@method');

Aby uzyskać nazwę akcji dla kontrolera, która jest uruchomiona, skorzystaj z metody `currentRouteAction`:

	$action = Route::currentRouteAction();

<a name="controller-filters"></a>
## Filtry kontrolera

[Filtry](/routing#route-filters), podobnie jak "normalne" reguły routingu mogą być definiowane w routingu dla kontrolerów:

	Route::get('profile', array('before' => 'auth',
				'uses' => 'UserController@showProfile'));

Masz możliwość określenia filtrów z poziomu kontrolera:

	class UserController extends BaseController {

		/**
		 * Instantiate a new UserController instance.
		 */
		public function __construct()
		{
			$this->beforeFilter('auth');

			$this->beforeFilter('csrf', array('on' => 'post'));

			$this->afterFilter('log', array('only' =>
								array('fooAction', 'barAction')));
		}

	}

Możesz również określić filtry za pomocą anonimowej funkcji:

	class UserController extends BaseController {

		/**
		 * Instantiate a new UserController instance.
		 */
		public function __construct()
		{
			$this->beforeFilter(function()
			{
				//
			});
		}

	}

<a name="restful-controllers"></a>
## Kontrolery RESTful

Laravel pozwala na łatwe zdefiniowanie pojedynczej reguly routingu, która obsłuży wszystkie akcje kontrolera, za pomocą prostej konwencji REST. Najpierw zdefiniuj regułę routingu przy pomocy metody `Route::controller`:

**Definiowanie kontrolera RESTful**

	Route::controller('users', 'UserController');

Metoda `controller` przyjmuje dwa argumenty. Pierwszy, to bazowy adres URI, który kieruje do kontrolera. Drugi parametr jest nazwą klasy kontrolera. Teraz wystarczy dodać metody do swojego kontrolera, poprzedzając każdą z nich metodą HTTP, której mają odpowiadać:

	class UserController extends BaseController {

		public function getIndex()
		{
			//
		}

		public function postProfile()
		{
			//
		}

	}

Metoda `index` będzie odpowiadać na główny adres URI obsługiwany przez kontroler, którym w tym przypadku jest `users`.

Jeśli akcja Twojego kontrolera składa się z wielu słów, możesz dostać się do danej akcji poprzez zastosowanie "myślnika" w adresie URI. Dla przykładu, poniższa akcja naszego kontrolera `UserController`, będzie dostępna dla adresu URI `users/admin-profile`:

	public function getAdminProfile() {}

<a name="resource-controllers"></a>
## Kontrolery zasobów

Kontrolery zasobów pozwalaja na prostszą budowę kontrolerów RESTful wokół naszych zasobów. Dla przykładu, możesz utworzyć kontroler, który zarządza "zdjęciami" przechowywanymi przez Twoją aplikację. Za pomocą komendy Artisan `controller:make` i metody `Route::resource`, możemy szybko utworzyć taki kontroler.

Aby utworzyć kontroler z poziomu linni komend, wykonaj następujące polecenie:

	php artisan controller:make PhotoController

Teraz możemy powiązać ścieżkę zasób z kontrolerem:

	Route::resource('photo', 'PhotoController');

Ta pojedyncza deklaracja routingu, tworzy tak naprawdę wiele reguł, które obsługują wiele akcji RESTful do zasobu zdjęć. Podobnie sam kontroler - on też będzie miał już przygotowane metody dla każdej z akcji. Natomiast każda akcja będzie zawierała informację, który adres URI oraz metodę HTTP obsługuje.

**Akcje obsługiwane przez kontroler zasobów**

Verb      | Path                  | Action       | Route Name
----------|-----------------------|--------------|---------------------
GET       | /resource             | index        | resource.index
GET       | /resource/create      | create       | resource.create
POST      | /resource             | store        | resource.store
GET       | /resource/{id}        | show         | resource.show
GET       | /resource/{id}/edit   | edit         | resource.edit
PUT/PATCH | /resource/{id}        | update       | resource.update
DELETE    | /resource/{id}        | destroy      | resource.destroy

Czasami możesz potrzebować tylko pewien zakres akcji dla zasobu:

	php artisan controller:make PhotoController --only=index,show

	php artisan controller:make PhotoController --except=index

Możesz również określić zakres akcji, które ma obsługiwać routing:

	Route::resource('photo', 'PhotoController',
					array('only' => array('index', 'show')));

<a name="handling-missing-methods"></a>
## Obsługa nieistniejących metod

Można zdefiniować metodę catch-all, która będzie wywoływana, kiedy pożądana metoda nie znajduje się w danym kontrolerze. Taka metoda powinna nosić nazwę `missingMethod` i jako swój jedyny argument przyjmować tablicę parametrów:

**Definiowanie metody catch-all**

	public function missingMethod($parameters)
	{
		//
	}