# Flutter
Die Flutter Anwendung ist in zwei Stufen aufgeteilt.
1. Nicht angemeldet
    
    Wenn der Benutzer nicht angemeldet ist hat er die Möglichkeit sich anzumelden oder zu registrieren. Bei erfolgreichem Anmelden wird er auf die Angemeldet Stufe weitergeleitet.

    __Logisseite:__    
    ![](images/flutter_screenshot1.png)
    __Registierungsseite:__
    ![](images/flutter_screenshot2.png)

1. Angemeldet
    
    Sobald der Benutzer angemeldet ist, landet dieser auf der ``Findenseite``. Die Anmeldung ist auch über die App laufzeit hinweg gespeichert.

![](images/flutter_page_navigation.png)

## Event Pages

### _Findenseite_

1. Auf der Findenseite werden alle aktuellen Events angezeigt die sich in einem gewissen Umkreis(default 50km) vom Standort des Gerätes. Der Umkreis kann über die Filteroptionen angepasst werden.
![](images/flutter_screenshot3.png)
1. Für eine __Detailansicht des jeweiligen Events__ kann auf das Event getippt werden. Auf der Detailsseite sind dann nochmal zusätzliche Informationen wie die Beschreibung und der Zeitraum des Events ersichtlich.
![](images/flutter_screenshot10.png)
1. Für das __Speichern in die Favoriten__ muss auf den Stern getippt werden.
1. Für das __Anlegen eines neuen Events__ kann der Plusbutton benutzt werden. Danach wird der Nutzer auf eine neue Anlegenseite weitergeleitet.
![](images/flutter_screenshot5.png)
![](images/flutter_screenshot6.png)
Nach Eingabe der Daten werden diese validiert und an den Server geschickt.
### _Favoritenseite_
Hier werden alle Events angezeigt die der Benutzer individuell gespeichert hat.
![](images/flutter_screenshot4.png)
### _Profilseite_
Auf der Profilseite kann sich der Benutzer abmelden und kann die eigenen Events gelöschen.
![](images/flutter_screenshot8.png)
![](images/flutter_screenshot9.png)

## Credential Management

Die Logindaten werden über Amplify gespeichert und für die Appservices bereitgestellt.

## Verwendete Abhängigkeiten

- amplify_flutter: Framework für die Kommunikation mit AWS Services
- amplify_auth_cognito: Framework für die Kommunikation mit AWS Cognito
- geolocator: Bibliothek für die Geolokation
- flutter_map: Bibliothek für Karten UI Elemente
- camera: Bibliothek für die Kamera UI Elemente


## Services

- AuthService: Verwaltet die An- und Abmeldung der Session
- LocationService: Verwaltet Position und GPS-Sensor
- ApiService: Fasst API-Zugriffe in einem Service zusammen
