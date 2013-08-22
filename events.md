# Zdarzenia

- [Podstawowe użycie](#basic-usage)
- [Obserwatorzy wildcard](#wildcard-listeners)
- [Używanie klas jako obserwatorów](#using-classes-as-listeners)
- [Kolejkowanie zdarzeń](#queued-events)
- [Subskrybenci zdarzenia](#event-subscribers)

<a name="basic-usage"></a>
## Podstawowe użycie

Klasa `Event` w Laravel dostarcza prostą implementację wzorca obserwatora. Dzięki temu mamy możliwość rejestrowania i uruchamiania zdarzeń w naszej aplikacji.

**Rejestrowanie zdarzenia**

	Event::listen('user.login', function($user)
	{
		$user->last_login = new DateTime;

		$user->save();
	});

**Uruchamianie zdarzenia**

	$event = Event::fire('user.login', array($user));

Możesz również określić priorytet podczas rejestracji zdarzenia. Obserwatory o większym priorytecie będą uruchamiane wcześniej, natomiast te o tym samym priorytecie będą uruchamiane w kolejności ich deklarowania.

**Rejestrowanie zdarzenia z priorytetem**

	Event::listen('user.login', 'LoginHandler', 10);

	Event::listen('user.login', 'OtherHandler', 5);

Czasami możesz nie chcieć, aby dane zdarzenie było nadal widoczne dla innych obserwetorów. W takim wypadku wystarczy, że zwrócisz wartość `false`:

**Zatrzymanie propagacji zdarzenia**

	Event::listen('user.login', function($event)
	{
		// Handle the event...

		return false;
	});

<a name="wildcard-listeners"></a>
## Obserwatory wildcard

Kiedy rejestrujesz obserwator dla zdarzenia, możesz wykorzystać gwiazdkę, aby określić wildcard:

**Rejestrowanie zdarzenia wildcard**

	Event::listen('foo.*', function($param, $event)
	{
		// Handle the event...
	});

Ten obserwator, będzie obsługiwał wszystkie zdarzenia, które zaczynają się od `foo.`. Zwróć uwagę, że pełna nazwa zdarzenia jest przypisana jako ostatni argument.

<a name="using-classes-as-listeners"></a>
## Używanie klas jako obserwatorów

W niektórych przypadkach, możesz chcieć użyć klasy zamiast anonimowej funkcji, aby obsłużyć zdarzenie. Można to osiągnąć za pomocą [kontenera IoC](/ioc), który pozwala na skorzystanie ze wstrzykiwania zależności.

**Rejestrowanie obserwatora dla klasy**

	Event::listen('user.login', 'LoginHandler');

Domyślnie dla klasy `LoginHandle`, zostanie wywołana metoda `handle`:

**Definiowanie klasy obserwatora**

	class LoginHandler {

		public function handle($data)
		{
			//
		}

	}

Jeśli nie chcesz używać domyślnej metody `handle`, możesz określić metodę, która ma zostać zarejestrowana:

**Określanie metody do zarejestrowania**

	Event::listen('user.login', 'LoginHandler@onLogin');

<a name="queued-events"></a>
## Kolejkowanie zdarzeń

Przy użyciu metod `queue` i `flush`, możesz "kolejkować" zdarzenia do uruchomienia, ale nie uruchamiać ich od razu:

**Rejestrowanie zdarzenia do zakolejkowania**

	Event::queue('foo', array($user));

**Rejestrowanie Flushera dla zdarzenia**

	Event::flusher('foo', function($user)
	{
		//
	});

Na koniec, możesz uruchomić "flushera" i przekazać wszystkie zakolejkowane zdarzenia za pomocą metody `flush`:

	Event::flush('foo');

<a name="event-subscribers"></a>
## Subskrybenci zdarzenia

Subskrybenci zdarzenia, to klasy, które które można zapisać do wielu zdarzeń z poziomu samej klasy. Sybskrybencji, powinni mieć zdefiniowaną metodę `subscribe`, do której zostanie przekazana instancja dispatchera event:

**Definiowanie subskrybenta zdarzenia**

	class UserEventHandler {

		/**
		 * Handle user login events.
		 */
		public function onUserLogin($event)
		{
			//
		}

		/**
		 * Handle user logout events.
		 */
		public function onUserLogout($event)
		{
			//
		}

		/**
		 * Register the listeners for the subscriber.
		 *
		 * @param  Illuminate\Events\Dispatcher  $events
		 * @return array
		 */
		public function subscribe($events)
		{
			$events->listen('user.login', 'UserEventHandler@onUserLogin');

			$events->listen('user.logout', 'UserEventHandler@onUserLogout');
		}

	}

Kiedy subskrybent zostanie zdefiniowany, może zostać zarejestrowany przy pomocy klasy `Event`.

**Rejestrowanie subskrybenta zdarzeń**

	$subscriber = new UserEventHandler;

	Event::subscribe($subscriber);
