# Żądania i dane wejściowe

- [Podstawowe dane wejściowe](#basic-input)
- [Ciasteczka](#cookies)
- [Stare dane wejściowe](#old-input)
- [Pliki](#files)
- [Informacje o żądaniu](#request-information)

<a name="basic-input"></a>
## Podstawowe dane wejściowe

Możesz uzyskac dostęp do wszystkich danych wejściowych użytkownika za pomocą kilku prostych metod. Nie musisz martwić się o nazwę metody HTTP, ponieważ wszystkie dane wejściowe są obsługiwane w ten sam sposób.

**Pobieranie wartości danej wejściowej**

	$name = Input::get('name');

**Pobieranie wartości domyślnej, jeśli brak jest danej wejściowej**

	$name = Input::get('name', 'Sally');

**Sprawdzanie, czy istnieje dana wartość wejściowa**

	if (Input::has('name'))
	{
		//
	}

**Pobieranie wszystkich danych wejściowych dla żądania**

	$input = Input::all();

**Pobieranie tylko wybranych danych wejściowych dla żądania**

	$input = Input::only('username', 'password');

	$input = Input::except('credit_card');

Niektóre biblioteki JavaScript, takie jak Backbone, mogą wysyłać dane wejściowe w formacie JSON. Dostęp do danych w takim formacie odbywa się normalnie, za pośrednictwem `Input::get`.

<a name="cookies"></a>
## Ciasteczka

Wszystkie ciasteczka utworzone przez framework Laravel są szyfrowane i podpisane za pomocą kodu uwierzytelniającego. Oznacza to, że ciasteczko będzie nieważne w przypadku wprowadzenia "ręcznych" zmian przez klienta.

**Pobieranie wartości ciasteczka**

	$value = Cookie::get('name');

**Dołączanie nowego ciasteczka do odpowiedzi na żądanie**

	$response = Response::make('Hello World');

	$response->withCookie(Cookie::make('name', 'value', $minutes));

**Tworzenie "wiecznego" ciasteczka (bez ograniczeń czasowych)**

	$cookie = Cookie::forever('name', 'value');

<a name="old-input"></a>
## Stare dane wejściowe

Możesz potrzebować zatrzymać dane wejściowe z jednego żądania i przekazać je do drugiego. Może to być przydatne, np. podczas ponownego uzupełniania pól formularza, kiedy wystąpiły błędy walidacji. 

**Przekazywanie danych wejściowych do zmiennych sesji**

	Input::flash();

**Przekazywanie tylko niektórych danych wejściowych do zmiennych sesji**

	Input::flashOnly('username', 'email');

	Input::flashExcept('password');

Ponieważ często będziesz chciał wykorzystywać przekazywanie danych do sesji razem z przekierowaniem do poprzedniej strony, możesz wykorzystać method chaining.

	return Redirect::to('form')->withInput();

	return Redirect::to('form')->withInput(Input::except('password'));

> **Uwaga:** Możesz przekazywać również inne dane do kolejnego żądania za pomocą klasy [sesji](/session).

**Pobieranie starych danych wejściowych z sesji**

	Input::old('username');

<a name="files"></a>
## Pliki

**Zwracanie przesłanego pliku**

	$file = Input::file('photo');

**Sprawdzanie, czy plik został przesłany**

	if (Input::hasFile('photo'))
	{
		//
	}

Obiekt zwrócony przez metodę `file` jest instancją klasy `Symfony\Component\HttpFoundation\File\UploadedFile`, która rozszerza klasę PHP `SplFileInfo` i dostarcza wiele różnych metod do pracy z plikiem.

**Przenoszenie przesłanego pliku**

	Input::file('photo')->move($destinationPath);

	Input::file('photo')->move($destinationPath, $fileName);

**Zwracanie ścieżki do przesłanego pliku**

	$path = Input::file('photo')->getRealPath();

**Zwracanie oryginalnej nazwy dla przesłanego pliku**

	$name = Input::file('photo')->getClientOriginalName();

**Zwracanie rozszerzenia dla przesłanego pliku**

	$extension = Input::file('photo')->getClientOriginalExtension();

**Zwracanie rozmiaru dla przesłanego pliku**

	$size = Input::file('photo')->getSize();

**Zwracanie typu MIME dla przesłanego pliku**

	$mime = Input::file('photo')->getMimeType();

<a name="request-information"></a>
## Informacje o żądaniu

Klasa `Request` dostarcza wiele metod do sprawdzania żądań HTTP dla Twojej aplikacji i rozszerza klasę `Symfony\Component\HttpFoundation\Request`. Oto niektóre z najważniejszych metod.

**Zwraca adres URI żądania**

	$uri = Request::path();

**Sprawdza, czy żądanie posiada ścieżkę pasującą do wzorca**

	if (Request::is('admin/*'))
	{
		//
	}

**Pobiera adres URL żądania**

	$url = Request::url();

**Zwraca segment żądania URI**

	$segment = Request::segment(1);

**Zwraca nagłówek żądania**

	$value = Request::header('Content-Type');

**Zwraca wartości ze zmiennej $_SERVER**

	$value = Request::server('PATH_INFO');

**Określa, czy żądanie wykorzystuje AJAX**

	if (Request::ajax())
	{
		//
	}

**Określa, czy żądanie wysłane jest po protokole HTTPS**

	if (Request::secure())
	{
		//
	}
