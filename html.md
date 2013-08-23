# Formularze i HTML

- [Otwieranie formularza](#opening-a-form)
- [Ochrona CSRF](#csrf-protection)
- [Bindowanie formularza modelem](#form-model-binding)
- [Pola label](#labels)
- [Pola text, textarea, password i hidden](#text)
- [Pola checkboxes i radio](#checkboxes-and-radio-buttons)
- [Pole file](#file-input)
- [Listy dropdown](#drop-down-lists)
- [Przyciski](#buttons)
- [Tworzenie makr](#custom-macros)

<a name="opening-a-form"></a>
## Otwieranie formularza

**Otwieranie formularza**

	{{ Form::open(array('url' => 'foo/bar')) }}
		//
	{{ Form::close() }}

Domyślnie, użyta zostanie metoda `POST`; jednak możesz określić metodę samodzielnie:

	echo Form::open(array('url' => 'foo/bar', 'method' => 'put'))

> **Uwaga:** Ponieważ formularze HTML wspierają jedynie metody `POST` i `GET`, to metody `PUT` i `DELETE` będą emulowane poprzez automatyczne dodanie ukrytego pola `_method` w Twoim formularzu.

Możesz również otworzyć formularz, który będzie kierował do nazwanej reguły routingu lub akcji kontrolera:

	echo Form::open(array('route' => 'route.name'))

	echo Form::open(array('action' => 'Controller@method'))

Możesz też dołączać parametry dla routingu:

	echo Form::open(array('route' => array('route.name', $user->id)))

	echo Form::open(array('action' => array('Controller@method', $user->id)))

Jeśli Twój formularz ma obsługiwać wgrywanie plików, musisz dodać opcję `files` do swojej tablicy:

	echo Form::open(array('url' => 'foo/bar', 'files' => true))

<a name="csrf-protection"></a>
## Ochrona CSRF

Laravel dostarcza łatwy sposób na ochronę Twojej aplikacji przed cross-site request forgeries. Najpierw, w Twojej sesji umieszczany jest losowy token. Nie przejmuj się, wszystko odbywa się automatycznie. Token CSRF będzie dodawany do Twoich formularzy automatycznie, jako ukryte pole. Jednak, jeśli chciałbyś sam wygenerować kod HTML dla ukrytego pola, możesz skorzystać z metody `token`:

**Dodawanie tokenu CSRF do formularza**

	echo Form::token();

**Dołączanie filtra CSRF do reguły routingu**

	Route::post('profile', array('before' => 'csrf', function()
	{
		//
	}));

<a name="form-model-binding"></a>
## Bindowanie formularza modelem

Często będziesz chciał wypelnić formularz na podstawie zawartości modelu. Aby to zrobić, możesz użyć metody `Form::model`:

**Otwieranie formularza modelu**

	echo Form::model($user, array('route' => array('user.update', $user->id)))

Jeśli teraz wygenerujesz element formularza, jak np. pole tekstowe, to wartość z modelu, która odpowiada nazwie pola zostanie automatycznie ustawiona, jako wartośc tego pola. Tak więc dla przykładu, jeśli pole tekstowe ma nazwę `email`, to zostanie podstawiona wartość z modelu z atrybutu o nazwie `email`. To jednak nie wszystko! Jeśli w sesji, w danych flash istnieje element, który odpowiada nazwie pola, to zostanie on ustawiony jako wartość tego pola, a nie atrybut z modelu. Tak więc, kolejność priorytetów wygląda następująco:
Now, when you generate a form element, like a text input, the model's value matching the field's name will automatically be set as the field value. So, for example, for a text input named `email`, the user model's `email` attribute would be set as the value. However, there's more! If there is an item in the Session flash data matching the input name, that will take precedence over the model's value. So, the priority looks like this:

1. Dane flash sesji (stare dane wejściowe)
2. Bezpośrednio przekazana wartość
3. Atrybut z modelu

To pozwala na szybką budowę formularzy, które nie tylko pobierają wartości z modelu, ale są również w łatwy sposób wypełniane ponownie, kiedy wystąpią błędy walidacji.

> **Uwaga:** Kiedy używasz `Form::model` upewnij się, zamykasz swój formularz za pomocą `Form::close`!

<a name="labels"></a>
## Pola label

**Generowanie elementu label**

	echo Form::label('email', 'E-Mail Address');

**Określanie dodatkowych atrybutów HTML**

	echo Form::label('email', 'E-Mail Address', array('class' => 'awesome'));

> **Uwaga:** Po stworzeniu pola label, każdy element formularza o nazwie odpowiadającej nazwie pola label otrzyma automatycznie ID odpowiadający nazwie pola label.

<a name="text"></a>
## Pola text, textarea, password i hidden

**Generowanie pola text**

	echo Form::text('username');

**Określanie domyślnej wartości**

	echo Form::text('email', 'example@gmail.com');

> **Uwaga:** Metody *hidden* i *textarea* działają na tej samej zaszadzie, co metoda *text*.

**Generowanie pola password**

	echo Form::password('password');
	
**Generowanie innych pól formularza**

	echo Form::email($name, $value = null, $attributes = array());
	echo Form::file($name, $attributes = array());
	
<a name="checkboxes-and-radio-buttons"></a>
## Pola checkbox i radio

**Generowanie pola checkbox lub radio**

	echo Form::checkbox('name', 'value');
	
	echo Form::radio('name', 'value');

**Generowanie zaznaczonego pola checkbox lub radio**

	echo Form::checkbox('name', 'value', true);
	
	echo Form::radio('name', 'value', true);

<a name="file-input"></a>
## Pole file

**Generowanie pola file**

	echo Form::file('image');

<a name="drop-down-lists"></a>
## Listy dropdown

**Generowanie listy dropdown**

	echo Form::select('size', array('L' => 'Large', 'S' => 'Small'));

**Generowanie listy dropdown z zaznaczoną domyślną wartością**

	echo Form::select('size', array('L' => 'Large', 'S' => 'Small'), 'S');

**Generowanie pogrupowanej listy**

	echo Form::select('animal', array(
		'Cats' => array('leopard' => 'Leopard'),
		'Dogs' => array('spaniel' => 'Spaniel'),
	));

<a name="buttons"></a>
## Przyciski

**Generowanie przycisku submit**

	echo Form::submit('Click Me!');

> **Uwaga:** Musisz utworzyć emenent button? Skorzystaj z metody *button*. Działa ona na tej samej zasadzie, co metoda *submit*.

<a name="custom-macros"></a>
## Tworzenie makr

Łatwo jest definiować swoje własne helpery dla klasy formularza, które nazywają się "makra". Oto jak to działa. Najpierw zarejestruj makro poprzez podanie nazwy w anonimowej funkcji:

**Rejestrowanie makra dla formularza**

	Form::macro('myField', function()
	{
		return '<input type="awesome">';
	});

Teraz możesz wywołać swoje makro poprzez użycie jego nazwy:

**Wywoływanie makra formularza**

	echo Form::myField();
