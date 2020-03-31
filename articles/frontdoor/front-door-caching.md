---
title: Azure Front Door - caching | Microsoft Documenten
description: Dit artikel helpt u te begrijpen hoe Azure Front Door de status van uw back-ends bewaakt
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: d4fed878e2c0b1430e963f43743fd772493d3270
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471741"
---
# <a name="caching-with-azure-front-door"></a>Caching met Azure-voordeur
In het volgende document wordt het gedrag voor voordeur opgegeven met routeringsregels die caching hebben ingeschakeld. Front Door is een modern Content Delivery Network (CDN) en dus ondersteunt het samen met dynamische siteversnelling en taakbalancering ook cachinggedrag, net als elke andere CDN.

## <a name="delivery-of-large-files"></a>Levering van grote bestanden
Azure Front Door levert grote bestanden zonder limiet op bestandsgrootte. Front Door maakt gebruik van een techniek genaamd object chunking. Als er een groot bestand wordt aangevraagd, haalt Front Door kleine onderdelen van het bestand op uit de back-end. Wanneer er een volledig bestand (of een bepaald bytebereik) wordt aangevraagd, wordt in de Front Door-omgeving het bestand in delen van 8 MB aangevraagd bij de back-end.

</br>Nadat de brok bij de Front Door-omgeving is aangekomen, wordt het in de cache opgeslagen en onmiddellijk aan de gebruiker geserveerd. Front Door dan pre-haalt de volgende brok in parallel. Deze pre-fetch zorgt ervoor dat de inhoud één stuk voor blijft op de gebruiker, wat de latentie vermindert. Dit proces wordt voortgezet totdat het hele bestand is gedownload (indien gevraagd), alle bytebereiken beschikbaar zijn (indien gevraagd) of de client de verbinding beëindigt.

