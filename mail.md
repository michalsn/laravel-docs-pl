# Poczta

- [Konfiguracja](#configuration)
- [Podstawowe użycie](#basic-usage)
- [Osadzanie załączników inline](#embedding-inline-attachments)
- [Kolejkowanie poczty](#queueing-mail)
- [Poczta i budowa aplikacji lokalnie](#mail-and-local-development)

<a name="configuration"> </ a>
## Konfiguracja

Laravel zapewnia czysty, prosty interfejs API dla popularnej biblioteki [SwiftMailer] (http://swiftmailer.org). Plikiem konfiguracji poczty jest  `app/config/mail.php` i zawiera opcje pozwalające na zmianę hosta SMTP, port i danych dostępu, a także ustawić globalny parametr `from` dla wszystkich wiadomości dostarczonych przez bibliotekę. Możesz użyć dowolnego serwera SMTP, który chcesz. Jeśli chcesz korzystać z funkcji PHP `mail` aby wysłać pocztę, możesz zmienić parametr `driver` na wartość `mail` w pliku konfiguracyjnym. Sterownik `sendmail` jest również dostępny.

<a name="basic-usage"> </ a>
## Podstawowe użycie

Metoda `Mail::send` służy do wysyłania wiadomości e-mail:

Mail::send('emails.welcome', $data, function($message)
{
$message->to('foo@example.com', 'John Smith')->subject('Welcome!');
});

Pierwszy argument przekazywany do metody `send` to nazwa widoku, który powinien zostać użyty jako treść wiadomości. Drugi, to `$data` zawierający dane które powinny być przekazywane do widoku, natomiast trzeci jest funkcją Closure pozwalającą określić dodatkowe opcje dotyczące wiadomości e-mail.

> ** Note:** Zmienna `$message` jest zawsze przekazywana do widoku wiadomości e-mail, a także pozwala na osadzanie załączników inline. Najlepiej uniknąć przekazywania zmiennej `$message` w widoku wiadomości.

Możesz także określić zwykły widok tekstu do użycia w dodatku do widoku HTML:

Mail::send(array('html.view', 'text.view'), $data, $callback);

Alternatywnie, możesz określić tylko jeden typ widoku za pomocą kluczy `html` lub `text`:

Mail::send(array('text' => 'view'), $data, $callback);

Możesz określić inne opcje dla wiadomości e-mail, takie jak kopie wiadomości czy załączniki:

Mail::send('emails.welcome', $data, function($message)
{
$message->from('us@example.com', 'Laravel');

$message->to('foo@example.com')->cc('bar@example.com');

$message->attach($pathToFile);
});

Podczas załączania plików do wiadomości, można również określić typ MIME oraz/lub nazwy pliku:

$message->attach($pathToFile, array('as' => $display, 'mime' => $mime));

> ** Note: ** Instancja `$message` przekazywana do funkcji Closure jest rozbudowaniem klasy wiadomości biblioteki SwiftMailer, pozwala to na wywołanie dowolnej metody tej klasy do zbudowania wiadomości e-mail.

<a name="embedding-inline-attachments"> </ a>
## Osadzanie załączników inline

Osadzanie obrazów w wiadomości jest zazwyczaj skomplikowane. Laravel natomiast dostarcza wygodny sposób do załączania obrazów do Twoich wiadomości i dostarcza właściwy CID.

** Osadzanie obrazu w widoku wiadomości e-mail **

<body>
Oto zdjęcie:

<img src="<?php echo $message->embed($pathToFile); ?>">
</body>

**Wstawianie surowych danych w widoku e-mail**

<body>
Oto obraz z surowymi danymi:

<img src="<?php echo $message->embedData($data, $name); ?> ">
</body>

Należy pamiętać, że zmienna `$message` jest zawsze przekazywana do widoku e-mail przez klasę `Mail`.

<a name="queueing-mail"> </ a>
## Kolejkowanie poczty

Ponieważ wysyłanie wiadomości e-mail może wydłużyć responsywność Twojej aplikacji, wielu programistów decyduje się na kolejkowanie wiadomości do wysyłania w tle. Laravel ułatwia tą czynność wbudowanym [API do kolwjkowania](/queues). Aby dodać wiadomość do kolejki, po prostu użyj metody `queue` klasy `Mail`:

**Kolejkowanie wiadomości**

Mail::queue('emails.welcome', $data, function($message)
{
$message->to('foo@example.com', 'John Smith')->subject('Welcome!');
});

Możesz również określić ilość sekund za ile chcesz wysłać wiadomość używając metody `later`:

Mail::later(5, 'emails.welcome', $data, function($message)
{
$message->to('foo@example.com', 'John Smith')->subject('Welcome!');
});

Jeżeli chcesz określić do której kolejki dodać wiadomość możez to zrobić za pomocą metod `queueOn i `laterOn`:

Mail::queueOn('queue-name', 'emails.welcome', $data, function($message)
{
$message->to('foo@example.com', 'John Smith')->subject('Welcome!');
});

<a name="mail-and-local-development"> </ a>
## Poczta i budowa aplikacji lokalnie

Podczas tworzenia aplikacji wysyłającej wiadomości e-mail zwykle chce się wyłączyć opcję wysyłania wiadomości z Twojej maszyny lokalnej. Aby to zrobić musisz korzystać z metody `Mail::pretend` lub ustawić opcję `pretend` w pliku `app/config/mail.php` na `true`. Kiedy obiekt do wysyłania poczty jest w trybie `pretend` wiadomość będzie zapisana w logach aplikacji zamiast wysłana do odbiorcy.

**Włączanie trybu pretend**

Mail::pretend();
