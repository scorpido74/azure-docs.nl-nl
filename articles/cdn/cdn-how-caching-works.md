---
title: Hoe caching werkt | Microsoft Documenten
description: Caching is het proces van het lokaal opslaan van gegevens, zodat toekomstige aanvragen voor die gegevens sneller kunnen worden geopend.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/30/2018
ms.author: allensu
ms.openlocfilehash: d0c438aee7f56e96feb7167fad718fd9519a9f76
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81253710"
---
# <a name="how-caching-works"></a>Hoe caching werkt

In dit artikel vindt u een overzicht van algemene cachingconcepten en hoe [Het Azure Content Delivery Network (CDN)](cdn-overview.md) caching gebruikt om de prestaties te verbeteren. Zie [Azure CDN-cachinggedrag beheren met cachingregels](cdn-caching-rules.md) en [Azure CDN-cachinggedrag beheren met caching-regels en Azure CDN-cachinggedrag met querytekenreeksen beheren.](cdn-query-string.md)

## <a name="introduction-to-caching"></a>Inleiding tot caching

Caching is het proces van het lokaal opslaan van gegevens, zodat toekomstige aanvragen voor die gegevens sneller kunnen worden geopend. In het meest voorkomende type caching, webbrowser caches, slaat een webbrowser kopieën van statische gegevens lokaal op een lokale harde schijf op. Door caching te gebruiken, kan de webbrowser voorkomen dat u meerdere retouren naar de server maakt en in plaats daarvan lokaal toegang krijgt tot dezelfde gegevens, waardoor tijd en middelen worden bespaard. Caching is zeer geschikt voor het lokaal beheren van kleine, statische gegevens zoals statische afbeeldingen, CSS-bestanden en JavaScript-bestanden.

Op dezelfde manier wordt caching gebruikt door een netwerk voor het leveren van inhoud op edge-servers dicht bij de gebruiker om te voorkomen dat aanvragen teruggaan naar de oorsprong en de latentie van eindgebruikers verminderen. In tegenstelling tot een webbrowsercache, die alleen voor één gebruiker wordt gebruikt, heeft het CDN een gedeelde cache. In een cdn-gedeelde cache kan een bestand dat door één gebruiker wordt aangevraagd, later door andere gebruikers worden geopend, waardoor het aantal aanvragen naar de oorsprongsserver aanzienlijk wordt afgenomen.

Dynamische resources die regelmatig veranderen of uniek zijn voor een individuele gebruiker, kunnen niet in de cache worden opgeslagen. Dit soort resources kunnen echter profiteren van dynamic site acceleration (DSA) optimalisatie op het Azure Content Delivery Network voor prestatieverbeteringen.

Caching kan op meerdere niveaus plaatsvinden tussen de oorsprongsserver en de eindgebruiker:

- Webserver: gebruikt een gedeelde cache (voor meerdere gebruikers).
- Content delivery network: Maakt gebruik van een gedeelde cache (voor meerdere gebruikers).
- Internet service provider (ISP): Maakt gebruik van een gedeelde cache (voor meerdere gebruikers).
- Webbrowser: maakt gebruik van een privécache (voor één gebruiker).

