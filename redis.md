# Redis

- [Wstęp](#introduction)
- [Konfiguracja](#configuration)
- [Używanie](#usage)
- [Pipelining](#pipelining)

<a name="introduction"></a>
## Wstęp

[Redis](http://redis.io) jest otwartym, zaawansowanym zbiorem typu klucz-wartość.Często jest porównywany z serwerem struktur danych z powodu kluczy które mogą przechowywać [strings](http://redis.io/topics/data-types#strings), [hashes](http://redis.io/topics/data-types#hashes), [lists](http://redis.io/topics/data-types#lists), [sets](http://redis.io/topics/data-types#sets), and [sorted sets](http://redis.io/topics/data-types#sorted-sets).

[Redis](http://redis.io) is an open source, advanced key-value store. It is often referred to as a data structure server since keys can contain [strings](http://redis.io/topics/data-types#strings), [hashes](http://redis.io/topics/data-types#hashes), [lists](http://redis.io/topics/data-types#lists), [sets](http://redis.io/topics/data-types#sets), and [sorted sets](http://redis.io/topics/data-types#sorted-sets).

> **Note:** Jeżeli masz zainstalowane rozszerzenie (Redis PHP) za pomocą PECL, będziesz musiał zmienić alias dla niego w pliku `app/config/app.php`.

> **Note:** If you have the Redis PHP extension installed via PECL, you will need to rename the alias for Redis in your `app/config/app.php` file.

<a name="configuration"></a>
## Konfiguracja

Konfiguracja Redis dla aplikacji przechowywana jest w pliku **app/config/database.php**. W pliku tym znajdziesz tablicę **redis** zawierającą serwery Redis używane przez aplikacje:

The Redis configuration for your application is stored in the **app/config/database.php** file. Within this file, you will see a **redis** array containing the Redis servers used by your application:

	'redis' => array(

		'cluster' => true,

		'default' => array('host' => '127.0.0.1', 'port' => 6379),

	),

Konfiguracja domyślnego serwera powinna być wystarczająca dla wersji deweloperskiej. Aczkolwiek, możesz dowolnie zmieniać ustaweiania w zależności od własnego środowiska. Po prostu nadaj każdemu serwerowi Redis nazwę i ustaw odpowiedni host i port używany przez serwer.

The default server configuration should suffice for development. However, you are free to modify this array based on your environment. Simply give each Redis server a name, and specify the host and port used by the server.

The `cluster` option will tell the Laravel Redis client to perform client-side sharding across your Redis nodes, allowing you to pool nodes and create a large amount of available RAM. However, note that client-side sharding does not handle failover; therefore, is primarily suited for cached data that is available from another primary data store.

Jeżeli Twój serwer Redis wymaga uwierzetelniania, możesz ustawić hasło dodając wartość z kluczem `password` do tablicy konfigurującą dostęp do serwera.

If your Redis server requires authentication, you may supply a password by adding a `password` key / value pair to your Redis server configuration array.

<a name="usage"></a>
## Używanie

Możesz pobrać instancję Redis wywołuąc metodę `Redis::conection`:

You may get a Redis instance by calling the `Redis::connection` method:

	$redis = Redis::connection();

To pozowli Ci na pobranie zwykłej domyślnej instancji serwera Redis. Jeżeli nie używasz klastrowania po stronie serwera, możesz przekazać nazwę serwera do metody `connection` aby uzyskać dostęp do wybranego serwera z konfiguracji:

This will give you an instance of the default Redis server. If you are not using server clustering, you may pass the server name to the `connection` method to get a specific server as defined in your Redis configuration:

	$redis = Redis::connection('other');

Kiedy masz instancję klienta Redis, możesz użyć dowolnej [komendy Redis](http://redis.io/commands). Laravel używa metod magicznych aby przekazać komendy do serwera Redis:

	$redis->set('name', 'Taylor');

	$name = $redis->get('name');

	$values = $redis->lrange('names', 5, 10);

Zauważ jak argumenty komend są przekazywane do magicznej metody. Oczywiście nie jesteś zmuszony do używanai metod magicznych, możesz także używać komend za pomocą metody `command`:

	$values = $redis->command('lrange', array(5, 10));

W momencie kiedy wywołujesz komendy dla domyślnego połączenia, używaj po prostu statycznych metod magicznych dla klasy `Redis`:

	Redis::set('name', 'Taylor');

	$name = Redis::get('name');

	$values = Redis::lrange('names', 5, 10);

> **Note:** Sterowniki Redis [cache](/cache) i [session](/session) są zawarte w Laravel.

<a name="pipelining"></a>
## Pipelining

Pipelining should be used when you need to send many commands to the server in one operation. To get started, use the `pipeline` command:

**Piping Many Commands To Your Servers**

	Redis::pipeline(function($pipe)
	{
		for ($i = 0; $i < 1000; $i++)
		{
			$pipe->set("key:$i", $i);
		}
	});
