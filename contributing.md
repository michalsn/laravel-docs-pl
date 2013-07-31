# Współtworzenie Laravel

- [Wprowadzenie](#introduction)
- [Wysyłanie zmian](#pull-requests)
- [Wytyczne odnośnie tworzenia kodu](#coding-guidelines)

<a name="introduction"></a>
## Wprowadzenie

Laravel jest frameworkiem open-source, to oznacza, że każdy może uczestniczyć w jego rozwoju i go współtworzyć. Kod źródłowy Laravel znajduje się obecnie na [Github](http://github.com), który umożliwia w łatwy sposób na stworzenie Twojej własnej kopii kodu, naniesienie zmian i późniejsze ich włączanie z powrotem do kodu frameworka.

<a name="pull-requests"></a>
## Wysyłanie zmian

Proces wysyłania zmian jest inny dla nowych funkcji i błędów. Przed wysłaniem zmian dla nowej funkcjonalności powinieneś zgłosić problem (issue) w temacie dołączając napis `[Proposal]`. Propozycja powinna opisywać nową funkcję oraz pomysły na jej implementację. Propozycja będzie oceniona i zostanie zatwierdzona lub odrzucona. Jeśli propozycja zostanie zatwierdzona, można wysłać pull-request z implementacją nowej funkcjonalności. Wysłane zmiany, które nie będą przestrzegać tych zasad będą od razu zamykane.

Przy wysyłaniu zmian dla błędów, można pominąć tworzenie propozycji. Jeśli uważasz, że znasz rozwiązanie dla błędu, który pojawił się na Github, możesz zostawić komentarz opisujący Twoją propozycję na naprawienie go.

### Prośby o nowe funkcje

Jeśli masz pomysł na nową funkcję, którą chciałbyś zobaczyć w Laravel, możesz zgłosić problem (issue) na Github, dołączając w tytule napis `[Request]`. Prośba o nową funkcjonalność zostanie oceniona przez głównego kontrybutora.

<a name="coding-guidelines"></a>
## Wytyczne odnośnie tworzenia kodu

Laravel stosuje się do standardów pisania kodu [PSR-0](https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-0.md) oraz [PSR-1](https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-1-basic-coding-standard.md). Dodatkowo, spełnione powinny być następujące standardy pisania kodu:

- Deklaracja namespace powinna znajdować się w tej samej linii co `<?php`.
- Przy nazwie klasy `{` powinien znajdować się w tej samej linii co nazwa klasy.
- Przy funkcjach i stukturach kontrolnych `{` powinien znajdować się w osobnej linii.
- Nazwy interfejsów powinny mieć suffix `Interface` (`FooInterface`)
