# Dokumentation

## Architektur
Im Projekt wurde sich dazu entschieden zwei Front-Ends anzubieten, die beide auf das gleiche Back-End zugreifen.

![Architekturübersicht](./images/architecture.png)

Während das eine Front-End auf Flutter basiert, basiert das andere Front-End auf Angular.

Das Back-End setzt sich aus einer Kombination an AWS-Services zusammen, die den Front-Ends alle nötigen Funktionen bereitstellen. Die genutzten Services sind dabei:
* Cognito
* S3
* DynamoDB
* Lambda
* API-Gateway

Während Cognito für die User-Authentifizierung und Registrierung zuständig ist, wird S3 genutzt, um zum einen den Front-Ends das Hochladen von Bildern zu ermöglichen und zum anderen zum Hosten der Angular Web-Applikation ([app.actevents.de](https://app.actevents.de)).

Über das API-Gateway wird den Front-Ends eine REST-API bereitgestellt (vgl. [Kommunikation zwischen Back-End und den Front-Ends](#chapter-api)), die für die Hauptfunktionen der Applikation zuständig ist. Durch diese REST-API werden wiederum Lambdas aufgerufen, die dann zum Beispiel auf die persistent gespeicherten Events in der DynamoDB zugreifen oder welche in dieser anlegen.

Zur Benutzerauthentifizierung wird Amazon Cognito verwendet. Dabei wird ein Benutzerpool mit zwei App-Clients erstellt, welcher die Authentifizierung an dem jeweiligen Client ermöglicht. Bei der Erstellung des Benutzerpools können verschiedene Attribute für einen Benutzer ausgewählt oder hinzugefügt werden. Für Actevents wird nur eine E-Mail Adresse benötigt. Cognito ermöglicht auch die Authentifizierung über externe Identitätsanbieter wie bspw. Google oder Facebook. 

## <a name="chapter-api"></a> Kommunikation zwischen Back-End und den Front-Ends 
Die Kommunikation zwischen dem Back-End und den Front-Ends funktioniert über eine REST-API, die folgende Endpunkte bereitstellt.

## Angular
Die Angular Anwendung wurde in 3 Bereiche/Module unterteilt:

1. Discover
1. Saved
1. Settings

### Kommunikation
Um die Kommunikation zwischen der Angular-Applikation und des API Gateways / Cognito / S3 zu vereinfachen wurden Services erstellt.
Diese kapseln die Logik, welche für die Kommunikation zu den Backend-Services benötigt wird und stellen den Komponenten eine einfache Schnittstelle bereit.

Hierfür wurde der vom Angular Framework bereitgestellte ```HttpClient``` über Dependency Injection verwendet.

Insgesamt wurden 3 Services implementiert:

1. __AuthService__, verantwortlich für die Kommunikation mit Cognito und das Credential-/Session-Management.
1. __EventsService__, verantwortlich für die Kommunikation mit dem API Gateway rund um das Abrufen und Anlegen von Events.
1. __FavoritesService__, verantwortlich für die Kommunikation mit dem API Gateway hinsichtlich der Favoriten.

### Module
#### Discover
Innerhalb der Discover Page werden alle Events in einem ausgewählten Umkreis angezeigt.
Hierbei steht ein Slider zur Verfügung, welcher bei Änderungen seinen neuen Wert im LocalStorage des Browsers persistiert und die Events und Kartenansicht basierend auf dem neuen Suchradius aktualisiert.

Abgerufen werden die Events über einen bereits angesprochenen ```EventsService```, welche die Events von der API Schnittstelle ```/events``` abruft.

In einer Karte, welche auf dem Framework ```OpenLayers``` basiert werden die Events in der Nähe über Marker dargestellt.

Wenn ein Event angeklickt wird öffnet sich die Detailansicht, worüber alle Informationen eingesehen werden können.
Außerdem lässt sich über die Detailansicht ein Event als Favorit markieren.

#### Saved
Auf der Saved Page werden einem Benutzer alle von ihm als Favorit markierten Events in einer Liste angezeigt.

Dieser werden von der API unter dem Endpunkt ```/favorites``` abgerufen.

Über einen klick auf ein gespeichertes Event wird ebenfalls die Detailseite aufgerufen.

#### Settings
Auf dieser Seite lassen sich die vom aktuell angemeldeten Nutzer erstellten Events managen.
Ebenfalls kann dieser sich über eine Schaltfläche von der Applikation abmelden und landet danach auf der Login/Register Page.

Auf der Unterseite der eigenen Events lassen sich über eine Schaltfläche neue Events erstellen.
Hierzu wird ein Formular geöffnet, welches mithilfe der Forms Funktionalität von Angular erstellt wurde und Validierung und Anzeige von Fehleingaben automatisch handhabt.

Um ein Event zu erstellen oder wieder zu löschen wird wieder der bereits angesprochene ```EventsService``` verwendet.


### Credential Management

Für die Kommunikation und das Credential-/Session-Management wurde die Amplify-Bibliothek von AWS verwendet.
Diese kapselt die direkte Kommunikation mit verschiedensten AWS Diensten und stellt eine schöne Schnittstelle bereit.

Innerhalb der Angular Applikation wurde nur der _Auth_ Teil von Amplify verwendet.

Über Methoden wie ```Auth.signIn(...)```, ```Auth.signUp(...)``` oder ```Auth.signOut()``` kann der Benutzer so angemeldet, registriert oder abgemeldet werden.
Cognito handhabt dabei das komplette Credentialmanagement wie das Speichern (LocalStorage) und Erneuern von ID-/Access-/Refresh-Tokens zur Authentifizierung über Cognito, ohne dass ein manuelles Eingreifen nötig wäre.
## Persistenz
Damit Events gespeichert bleiben, wird Amazon DynamoDB verwendet. DynamoDB ist eine NoSQL-Datenbank. Die Events haben folgende Attribute in der Datenbank.

* id: Die UUID des Events
* dates: Die Anfangs- und Endzeit des Events 
  * begin
  * end
* description: Die Beschreibung des Events
* image: Die UUID des zugehörigen Bildes im S3-Bucket
* location: Der Ort des Events in geographischen Koordinaten
  * latitude
  * longitude
* name: Der Name des Events
* organizer: Der Ersteller bzw. Organisator des Events
* price: Der Preis des Events
* tags: Die Tags des Events

Das Persistieren der Bilder folgt über einen Amazon S3-Bucket. Hier können die einzelnen Bilder abgelegt werden. Der Name eines solchen Bildes ist hierbei eine UUID. Diese ist in der Datenbank bei dem zugehörigen Event abgelegt. Das Hochladen der Bilder folgt über einen generierten Link. Dieser enthält Parameter wie den S3-Bucket, den Name der Datei und den zulässigen Inhalt der  hochgeladen werden darf.