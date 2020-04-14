---
title: Http-headers X-EC-Debugn voor Azure CDN-regels | Microsoft Documenten
description: De header Foutopsporing van X-EC-Debug bevat aanvullende informatie over het cachebeleid dat wordt toegepast op het gevraagde element. Deze headers zijn specifiek voor Verizon.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2018
ms.author: allensu
ms.openlocfilehash: 52aae3bdd2fe82eea6cbd500723192c88c293a1e
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81260493"
---
# <a name="x-ec-debug-http-headers-for-azure-cdn-rules-engine"></a>Http-headers x-EC-Debugn voor Azure CDN-regels
De foutopsporingscache-aanvraagkop `X-EC-Debug`bevat aanvullende informatie over het cachebeleid dat wordt toegepast op het aangevraagde element. Deze headers zijn specifiek voor **Azure CDN Premium van** Verizon-producten.

## <a name="usage"></a>Gebruik
Het antwoord dat van de POP-servers naar een gebruiker wordt verzonden, bevat de `X-EC-Debug` koptekst alleen wanneer aan de volgende voorwaarden is voldaan:

- De [functie Antwoordkoppen foutopsporing zoeken is](cdn-verizon-premium-rules-engine-reference-features.md#debug-cache-response-headers) ingeschakeld op de regelsengine voor het opgegeven verzoek.
- De opgegeven aanvraag definieert de set foutopsporingscacheresponskoppen die in het antwoord worden opgenomen.

## <a name="requesting-debug-cache-information"></a>Informatie over foutopsporingscache aanvragen
Gebruik de volgende richtlijnen in het opgegeven verzoek om de foutopsporingscachegegevens te definiëren die in het antwoord worden opgenomen:

Aanvraagheader | Beschrijving |
---------------|-------------|
X-EC-Debug: x-ec-cache | [Statuscode cache](#cache-status-code-information)
X-EC-Debug: x-ec-cache-remote | [Statuscode cache](#cache-status-code-information)
X-EC-Debug: x-ec-check-cacheable | [Cacheable](#cacheable-response-header)
X-EC-Debug: x-ec-cache-toets | [Cachesleutel](#cache-key-response-header)
X-EC-Debug: x-ec-cache-status | [Cachestatus](#cache-state-response-header)

### <a name="syntax"></a>Syntaxis

Antwoordkoppen voor foutopsporingscache kunnen worden aangevraagd door de volgende koptekst en de opgegeven richtlijnen in het verzoek op te nemen:

`X-EC-Debug: Directive1,Directive2,DirectiveN`

### <a name="sample-x-ec-debug-header"></a>Voorbeeld van de koptekst X-EC-Debug

`X-EC-Debug: x-ec-cache,x-ec-check-cacheable,x-ec-cache-key,x-ec-cache-state`

## <a name="cache-status-code-information"></a>Informatie over cachestatusstatus
De x-EC-Debug-antwoordkop kan een server identificeren en hoe deze de respons heeft verwerkt via de volgende richtlijnen:

Header | Beschrijving
-------|------------
X-EC-Debug: x-ec-cache | Deze koptekst wordt gerapporteerd wanneer inhoud wordt doorgestuurd via het CDN. Het identificeert de POP-server die aan het verzoek voldoet.
X-EC-Debug: x-ec-cache-remote | Deze header wordt alleen gerapporteerd wanneer de gevraagde inhoud in de cache is opgeslagen op een origin shield-server of een ADN-gatewayserver.

### <a name="response-header-format"></a>Indeling voor antwoordkoppen

De koptekst X-EC-Debug rapporteert de statuscodegegevens van de cache in de volgende indeling:

- `X-EC-Debug: x-ec-cache: <StatusCode from Platform (POP/ID)>`

- `X-EC-Debug: x-ec-cache-remote: <StatusCode from Platform (POP/ID)>`

De termen die in de bovenstaande syntaxis van de antwoordkop worden gebruikt, worden als volgt gedefinieerd:
- StatusCode: geeft aan hoe de gevraagde inhoud is verwerkt door het CDN, dat wordt weergegeven via een statuscode voor de cache.
    
    De TCP_DENIED statuscode kan worden gerapporteerd in plaats van GEEN wanneer een ongeautoriseerd verzoek wordt geweigerd vanwege verificatie op basis van tokens. De STATUSCODE GEEN blijft echter worden gebruikt bij het weergeven van cachestatusrapporten of ruwe logboekgegevens.

- Platform: Geeft het platform aan waarop de inhoud is aangevraagd. Voor dit veld gelden de volgende codes:

    Code  | Platform
    ------| --------
    ECAcc | HTTP Groot
    Ecs   | HTTP Klein
    Ecd   | Application Delivery Network (ADN)

- POP: Geeft de [POP](cdn-pop-abbreviations.md) aan die de aanvraag heeft afgehandeld. 

### <a name="sample-response-headers"></a>Voorbeeldresponskoppen

De volgende voorbeeldkoppen bevatten cachestatuscodegegevens voor een aanvraag:

- `X-EC-Debug: x-ec-cache: TCP_HIT from ECD (lga/0FE8)`

- `X-EC-Debug: x-ec-cache-remote: TCP_HIT from ECD (dca/EF00)`

## <a name="cacheable-response-header"></a>In cache bare antwoordkop
De `X-EC-Debug: x-ec-check-cacheable` antwoordkop geeft aan of de gevraagde inhoud in de cache had kunnen worden opgeslagen.

Deze reactiekop geeft niet aan of er caching heeft plaatsgevonden. Het geeft eerder aan of het verzoek in aanmerking kwam voor caching.

### <a name="response-header-format"></a>Indeling voor antwoordkoppen

De `X-EC-Debug` antwoordkop die rapporteert of een aanvraag in de cache had kunnen worden opgeslagen, is in de volgende indeling:

`X-EC-Debug: x-ec-check-cacheable: <cacheable status>`

De term die in de bovenstaande syntaxis van de antwoordkop wordt gebruikt, wordt als volgt gedefinieerd:

Waarde  | Beschrijving
-------| --------
JA    | Geeft aan dat de gevraagde inhoud in aanmerking kwam voor caching.
NO     | Geeft aan dat de gevraagde inhoud niet in aanmerking kwam voor caching. Deze status kan te wijten zijn aan een van de volgende redenen: <br /> - Klantspecifieke configuratie: een configuratie die specifiek is voor uw account, kan voorkomen dat de popservers een asset incachen. Rules Engine kan bijvoorbeeld voorkomen dat een asset in de cache wordt opgeslagen door de functie Cache omzeilen in te schakelen voor in aanmerking komende aanvragen.<br /> - Cache response headers: De cachebeheer- en verloopt-headers van het gevraagde element kunnen voorkomen dat de POP-servers deze in cache plaatsen.
UNKNOWN | Geeft aan dat de servers niet konden beoordelen of het gevraagde element cachebaar was. Deze status treedt meestal op wanneer het verzoek wordt geweigerd vanwege verificatie op basis van tokens.

### <a name="sample-response-header"></a>Voorbeeldreactiekop

De volgende voorbeeldantwoordkop geeft aan of de gevraagde inhoud in de cache had kunnen worden opgeslagen:

`X-EC-Debug: x-ec-check-cacheable: YES`

## <a name="cache-key-response-header"></a>Cache-Key-antwoordkop
De `X-EC-Debug: x-ec-cache-key` antwoordkop geeft de fysieke cachesleutel aan die is gekoppeld aan de gevraagde inhoud. Een fysieke cachesleutel bestaat uit een pad dat een asset identificeert ten behoeve van caching. Met andere woorden, de servers controleren op een in de cache opgeslagen versie van een asset op basis van het pad zoals gedefinieerd door de cachesleutel.

Deze fysieke cachesleutel begint met een double forward slash (/) gevolgd door het protocol dat wordt gebruikt om de inhoud op te vragen (HTTP of HTTPS). Dit protocol wordt gevolgd door het relatieve pad naar het gevraagde element, dat begint met het toegangspunt voor inhoud (bijvoorbeeld _/000001/_).

*Http-platforms*zijn standaardcache geconfigureerd, wat betekent dat querytekenreeksen worden genegeerd door het cachemechanisme. Dit type configuratie voorkomt dat de cachesleutel querytekenreeksgegevens opslaat.

Als een querytekenreeks wordt opgenomen in de cachesleutel, wordt deze geconverteerd naar het hash-equivalent en vervolgens ingevoegd&lt;tussen&gt;de naam van het gevraagde element en de bestandsextensie (bijvoorbeeld de waarde van assethash .html).

### <a name="response-header-format"></a>Indeling voor antwoordkoppen

De `X-EC-Debug` antwoordkop rapporteert fysieke cachesleutelgegevens in de volgende indeling:

`X-EC-Debug: x-ec-cache-key: CacheKey`

### <a name="sample-response-header"></a>Voorbeeldreactiekop

De volgende voorbeeldantwoordkop geeft de fysieke cachesleutel voor de gevraagde inhoud aan:

`X-EC-Debug: x-ec-cache-key: //http/800001/origin/images/foo.jpg`

## <a name="cache-state-response-header"></a>Koptekst cachestatusstatus
De `X-EC-Debug: x-ec-cache-state` antwoordkop geeft de cachestatus van de gevraagde inhoud aan op het moment dat deze is aangevraagd.

### <a name="response-header-format"></a>Indeling voor antwoordkoppen

De `X-EC-Debug` antwoordkop rapporteert cachestatusgegevens in de volgende indeling:

`X-EC-Debug: x-ec-cache-state: max-age=MASeconds (MATimePeriod); cache-ts=UnixTime (ddd, dd MMM yyyy HH:mm:ss GMT); cache-age=CASeconds (CATimePeriod); remaining-ttl=RTSeconds (RTTimePeriod); expires-delta=ExpiresSeconds`

De termen die in de bovenstaande syntaxis van de antwoordkop worden gebruikt, worden als volgt gedefinieerd:

- MASeconds: geeft de maximale leeftijd (in seconden) aan zoals gedefinieerd door de cachebeheerkoppen van de gevraagde inhoud.

- MATimePeriod: converteert de maximumleeftijdwaarde (dat wil zeggen MASeconds) naar het geschatte equivalent van een grotere eenheid (bijvoorbeeld dagen). 

- UnixTime: geeft de cachetijdstempel van de gevraagde inhoud aan in de Unix-tijd (ook bekend als POSIX-tijd of Unix-tijdperk). De cachetijdstempel geeft de begindatum/-tijd aan van waaruit de TTL van een asset wordt berekend. 

    Als de oorsprongsserver geen gebruik maakt van een HTTP-cachingserver van derden of als die server de leeftijdsreactiekop niet retourneert, is de cache-tijdstempel altijd de datum/tijd waarop het item is opgehaald of opnieuw is gevalideerd. Anders gebruiken de POP-servers het veld Age om de TTL van het actief als volgt te berekenen: Ophalen/Opnieuw validerenDateTime - Leeftijd.

- ddd, dd MMM yyyy HH:mm:ss GMT: Geeft de cachetijdstempel van de gevraagde inhoud aan. Zie de unixtime-term hierboven voor meer informatie.

- CASeconds: geeft het aantal seconden aan dat is verstreken sinds de tijdstempel voor de cache.

- RTSeconds: geeft het aantal resterende seconden aan waarvoor de inhoud in de cache als vers wordt beschouwd. Deze waarde wordt als volgt berekend: RTSeconds = max-age - cache leeftijd.

- RTTimePeriod: converteert de resterende TTL-waarde (dat wil zeggen RTSeconds) naar het geschatte equivalent van een grotere eenheid (bijvoorbeeld dagen).

- VerlopenSeconden: geeft het aantal resterende seconden aan vóór de datum/tijd die is opgegeven in de `Expires` antwoordkop. Als `Expires` de antwoordkop niet in het antwoord is opgenomen, is de waarde van deze term *geen*.

### <a name="sample-response-header"></a>Voorbeeldreactiekop

De volgende voorbeeldantwoordkop geeft de cachestatus van de gevraagde inhoud aan op het moment dat deze is aangevraagd:

```X-EC-Debug: x-ec-cache-state: max-age=604800 (7d); cache-ts=1341802519 (Mon, 09 Jul 2012 02:55:19 GMT); cache-age=0 (0s); remaining-ttl=604800 (7d); expires-delta=none```