Elke cache beheert doorgaans zijn eigen bronversheid en voert validatie uit wanneer een bestand verouderd is. Dit gedrag wordt gedefinieerd in de HTTP-cachespecificatie, [RFC 7234](https://tools.ietf.org/html/rfc7234).

### <a name="resource-freshness"></a>Bronversheid

Omdat een bron in de cache mogelijk verouderd of verouderd kan zijn (in vergelijking met de bijbehorende bron op de oorsprongsserver), is het belangrijk dat elk cachemechanisme bepaalt wanneer de inhoud wordt vernieuwd. Om tijd- en bandbreedteverbruik te besparen, wordt een bron in de cache niet vergeleken met de versie op de oorsprongsserver elke keer dat deze wordt geopend. In plaats daarvan, zolang een bron in de cache wordt beschouwd als vers, wordt aangenomen dat deze de meest recente versie is en rechtstreeks naar de client wordt verzonden. Een bron in de cache wordt als vers beschouwd wanneer de leeftijd kleiner is dan de leeftijd of periode die wordt gedefinieerd door een cache-instelling. Wanneer een browser bijvoorbeeld een webpagina opnieuw laadt, wordt gecontroleerd of elke bron in de cache op uw harde schijf vers is en wordt deze geladen. Als de bron niet vers (verouderd) is, wordt een up-to-date kopie van de server geladen.

### <a name="validation"></a>Validatie

Als een bron als verouderd wordt beschouwd, wordt de oorsprongsserver gevraagd deze te valideren, dat wil zeggen of de gegevens in de cache nog steeds overeenkomen met wat er op de oorsprongsserver staat. Als het bestand is gewijzigd op de oorsprongsserver, wordt de versie van de bron bijgewerkt in de cache. Als de bron anders vers is, worden de gegevens rechtstreeks vanuit de cache geleverd zonder deze eerst te valideren.

### <a name="cdn-caching"></a>CDN-caching

Caching is een integraal onderdeel van de manier waarop een CDN werkt om de levering te versnellen en de herkomstbelasting voor statische elementen zoals afbeeldingen, lettertypen en video's te verminderen. In CDN-caching worden statische resources selectief opgeslagen op strategisch geplaatste servers die meer lokaal zijn voor een gebruiker en de volgende voordelen bieden:

- Omdat het meeste webverkeer statisch is (bijvoorbeeld afbeeldingen, lettertypen en video's), vermindert CDN-caching de netwerklatentie door inhoud dichter bij de gebruiker te plaatsen, waardoor de afstand die gegevens worden afgelegd, wordt verminderd.

- Door het werk te ontladen naar een CDN, kan caching het netwerkverkeer en de belasting op de oorsprongsserver verminderen. Hierdoor worden de kosten en resourcevereisten voor de toepassing verminderd, zelfs wanneer er grote aantallen gebruikers zijn.

Net als bij de manier waarop caching wordt geïmplementeerd in een webbrowser, u bepalen hoe caching wordt uitgevoerd in een CDN door headers van de cacherichtlijn te verzenden. Cache-directive headers zijn HTTP-headers, die doorgaans worden toegevoegd door de origin-server. Hoewel de meeste van deze headers oorspronkelijk zijn ontworpen om caching in clientbrowsers aan te pakken, worden ze nu ook gebruikt door alle tussenliggende caches, zoals CDN's. 

Twee kopteksten kunnen worden gebruikt `Cache-Control` om `Expires`cacheversheid te definiëren: en . `Cache-Control`is meer actueel en `Expires`heeft voorrang op , als beide bestaan. Er zijn ook twee soorten kopteksten die `ETag` worden `Last-Modified`gebruikt voor validatie (validators genoemd): en . `ETag`is actueler en heeft `Last-Modified`voorrang op, als beide zijn gedefinieerd.  

## <a name="cache-directive-headers"></a>Headers van de cacherichtlijn

> [!IMPORTANT]
> Standaard negeert een Azure CDN-eindpunt dat is geoptimaliseerd voor DSA cache-directive-headers en omleidingen. Voor **Azure CDN Standard van Verizon** en Azure **CDN Standard van Akamai-profielen** u aanpassen hoe een Azure CDN-eindpunt deze kopteksten behandelt met [CDN-cachingregels](cdn-caching-rules.md) om caching mogelijk te maken. Alleen voor **Azure CDN Premium van Verizon-profielen** gebruikt u de [regelsengine](cdn-rules-engine.md) om caching in te schakelen.

Azure CDN ondersteunt de volgende HTTP-cache-richtlijnheaders, die cacheduur en cachedelen definiëren.

**Cachebeheer:**
- Geïntroduceerd in HTTP 1.1 om webuitgevers meer controle te geven over `Expires` hun inhoud en om de beperkingen van de header aan te pakken.
- Hiermee overschrijft u de `Expires` `Cache-Control` koptekst, als deze beide is gedefinieerd.
- Wanneer deze wordt gebruikt in een HTTP-aanvraag `Cache-Control` van de client naar de CDN POP, wordt deze standaard genegeerd door alle Azure CDN-profielen.
- Wanneer u wordt gebruikt in een HTTP-antwoord van de client op de CDN POP:
     - **Azure CDN Standard/Premium van Verizon** en **Azure CDN Standard van Microsoft** ondersteunen alle `Cache-Control` richtlijnen.
     - **Azure CDN Standard van Akamai** ondersteunt alleen de volgende `Cache-Control` richtlijnen; alle anderen worden genegeerd:
         - `max-age`: Een cache kan de inhoud opslaan voor het opgegeven aantal seconden. Bijvoorbeeld `Cache-Control: max-age=5`. Deze richtlijn bepaalt de maximale tijd dat de inhoud als vers wordt beschouwd.
         - `no-cache`: Cache de inhoud, maar valideer de inhoud elke keer voordat u deze uit de cache levert. Gelijk `Cache-Control: max-age=0`aan .
         - `no-store`: Nooit de inhoud in de cache opgeslagen. Verwijder inhoud als deze eerder is opgeslagen.

**Verloopt:**
- Legacy-header geïntroduceerd in HTTP 1.0; ondersteund voor backwards compatibility.
- Gebruikt een op datum gebaseerde vervaldatum met tweede precisie. 
- Vergelijkbaar `Cache-Control: max-age`met .
- Gebruikt `Cache-Control` wanneer niet bestaat.

**Pragma:**
   - Niet standaard gehonoreerd door Azure CDN.
   - Legacy-header geïntroduceerd in HTTP 1.0; ondersteund voor backwards compatibility.
   - Wordt gebruikt als clientrequest-header `no-cache`met de volgende richtlijn: . Deze richtlijn geeft de server de opdracht om een nieuwe versie van de bron te leveren.
   - `Pragma: no-cache`is gelijk `Cache-Control: no-cache`aan .

## <a name="validators"></a>Validators

Wanneer de cache verouderd is, worden HTTP-cachevalidators gebruikt om de in de cache opgeslagen versie van een bestand te vergelijken met de versie op de oorspronkelijke server. **Azure CDN Standard/Premium** van `ETag` `Last-Modified` Verizon ondersteunt beide standaard en validators, terwijl **Azure CDN Standard van Microsoft** en Azure **CDN Standard van Akamai** standaard `Last-Modified` worden ondersteund.

**ETag:**
- **Azure CDN Standard/Premium** `ETag` van Verizon ondersteunt standaard, terwijl **Azure CDN Standard van Microsoft** en Azure **CDN Standard van Akamai** dat niet doen.
- `ETag`definieert een tekenreeks die uniek is voor elk bestand en elke versie van een bestand. Bijvoorbeeld `ETag: "17f0ddd99ed5bbe4edffdd6496d7131f"`.
- Geïntroduceerd in HTTP 1.1 en `Last-Modified`is actueler dan . Handig wanneer de laatste gewijzigde datum moeilijk te bepalen is.
- Ondersteunt zowel sterke validatie als zwakke validatie; Azure CDN ondersteunt echter alleen sterke validatie. Voor een sterke validatie moeten de twee resourcerepresentaties byte-for-byte identiek zijn. 
- Een cache valideert een `ETag` bestand `If-None-Match` dat wordt gebruikt `ETag` door een koptekst met een of meer validators in de aanvraag te verzenden. Bijvoorbeeld `If-None-Match: "17f0ddd99ed5bbe4edffdd6496d7131f"`. Als de versie van `ETag` de server overeenkomt met een validator in de lijst, wordt statuscode 304 (niet gewijzigd) in de reactie uitgevoerd. Als de versie anders is, reageert de server met statuscode 200 (OK) en de bijgewerkte bron.

**Laatst gewijzigd:**
- Voor **Azure CDN Standard/Premium van Verizon** `Last-Modified` wordt alleen gebruikt als `ETag` het geen deel uitmaakt van het HTTP-antwoord. 
- Hiermee geeft u de datum en tijd op waarop de oorspronkelijke server heeft bepaald dat de bron voor het laatst is gewijzigd. Bijvoorbeeld `Last-Modified: Thu, 19 Oct 2017 09:28:00 GMT`.
- Een cache valideert `Last-Modified` een bestand `If-Modified-Since` met behulp van een header met een datum en tijd in de aanvraag. De oorsprongsserver vergelijkt `Last-Modified` die datum met de koptekst van de nieuwste bron. Als de bron niet is gewijzigd sinds de opgegeven tijd, retourneert de server statuscode 304 (niet gewijzigd) in zijn antwoord. Als de bron is gewijzigd, retourneert de server statuscode 200 (OK) en de bijgewerkte bron.

## <a name="determining-which-files-can-be-cached"></a>Bepalen welke bestanden in de cache kunnen worden opgeslagen

Niet alle bronnen kunnen in de cache worden opgeslagen. In de volgende tabel ziet u welke resources in de cache kunnen worden opgeslagen, op basis van het type HTTP-antwoord. Bronnen die worden geleverd met HTTP-antwoorden die niet aan al deze voorwaarden voldoen, kunnen niet in de cache worden opgeslagen. Alleen voor **Azure CDN Premium van Verizon** u de regelsengine gebruiken om een aantal van deze voorwaarden aan te passen.

|                   | Azure CDN van Microsoft          | Azure CDN van Verizon | Azure CDN van Akamai        |
|-------------------|-----------------------------------|------------------------|------------------------------|
| HTTP-statuscode | 200, 203, 206, 300, 301, 410, 416 | 200                    | 200, 203, 300, 301, 302, 401 |
| HTTP-methoden      | GET, HOOFD                         | GET                    | GET                          |
| Limieten voor bestandsgrootte  | 300 GB                            | 300 GB                 | - Algemene webdelivery optimalisatie: 1,8 GB<br />- Optimalisaties voor mediastreaming: 1,8 GB<br />- Grote bestandsoptimalisatie: 150 GB |

Als **Azure CDN Standard van Microsoft** aan een bron wil werken, moet de oorsprongsserver alle HEAD- en GET HTTP-aanvragen ondersteunen en moeten de waarden voor inhoudslengte hetzelfde zijn voor elke HEAD- en GET HTTP-antwoorden voor het item. Voor een HEAD-verzoek moet de oorsprongsserver het HEAD-verzoek ondersteunen en met dezelfde headers reageren als wanneer hij een GET-verzoek heeft ontvangen.

## <a name="default-caching-behavior"></a>Standaardcachegedrag

In de volgende tabel wordt het standaard caching-gedrag voor de Azure CDN-producten en de bijbehorende optimalisaties beschreven.

|    | Microsoft: Algemene weblevering | Verizon: Algemene weblevering | Verizon: DSA | Akamai: Algemene weblevering | Akamai: DSA | Akamai: Groot bestand downloaden | Akamai: algemene of VOD media streaming |
|------------------------|--------|-------|------|--------|------|-------|--------|
| **Eer oorsprong**       | Ja    | Ja   | Nee   | Ja    | Nee   | Ja   | Ja    |
| **DUUR van cdn-cache** | 2 dagen |7 dagen | Geen | 7 dagen | Geen | 1 dag | 1 jaar |

**Honor origin**: geeft aan of de ondersteunde cache-richtlijnheaders moeten worden gehonoreerd als deze bestaan in de HTTP-respons van de oorspronkelijke server.

**CDN-cacheduur**: hiermee geeft u de hoeveelheid tijd op waarvoor een resource in de cache is opgeslagen op het Azure CDN. Als **Honor origin** echter ja is en het HTTP-antwoord van `Expires` `Cache-Control: max-age`de oorsprongsserver de header cache-directive bevat of , gebruikt Azure CDN in plaats daarvan de duurwaarde die door de koptekst is opgegeven. 

## <a name="next-steps"></a>Volgende stappen

- Zie Azure [CDN-cachinggedrag beheren en](cdn-caching-rules.md)overschrijven met cachingregels voor meer informatie over het standaardcachinggedrag op het CDN. 
- Zie [Azure CDN-cachinggedrag met querytekenreeksen](cdn-query-string.md)beheren voor meer informatie over het gebruik van querytekenreeksen om het cachinggedrag te beheren.



