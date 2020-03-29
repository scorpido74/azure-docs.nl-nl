---
title: Kernrapporten van Verizon | Microsoft Documenten
description: 'U gebruikspatronen voor uw CDN bekijken aan de hand van de volgende rapporten: Bandbreedte, Overgedragen gegevens, treffers, cachestatussen, cachehitratio, IPV4/IPV6-gegevens overgedragen.'
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 5a0d9018-8bdb-48ff-84df-23648ebcf763
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: d48ddafdc1ec30ae1533b3a3101582f33e7f4b5c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67594165"
---
# <a name="core-reports-from-verizon"></a>Core Rapporten van Verizon

[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Door Verizon Core Reports te gebruiken via de Portal voor Verizon-profielen beheren, u gebruikspatronen voor uw CDN bekijken met de volgende rapporten:

* Bandbreedte
* Overgedragen gegevens
* Treffers
* Cachestatussen
* Cache hitratio
* IPV4/IPV6-gegevens overgedragen

## <a name="accessing-verizon-core-reports"></a>Toegang tot Verizon Core Reports
1. Klik in het CDN-profielblad op de knop **Beheren.**
   
    ![Knop CDN-profiel beheren](./media/cdn-reports/cdn-manage-btn.png)
   
    Het CDN-beheerportaal wordt geopend.
2. Plaats de plaats op het tabblad **Analytics** en plaats de plaats boven de flyout **Core Reports.** Klik op een rapport in het menu.
   
    ![CDN-beheerportal - menu Kernrapporten](./media/cdn-reports/cdn-core-reports.png)

3. Selecteer voor elk rapport een datumbereik in de lijst **Datumbereik.** U een vooraf gedefinieerd datumbereik selecteren, zoals **Vandaag** of **Deze week,** of u **Aangepast** selecteren en handmatig een datumbereik invoeren door op de agendapictogrammen te klikken. 

4. Nadat u een datumbereik hebt geselecteerd, klikt u op **Gaan** om het rapport te genereren. 

4. Als u de gegevens in Excel-indeling wilt exporteren, klikt u op het Excel-pictogram boven de knop **Start.**

## <a name="bandwidth"></a>Bandbreedte
Het bandbreedterapport bestaat uit een grafiek en gegevenstabel die het CDN-bandbreedtegebruik voor HTTP en HTTPS over een bepaalde periode in Mbps aangeeft. U het bandbreedtegebruik bekijken voor alle POP's of voor een bepaalde POP. Met dit rapport u de verkeerspieken en -distributie voor POP's bekijken.

Selecteer in de lijst **Randknooppunten** **alle randknooppunten** om verkeer van alle knooppunten te bekijken of een specifiek gebied te selecteren.

Het rapport wordt elke vijf minuten bijgewerkt.

![Bandbreedterapport](./media/cdn-reports/cdn-bandwidth.png)

## <a name="data-transferred"></a>Overgedragen gegevens
Dit rapport bestaat uit een grafiek en gegevenstabel die het CDN-verkeersgebruik voor HTTP en HTTPS over een bepaalde periode, in GB aangeeft. U het verkeersgebruik bekijken voor alle POP's of voor een bepaalde POP. Met dit rapport u de verkeerspieken en -verdeling over POP's bekijken.

Selecteer in de lijst **Randknooppunten** **alle randknooppunten** om verkeer van alle knooppunten te bekijken of een specifiek gebied te selecteren.

Het rapport wordt elke vijf minuten bijgewerkt.

![Rapport over overgedragen gegevens](./media/cdn-reports/cdn-data-transferred.png)

## <a name="hits-status-codes"></a>Hits (statuscodes)
In dit rapport wordt de verdeling van de statuscodes voor aanvragen voor uw inhoud beschreven. Elke aanvraag voor inhoud genereert een HTTP-statuscode. De statuscode beschrijft hoe edge-POP's de aanvraag hebben afgehandeld. Een statuscode van 2xx geeft bijvoorbeeld aan dat de aanvraag met succes is weergegeven aan een client, terwijl een statuscode van 4xx aangeeft dat er een fout is opgetreden. Zie [Lijst met HTTP-statuscodes](https://en.wikipedia.org/wiki/List_of_HTTP_status_codes)voor meer informatie over HTTP-statuscodes .

![Rapport Hits](./media/cdn-reports/cdn-hits.png)

## <a name="cache-statuses"></a>Cachestatussen
In dit rapport wordt de verdeling van cachehits en cache-missers voor clientaanvragen beschreven. Omdat de snelste prestaties resulteren in cachehits, u de leveringssnelheden van gegevens optimaliseren door cachefouten en verlopen cachehits te minimaliseren. 

Als u cachefouten wilt verminderen, configureert u uw origin-server om het gebruik van het volgende te minimaliseren: 
 * `no-cache`antwoordkoppen
 * Query-string caching, tenzij strikt nodig  
 * Niet-cachebare antwoordcodes

Als u verlopen cachehits wilt verminderen, stelt u een asset in `max-age` op een lange periode om het aantal aanvragen voor de oorspronkelijke server te minimaliseren.

![Rapport Cachestatussen](./media/cdn-reports/cdn-cache-statuses.png)

### <a name="main-cache-statuses-include"></a>Hoofdcachestatussen zijn:
* TCP_HIT: Geserveerd vanaf edge server. Het object bevond zich in de cache en heeft de maximumleeftijd niet overschreden.
* TCP_MISS: Geserveerd vanaf origin server. Het object bevond zich niet in de cache en het antwoord was terug naar de oorsprong.
* TCP_EXPIRED _MISS: Vanaf oorsprong server geserveerd na revalidatie met oorsprong. Het object bevond zich in de cache, maar had zijn maximale leeftijd overschreden. Een revalidatie met oorsprong heeft ertoe geleid dat het cacheobject werd vervangen door een nieuw antwoord van oorsprong.
* TCP_EXPIRED _HIT: Geserveerd van Edge na revalidatie met oorsprong. Het object bevond zich in de cache, maar had zijn maximumleeftijd overschreden. Een validatie met de oorsprongsserver heeft ertoe geleid dat het cacheobject niet is gewijzigd.

### <a name="full-list-of-cache-statuses"></a>Volledige lijst met cachestatussen
* TCP_HIT - Deze status wordt gerapporteerd wanneer een aanvraag rechtstreeks van de POP aan de client wordt betekend. Een asset wordt onmiddellijk geserveerd vanaf een POP wanneer het in de cache wordt opgeslagen op de POP die het dichtst bij de client staat en een geldige time-to-live (TTL) heeft. TTL wordt bepaald door de volgende antwoordkoppen:
  
  * Cache-Control: s-maxage
  * Cache-Control: max-leeftijd
  * Verloopt
* TCP_MISS: Deze status geeft aan dat er geen in de cache opgeslagen versie van het gevraagde element is gevonden op de POP die het dichtst bij de client staat. Het item wordt aangevraagd bij een origin-server of een origin shield-server. Als de origin-server of de origin shield-server een asset retourneert, wordt deze weergegeven aan de client en in de cache opgeslagen op zowel de client als de edge-server. Anders wordt een niet-200-statuscode (bijvoorbeeld 403 Verboden of 404 niet gevonden) geretourneerd.
* TCP_EXPIRED_HIT: Deze status wordt gerapporteerd wanneer een verzoek dat zich richt op een actief met een verlopen TTL rechtstreeks van de POP aan de client is aangeboden. Bijvoorbeeld wanneer de maximumleeftijd van het actief is verstreken. 
  
   Een verlopen aanvraag resulteert meestal in een validatieverzoek naar de oorspronkelijke server. Als er een TCP_EXPIRED_HIT status optreedt, moet de oorsprongsserver aangeven dat er geen nieuwere versie van het item bestaat. Deze situatie resulteert meestal in een update van de headers Cache-Control en Expires van het asset.
* TCP_EXPIRED_MISS: Deze status wordt gerapporteerd wanneer een nieuwere versie van een verlopen item in de cache van de POP aan de client wordt geserveerd. Deze status treedt op wanneer de TTL voor een in de cache opgeslagen asset is verlopen (bijvoorbeeld verlopen max-age) en de oorsprongsserver een nieuwere versie van dat item retourneert. Deze nieuwe versie van het item wordt geserveerd aan de client in plaats van de versie in de cache. Bovendien wordt het in de cache opgeslagen op de edge-server en de client.
* CONFIG_NOCACHE: Deze status geeft aan dat een klantspecifieke configuratie, waardoor de edge POP heeft voorkomen dat het element in de cache werd opgeslagen.
* NONE - Deze status geeft aan dat er geen versheidscontrole voor cache-inhoud is uitgevoerd.
* TCP_CLIENT_REFRESH_MISS: Deze status wordt gerapporteerd wanneer een HTTP-client, zoals een browser, een edge POP dwingt om een nieuwe versie van een verouderd item op te halen van de oorsprongsserver. Standaard voorkomen de servers dat een HTTP-client de edge-servers dwingt om een nieuwe versie van het item op te halen van de origin-server.
* TCP_PARTIAL_HIT: Deze status wordt gerapporteerd wanneer een aanvraag voor bytebereik resulteert in een treffer voor een item in de cache. Het gevraagde byte-assortiment wordt onmiddellijk van de POP aan de klant aangeboden.
* UNCACHEABLE: Deze status wordt gerapporteerd `Cache-Control` wanneer `Expires` de kopteksten en kopteksten van een asset aangeven dat het niet in de cache mag worden opgeslagen op een POP of door de HTTP-client. Dit soort aanvragen worden bediend vanaf de oorsprongsserver.

## <a name="cache-hit-ratio"></a>Cache hitratio
Dit rapport geeft het percentage aanvragen in de cache aan dat rechtstreeks vanuit de cache is weergegeven.

Het rapport bevat de volgende details:

* De gevraagde inhoud is opgeslagen op de POP die het dichtst bij de aanvrager staat.
* Het verzoek werd direct vanaf de rand van ons netwerk ingediend.
* Het verzoek vereist geen validatie met de origin-server.

Het rapport bevat niet:

* Aanvragen die worden geweigerd vanwege filteropties voor land/regio.
* Aanvragen voor activa waarvan de headers aangeven dat ze niet in de cache mogen worden opgeslagen. Bijvoorbeeld, `Cache-Control: private`of `Cache-Control: no-cache` `Pragma: no-cache` headers voorkomen dat een asset in de cache wordt opgeslagen.
* Aanvragen voor bytebereik voor gedeeltelijk gecachede inhoud.

De formule is: (TCP_ HIT/(TCP_ HIT+TCP_MISS)*100

![Rapport met de cachehitratio](./media/cdn-reports/cdn-cache-hit-ratio.png)

## <a name="ipv4ipv6-data-transferred"></a>IPV4/IPV6 Gegevens overgedragen
Dit rapport toont de verdeling van het verkeersgebruik in IPV4 vs IPV6.

![IPV4/IPV6 Gegevens overgedragen](./media/cdn-reports/cdn-ipv4-ipv6.png)

## <a name="considerations"></a>Overwegingen
Rapporten kunnen alleen worden gegenereerd in de afgelopen 18 maanden.