</br>Lees [RFC 7233](https://web.archive.org/web/20171009165003/http://www.rfc-base.org/rfc-7233.html)voor meer informatie over de byte-range aanvraag.
Voordeur caches alle brokken als ze worden ontvangen en dus het hele bestand hoeft niet te worden opgeslagen op de voordeur cache. Latere aanvragen voor het bestand of bytebereiken worden vanuit de cache weergegeven. Als niet alle segmenten in de cache zijn opgeslagen, wordt vooraf ophalen gebruikt om brokken van de backend aan te vragen. Deze optimalisatie is afhankelijk van het vermogen van de backend om byte-range aanvragen te ondersteunen; Als de backend geen byte-range aanvragen ondersteunt, is deze optimalisatie niet effectief.

## <a name="file-compression"></a>Bestandscompressie
Front Door kan de inhoud op de rand dynamisch comprimeren, wat resulteert in een kleinere en snellere respons op uw klanten. Alle bestanden komen in aanmerking voor compressie. Een bestand moet echter van een MIME-type zijn dat in aanmerking komt voor compressielijst. Op dit moment staat Front Door niet toe dat deze lijst wordt gewijzigd. De huidige lijst is:</br>
- "toepassing/eot"
- "toepassing/lettertype"
- "toepassing/font-sfnt"
- "applicatie/javascript"
- 'application/json'
- "toepassing/opentype"
- "toepassing/otf"
- "toepassing/pkcs7-mime"
- "toepassing/truetype"
- "toepassing/ttf",
- "application/vnd.ms-fontobject"
- "toepassing/xhtml+xml"
- "toepassing/xml"
- "application/xml+rss"
- "application/x-font-opentype"
- "application/x-font-truetype"
- "toepassing/x-font-ttf"
- "toepassing/x-httpd-cgi"
- "toepassing/x-mpegurl"
- "toepassing/x-opentype"
- "toepassing/x-otf"
- "toepassing/x-perl"
- "toepassing/x-ttf"
- "applicatie/x-javascript"
- "lettertype/eot"
- "lettertype/ttf"
- "lettertype/otf"
- "lettertype/opentype"
- "image/svg+xml"
- "tekst/css"
- "tekst/csv"
- "tekst/html"
- "tekst/javascript"
- "tekst/js", "tekst/effen"
- "tekst/richtext"
- "tekst/tab-gescheiden-waarden"
- "tekst/xml"
- "tekst/x-script"
- "tekst/x-component"
- "tekst/x-java-bron"

Bovendien moet het bestand ook tussen 1 KB en 8 MB groot zijn, inclusief.

Deze profielen ondersteunen de volgende compressiecoderingen:
- [Gzip (GNU zip)](https://en.wikipedia.org/wiki/Gzip)
- [Brotli Brotli](https://en.wikipedia.org/wiki/Brotli)

Als een aanvraag gzip- en Brotli-compressie ondersteunt, heeft Brotli-compressie voorrang.</br>
Wanneer een aanvraag voor een asset compressie opgeeft en de aanvraag resulteert in een cachemiss, voert Front Door compressie van het element rechtstreeks uit op de POP-server. Daarna wordt het gecomprimeerde bestand vanuit de cache weergegeven. Het resulterende item wordt geretourneerd met een transfer-encoding: chunked.

## <a name="query-string-behavior"></a>Querytekenreeksgedrag
Met Front Door u bepalen hoe bestanden in de cache worden opgeslagen voor een webaanvraag die een querytekenreeks bevat. In een webaanvraag met een querytekenreeks is de querytekenreeks dat gedeelte van de aanvraag dat optreedt na een vraagteken (?). Een querytekenreeks kan een of meer sleutelwaardeparen bevatten, waarbij de veldnaam en de waarde ervan worden gescheiden door een gelijk teken (=). Elk sleutelwaardepaar wordt gescheiden door een ampère (&). Bijvoorbeeld `http://www.contoso.com/content.mov?field1=value1&field2=value2`. Als er meer dan één sleutelwaardepaar in een queryreeks van een aanvraag zit, maakt de volgorde er niet toe.
- **Querytekenreeksen negeren:** standaardmodus. In deze modus geeft Front Door de querytekenreeksen van de aanvrager door aan de backend op de eerste aanvraag en caches het item. Alle volgende aanvragen voor het item die vanuit de frontdoor-omgeving worden weergegeven, negeren de querytekenreeksen totdat het in de cache opgeslagen element is verlopen.

- **Cache elke unieke URL:** In deze modus wordt elk verzoek met een unieke URL, inclusief de querytekenreeks, behandeld als een uniek item met een eigen cache. Het antwoord van de backend voor `www.example.ashx?q=test1` een aanvraag wordt bijvoorbeeld in de cache opgeslagen in de frontdoor-omgeving en geretourneerd voor volgende caches met dezelfde querytekenreeks. Een aanvraag `www.example.ashx?q=test2` voor wordt in de cache opgeslagen als een afzonderlijk item met een eigen time-to-live-instelling.

## <a name="cache-purge"></a>Cachewissen
Front Door cache assets until the asset's time-to-live (TTL) expires. Nadat de TTL van het actief is verlopen, zal de Front Door-omgeving, wanneer een klant het actief aanvraagt, een nieuwe bijgewerkte kopie van het item ophalen om de clientaanvraag te dienen en de cache op te slaan.
</br>De beste manier om ervoor te zorgen dat uw gebruikers altijd de nieuwste kopie van uw assets verkrijgen, is om uw assets voor elke update te versien en te publiceren als nieuwe URL's. Front Door zal onmiddellijk de nieuwe activa ophalen voor de volgende klantaanvragen. Soms wilt u inhoud in de cache verwijderen van alle randknooppunten en ze allemaal dwingen om nieuwe bijgewerkte elementen op te halen. Dit kan te wijten zijn aan updates van uw webtoepassing of om snel elementen bij te werken die onjuiste informatie bevatten.

</br>Selecteer welke elementen u wilt verwijderen uit de randknooppunten. Als u alle elementen wilt wissen, klikt u op het selectievakje Alle wissen. Typ anders het pad van elk element dat u wilt wissen in het tekstvak Pad. Onderstaande indelingen worden ondersteund in het pad.
1. **Eén padzuivering**: Verwijder afzonderlijke activa(en) door het volledige pad van het actief (zonder protocol en domein) op te geven, met de bestandsextensie, bijvoorbeeld /pictures/strasbourg.png;
2. **Wildcard purge**: Sterretje\*( ) kan worden gebruikt als een wildcard. Verwijder alle mappen, submappen en bestanden onder een\* eindpunt met / in het pad of verwijder alle submappen en\*bestanden onder een\*specifieke map door de map op te geven gevolgd door / , bijvoorbeeld /pictures/ .
3. **Worteldomeinzuivering:** Verwijder de wortel van het eindpunt met "/" in het pad.

Cache zuiveringen op de voordeur zijn case-ongevoelig. Bovendien zijn ze query string agnostisch, wat betekent dat het zuiveren van een URL zal wissen alle query-string variaties van het. 

## <a name="cache-expiration"></a>Cache vervaldatum
De volgende volgorde van kopteksten wordt gebruikt om te bepalen hoe lang een item in onze cache wordt opgeslagen:</br>
1. Cache-Control: s-maxage=\<seconden>
2. Cache-Control: max-age=\<seconden>
3. Verloopt: \<http-datum>

Cache-Control responskoppen die aangeven dat het antwoord niet in de cache wordt opgeslagen, zoals Cache-Control: privé, Cache-Control: no-cache en Cache-Control: no-store worden gehonoreerd. Als er echter meerdere aanvragen tijdens de vlucht zijn op een POP voor dezelfde URL, kunnen ze het antwoord delen. Als er geen Cache-Control aanwezig is, is het standaardgedrag dat AFD de bron in de cache cache voor X-hoeveelheid tijd waar X willekeurig wordt gekozen tussen 1 tot 3 dagen.

## <a name="request-headers"></a>Aanvraagheaders

De volgende aanvraagkoppen worden niet doorgestuurd naar een backend bij het gebruik van caching.
- Inhoudslengte
- Overdrachtcodering

## <a name="next-steps"></a>Volgende stappen

- Lees hoe u [een Front Door maakt](quickstart-create-front-door.md).
- Lees [hoe Front Door werkt](front-door-routing-architecture.md).
