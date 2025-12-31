# Jachtseizoen-Web
Een recreatie van het populaire spel "Jachtseizoen" in React
<br>
# Informatie
Binnen deze repository kan je alle code vinden (front- en backend), voor mijn webapp recreatie van Jachtseizoen. Deze versie is volledig uitgerust met een kamersysteem, zelf te kiezen usernames, automatische teamkeuzes, en "2 factor tag verification".
## Wat?
Voor dit project worden volgende applicaties gebruikt:<br>
Frontend: React, shadcn/ui, Tailwind CSS<br>
Backend: NodeJS, ExpressJS, Socket.IO<br>
Maps: Leaflet en OpenStreetMap<br>
State management: Zustand<br>

## Hoe werkt het?
(dit proces neemt aan dat je de app niet zelf host, en dat je de publiek beschikbare versie gebruikt.)
### Begin
Voordat het spel begint maakt er iemand via het startscherm een kamer aan. Eens dat de kamer aangemaakt is, komt er een speciale kamercode tevoorschijn. Deze kunnen de andere spelers dan invullen op het startscherm. Na het toetreden EN om de 10 seconden wordt er door de backend een "room:update" event uitgestuurd naar iedereen in de kamer, met de code van de kamer, en alle spelers in de kamer, en hun relevante informatie (gebruikersnaam, team, ...).<br><br>
De host (in dit geval de persoon die de kamer heeft gemaakt) kan in de instellingen meerdere opties wijzigen, het aantal zoekers (en ook dus het aantal jagers), de locatie ping delay, en de duur van het spel.<br><br>
### Spel zelf
Eens alle gewenste spelers toegetreden zijn in de kamer, kan de host het spel starten. Na het starten worden de teams gekozen, weergegeven en wordt er een 10 seconden delay gestart tot het echt starten van het spel. Eens dat het spel gestart is, mogen de verstoppers gaan rennen. Nadat de door de host gekozen delay is afgelopen, zal er een locatie ping verstuurd worden via de "ping:request". "ping:response" en "ping:broadcast" events (de laatste is de event die de locatie van de verstoppers deelt met de jagers).
<br><br>
Het spel gaat dan door zoals een normaal Jachtseizoen spel (locatie versturen om de zoveel minuten, jagers proberen renners te vangen, ...).
<br>
### Verstopper gevangen
Wanneer een jager een renner vangt, wordt dit geverifieerd aan de hand van "Multi Factor Tag Verification", of verkort "MFTV". Hierover kan je meer lezen in het betreffend hoofdstuk.<br><br>
### Win condities
In Jachtseizoen zijn er volgende win condities (voorwaarden om een bepaald team te laten winnen): alle verstoppers gevangen OF de speltijd is afgelopen.
<br><br>
Wanneer de jagers alle verstoppers hebben gevangen, krijgen de jagers de overwinning toegekend. Dit wordt dan (alhoewel tijdelijk) weergegeven naast hun naam voor de duur van het bestaan van de lobby.
<br>
Wanneer de speltijd afgelopen is, en de jagers niet alle verstoppers hebben kunnen vangen, wordt de overwinning toegekend aan de verstoppers. Dit wordt dan (alhoewel tijdelijk) weergegeven naast hun naam voor de duur van het bestaan van de lobby.
### Na het spel
Na het spel worden alle spelers terug naar de kamer zelf gebracht, en wordt het spel zelf afgesloten. Hun vorige locatiedata wordt dus ook gewist.
### Start volgend spel
De spelers krijgen opnieuw willekeurig een team toegekend, aan de hand van het aantal gewenste zoekers. Hierna gaat het spel gewoon door zoals het vorig.<br>


