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

## <a name="chapter-api"></a> Kommunikation zwischen Back-End und den Front-Ends 
Die Kommunikation zwischen dem Back-End und den Front-Ends funktioniert über eine REST-API, die folgende Endpunkte bereitstellt.

## Angular
Die Angular Anwendung wurde in 3 Bereiche/Module unterteilt:

1. Discover
1. Saved
1. Settings

#### Discover
Innerhalb der Discover Page werden alle Events in einem ausgewählten Umkreis angezeigt.
Hierbei steht ein Slider zur Verfügung, welcher bei Änderungen seinen neuen Wert im LocalStorage des Browsers persistiert und die Events und Kartenansicht basierend auf dem neuen Suchradius aktualisiert.

Abgerufen werden die Events über den von Angular bereitgestellten ```HttpClient``` von der API Schnittstelle ```/events```.

In einer Karte, welche auf dem Framework ```OpenLayers``` basiert werden die Events in der Nähe über Marker dargestellt.

Wenn ein Event angeklickt wird öffnet sich die Detailansicht, worüber alle Informationen eingesehen werden können.
Außerdem lässt sich über die Detailansicht ein Event als Favorit markieren.

#### Saved
Auf der Saved Page werden einem Benutzer alle von ihm als Favorit markierten Events angezeigt


### Credential Management

## Flutter
UI Flutter

### Credential Management
