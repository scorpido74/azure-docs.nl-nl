---
title: Voor deur van Azure front-caching | Microsoft Docs
description: Dit artikel helpt u te begrijpen hoe de status van uw back-ends wordt gecontroleerd door Azure front-deur
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "79471741"
---
# <a name="caching-with-azure-front-door"></a>Caching met de voor deur van Azure
In het volgende document wordt het gedrag voor de voor deur opgegeven met routerings regels waarvoor caching is ingeschakeld. De voor deur is een modern Content Delivery Network (CDN), samen met dynamische site versnelling en taak verdeling, ondersteunt ook caching-gedrag, net als bij andere CDN.

## <a name="delivery-of-large-files"></a>Levering van grote bestanden
Azure front-deur levert grote bestanden zonder een limiet voor de bestands grootte. De voor deur maakt gebruik van een techniek die object Chunking wordt genoemd. Als er een groot bestand wordt aangevraagd, haalt Front Door kleine onderdelen van het bestand op uit de back-end. Wanneer er een volledig bestand (of een bepaald bytebereik) wordt aangevraagd, wordt in de Front Door-omgeving het bestand in delen van 8 MB aangevraagd bij de back-end.

</br>Nadat het segment in de front-deur omgeving arriveert, wordt het in de cache opgeslagen en direct aan de gebruiker geleverd. De voor deur haalt vervolgens het volgende segment parallel op. Met deze vooraf opgehaalde, zorgt u ervoor dat de inhoud één segment vóór de gebruiker blijft, waardoor de latentie wordt verminderd. Dit proces wordt voortgezet totdat het hele bestand is gedownload (indien aangevraagd), alle byte bereiken beschikbaar zijn (indien aangevraagd) of de client beëindigt de verbinding.

