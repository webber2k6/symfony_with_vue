# SPAs mit Vue.js und Symfony
Diese Single Page Application ist auf Basis des Artikels von Jörg Moldenhauer im Entwickler Magazin entstanden.

## Umgebung aufsetzen
Die docker-compose.yml enthält einen PHP-Container mit Webserver und Composer und einen MariaDB-Container. 

## Projekt aufsetzen
Die Installation des Symfony-Backends erfolgt mit Composer.

* `composer create-project symfony/skeleton contact-manager`

Anschließend ist die Anwendung bereits im Webserver aufrufbar und zeigt die Standardseite.

## API erstellen
Symfony kann um zusätzliche Pakete erweitert werden, um Entitäten zu generieren, die Datenbank zu migrieren und
eine API-Dokumentation auszugeben. Dazu werden folgende Pakete installiert:

* `composer require maker debug -dev`
* `composer require api`

## Datenbank migrieren
In der `.env` Datei muss die korrekte Datenbankverbindung hinterlegt werden. Diese ergibt sich aus dem Namen
des Datenbankservers, dem Benutzername, dem Passwort, dem Datenbankname und der Version des Datenbanksystems.

> mysql://root:root@localhost:3306/db?serverVersion=mariadb-10.6.5

Mit einem eigenen Docker-Container wird die Datenbank initial erstellt. Auf einer bestehenden Datenbank-Instanz
kann die Datenbank mit dem Befehl `bin/console doctrine:database:create` erstellt werden.

## Entitäten anlegen
Das Maker Bundle kann zum Generieren von Entitäten verwendet werden. Der Befehl `bin/console make:entity` startet
einen kleinen Assistenten, der den zugehörigen Code generiert.

Im Beispiel wird eine Entität `Contact` mit den Feldern `firstName`, `lastName` und `emailAddress` angelegt.

Anschließend kann eine neue Migration angelegt und in die Datenbank übernommen werden.

* `bin/console make:migration`
* `bin/console doctrine:migrations:migrate`

Die Definition der Schnittstelle inklusive der generierten Endpunkte kann unter der URL `/api` abgerufen werden.
Einzelne Endpunkte können darüber auch getestet werden.

## Validierung

Mithilfe von Annotationen können Validatoren zu einer Entität bzw. einzelnen Eigenschaften hinzugefügt werden.
Diese werden zum großen Teil mit Symfony mitgeliefert, können aber auch selbst erstellt werden.

```
use Doctrine\ORM\Mapping as ORM;
use Symfony\Component\Validator\Constraints as Assert
class Contact
{
    #[Assert\NotBlank]
    #[ORM\Column(type: 'string', length: 255)]
    private string $firstName;
}
```

## Vue.js installieren

Zur Integration von webpack wird die Symfony Komponente Webpack Encore verwendet.

* `composer require encore`

Die Dateien für Symfony UX werden nicht benötigt und können wieder entfernt werden:

* /assets/*
* `@symfony/stimulus-bridge` aus der Datei `package.json`
* `enableStimulusBridge(...)` aus der Datei `webpack.config.js`

Mithilfe von Yarn werden die JavaScript-Abhängigkeiten installiert:

* `yarn add vue@next vue-loader@^16.7.0 @vue/compiler-sfc vue-router@next axios --dev`

In meinem Fall war es erforderlich weitere Babel-Pakete zu installieren.

* `yarn add @babel/core @babel/plugin-proposal-class-properties @babel/plugin-syntax-dynamic-import @babel/preset-env`

Anschließend kann webpack mit dem Kompilieren beginnen.

* `yarn dev-server`
