# Instalacja

- [Zainstaluj Composer](#install-composer)
- [Zainstaluj Laravel](#install-laravel)
- [Wymagania dla serwera](#server-requirements)
- [Konfiguracja](#configuration)
- [Przyjazne adresy URL](#pretty-urls)

<a name="install-composer"></a>
## Zainstaluj Composer

Laravel wykorzystuje [Composer](http://getcomposer.org) do zarządzania swoimi zależnościami. Pierwszym krokiem jest pobranie pliku `composer.phar`. Jeśli będziesz już miał archiwum PHAR, możesz trzymać je lokalnie w katalogu swojego projektu lub przenieść je do folderu `usr/local/bin`, aby móc z niego korzystać globalnie w systemie. Na platformie Windows, dla Composera możesz użyć [instalatora Windows](https://getcomposer.org/Composer-Setup.exe).

<a name="install-laravel"></a>
## Zainstaluj Laravel

### Za pośrednictwem Composer Create-Project

Możesz zainstalować Laravel przy pomocy polecenia Composera z poziomu linii komend `create-project:

	composer create-project laravel/laravel

### Za pośrednictwem zwykłego downloadu

Jeśli masz już zainstalowany Composer, pobierz [najnowszą wersję](https://github.com/laravel/laravel/archive/master.zip) frameworka Laravel i wypakuj ją do katalogu na swoim serwerze. Następnie z posiomu głównego katalogu projektu wykonaj komendę `php composer.phar install` (lub `composer install`), aby zainstalować zależności dla frameworka. Ten proces wymaga, aby na serwerze był zainstalowany Git.

Jeśli chcesz update'ować framework Laravel, możesz skorzystać z komendy `php composer.phar update`.

<a name="server-requirements"></a>
## Wymagania dla serwera

Framework Laravel ma kilka wymagań systemowych:

- PHP >= 5.3.7
- MCrypt PHP Extension

<a name="configuration"></a>
## Konfiguracja

Laravel na początku właściwie nie wymaga dodatkowej konfiguracji. Możesz od razu przystąpić do pracy! Możesz jednak zechcieć spojrzeć na plik `app/config/app.php` i jego dokumentację. Znajduje się w nim kilka opcji, takich jak `timezone` i `locale`, które możesz chcieć zmienić w zależności od wymagań Twojej aplikacji.

> **Uwaga:** Opcją konfiguracyjną, którą na pewno powinieneś ustawić, jest opcja `key` z pliku `app/config/app.php`. Ta wartość powinna zawierać losowy, 32 znakowy ciąg. Ten klucz jest używany podczas kodowania danych. Zakodowane dane nie będą bezpieczne dopóki klucz nie będzie poprawnie ustawiony. Wartość dla klucza, może zostać ustawiona szybko poprzez następującą komendę dla artisan: `php artisan key:generate`.

<a name="permissions"></a>
### Uprawnienia

Laravel wymaga, aby dla katalogu app/storage ustawione były prawa do zapisu na serwerze.

<a name="paths"></a>
### Ścieżki

Niektóre ze ścieżek do folderów frameworka są konfigurowalne. Aby zmienić lokalizację dla tych katalogów, należy edytować plik `bootstrap/paths.php`.

> **Uwaga:** Laravel jest zaprojektowany w ten sposób, aby chronić kod Twojej aplikacji oraz pliki lokalne. Dzieje się to poprzez umieszczenie wyłącznie niezbędnych plików w folderze o dostępie publicznym. Zalecane jest również, abyś ustawił katalog public jako documentRoot (inaczej, główny katalog na który kieruje domena) lub  umieścił zawartość katalogu public w głównym folderze, a wszystkie inne pliki Laravel poza nim.

<a name="pretty-urls"></a>
## Przyjazne adresy URL

Framework dostarcza swój własny plik `public/.htaccess`, który pozwala na stosowanie adresów bez części `index.php`. Jeśli używasz serwera Apache, upewnij się, że włączony jest moduł `mod_rewrite`.

Jeśli plik `.htaccess` dostarczony razem z Laravel nie działa z Twoim serwerem Apache, możesz spróbować tej konfiguracji pliku:

	Options +FollowSymLinks
	RewriteEngine On

	RewriteCond %{REQUEST_FILENAME} !-d
	RewriteCond %{REQUEST_FILENAME} !-f
	RewriteRule ^ index.php [L]