</br>Lees [RFC 7233](https://web.archive.org/web/20171009165003/http://www.rfc-base.org/rfc-7233.html)voor meer informatie over de aanvraag voor byte bereik.
Bij de voor deur worden eventuele segmenten in de cache opgeslagen wanneer ze worden ontvangen, zodat het hele bestand niet in de cache hoeft te worden opgeslagen in de front-deur cache. Volgende aanvragen voor het bestand of de byte bereik worden vanuit de cache verwerkt. Als niet alle segmenten in de cache zijn opgeslagen, wordt vooraf ophalen gebruikt voor het aanvragen van segmenten van de back-end. Deze optimalisatie is afhankelijk van de mogelijkheid van de back-end om aanvragen voor byte bereik te ondersteunen; Als de back-end geen aanvragen voor byte bereik ondersteunt, is deze optimalisatie niet effectief.

## <a name="file-compression"></a>Bestandscompressie
De voor deur kan inhoud op de rand dynamisch comprimeren, wat resulteert in een kleiner en sneller antwoord op uw clients. Alle bestanden komen in aanmerking voor compressie. Een bestand moet echter een MIME-type zijn dat in aanmerking komt voor compressie lijst. Op dit moment staat de voor deur niet toe dat deze lijst wordt gewijzigd. De huidige lijst is:</br>
- ' Application/EOT '
- toepassing/letter type
- "toepassing/letter type-sfnt"
- "toepassing/Java script"
- 'application/json'
- toepassing/open type
- ' Application/otf '
- ' application/pkcs7-MIME '
- "toepassing/True Type"
- ' Application/ttf ',
- ' application/vnd. MS-fontobject '
- "toepassing/XHTML en XML"
- ' Application/XML '
- ' Application/XML + RSS '
- ' Application/x: font-open type '
- ' Application/x: font-True Type '
- ' Application/x: font-ttf '
- "Application/x-httpd-cgi"
- ' Application/x-mpegurl '
- ' Application/x-open type '
- ' Application/x-otf '
- ' Application/x-perl '
- ' Application/x-ttf '
- ' Application/x-java script '
- "font/EOT"
- "font/ttf"
- "font/otf"
- "letter type/open type"
- "afbeelding/SVG + XML"
- "tekst/css"
- "tekst/CSV"
- "tekst/html"
- "tekst/java script"
- "tekst/js", "tekst/zonder opmaak"
- "tekst/RTF"
- "tekst/door tabs gescheiden waarden"
- "tekst/XML"
- "tekst/x-script"
- "tekst/x-onderdeel"
- "tekst/x-Java-bron"

Daarnaast moet het bestand ook tussen 1 KB en 8 MB groot zijn.

Deze profielen ondersteunen de volgende compressie coderingen:
- [Gzip (GNU-zip)](https://en.wikipedia.org/wiki/Gzip)
- [Brotli](https://en.wikipedia.org/wiki/Brotli)

Als een aanvraag gzip en Brotli-compressie ondersteunt, heeft Brotli-compressie prioriteit.</br>
Wanneer een aanvraag voor een Asset compressie specificeert en de aanvraag resulteert in een Missing in een cache, voert de front-deur compressie van de Asset rechtstreeks op de POP-server uit. Daarna wordt het gecomprimeerde bestand vanuit de cache verwerkt. Het resulterende item wordt geretourneerd met een overdracht-Encoding: gesegmenteerd.

## <a name="query-string-behavior"></a>Query teken reeks gedrag
Met de voor deur kunt u bepalen hoe bestanden in de cache worden opgeslagen voor een webaanvraag die een query reeks bevat. In een webaanvraag met een query reeks is de query reeks het gedeelte van de aanvraag dat wordt uitgevoerd na een vraag teken (?). Een query reeks kan een of meer sleutel-waardeparen bevatten, waarbij de veld naam en de waarde ervan gescheiden worden door een gelijkteken (=). Elk sleutel-waardepaar wordt gescheiden door een en-teken (&). Bijvoorbeeld `http://www.contoso.com/content.mov?field1=value1&field2=value2`. Als er meer dan één sleutel/waarde-paar in een query reeks van een aanvraag is, is de volg orde hiervan niet van belang.
- **Query reeksen negeren**: standaard modus. In deze modus geeft de voor deur de query teken reeksen van de aanvrager door aan de back-end bij de eerste aanvraag en slaat het de Asset op in de cache. Alle volgende aanvragen voor de activa die worden bediend vanuit de voor deur, negeren de query teken reeksen totdat het activum in de cache verloopt.

- **Elke unieke URL in de cache opslaan**: in deze modus wordt elke aanvraag met een unieke URL, inclusief de query reeks, behandeld als een unieke Asset met een eigen cache. Het antwoord van de back-end voor een aanvraag voor wordt bijvoorbeeld in `www.example.ashx?q=test1` de cache geplaatst in de front-deur omgeving en wordt geretourneerd voor volgende caches met dezelfde query reeks. Een aanvraag voor `www.example.ashx?q=test2` wordt in de cache opgeslagen als een afzonderlijk activum met een eigen time-to-Live-instelling.

## <a name="cache-purge"></a>Cache opschonen
Met de voor deur worden assets in de cache opgeslagen totdat de TTL (time-to-Live) van het activum verloopt. Nadat de TTL van het activum verloopt, haalt de front-deur omgeving een nieuwe bijgewerkte kopie van de Asset op om de client aanvraag te leveren en de cache op te slaan wanneer een client de Asset aanvraagt.
</br>De best practice om ervoor te zorgen dat uw gebruikers altijd de nieuwste kopie van uw assets verkrijgen, is om uw assets voor elke update te maken en ze als nieuwe Url's te publiceren. Met de voor deur worden onmiddellijk de nieuwe assets opgehaald voor de volgende client aanvragen. Soms wilt u in de cache opgeslagen inhoud uit alle Edge-knoop punten verwijderen en alle nieuwe bijgewerkte assets laten afdwingen. Dit kan worden veroorzaakt door updates van uw webtoepassing, of om snel assets bij te werken die onjuiste informatie bevatten.

</br>Selecteer welke assets u wilt verwijderen uit de Edge-knoop punten. Als u alle assets wilt wissen, klikt u op het selectie vakje alles opschonen. Als dat niet het geval is, typt u het pad van elk activum dat u wilt verwijderen in het tekstvak pad. De onderstaande indelingen worden ondersteund in het pad.
1. **Eén pad leegmaken**: afzonderlijke activa opschonen door het volledige pad van de asset (zonder het protocol en domein) op te geven, met de bestands extensie, bijvoorbeeld/pictures/strasbourg.png;
2. **Joker tekens opschonen**: sterretje ( \* ) kan worden gebruikt als Joker teken. Verwijder alle mappen, submappen en bestanden onder een eind punt met/ \* in het pad of verwijder alle submappen en bestanden in een specifieke map door de map op te geven gevolgd door/ \* , bijvoorbeeld/pictures/ \* .
3. **Basis domein opschonen**: de hoofdmap van het eind punt met '/' in het pad opschonen.

Cache verwijderingen op de voor deur zijn hoofdletter gevoelig. Daarnaast zijn ze query teken reeks neutraal, wat betekent dat als u een URL opschoont, alle wijzigingen in de query teken reeks worden verwijderd. 

## <a name="cache-expiration"></a>Verval datum van cache
De volgende volg orde van koppen wordt gebruikt om te bepalen hoe lang een item wordt opgeslagen in de cache:</br>
1. Cache-Control: s-maxAge =\<seconds>
2. Cache-Control: Max-Age =\<seconds>
3. Verstreken\<http-date>

Cache-Control-antwoord headers die aangeven dat het antwoord niet in de cache moet worden opgeslagen, zoals cache-Control: private, caching-Control: no-cache en Cache-Control: No-Store is gehonoreerd. Als er echter meerdere aanvragen in de vlucht zijn bij een POP voor dezelfde URL, kunnen ze de reactie delen. Als er geen cache-Control aanwezig is, is het standaard gedrag dat AFD de resource gedurende X-tijd in de cache plaatst waarbij X wille keurig tussen 1 en 3 dagen wordt gekozen.

## <a name="request-headers"></a>Aanvraagheaders

De volgende aanvraag headers worden niet doorgestuurd naar een back-end wanneer caching wordt gebruikt.
- Content-length
- Overdracht-encoding

## <a name="next-steps"></a>Volgende stappen

- Lees hoe u [een Front Door maakt](quickstart-create-front-door.md).
- Lees [hoe Front Door werkt](front-door-routing-architecture.md).
