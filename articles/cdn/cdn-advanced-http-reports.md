---
title: Gebruiksstatistieken analyseren met geavanceerde HTTP-rapporten van Azure CDN | Microsoft Documenten
description: Meer informatie over het maken van geavanceerde HTTP-rapporten in Microsoft Azure CDN. Deze rapporten geven gedetailleerde informatie over cdn-activiteiten.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: ef90adc1-580e-4955-8ff1-bde3f3cafc5d
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 0b0eec2425f8a1663eb7a09c83a6bad037d1d79c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67594111"
---
# <a name="analyze-usage-statistics-with-azure-cdn-advanced-http-reports"></a>Gebruiksstatistieken analyseren met geavanceerde HTTP-rapporten van Azure CDN
## <a name="overview"></a>Overzicht
In dit document wordt geavanceerde HTTP-rapportage in Microsoft Azure CDN uitgelegd. Deze rapporten geven gedetailleerde informatie over cdn-activiteiten.

[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="accessing-advanced-http-reports"></a>Toegang tot geavanceerde HTTP-rapporten
1. Klik in het CDN-profielblad op de knop **Beheren.**
   
    ![Knop CDN-profielblad beheer](./media/cdn-advanced-http-reports/cdn-manage-btn.png)
   
    Het CDN-beheerportaal wordt geopend.
2. Plaats de plaats op het tabblad **Analytics** en plaats de plaats vervolgens boven de **flyout Geavanceerde HTTP-rapporten.**  Klik op **HTTP Large Platform**.
   
    ![CDN-beheerportal - menu Geavanceerde rapporten](./media/cdn-advanced-http-reports/cdn-advanced-reports.png)
   
    Rapportopties worden weergegeven.

## <a name="geography-reports-map-based"></a>Aardrijkskunderapporten (op basis van kaarten)
Er zijn vijf rapporten die gebruik maken van een kaart om aan te geven van welke regio's uw inhoud wordt aangevraagd. Deze rapporten zijn World Map, United States Map, Canada Map, Europe Map en Asia Pacific Map.

Elk rapport op basis van kaarten rangschikt geografische entiteiten (d.w.z. landen/regio's, er wordt een kaart verstrekt waarmee u visualiseren van welke locaties uw inhoud wordt aangevraagd. Het is in staat om dit te doen door kleurcodering elke regio op basis van de hoeveelheid vraag ervaren in die regio. Lichtere gearceerde gebieden geven een lagere vraag naar uw inhoud aan, terwijl donkere regio's wijzen op een hogere vraag naar uw inhoud.

Gedetailleerde verkeers- en bandbreedte-informatie voor elke regio wordt direct onder de kaart weergegeven. Hiermee u het totale aantal treffers, het percentage treffers, de totale hoeveelheid overgedragen gegevens (in gigabytes) en het percentage overgedragen gegevens voor elke regio bekijken. Bekijk een beschrijving voor elk van deze statistieken. Ten slotte worden de naam en het percentage treffers dat zich in de regio in de regio heeft voorgedaan, weergegeven als een knopinfo wanneer u boven een regio zweeft (d.w.z. land/regio, staat of provincie).

Hieronder vindt u een korte beschrijving voor elk type aardrijkskunderapport op basis van kaarten.

| Rapportnaam | Beschrijving |
| --- | --- |
| Wereldkaart |Met dit rapport u de wereldwijde vraag naar uw CDN-inhoud bekijken. Elk land/regio heeft een kleurcode op de wereldkaart om het percentage hits aan te geven dat afkomstig is uit die regio. |
| Kaart van de Verenigde Staten |Met dit rapport u de vraag naar uw CDN-inhoud in de Verenigde Staten bekijken. Elke status is kleurgecodeerd op deze kaart om het percentage treffers aan te geven dat afkomstig is uit dat gebied. |
| Canada Kaart |Met dit rapport u de vraag naar uw CDN-inhoud in Canada bekijken. Elke provincie is kleurgecodeerd op deze kaart om het percentage treffers aan te geven dat afkomstig is uit die regio. |
| Europakaart |Met dit rapport u de vraag naar uw CDN-inhoud in Europa bekijken. Elk land/regio heeft kleurgecodeerd op deze kaart om het percentage treffers aan te geven dat afkomstig is uit die regio. |
| Asia Pacific Kaart |Met dit rapport u de vraag naar uw CDN-inhoud in Azië bekijken. Elk land/regio heeft kleurgecodeerd op deze kaart om het percentage treffers aan te geven dat afkomstig is uit die regio. |

## <a name="geography-reports-bar-charts"></a>Aardrijkskunderapporten (staafdiagrammen)
Er zijn twee aanvullende rapporten die statistische informatie bieden op basis van geografie, die topsteden en toplanden zijn. Deze rapporten rangschikken steden en landen/regio's, respectievelijk, op basis van het aantal hits dat afkomstig is uit die landen / regio's. Bij het genereren van dit type rapport geeft een staafdiagram de top 10 steden of landen/regio's aan die inhoud hebben aangevraagd via een specifiek platform. Met dit staafdiagram u snel de regio's beoordelen die het hoogste aantal aanvragen voor uw inhoud genereren.

De linkerkant van de grafiek (y-as) geeft aan hoeveel treffers er in het opgegeven gebied zijn opgetreden. Direct onder de grafiek (x-as) vindt u een label voor elk van de top 10 regio's.

### <a name="using-the-bar-charts"></a>De staafdiagrammen gebruiken
* Als u de boventoon over een balk houdt, worden de naam en het totale aantal treffers dat in de regio is opgetreden, weergegeven als een knopinfo.
* De tooltip voor het rapport Topsteden identificeert een stad met de naam, staat/provincie en de afkorting land/regio.
* Als de stad of regio (d.w.z. staat/provincie) waaruit een verzoek afkomstig is niet kon worden vastgesteld, dan zal dit aangeven dat ze onbekend zijn. Als het land/de regio onbekend is, worden er twee vraagtekens weergegeven (d.w.z., ??).
* Een rapport kan statistieken bevatten voor 'Europa' of de 'Regio Azië/Stille Oceaan'. Deze posten zijn niet bedoeld om statistische informatie te verstrekken over alle IP-adressen in die regio's. In plaats daarvan zijn ze alleen van toepassing op verzoeken die afkomstig zijn van IP-adressen die verspreid zijn over Europa of Azië/Pacific in plaats van naar een specifieke stad of land/regio.

De gegevens die zijn gebruikt om het staafdiagram te genereren, kunnen eronder worden weergegeven. Daar vindt u het totale aantal hits, het percentage hits, de hoeveelheid overgedragen gegevens (in gigabytes) en het percentage gegevens dat wordt overgedragen voor de top 250 regio's. Bekijk een beschrijving voor elk van deze statistieken.

Voor beide soorten rapporten hieronder wordt een korte beschrijving gegeven.

| Rapportnaam | Beschrijving |
| --- | --- |
| Topsteden |Dit rapport rangschikt steden op basis van het aantal hits dat afkomstig is uit die regio. |
| Toplanden |Dit rapport rangschikt landen/regio's op basis van het aantal treffers dat afkomstig is uit dat land/de regio. |

## <a name="daily-summary"></a>Dagelijkse samenvatting
Met het dagelijkse overzichtsrapport u dagelijks het totale aantal treffers en gegevens bekijken dat over een bepaald platform wordt overgedragen. Deze informatie kan worden gebruikt om snel cdn-activiteitspatronen te onderscheiden. Dit rapport kan u bijvoorbeeld helpen bij het detecteren welke dagen hoger of lager zijn dan verwacht verkeer.

Bij het genereren van dit type rapport geeft een staafdiagram een visuele indicatie van de hoeveelheid platformspecifieke vraag die in de periode waarop het rapport betrekking heeft, dagelijks wordt ervaren. Zij doet dit door een balk voor elke dag in het rapport weer te geven. Als u bijvoorbeeld de periode 'Vorige week' selecteert, wordt een staafdiagram met zeven balken gegenereerd. Elke balk geeft het totale aantal hits aan dat op die dag is ervaren.

De linkerkant van de grafiek (y-as) geeft aan hoeveel treffers er op de opgegeven datum zijn opgetreden. Direct onder de grafiek (x-as) vindt u een label dat de datum (Notatie: YYYY-MM-DD) aangeeft voor elke dag die in het rapport is opgenomen.

> [!TIP]
> Als u de boventoon over een balk houdt, wordt het totale aantal treffers dat op die datum is opgetreden, weergegeven als een knopinfo.
> 
> 

De gegevens die zijn gebruikt om het staafdiagram te genereren, kunnen eronder worden weergegeven. Daar vindt u het totale aantal treffers en de hoeveelheid overgedragen gegevens (in gigabytes) voor elke dag die door het rapport wordt gedekt.

## <a name="by-hour"></a>Per uur
Met het by-hour-rapport u het totale aantal treffers en gegevens dat over een bepaald platform wordt overgedragen, op uurbasis bekijken. Deze informatie kan worden gebruikt om snel cdn-activiteitspatronen te onderscheiden. Dit rapport kan u bijvoorbeeld helpen bij het detecteren van de perioden gedurende de dag die hoger of lager zijn dan verwacht verkeer.

Bij het genereren van dit type rapport geeft een staafdiagram een visuele indicatie van de hoeveelheid platformspecifieke vraag die op uurbasis wordt ervaren gedurende de periode waarop het rapport betrekking heeft. Zij zal dit doen door een balk weer te geven voor elk uur dat onder het rapport valt. Als u bijvoorbeeld een periode van 24 uur selecteert, genereert u een staafdiagram met vierentwintig balken. Elke balk geeft het totale aantal hits aan dat tijdens dat uur is ervaren.

De linkerkant van de grafiek (y-as) geeft aan hoeveel treffers er op het opgegeven uur zijn opgetreden. Direct onder de grafiek (x-as) vindt u een label dat de datum/tijd (Formaat: YYYY-MM-DD hh:mm) aangeeft voor elk uur dat in het rapport is opgenomen. Tijd wordt gerapporteerd met behulp van 24-uursindeling en deze wordt opgegeven met behulp van de UTC/GMT-tijdzone.

> [!TIP]
> Als u de boventoon over een balk zweeft, wordt het totale aantal treffers dat tijdens dat uur is opgetreden, weergegeven als een knopinfo.
> 
> 

De gegevens die zijn gebruikt om het staafdiagram te genereren, kunnen eronder worden weergegeven. Daar vindt u het totale aantal treffers en de hoeveelheid overgedragen gegevens (in gigabytes) voor elk uur dat door het rapport wordt gedekt.

## <a name="by-file"></a>Per bestand
Met het rapport Per bestand u de hoeveelheid vraag en het verkeer bekijken dat via een bepaald platform is gemaakt voor de meest gevraagde assets. Bij het genereren van dit type rapport wordt een staafdiagram gegenereerd in de top 10 van meest gevraagde assets gedurende de opgegeven periode.

> [!NOTE]
> Voor de toepassing van dit rapport worden edge CNAME-URL's geconverteerd naar hun equivalente CDN-URL's. Hierdoor kan een nauwkeurige telling voor het totale aantal hits in verband met een actief, ongeacht de CDN of edge CNAME URL gebruikt om het te vragen.
> 
> 

De linkerkant van de grafiek (y-as) geeft het aantal aanvragen voor elk actief over de opgegeven periode aan. Direct onder de grafiek (x-as) vindt u een label dat de bestandsnaam voor elk van de top 10 gevraagde elementen aangeeft.

De gegevens die zijn gebruikt om het staafdiagram te genereren, kunnen eronder worden weergegeven. Daar vindt u de volgende informatie voor elk van de top 250 gevraagde activa: relatief pad, het totale aantal hits, het percentage hits, de hoeveelheid overgedragen gegevens (in gigabytes) en het percentage overgedragen gegevens.

## <a name="by-file-detail"></a>Op bestandsdetail
Met het rapport Per bestand detail u de hoeveelheid vraag en het verkeer dat via een bepaald platform voor een bepaald element voor een specifiek actief wordt gemaakt, bekijken. Helemaal boven in dit rapport bevindt zich de optie Bestandsdetails voor. Deze optie bevat een lijst van uw meest gevraagde assets op het geselecteerde platform. Als u een rapport Per bestanddetail wilt genereren, moet u het gewenste element selecteren in de optie Bestandsdetails voor. Waarna een staafdiagram de hoeveelheid dagelijkse vraag aangeeft die het gedurende de opgegeven periode heeft gegenereerd.

De linkerkant van de grafiek (y-as) geeft het totale aantal aanvragen aan dat een actief op een bepaalde dag heeft ervaren. Direct onder de grafiek (x-as) vindt u een label dat de datum aangeeft (Formaat: YYYY-MM-DD) waarvoor CDN-vraag naar het actief werd gerapporteerd.

De gegevens die zijn gebruikt om het staafdiagram te genereren, kunnen eronder worden weergegeven. Daar vindt u het totale aantal treffers en de hoeveelheid overgedragen gegevens (in gigabytes) voor elke dag die door het rapport wordt gedekt.

## <a name="by-file-type"></a>Op bestandstype
Met het rapport Per bestandstype u de hoeveelheid vraag en het verkeer per bestandstype weergeven. Bij het genereren van dit type rapport geeft een donutdiagram het percentage treffers aan dat wordt gegenereerd door de top 10-bestandstypen.

> [!TIP]
> Als u de boventoon over een segment in de donutgrafiek zweeft, wordt het internetmediatype van dat bestandstype weergegeven als een knopinfo.
> 
> 

De gegevens die zijn gebruikt om de donutgrafiek te genereren, kunnen eronder worden bekeken. Daar vindt u de bestandsnaam extensie / Internet media type, het totale aantal hits, het percentage hits, de hoeveelheid gegevens overgedragen (in gigabytes), en het percentage van de gegevens overgedragen voor elk van de top 250 bestandstypen.

## <a name="by-directory"></a>Per directory
Met het by directory-rapport u de hoeveelheid vraag en het verkeer bekijken dat via een bepaald platform is gemaakt voor inhoud uit een specifieke map. Bij het genereren van dit type rapport geeft een staafdiagram het totale aantal hits aan dat wordt gegenereerd door inhoud in de top 10-mappen.

### <a name="using-the-bar-chart"></a>Het staafdiagram gebruiken
* Plaats de plaats boven een balk om het relatieve pad naar de bijbehorende map weer te geven.
* Inhoud die is opgeslagen in een submap van een map telt niet mee bij het berekenen van de vraag per map. Deze berekening is uitsluitend gebaseerd op het aantal aanvragen dat wordt gegenereerd voor inhoud die is opgeslagen in de werkelijke map.
* Voor de toepassing van dit rapport worden edge CNAME-URL's geconverteerd naar hun equivalente CDN-URL's. Dit maakt een nauwkeurige telling mogelijk voor alle statistieken die zijn gekoppeld aan een asset, ongeacht de CDN- of edge-CNAME-URL die wordt gebruikt om deze te aanvragen.

De linkerkant van de grafiek (y-as) geeft het totale aantal aanvragen voor de inhoud die is opgeslagen in uw top 10 mappen aan. Elke balk op de grafiek vertegenwoordigt een map. Gebruik het kleurcoderingsschema om een balk te koppelen aan een map die wordt vermeld in de sectie Volledige mappen van de top 250.

De gegevens die zijn gebruikt om het staafdiagram te genereren, kunnen eronder worden weergegeven. Daar vindt u de volgende informatie voor elk van de top 250 mappen: relatief pad, het totale aantal hits, het percentage hits, de hoeveelheid overgedragen gegevens (in gigabytes) en het percentage overgedragen gegevens.

## <a name="by-browser"></a>Per browser
Met het rapport By Browser u zien welke browsers zijn gebruikt om inhoud op te vragen. Bij het genereren van dit type rapport geeft een cirkeldiagram het percentage aanvragen aan dat door de top 10-browsers wordt verwerkt.

### <a name="using-the-pie-chart"></a>Het cirkeldiagram gebruiken
* Plaats de muisaanwijzer op een segment in het cirkeldiagram om de naam en versie van een browser weer te geven.
* Voor de toepassing van dit rapport wordt elke unieke browser/versiecombinatie beschouwd als een andere browser.
* Het segment met de naam 'Overig' geeft het percentage aanvragen aan dat door alle andere browsers en versies wordt verwerkt.

De gegevens die zijn gebruikt om het cirkeldiagram te genereren, kunnen eronder worden weergegeven. Daar vindt u het browsertype/versienummer, het totale aantal hits en het percentage hits voor elk van de top 250 browsers.

## <a name="by-referrer"></a>Door verwijzer
Met het rapport Doorverwijzer u de beste verwijzers naar inhoud op het geselecteerde platform bekijken. Een verwijzer geeft de hostnaam aan waaruit een aanvraag is gegenereerd. Bij het genereren van dit type rapport geeft een staafdiagram de hoeveelheid vraag (d.w.z. hits) aan die door de top 10-verwijzers worden gegenereerd.

De linkerkant van de grafiek (y-as) geeft het totale aantal aanvragen aan dat een actief voor elke verwijzer heeft ervaren. Elke balk op de grafiek vertegenwoordigt een verwijzer. Gebruik het kleurcoderingsschema om een balk af te koppelen aan een verwijzer in de sectie Top 250 Referrer.

De gegevens die zijn gebruikt om het staafdiagram te genereren, kunnen eronder worden weergegeven. Daar vindt u de URL, het totale aantal hits en het percentage hits dat wordt gegenereerd uit elk van de top 250 referrers.

## <a name="by-download"></a>Door te downloaden
Met het rapport By Download u downloadpatronen analyseren voor uw meest gevraagde inhoud. De bovenkant van het rapport bevat een staafdiagram dat geprobeerde downloads vergelijkt met voltooide downloads voor de top 10 gevraagde assets. Elke balk is kleurgecodeerd op basis van of het een poging tot download (blauw) of een voltooide download (groen) is.

> [!NOTE]
> Voor de toepassing van dit rapport worden edge CNAME-URL's geconverteerd naar hun equivalente CDN-URL's. Dit maakt een nauwkeurige telling mogelijk voor alle statistieken die zijn gekoppeld aan een asset, ongeacht de CDN- of edge-CNAME-URL die wordt gebruikt om deze te aanvragen.
> 
> 

De linkerkant van de grafiek (y-as) geeft de bestandsnaam aan voor elk van de top 10 gevraagde elementen. Direct onder de grafiek (x-as) vindt u labels die het totale aantal geprobeerde/voltooide downloads aangeven.

Direct onder het staafdiagram wordt de volgende informatie weergegeven voor de top 250 gevraagde activa: relatief pad (inclusief bestandsnaam), het aantal keren dat deze is gedownload tot voltooiing, het aantal keren dat deze is aangevraagd en het percentage van de verzoeken die resulteerden in een volledige download.

> [!TIP]
> Ons CDN wordt niet geïnformeerd door een HTTP-client (d.w.z. browser) wanneer een asset volledig is gedownload. Als gevolg hiervan moeten we berekenen of een actief volledig is gedownload op basis van statuscodes en byte-range aanvragen. Het eerste waar we naar zoeken bij het maken van deze berekening is of de aanvraag resulteert in een 200 OK statuscode. Als dat zo is, dan kijken we naar byte-range verzoeken om ervoor te zorgen dat ze het hele actief te dekken. Ten slotte vergelijken we de hoeveelheid gegevens die worden overgedragen met de grootte van het gevraagde actief. Als de overgedragen gegevens gelijk zijn aan of groter zijn dan de bestandsgrootte en de aanvragen voor bytebereik geschikt zijn voor dat element, wordt de treffer geteld als een volledige download.
> 
> Vanwege de interpretatieve aard van dit rapport moet u rekening houden met de volgende punten die de consistentie en nauwkeurigheid van dit rapport kunnen veranderen.
> 
> * Verkeerspatronen kunnen niet nauwkeurig worden voorspeld wanneer gebruikersagenten zich anders gedragen. Dit kan leiden tot voltooide downloadresultaten die groter zijn dan 100%.
> * Assets die profiteren van HTTP Progressive Download worden mogelijk niet nauwkeurig weergegeven door dit rapport. Dit is te wijten aan gebruikers die op zoek zijn naar verschillende posities in een video.
> 
> 

## <a name="by-404-errors"></a>Door 404 fouten
Met het rapport Door 404 Fouten u het type inhoud identificeren dat het meeste aantal statuscodes van 404 niet gevonden genereert. De bovenkant van het rapport bevat een staafdiagram voor de top 10-elementen waarvoor een statuscode voor 404 niet gevonden is geretourneerd. In dit staafdiagram wordt het totale aantal aanvragen vergeleken met aanvragen die hebben geleid tot een statuscode 404 niet gevonden voor deze activa. Elke balk heeft een kleurcode. Een gele balk wordt gebruikt om aan te geven dat de aanvraag heeft geresulteerd in een statuscode 404 niet gevonden. Een rode balk wordt gebruikt om het totale aantal aanvragen voor het actief aan te geven.

> [!NOTE]
> Let voor de toepassing van dit verslag op:
> 
> * Een treffer vertegenwoordigt elke aanvraag voor een item, ongeacht de statuscode.
> * Edge CNAME URL's worden geconverteerd naar hun equivalente CDN-URL's. Dit maakt een nauwkeurige telling mogelijk voor alle statistieken die zijn gekoppeld aan een asset, ongeacht de CDN- of edge-CNAME-URL die wordt gebruikt om deze te aanvragen.
> 
> 

De linkerkant van de grafiek (y-as) geeft de bestandsnaam aan voor elk van de top 10 gevraagde elementen die resulteerden in een statuscode 404 niet gevonden. Direct onder de grafiek (x-as) vindt u labels die het totale aantal aanvragen en het aantal aanvragen aangeven dat heeft geleid tot een statuscode 404 niet gevonden.

Direct onder de staafdiagram worden de volgende gegevens weergegeven voor de top 250 gevraagde activa: relatief pad (inclusief bestandsnaam), het aantal aanvragen dat resulteerde in een statuscode 404 niet gevonden, het totale aantal keren dat het actief is aangevraagd en het percentage aanvragen dat resulteerde in een statuscode 404 Niet gevonden.

## <a name="see-also"></a>Zie ook
* [Overzicht van Azure CDN](cdn-overview.md)
* [Realtime statistieken in Microsoft Azure CDN](cdn-real-time-stats.md)
* [StandaardHTTP-gedrag overschrijven met de rules-engine](cdn-rules-engine.md)
* [Edge-prestaties analyseren](cdn-edge-performance.md)

