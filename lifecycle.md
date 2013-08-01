# Schemat działania

- [Wprowadzenie](#overview)
- [Pliki startowe](#start-files)
- [Zdarzenia aplikacji](#application-events)

<a name="overview"></a>
## Wprowadzenie

Schemat działania frameworka jest dosyć prosty. Żądanie dociera do Twojej aplikacji i jest wysyłane do odpowiedniej reguły routingu lub kontrolera. Stamtąd, odpowiedź jest wysyłana z powrotem do przeglądarki i wyświetlana na ekranie. Czasami możesz zechcieć wykonać jakieś operacje przed lub po tym jak dana reguła routingu zostanie wywołana. Mamy kilka możliwości, aby to zrobić. Pierwszą z nich jest wykorzystanie plików "startowych", a drugą zdarzeń apliakcji.

<a name="start-files"></a>
## Pliki startowe

Pliki startowe Twojej aplikacji znajdują się w katalogu `app/start`. Domyślnie dołączone są trzy pliki: `global.php`, `local.php` oraz `artisan.php`. Aby uzyskać więcej informacji na temat pliku `artisan.php`, zajrzyj do rozdziału [linia komend Artisan](/commands#registering-commands).

Plik startowy `global.php` zawiera domyślnie kilka podstawowych rzeczy, takich jak rejestracja [logowania](/errors) oraz dołączanie pliku `app/filters.php`. Możesz jednak dodać do tego pliku cokolwiek tylko chcesz. To co dodasz będzie dołączane do _każdego_ wywołania Twojej aplikacji, niezależnie od środowiska. Z drugiej strony, plik `local.php`, jest wywoływany tylko wtedy, gdy aplikacja działa w środowisku `local`. Jeśli chcesz uzyskać więcej informacji na temat środowisk, zajrzyj do rozdziału [konfiguracja](/configuration).

Oczywiście, jeśli posiadasz inne środowiska poza `local`, to dla nich również możesz utworzyć pliki startowe. Będą one automatycznie wczytywane, kiedy Twoja aplikacja będzie działała w danym środowisku.

<a name="application-events"></a>
## Zdarzenia aplikacji

Możesz wykonywać określone działania przed lub po wykonaniu żądania do aplikacji. Możesz to osiągnąć poprzez rejestrację zdarzeń `before`, `after`, `close`, `finish` lub `shutdown`: 

**Rejestrowanie zdarzeń aplikacji**

	App::before(function()
	{
		//
	});

	App::after(function($request, $response)
	{
		//
	});

Obserwatory dla tych zdarzeń będą uruchomione przed (`before`) i po (`after`) każdym żądaniu do Twojej aplikacji.
