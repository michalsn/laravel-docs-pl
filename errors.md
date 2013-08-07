# Błędy i logowanie

- [Szczegóły błędu](#error-detail)
- [Obsługa błędów](#handling-errors)
- [Wyjątki HTTP](#http-exceptions)
- [Obsługa błedów 404](#handling-404-errors)
- [Logowanie](#logging)

<a name="error-detail"></a>
## Szczegóły błędu

Domyślnie, w Twojej aplikacji włączone jest wyświetlanie szczegółowych błędów. To oznacza, że kiedy wystąpi błąd, zostanie wyświetlona strona błędu z dokładnym miejscem jego wystąpienia oraz wiadomością błędu. Możesz wyłączyć wyświetlanie szczegółowych informacji o błędach poprzez ustawienie opcji `debug` na wartość `false` w pliku `app/config/app.php`. **Jeśli działasz w środowisku produkcyjnym, to wyłączenie opcji wyświetlania błędów jest stanowczo zalecane.**

<a name="handling-errors"></a>
## Obsługa błędów

Domyślnie plik `app/start/global.php` obsługuje błędy dla wszystkich wyjątków:

	App::error(function(Exception $exception)
	{
		Log::error($exception);
	});

To najbardziej postawowa obsługa błędów. Jeśli potzrebujesz, to możesz zdefiniować więcej handlerów. Handlery są wywoływane bazując na typie wyjątku, który obsługują. Dla przykładu, możesz utworzyć handler, który obsługuje tylko instancje `RuntimeException`:

	App::error(function(RuntimeException $exception)
	{
		// Handle the exception...
	});

If an exception handler returns a response, that response will be sent to the browser and no other error handlers will be called:
Jeśli obsługa wyjątku, zwróci dane wyjściowe, to te dane zostaną przesłane do przeglądarki i żadna kolejna obsługa błędów nie zostanie wywołana:

	App::error(function(InvalidUserException $exception)
	{
		Log::error($exception);

		return 'Sorry! Something is wrong with this account!';
	});

Aby nasłuchiwać błędów krytycznych PHP, mozesz użyć metody `App::fatal`:

	App::fatal(function($exception)
	{
		//
	});

<a name="http-exceptions"></a>
## Wyjątki HTTP

Wyjątki, w odniesieniu do protokołu HTTP, odnoszą się do błędów, które mogą wystąpić podczas żądania klienta. Może to być błąd typu nie znaleziono strony (404), błąd braku autoryzacji (401) lub nawet błąd 500. Aby zwrócić takie odpowiedzi, należy użyć:

	App::abort(404, 'Page not found');

Pierwszy argument to kod statusu nagłówka HTTP, a drugi to dowolna wiadomość, która zostanie wyświetlona razem z błędem.

W celu wygenerowania błędu 401 - braku autoryzacji, wystarczy zrobić tak:

	App::abort(401, 'You are not authorized.');

Te wyjątki mogą być wywołane w dowolnym miejscu podczas obsługi żądania.

<a name="handling-404-errors"></a>
## Obsługa błędów 404

Możesz zarejstrować handler, który będzie obsługiwał wszystkie błędu typu "404 Not Found" w Twojej aplikacji. W ten sposób będziesz mógł wyświeltać własne strony błędów 404:

	App::missing(function($exception)
	{
		return Response::view('errors.missing', array(), 404);
	});

<a name="logging"></a>
## Logowanie

Mechanizm logowania w Laravel dostarcza prostą warstwę, opartą na potężnej bibliotece [Monolog](http://github.com/seldaek/monolog). Domyślnie, Laravel jest tak skonfigurowany, aby tworzyć codzienne logi dla Twojej aplikacji. Pliki z logami są przechowywane w katalogu `app/storage/logs`. Informacje w logach możesz zapisywać w ten sposób:

	Log::info('This is some useful information.');

	Log::warning('Something could be going wrong.');

	Log::error('Something is really going wrong.');

Do dyspozycji jest siedem poziomów logowania zdefiniowanych w dokumencie [RFC 5424](http://tools.ietf.org/html/rfc5424): **debug**, **info**, **notice**, **warning**, **error**, **critical** i **alert**.

Do metod logowania można przypisać tablicę z danymi kontekstowymi:

	Log::info('Log message', array('context' => 'Other helpful information'));

Monolog posiada wiele dodatkowych metod, które możesz wykorzystać do logowania. Jeśli jest taka potrzeba, możesz uzyskać dostęp do podstawowej instancji Monolog, używanej przez Laravel:

	$monolog = Log::getMonolog();

Możesz również zarejestrować zdarzenie, aby przechwytywać wszystkie wiadomości przekazywane do logu:

**Rejestrowanie obserwatora dla logowania**

	Log::listen(function($level, $message, $context)
	{
		//
	});
