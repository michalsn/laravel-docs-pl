# Funkcje pomocnicze

- [Tablice](#arrays)
- [Ściezki](#paths)
- [Ciągi](#strings)
- [Adresy URL](#urls)
- [Różne](#miscellaneous)

<a name="arrays"></a>
## Tablice

### array_add

Funkcja `array_add` dodaje podaną parę klucz / wartość do tablicy, o ile dany klucz nie istnieje już w tablicy.

	$array = array('foo' => 'bar');

	$array = array_add($array, 'key', 'value');

### array_divide

Funkcja `array_divide` zwraca dwie tablice. Jedna zawiera klucze, a druga wartości oryginalnej tablicy.

	$array = array('foo' => 'bar');

	list($keys, $values) = array_divide($array);

### array_dot

Funkcja `array_dot` spłaszcza wielowymiarową tablicę do tablicy jednowymiarowej, która używa notacji "kropkowej", aby określić poziom zagnieżdżenia.

	$array = array('foo' => array('bar' => 'baz'));

	$array = array_dot($array);

	// array('foo.bar' => 'baz');

### array_except

Funkcja `array_except` usuwa podaną parę klucz / wartość z tablicy

	$array = array_except($array, array('keys', 'to', 'remove'));

### array_fetch

Funkcja `array_fetch` zwraca spłaszczoną tablicę zawierającą zaznaczony, zagnieżdżony elementy.

	$array = array(array('name' => 'Taylor'), array('name' => 'Dayle'));

	var_dump(array_fetch($array, 'name'));

	// array('Taylor', 'Dayle');

### array_first

Funkcja `array_first` zwraca pierwszy element tablicy, który spełnia określony warunek.

	$array = array(100, 200, 300);

	$value = array_first($array, function($key, $value)
	{
		return $value >= 150;
	});

Domyślna wartość może zostać przypisana jako trzeci parametr:

	$value = array_first($array, $callback, $default);

### array_flatten

Funkcja `array_flatten` spłaszczy wielowymiarową tablicę do tablicy jednowymiarowej.

	$array = array('name' => 'Joe', 'languages' => array('PHP', 'Ruby'));

	$array = array_flatten($array);

	// array('Joe', 'PHP', 'Ruby');

### array_forget

Funkcja `array_forget` usunie podaną parę klucz / wartość z głęboko zagnieżdżonej tablicy, przy pomocy notacji "kropkowej".

	$array = array('names' => array('joe' => array('programmer')));

	$array = array_forget($array, 'names.joe');

### array_get

Funkcja `array_get` zwróci wartość z głęboko zagnieżdżonej tablicy, przy pomocy notacji "kropkowej".

	$array = array('names' => array('joe' => array('programmer')));

	$value = array_get($array, 'names.joe');

### array_only

Funkcja `array_only` zwróci tylko określoną parę klucz / wartość z tablicy.

	$array = array('name' => 'Joe', 'age' => 27, 'votes' => 1);

	$array = array_only($array, array('name', 'votes'));

### array_pluck

Funkcja `array_pluck` zwróci tylko listę podanych par klucz / wartość z tablicy.

	$array = array(array('name' => 'Taylor'), array('name' => 'Dayle'));

	$array = array_pluck($array, 'name');

	// array('Taylor', 'Dayle');

### array_pull

Funkcja `array_pull` zwróci daną parę klucz / wartość z tablicy oraz jednocześnie usunie ją z niej.

	$array = array('name' => 'Taylor', 'age' => 27);

	$name = array_pull($array, 'name');

### array_set

Funkcja `array_set` ustawi wartość w głęboko zagnieżdżonej tablicy, przy pomocy notacji "kropkowej".

	$array = array('names' => array('programmer' => 'Joe'));

	array_set($array, 'names.editor', 'Taylor');

### array_sort

Funkcja `array_sort` posortuje tablicę według wyników z anonimowej funkcji. 

	$array = array(
		array('name' => 'Jill'),
		array('name' => 'Barry'),
	);

	$array = array_values(array_sort($array, function($value)
	{
		return $value['name'];
	}));

### head

Zwraca pierwszy element w tablicy. Przydatne przy chainingu metod w PHP 5.3.x.

	$first = head($this->returnsArray('foo'));

### last

Zwraca ostatni element w tablicy. Przydatne przy chainingu metod.

	$last = last($this->returnsArray('foo'));

<a name="paths"></a>
## Ścieżki

### app_path

Zwraca pełną ścieżkę do folderu `application`.

### base_path

Zwraca pełną ścieżkę do głównego folderu, w którym zainstalowano aplikację.

### public_path

Zwraca pełną ścieżkę do folderu `public`.

### storage_path

Zwraca pełną ścieżkę do folderu `application/storage`.

<a name="strings"></a>
## Ciągi

### camel_case

Zamienia podany ciąg na postać `camelCase`.

	$camel = camel_case('foo_bar');

	// fooBar

### class_basename

Zwraca nazwę klasy z danej klasy, bez przestrzeni nazw.

	$class = class_basename('Foo\Bar\Baz');

	// Baz

### e

Uruchamia funkcję `htmlentites` dla danego ciągu, ze wsparciem dla UTF-8.

	$entities = e('<html>foo</html>');

### ends_with

Określa, czy dany ciąg kończy się danym ciągiem.

	$value = ends_with('This is my name', 'name');

### snake_case

Zamienia dany ciąg na postać `snake_case`.

	$snake = snake_case('fooBar');

	// foo_bar

### starts_with

Określa, czy dany ciąg zaczyna się danym ciągiem.

	$value = starts_with('This is my name', 'This');

### str_contains

Określa, czy dany ciąg zawiera dany ciąg.

	$value = str_contains('This is my name', 'my');

### str_finish

Dodaje pojedyncze wystąpienie do danego ciągu. Usuwa dodatkowe wystąpienia.

	$string = str_finish('this/string', '/');

	// this/string/

### str_is

Określa, czy dany ciąg pasuje do podanego wzoru. Gwiazdka może być użyta jako wildcard.

	$value = str_is('foo*', 'foobar');

### str_plural

Zamienia ciąg na wersję w liczbie mnogiej (tylko dla języka angielskiego)

	$plural = str_plural('car');

### str_random

Generuje losowy ciąg o podanej długości.

	$string = str_random(40);

### str_singular

Zamienia ciąg na wersję w liczbie pojedynczej (tylko dla języka angielskiego)

	$singular = str_singular('cars');

### studly_case

Zamienia podany ciąg na postać `StudlyCase`.

	$value = studly_case('foo_bar');

	// FooBar

### trans

Tłumaczy podaną linię. Alias dla `Lang::get`.

	$value = trans('validation.required'):

### trans_choice

Tłumaczy podaną linię, biorąc pod uwagę odmianę. Alias dla `Lang::choice`.

	$value = trans_choice('foo.bar', $count);

<a name="urls"></a>
## Adresy URL

### action

Generuje adres URL do podanej akcji kontrolera.

	$url = action('HomeController@getIndex', $params);

### asset

Generuje adres URL dla zasobu.

	$url = asset('img/photo.jpg');

### link_to

Generuje link HTML dla podanego adresu URL.

	echo link_to('foo/bar', $title, $attributes = array(), $secure = null);

### link_to_asset

Generuje link HTML do podanego zasobu.

	echo link_to_asset('foo/bar.zip', $title, $attributes = array(), $secure = null);

### link_to_route

Generuje link HTML do podanego routingu.

	echo link_to_route('route.name', $title, $parameters = array(), $attributes = array());

### link_to_action

Generuje link HTML do podanej akcji kontrolera.

	echo link_to_action('HomeController@getIndex', $title, $parameters = array(), $attributes = array());

### secure_asset

Generuje link HTML do podanego zasobu przy użyciu HTTPS.

	echo secure_asset('foo/bar.zip', $title, $attributes = array());

### secure_url

Generuje pełny adres URL do podanej ścieżki przy pomocy HTTPS.

	echo secure_url('foo/bar', $parameters = array());

### url

Generuje pełny adres URL do podanej ścieżki

	echo url('foo/bar', $parameters = array(), $secure = null);

<a name="miscellaneous"></a>
## Różne

### csrf_token

Zwraca wartość aktualnego tokenu CSRF.

	$token = csrf_token();

### dd

Zrzuca podaną wartość i kończy wykonywanie skryptu.

	dd($value);

### value

Jeśli podana wartość jest anonimową funkcją, zwraca wartość zwroconą przez anonimową funkcję. W innym wypadku, zwraca wartość.

	$value = value(function() { return 'bar'; });

### with

Zwraca podany obiekt. Przydatne przy chainingu metod dla konstruktorów w PHP 5.3.x.

	$value = with(new Foo)->doWork();