## Wat is "Multi Factor Tag Verification" (MFTV)?
MFTV is een systeem waarbij beide spelers (dus de jager en verstopper in kwestie) moeten bevestigen dat er een persoon gevangen is.
### Hoe werkt het?
Bij de verstopper is er op de webpagina een knop "Gevangen?" zichtbaar. Wanneer er op die knop gedrukt is, komt er een 6 cijferige code tevoorschijn.<br>
Wanneer de jager iemand vangt, drukt die persoon op de "Gevangen!" knop naast de verstopper in kwestie. Daar kan de jager de 6 cijferige code van hiervoor invullen.<br>
Wanneer deze code is ingevuld, krijgt de verstopper een popup om te bevestigen of hij/zij/x wel degelijk gevangen is. Wanneer dit wordt bevestigd, wordt de verstopper als "gevangen" gemarkeerd, en worden die persoon zijn locatie pings niet meer verzonden.

## Alle bestaande events
### Room events
```
room:create, wordt aangeroepen bij het aanmaken van een kamer
Deze event maakt in de backend een kamer aan, waaraan andere spelers kunnen toetreden

room:join, wordt aangeroepen bij het toetreden van een kamer
Deze event voegt een speler toe aan een kamer

room:update, wordt aangeroepen bij het toetreden van een kamer, en elke 10 seconden wanneer je in een kamer zit
Deze event geeft alle spelers in de kamer terug, voor een live spelerlijst display. (dit kan veranderd worden dat na een room:join request een verzoek tot spelerlijst updaten wordt gemaakt)
```
### Locatie events
```
ping:request, wordt elke X seconden/minuten aangeroepen, waarbij X de door de host ingestelde delay is.
Deze event wordt aangeroepen om bij de verstoppers hun locatie ping te updaten.

ping:response, wordt door de client verstuurd naar de server na een ping:request event.
Deze event wordt door de verstoppers verstuurd naar de server met hun locatiegegevens voor hun plaatsing op de kaart

ping:broadcast, wordt aangeroepen nadat alle verstoppers een ping:response request hebben gestuurd naar de server
Deze event geeft de gedeelde locatie terug aan de jagers, waarna deze op hun kaart geplaatst wordt.
```
### Capture events
```
capture:claim, wordt niet gebruikt.

capture:requestConfirm, wordt verstuurt door de jager naar de backend als 1e stap in het MFTV proces
Deze event deelt met de verstopper dat er gevraagd wordt voor een confirmatie tot zijn capture binnen het spel

capture:confirmCapture (voorheen bekend als codeShown), wordt verstuurd door de verstopper bij het bekijken van zijn code.
Deze event deelt aan de jager dat de verstopper de code toont, en dat deze ingevuld kan worden aan zijn kant. Wanneer deze event verstuurd wordt VOORDAT er een "capture:requestConfirm" event verstuurd is, krijgt de verstopper een errorbericht terug.

capture:confirmCode (voorheen bekend als confirmed), wordt verstuurd door de jager bij het invullen van de verstopper zijn code.
Deze event stuurt de door de jager ingevulde code naar de server, voor verificatie 

capture:success, wordt verstuurd door de server naar elke speler in de kamer
Deze event bevestigt de capture van een verstopper, en verandert de staat van de verstopper naar "gevangen", met de hieraan gebonden veranderingen. Hieraan kunnen alle spelers zien wanneer er een verstopper gevangen is.
```

# Wanneer?
Ikzelf ben van plan dit project te starten (hoogstwaarschijnlijk) in de paasvakantie in 2026, aangezien ik dan 2 weken de tijd heb om dit af te werken.<br>
De uiteindelijke release zal natuurlijk veel later zijn (hopelijk rond de zomer). Er zal natuurlijk ook promo gemaakt worden op sociale media (YouTube, TikTok, ...)<br>

## Zelf media maken?
Dat kan zeker, en wordt erg geapprecieerd door mij. Ik vraag alleen om de website te vermelden in de beschrijving/comments, en "#jachtseizoenweb" te gebruiken. In ruil hiervoor kan je jezelf aanmelden voor speciale voordelen, zoals vroege toegang tot nieuwe functies, ...
