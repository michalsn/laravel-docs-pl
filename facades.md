# Fasady

- [Wprowadzenie](#introduction)
- [Wyjasnienie](#explanation)
- [Praktyczne wykorzystanie](#practical-usage)
- [Tworzenie fasad](#creating-facades)
- [Mocking fasad](#mocking-facades)

<a name="introduction"></a>
## Wprowadzenie

Fasady dostarczają "statyczny" interfejs dla klas, które są dostępne w [kontenerze IoC](/ioc) aplikacji. Laravel ma wiele fasad, a Ty prawdopodobnie korzystałeś z nich nawet o tym nie wiedząc!

Okazjonalnie, możesz chcieć utworzyć swoją własną fasadę, dla aplikacji lub pakietu. Zobaczmy więc na czym polega cały koncept, budowa i użytkowanie takich klas.

> **Uwaga:** Przed zagłębieniem się w temat fasad, zalecamy wcześniejsze, dokładne zapoznanie się z tym jak działa [kontener IoC](/ioc) w Laravel.

<a name="explanation"></a>
## Wyjaśnienie

W koncepcji aplikacji Laravel, fasada jest klasą która umożliwia dostęp do obiektu z kontenera. Mechanizm, który to umożliwia znajduje się w klasie `Facade`. Fasady Laravel i wszystkie niestandardowe fasady, które sam stworzysz, będą rozszerzać bazową klasę `Facade`.

Twoja klasa fasady musi mieć zaimplementowaną jedynie pojedynczą metodę: `getFacadeAccessor`. Zadaniem metody `getFacadeAccessor` jest określenie co otrzymamy z kontenera. Bazowa klasa `Fasade` używa magicznej metody `__callStatic()`, aby odwołać się z Twojej fasady do porządanego obiektu.

<a name="practical-usage"></a>
## Praktyczne wykorzystanie

W przykładzie poniżej, wykonywane jest odwołanie do systemu cache Laravel. Po spojrzeniu na kod, można odnieść wrażenie, że wywoływana jest metoda statyczna `get` z klasy `Cache`.

	$value = Cache::get('key');

Jednak jeśli spojrzymy na klasę `Illuminate\Support\Facades\Cache` zauważymy, że nie istnieje metoda statyczna `get`:

	class Cache extends Facade {

		/**
		 * Get the registered name of the component.
		 *
		 * @return string
		 */
		protected static function getFacadeAccessor() { return 'cache'; }

	}

Klasa Cache, rozszerza klasę bazową `Facade` i definiuje metodę `getFacadeAccessor()`. Pamiętaj, że zadaniem tej metody jest zwrócenie nazwy bindowania IoC.

Kiedy użytkownik odwołuje się do jakiejkolwiek statycznej metody dla fasady `Cache`, Laravel zwraca bindowanie `cache` z kontenera IoC i uruchamia żądaną metodę (w tym przypadku `get`) z tego obiektu.

tak więc, nasze wywołanie `Cache::get`, może zostać przepisane jako:

	$value = $app->make('cache')->get('key');

<a name="creating-facades"></a>
## Tworzenie fasad

Tworzenie fasady dla Twojej własnej aplikacji lub pakietu jest proste. Potrzebujesz jedynie 3 rzeczy:

- Bindowania IoC
- Klasy fasady.
- Aliasu konfiguracyjnego dla fasady.

Spójrzmy na przykład. Mamy tutaj klasę zdefiniowaną jako `PaymentGateway\Payment`.

	namespace PaymentGateway;

	class Payment {

		public function process()
		{
			//
		}

	}

Musimy być w stanie zwrócić tę klasę z kontenera IoC. Dodajmy więc bindowanie:

	App::bind('payment', function()
	{
		return new \PaymentGateway\Payment;
	});

Świetnym miejscem na rejestrację tego bindowania, byłoby stworzenie nowego [providera usług](/ioc#service-providers) o nazwie `PaymentServiceProvider` i dodanie bindowania do metody `register`. Możesz wtedy skonfigurować Laravel, aby ładowało Twojego providera usług z pliku konfiguracyjnego `app/config/app.php`.

Następnie, możemy utworzyć naszą własną klasę fasady:

	use Illuminate\Support\Facades\Facade;

	class Payment extends Facade {

		protected static function getFacadeAccessor() { return 'payment'; }

	}

Na koniec, jeśli chcemy, możemy dodać alias dla naszej fasady do tablicy `aliases` w pliku konfiguracyjnym `app/config/app.php`. Teraz, możemy wywołać metodę `process` jako instancję klasy `Payment`.

	Payment::process();

### Uwaga dotycząca automatycznego ładowania aliasów

Klasy w tablicy `aliases` nie są dostępne w niektorych instancjach ponieważ [PHP nie będzie próbował automatycznie ładować klas o niezdefiniowanym typowaniu](https://bugs.php.net/bug.php?id=39003). 
Jeśli `\ServiceWrapper\ApiTimeoutException` ma alias `ApiTimeoutException`, to `catch(ApiTimeoutException $e)` poza przestrzenią nazw `\ServiceWrapper` nigdy nie uruchomi wyjątku, nawet jeśli ten wyjątek nastapi. Podobnym problem występuje w Modelach, które są typowane do klasy aliasu. Jedynym obejściem jest zapomnienie o aliasach i skorzystanie z `use` przy klasach, które chcesz typować na górze każdego pliku, który go potrzebuje.

<a name="mocking-facades"></a>
## Mocking fasad

Testowanie jest ważnym powodem, dla którego fasady działają w taki, a nie inny sposób. Właściwie tesowanie jest głównym powodem dla którego fasady w ogóle istnieją. Po więcej informacji zajrzyj do sekcji [mocking fasad](/testing#mocking-facades) w dokumentacji.
