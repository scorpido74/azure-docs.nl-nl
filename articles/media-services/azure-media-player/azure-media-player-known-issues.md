---
title: Bekende problemen met Azure Media Player
description: De huidige release heeft de volgende bekende problemen.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.openlocfilehash: ff8dc58b9122e5173a9a6065e2efdbc5697be0d7
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727214"
---
# <a name="known-issues"></a>Bekende problemen #

De huidige release heeft de volgende bekende problemen:

## <a name="azure-media-player"></a>Azure Media Player ##

- Onjuist geconfigureerde encoders kunnen problemen veroorzaken met het afspelen
- Streams met tijdstempels groter dan 2^53 kunnen problemen met afspelen hebben.
  - Mitigatie: gebruik 90 kHz video en 44,1 kHz audiotijdschalen
- Geen automatisch afspelen op mobiele apparaten zonder interactie van de gebruiker. Het is geblokkeerd door het platform.
- Zoeken in de buurt van discontinuïteiten kan leiden tot het afspelen van fouten.
- Het downloaden van grote presentaties kan ertoe leiden dat de gebruikersinterface wordt vergrendeld.
- Kan dezelfde bron niet automatisch opnieuw afspelen nadat de presentatie is afgelopen.
  - Als u een bron opnieuw wilt afspelen nadat deze is beëindigd, moet de bron opnieuw worden ingesteld.
- Lege manifesten kunnen problemen veroorzaken met de speler.
  - Dit probleem kan optreden bij het starten van een live stream en er worden niet genoeg brokken gevonden in het manifest.
- Afspeelpositie misschien buiten de zoekbalk van de gebruikersinterface.
- Het bestellen van evenementen is niet consistent voor alle technici.
- Gebufferde eigenschap is niet consistent tussen techs.
- nativeControlsForTouch moet false (default) zijn om te voorkomen dat 'Object geen eigenschap of methode 'setControls' ondersteunt'
- Posters moeten nu absolute urls zijn
- Kleine esthetische problemen kunnen optreden in de gebruikersinterface bij het gebruik van de modus met een hoog contrast van het apparaat
- URL's die "%" of '+' in de volledig gedecodeerde tekenreeks bevatten, kunnen problemen hebben met het instellen van de bron

## <a name="ad-insertion"></a>Advertentie-invoeging ##

- Advertenties kunnen problemen hebben met het plaatsen (op aanvraag of live) wanneer een adblocker in de browser wordt geïnstalleerd
- Mobiele apparaten kunnen problemen hebben met het afspelen van advertenties.

## <a name="azurehtml5js"></a>AzureHtml5JS ##

- In het DVR-venster van Live-inhoud blijft de tijdlijn, wanneer de inhoud is voltooid, groeien totdat de tijdlijn naar het gebied wordt gezocht of het einde van de presentatie bereikt.
- Live presentaties in Firefox met MSE ingeschakeld heeft een aantal problemen
- Assets die alleen audio of video zijn, worden niet afgespeeld via de AzureHtml5JS-technologie.
  - Als u elementen wilt afspelen zonder audio of video, u dit doen door lege audio of video in te voegen met het [azure media services explorer-hulpprogramma](https://aka.ms/amse)
    - Instructies voor het invoegen van stille audio vindt u [hier](https://azure.microsoft.com/documentation/articles/media-services-advanced-encoding-with-mes/#silent_audio)

## <a name="flash"></a>Flash ##

- AES-inhoud wordt niet afgespeeld in Flash-versie 30.0.0.134 vanwege een bug in de cachelogica van Adobe. Adobe heeft het probleem opgelost en uitgebracht in 30.0.0.154
- Tech en http mislukkingen (zoals 404 netwerk time-outs), zal de speler langer duren om te herstellen dan andere techs.
- Klik op video gebied met flashSS tech zal niet spelen / pauzeren van de speler.
- Als de gebruiker Flash heeft geïnstalleerd, maar geen toestemming geeft om het op de site te laden, kan er oneindig worden spinnen. Dit komt omdat de speler denkt dat de plugin is geïnstalleerd en beschikbaar en het denkt dat de plugin draait de inhoud. JavaScript-code is verzonden, maar de browserinstellingen hebben de plug-in geblokkeerd totdat de gebruiker de prompt accepteert om de plug-in toe te staan. Dit kan in alle browsers gebeuren.  

## <a name="silverlight"></a>Silverlight ##

- Ontbrekende functies
- Tech en http mislukkingen (zoals 404 netwerk time-outs), zal de speler langer duren om te herstellen dan andere techs.
- Safari en Firefox op Mac afspelen met `"http://` Silverlight vereist expliciet definiëren of `https://` voor de bron.
- Als een API ontbreekt voor deze technologie, zal het over het algemeen null retourneren.
- Als de gebruiker Flash heeft geïnstalleerd, maar geen toestemming geeft om het op de site te laden, kan er oneindig worden spinnen. Dit komt omdat de speler denkt dat de plugin is geïnstalleerd en beschikbaar en het denkt dat de plugin draait de inhoud. JavaScript-code is verzonden, maar de browserinstellingen hebben de plug-in geblokkeerd totdat de gebruiker de prompt accepteert om de plug-in toe te staan. Dit kan in alle browsers gebeuren.  

## <a name="native-html5"></a>Native HTML5 ##

- Html5-technologie verzendt alleen canplaythrough-gebeurtenissen voor de eerste setbron.
- Deze technologie ondersteunt alleen wat de browser heeft geïmplementeerd.  Sommige functies kunnen ontbreken in deze tech.  
- Als een API ontbreekt voor deze technologie, zal het over het algemeen null retourneren.
- Er zijn problemen met bijschriften en ondertitels op deze tech. Ze kunnen wel of niet beschikbaar zijn of zichtbaar op deze tech.
- Het hebben van beperkte bandbreedte in HLS/ Html5 tech scenario resulteert in audio afspelen zonder video.

### <a name="microsoft"></a>Microsoft ###

- IE8-weergave werkt momenteel niet vanwege onverenigbaarheid met ECMAScript 5
- In IE en sommige versies van Edge kan het volledige scherm niet worden ingevoerd door tabben op de knop en deze te selecteren of de F/f hotkey te gebruiken.

## <a name="google"></a>Google ##

- Meerdere coderingsprofielen in hetzelfde manifest hebben problemen met het afspelen in Chrome en worden niet aanbevolen.
- Chrome kan HE-AAC niet afspelen met AzureHtml5JS. Volg details op de [bug tracker](https://bugs.chromium.org/p/chromium/issues/detail?id=534301).
- Vanaf Chrome v58 moet widevine-inhoud worden geladen/afgespeeld via het https:// protocol anders wordt het afspelen mislukt.

## <a name="mozilla"></a>Mozilla ##

- Voor audiostreamswitch zijn audiostreams vereist dat ze dezelfde codec-privégegevens hebben wanneer ze AzureHtml5JS gebruiken. Firefox-platform vereist dit.

## <a name="apple"></a>Apple ##

- Safari op Mac maakt de Power Saver-modus standaard mogelijk met de instelling 'Stop plug-ins to save power", die plug-ins zoals Flash en Silverlight blokkeert wanneer ze geloven dat het niet in het voordeel is van de gebruiker. Dit blok blokkeert niet de bestaande plugin, alleen mogelijkheden. Gezien de standaard techOrder kan dit problemen veroorzaken bij het
  - Mitigatie 1: Als de videospeler 'front and center' is (binnen een grens van 3000 x 3000 pixels vanaf de linkerbovenhoek van het document), moet deze nog steeds worden afgespeeld.
  - Mitigatie 2: Wijzig de techOrder voor Safari als ["azureHtml5JS", "html5"]. Deze mitigatie heeft implicatie van het niet krijgen van alle functies die beschikbaar zijn in de FlashSS tech.
- PlayReady-content via Silverlight kan problemen hebben met het afspelen in Safari.
- AES- en beperkte token-inhoud wordt niet afgespeeld met iOS- en oudere Android-apparaten.
  - Om dit scenario te bereiken, moet een proxy worden toegevoegd aan uw service.
- Standaardskin voor iOS Phone wordt weergegeven.
- iPhone afspelen gebeurt altijd in de native player fullscreen.
  - Functies, waaronder bijschriften, blijven mogelijk niet bestaan in deze niet-inline weergave.
- Wanneer de live presentatie is afgelopen, beëindigen iOS-apparaten de presentatie niet goed.
- iOS staat geen DVR-mogelijkheden toe.
- iOS audiostream-switch kan alleen worden gedaan via de gebruikersinterface van iOS native player en vereist dat audiostreams dezelfde codec-privégegevens hebben
- Oudere versies van Safari kunnen mogelijk problemen hebben met het afspelen van livestreams.

## <a name="older-android"></a>Oudere Android ##

- AES- en beperkte token-inhoud wordt niet afgespeeld met iOS- en oudere Android-apparaten.
  - Om dit scenario te bereiken, moet een proxy worden toegevoegd aan uw service.

## <a name="next-steps"></a>Volgende stappen ##

- [Snel start azure mediaspeler](azure-media-player-quickstart.md)