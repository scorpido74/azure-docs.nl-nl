---
title: Prestaties van het randknooppunt analyseren in Azure CDN | Microsoft Documenten
description: Analyseer de prestaties van het randknooppunt in Microsoft Azure CDN. Edge Performance Analytics biedt gedetailleerd informatieverkeer en bandbreedtegebruik voor het CDN.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 8cc596a7-3e01-4f76-af7b-a05a1421517e
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: b8a65d4ae6aaac78e642c851a66b745a940fa0ad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67593903"
---
# <a name="analyze-edge-node-performance-in-microsoft-azure-cdn"></a>Prestaties van edge nod analyseren in Microsoft Azure CDN
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Overzicht
Edge performance analytics biedt gedetailleerd informatieverkeer en bandbreedtegebruik voor het CDN. Deze informatie kan vervolgens worden gebruikt om trending statistieken te genereren, waarmee u inzicht krijgt in hoe uw assets in de cache worden opgeslagen en aan uw klanten worden geleverd. Op zijn beurt u een strategie vormen over hoe u de levering van uw inhoud optimaliseren en om te bepalen welke problemen moeten worden aangepakt om het CDN beter te kunnen gebruiken. Hierdoor u niet alleen de prestaties van de gegevensbezorging verbeteren, maar u ook uw CDN-kosten verlagen.

> [!NOTE]
> Alle rapporten gebruiken UTC/GMT-notatie bij het opgeven van een datum/tijd.
> 
> 

## <a name="reports-and-log-collection"></a>Rapporten en logboekverzameling
CDN-activiteitsgegevens moeten worden verzameld door de Edge Performance Analytics-module voordat deze rapporten kunnen genereren. Dit incassoproces vindt één keer per dag plaats en dekt de activiteit die tijdens de vorige dag heeft plaatsgevonden. Dit betekent dat de statistieken van een rapport een voorbeeld van de statistieken van de dag vertegenwoordigen op het moment dat het werd verwerkt en niet noodzakelijkerwijs de volledige set gegevens voor de huidige dag bevatten. De primaire functie van deze rapporten is het beoordelen van de prestaties. Ze mogen niet worden gebruikt voor factureringsdoeleinden of exacte numerieke statistieken.

> [!NOTE]
> De ruwe gegevens waaruit Edge Performance Analytic-rapporten worden gegenereerd, zijn ten minste 90 dagen beschikbaar.
> 
> 

## <a name="dashboard"></a>Dashboard
Het Edge Performance Analytics-dashboard houdt het huidige en historische CDN-verkeer bij via een grafiek en statistieken. Gebruik dit dashboard om recente en langetermijntrends op het werk van CDN-verkeer voor uw account te detecteren.

Dit dashboard bestaat uit:

* Een interactieve grafiek die het mogelijk maakt de visualisatie van belangrijke statistieken en trends.
* Een tijdlijn die een gevoel geeft van langetermijnpatronen voor belangrijke statistieken en trends.
* Belangrijke statistieken en statistische informatie over hoe ons CDN-netwerk het siteverkeer verbetert, gemeten aan de hand van de algehele prestaties, het gebruik en de efficiëntie.

### <a name="accessing-the-edge-performance-dashboard"></a>Toegang tot het dashboard voor edge performance
1. Klik in het CDN-profielblad op de knop **Beheren.**
   
    ![Knop CDN-profielblad beheer](./media/cdn-edge-performance/cdn-manage-btn.png)
   
    Het CDN-beheerportaal wordt geopend.
2. Plaats de plaats op het tabblad **Analytics** en plaats de plaats vervolgens boven de flyout **edge performance analytics.**  Klik op **dashboard**.
   
    Het edge node analytics-dashboard wordt weergegeven.

### <a name="chart"></a>Grafiek
Het dashboard bevat een grafiek die een statistiek bijhoudt over de periode die is geselecteerd in de tijdlijn die direct eronder wordt weergegeven.  Een tijdlijn die grafieken tot de laatste twee jaar van CDN-activiteit wordt weergegeven direct onder de grafiek.

#### <a name="using-the-chart"></a>De grafiek gebruiken
* Standaard wordt de cache-efficiëntiesnelheid van de afgelopen 30 dagen in kaart gebracht.
* Deze grafiek wordt gegenereerd op basis van gegevens die dagelijks worden verzameld.
* Als u meer dan een dag op de lijngrafiek zweeft, wordt een datum en de waarde van de statistiek op die datum vermeld.
* Klik op Weekends markeren om een overlay van lichtgrijze verticale balken in te schakelen die weekends in de grafiek weergeven. Dit type overlay is handig voor het identificeren van verkeerspatronen in het weekend.
* Klik op Een jaar geleden weergeven om een overlay van de activiteit van het vorige jaar in dezelfde periode in de grafiek in te schakelen. Dit type vergelijking geeft inzicht in lange termijn CDN-gebruikspatronen. De rechterbovenhoek van de grafiek bevat een legenda die de kleurcode voor elke lijngrafiek aangeeft.

#### <a name="updating-the-chart"></a>De grafiek bijwerken
* Tijdbereik: Voer een van de volgende handelingen uit:
  * Selecteer het gewenste gebied in de tijdlijn. De grafiek wordt bijgewerkt met gegevens die overeenkomen met de geselecteerde periode.
  * Dubbelklik op de grafiek om alle beschikbare historische gegevens weer te geven tot een maximum van twee jaar.
* Statistiek: klik op het grafiekpictogram dat naast de gewenste statistiek wordt weergegeven. De grafiek en de tijdlijn worden vernieuwd met gegevens voor de bijbehorende statistiek.

### <a name="key-metrics-and-statistics"></a>Belangrijkste statistieken en statistieken
#### <a name="efficiency-metrics"></a>Efficiëntiestatistieken
Het doel van deze statistieken is om te zien of cache-efficiëntie kan worden verbeterd. De belangrijkste voordelen van cache-efficiëntie zijn:

* Verminderde belasting op de oorsprongsserver die kan leiden tot:
  * Betere webserverprestaties.
  * Lagere operationele kosten.
* Verbeterde versnelling van de gegevenslevering, aangezien meer aanvragen rechtstreeks vanuit het CDN worden ingediend.

| Veld | Beschrijving |
| --- | --- |
| Cache-efficiëntie |Geeft het percentage overgedragen gegevens aan dat vanuit de cache is weergegeven. Deze statistiek meet wanneer een in de cache opgeslagen versie van de gevraagde inhoud rechtstreeks van de CDN (edge servers) aan aanvragers (bijvoorbeeld webbrowser) werd aangeboden |
| Hitsnelheid |Geeft het percentage aanvragen aan dat vanuit de cache is ontvangen. Deze statistiek meet wanneer een in de cache opgeslagen versie van de gevraagde inhoud rechtstreeks van de CDN (edge servers) aan aanvragers (bijvoorbeeld webbrowser) werd aangeboden. |
| % van de externe bytes - Geen cache-config |Geeft het percentage verkeer aan dat is weergegeven van oorsprongsservers naar de CDN (edge servers) dat niet in de cache wordt opgeslagen als gevolg van de functie Cache omzeilen (HTTP Rules Engine). |
| % van de externe bytes - verlopen cache |Hiermee geeft u het percentage verkeer aan dat is weergegeven van oorsprongsservers naar de CDN (edge servers) als gevolg van verouderde inhoudsvalidatie. |

#### <a name="usage-metrics"></a>Metrische gebruiksgegevens
Het doel van deze statistieken is om inzicht te geven in de volgende kostenbesparende maatregelen:

* Het minimaliseren van de operationele kosten via het CDN.
* Vermindering van cdn-uitgaven door cache-efficiëntie en compressie.

> [!NOTE]
> Verkeersvolumenummers vertegenwoordigen verkeer dat is gebruikt bij berekeningen van ratio's en percentages en mogen slechts een deel van het totale verkeer voor klanten met een hoog volume weergeven.
> 
> 

| Veld | Beschrijving |
| --- | --- |
| Ave Bytes Uit |Geeft het gemiddelde aantal bytes aan dat wordt overgedragen voor elk verzoek dat van de CDN (edge servers) naar de aanvrager wordt verzonden (bijvoorbeeld webbrowser). |
| Geen cache config byte-snelheid |Hiermee geeft u het percentage verkeer aan dat van de CDN (edge servers) naar de aanvrager (bijvoorbeeld webbrowser) wordt weergegeven, dat niet in de cache wordt opgeslagen vanwege de functie Bypass-cache. |
| Gecomprimeerde bytesnelheid |Geeft het percentage verkeer aan dat van de CDN (edge servers) naar aanvragers (bijvoorbeeld webbrowser) wordt verzonden in een gecomprimeerde indeling. |
| Bytes uit |Geeft de hoeveelheid gegevens aan in bytes die zijn geleverd van de CDN (edge servers) aan de aanvrager (bijvoorbeeld webbrowser). |
| Bytes In |Geeft de hoeveelheid gegevens aan, in bytes, verzonden van aanvragers (bijvoorbeeld webbrowser) naar het CDN (edge servers). |
| Bytes afstandsbediening |Geeft de hoeveelheid gegevens aan, in bytes, die van CDN- en klantoorsprongservers naar het CDN (edge servers) worden verzonden. |

#### <a name="performance-metrics"></a>Metrische gegevens voor prestaties
Het doel van deze statistieken is om de algehele CDN-prestaties voor uw verkeer bij te houden.

| Veld | Beschrijving |
| --- | --- |
| Overdrachtssnelheid |Geeft de gemiddelde snelheid aan waarmee inhoud van het CDN naar een aanvrager is overgebracht. |
| Duur |Geeft de gemiddelde tijd aan, in milliseconden, het duurde om een asset te leveren aan een aanvrager (bijvoorbeeld webbrowser). |
| Gecomprimeerde aanvraagsnelheid |Hiermee geeft u het percentage treffers aan dat van de CDN (edge servers) naar de aanvrager (bijvoorbeeld webbrowser) in een gecomprimeerde indeling is geleverd. |
| 4xx Foutpercentage |Geeft het percentage hits aan dat een 4xx-statuscode heeft gegenereerd. |
| 5xx Foutpercentage |Geeft het percentage hits aan dat een statuscode van 5xx heeft gegenereerd. |
| Treffers |Geeft het aantal aanvragen voor CDN-inhoud aan. |

#### <a name="secure-traffic-metrics"></a>Statistieken over veilig verkeer
Het doel van deze statistieken is om cdn-prestaties voor HTTPS-verkeer bij te houden.

| Veld | Beschrijving |
| --- | --- |
| Veilige cache-efficiëntie |Geeft het percentage gegevens aan dat wordt overgedragen voor HTTPS-verzoeken dat vanuit de cache is weergegeven. Deze statistiek meet wanneer een in de cache opgeslagen versie van de gevraagde inhoud rechtstreeks van de CDN (edge servers) naar aanvragers (bijvoorbeeld webbrowser) via HTTPS werd aangeboden. |
| Veilige overdrachtssnelheid |Geeft de gemiddelde snelheid aan waarmee inhoud van de CDN (edge servers) naar aanvragers (bijvoorbeeld webservers) via HTTPS is overgedragen. |
| Gemiddelde beveiligde duur |Geeft de gemiddelde tijd aan, in milliseconden, het duurde om een asset te leveren aan een aanvrager (bijvoorbeeld webbrowser) via HTTPS. |
| Beveiligde hits |Geeft het aantal HTTPS-aanvragen voor CDN-inhoud aan. |
| Beveiligde bytes uit |Geeft de hoeveelheid HTTPS-verkeer aan, in bytes, die zijn geleverd van de CDN (edge servers) aan de aanvrager (bijvoorbeeld webbrowser). |

## <a name="reports"></a>Rapporten
Elk rapport in deze module bevat een grafiek en statistieken over bandbreedte en verkeersgebruik voor verschillende soorten statistieken (bijvoorbeeld HTTP-statuscodes, statuscodes in de cache, URL van aanvragen, enz.). Deze informatie kan worden gebruikt om dieper in te gaan op de manier waarop inhoud aan uw klanten wordt aangeboden en om het GEDRAG van CDN te verfijnen om de prestaties van de gegevensbezorging te verbeteren.

### <a name="accessing-the-edge-performance-reports"></a>Toegang tot de prestatierapporten van edge
1. Klik in het CDN-profielblad op de knop **Beheren.**
   
    ![Knop CDN-profielblad beheer](./media/cdn-edge-performance/cdn-manage-btn.png)
   
    Het CDN-beheerportaal wordt geopend.
2. Plaats de plaats op het tabblad **Analytics** en plaats de plaats vervolgens boven de flyout **edge performance analytics.**  Klik op **HTTP Large Object**.
   
    Het scherm met edge node analytics-rapporten wordt weergegeven.

| Rapport | Beschrijving |
| --- | --- |
| Dagelijkse samenvatting |Hiermee u dagelijkse verkeerstrends over een bepaalde periode bekijken. Elke balk in deze grafiek vertegenwoordigt een bepaalde datum. De grootte van de balk geeft het totale aantal treffers aan dat op die datum is opgetreden. |
| Uuroverzicht |Hiermee u verkeerstrends per uur over een bepaalde periode bekijken. Elke balk op deze grafiek vertegenwoordigt één uur op een bepaalde datum. De grootte van de balk geeft het totale aantal treffers aan dat tijdens dat uur is opgetreden. |
| Protocollen |Hiermee wordt de uitsplitsing van het verkeer tussen de HTTP- en HTTPS-protocollen weergegeven. Een donutdiagram geeft het percentage treffers aan dat voor elk type protocol is opgetreden. |
| HTTP-methoden |Hiermee u snel een idee krijgen van welke HTTP-methoden worden gebruikt om uw gegevens op te vragen. De meest voorkomende HTTP-aanvraagmethoden zijn meestal GET, HEAD en POST. Een donutdiagram geeft het percentage treffers aan dat is opgetreden voor elk type HTTP-aanvraagmethode. |
|  URL's |Bevat een grafiek met de top 10 gevraagde URL's. Voor elke URL wordt een balk weergegeven. De hoogte van de balk geeft aan hoeveel hits die bepaalde URL heeft gegenereerd in de periode die in het rapport wordt bestreken. Statistieken voor de top 100 gevraagde URL's worden direct onder deze grafiek weergegeven. |
| CNAMEs |Bevat een grafiek met de top 10 CNAMEs die worden gebruikt om activa op te vragen gedurende de periode van een rapport. Statistieken voor de top 100 gevraagde CNAMEs worden direct onder deze grafiek weergegeven. |
| Oorsprong |Bevat een grafiek met de top 10 CDN- of klantoorsprongservers waaruit activa gedurende een bepaalde periode zijn aangevraagd. Statistieken voor de top 100 gevraagde CDN- of klantorigin-servers worden direct onder deze grafiek weergegeven. Servers van klantoorsprong worden geïdentificeerd aan de andere naam die is gedefinieerd in de optie Mapnaam. |
| Geo POP's |Laat zien hoeveel van uw verkeer wordt gerouteerd via een bepaald point-of-presence (POP). De afkorting met drie letters vertegenwoordigt een POP in ons CDN-netwerk. |
| Clients |Bevat een grafiek met de top 10 clients die activa hebben aangevraagd gedurende een bepaalde periode. Voor de toepassing van dit rapport worden alle verzoeken die afkomstig zijn van hetzelfde IP-adres beschouwd als van dezelfde client. Statistieken voor de top 100 clients worden direct onder deze grafiek weergegeven. Dit rapport is handig voor het bepalen van downloadactiviteitspatronen voor uw topclients. |
| Cachestatussen |Geeft een gedetailleerde uitsplitsing van cachegedrag, wat benaderingen kan onthullen voor het verbeteren van de algehele gebruikerservaring. Aangezien de snelste prestaties afkomstig zijn van cachehits, u de leveringssnelheden van gegevens optimaliseren door cachefouten en verlopen cachehits te minimaliseren. |
| NONE Details |Bevat een grafiek met de top 10 URL's voor elementen waarvoor de versheid van cache-inhoud gedurende een bepaalde periode niet is gecontroleerd. Statistieken voor de top 100 URL's voor dit soort assets worden direct onder deze grafiek weergegeven. |
| CONFIG_NOCACHE Details |Bevat een grafiek met de bovenste 10 URL's voor elementen die niet in de cache zijn opgeslagen vanwege de CDN-configuratie van de klant. Deze typen assets werden rechtstreeks vanaf de oorsprongsserver bediend. Statistieken voor de top 100 URL's voor dit soort assets worden direct onder deze grafiek weergegeven. |
| NIET-CACHEBARE gegevens |Bevat een grafiek met de bovenste 10 URL's voor elementen die niet in de cache kunnen worden opgeslagen vanwege het aanvragen van kopgegevens. Statistieken voor de top 100 URL's voor dit soort assets worden direct onder deze grafiek weergegeven. |
| TCP_HIT Details |Bevat een grafiek met de top 10 URL's voor assets die onmiddellijk vanuit de cache worden weergegeven. Statistieken voor de top 100 URL's voor dit soort assets worden direct onder deze grafiek weergegeven. |
| TCP_MISS Details |Bevat een grafiek met de top 10 URL's voor elementen met een cachestatus van TCP_MISS. Statistieken voor de top 100 URL's voor dit soort assets worden direct onder deze grafiek weergegeven. |
| TCP_EXPIRED_HIT Details |Bevat een grafiek met de top 10 URL's voor verouderde elementen die rechtstreeks vanuit de POP zijn weergegeven. Statistieken voor de top 100 URL's voor dit soort assets worden direct onder deze grafiek weergegeven. |
| TCP_EXPIRED_MISS Details |Bevat een grafiek met de top 10 URL's voor verouderde elementen waarvoor een nieuwe versie moest worden opgehaald van de oorsprongsserver. Statistieken voor de top 100 URL's voor dit soort assets worden direct onder deze grafiek weergegeven. |
| TCP_CLIENT_REFRESH_MISS Details |Bevat een staafdiagram met de bovenste 10 URL's voor assets die zijn opgehaald van een oorsprongsserver vanwege een no-cache-aanvraag van de client. Statistieken voor de top 100 URL's voor dit soort aanvragen worden direct onder deze grafiek weergegeven. |
| Clientaanvraagtypen |Geeft het type aanvragen aan dat is gedaan door HTTP-clients (bijvoorbeeld browsers). Dit rapport bevat een donutgrafiek die inzicht geeft in hoe aanvragen worden behandeld. Bandbreedte- en verkeersinformatie voor elk aanvraagtype wordt onder de grafiek weergegeven. |
| User Agent |Bevat een staafdiagram met de top 10 gebruikersagents om uw inhoud op te vragen via onze CDN. Een gebruikersagent is doorgaans een webbrowser, mediaspeler of een browser voor mobiele telefoons. Statistieken voor de top 100 gebruikersagents worden direct onder deze grafiek weergegeven. |
| Verwijzers |Bevat een staafgrafiek met de top 10 referrers van inhoud die via ons CDN wordt geopend. Een verwijzer is doorgaans de URL van de webpagina of bron die naar uw inhoud verwijst. Gedetailleerde informatie wordt gegeven onder de grafiek voor de top 100 verwijzers. |
| Compressietypen |Bevat een donutgrafiek die de gevraagde elementen opsplitst door of ze zijn gecomprimeerd door onze edge-servers. Het percentage gecomprimeerde elementen wordt opgesplitst naar het type compressie dat wordt gebruikt. Gedetailleerde informatie wordt weergegeven onder de grafiek voor elk compressietype en elke status. |
| Bestandstypen |Bevat een staafdiagram met de top 10 bestandstypen die via ons CDN zijn aangevraagd voor uw account. Voor de toepassing van dit rapport wordt een bestandstype gedefinieerd door de bestandsnaamextensie van het \[actief en\]het \[internetmediatype\](bijvoorbeeld \[.html-tekst/html, .htm-tekst/html, .aspx-tekst/html,\]enz.). Gedetailleerde informatie wordt weergegeven onder de grafiek voor de top 100 bestandstypen. |
| Unieke bestanden |Bevat een grafiek die het totale aantal unieke activa dat op een bepaalde dag is aangevraagd, over een bepaalde periode vastzet. |
| Token Auth-overzicht |Bevat een cirkeldiagram dat een snel overzicht geeft over de vraag of aangevraagde elementen zijn beschermd door tokenverificatie. Beveiligde elementen worden weergegeven in de grafiek op basis van de resultaten van hun poging tot verificatie. |
| Token Auth-weigeringsgegevens |Bevat een staafgrafiek waarmee u de top 10-aanvragen bekijken die zijn geweigerd vanwege verificatie op basis van tokens. |
| HTTP-antwoordcodes |Biedt een uitsplitsing van de HTTP-statuscodes (bijvoorbeeld 200 OK, 403 Verboden, 404 niet gevonden, enz.) die door onze edge-servers aan uw HTTP-clients zijn geleverd. Met een cirkeldiagram u snel beoordelen hoe uw assets zijn bediend. Gedetailleerde statistische gegevens worden verstrekt voor elke responscode onder de grafiek. |
| 404 fouten |Bevat een staafdiagram waarmee u de top 10-aanvragen bekijken die hebben geleid tot een antwoordcode van 404 niet gevonden. |
| 403 Fouten |Bevat een staafdiagram waarmee u de top 10-aanvragen bekijken die hebben geleid tot een 403 verboden antwoordcode. Een 403 Verboden reactiecode treedt op wanneer een verzoek wordt geweigerd door een server van de oorsprong van de klant of een edge-server op onze POP. |
| 4xx-fouten |Bevat een staafdiagram waarmee u de top 10-aanvragen bekijken die hebben geleid tot een antwoordcode in het bereik van 400. Uitgesloten van dit rapport zijn 403 Niet gevonden en 404 Verboden reactiecodes. Doorgaans treedt een 4xx-antwoordcode op wanneer een aanvraag wordt geweigerd als gevolg van een clientfout. |
| 504 Fouten |Bevat een staafdiagram waarmee u de top 10-aanvragen bekijken die hebben geleid tot een 504 Gateway Timeout-antwoordcode. Een 504 Gateway Timeout-antwoordcode treedt op wanneer een time-out optreedt wanneer een HTTP-proxy probeert te communiceren met een andere server. In het geval van onze CDN treedt een 504 Gateway Timeout-antwoordcode meestal op wanneer een edge-server geen communicatie kan tot stand brengen met een server van klantoorsprong. |
| 502 fouten |Bevat een staafdiagram waarmee u de top 10-aanvragen bekijken die hebben geleid tot een 502 Bad Gateway-antwoordcode. Een 502 Bad Gateway-antwoordcode treedt op wanneer een HTTP-protocolfout optreedt tussen een server en een HTTP-proxy. In het geval van onze CDN treedt een 502 Bad Gateway-antwoordcode meestal op wanneer een server van klantoorsprong een ongeldig antwoord op een edge-server retourneert. Een antwoord is ongeldig als het niet kan worden ontleed of als het onvolledig is. |
| 5xx-fouten |Bevat een staafdiagram waarmee u de top 10-aanvragen bekijken die hebben geleid tot een antwoordcode in het 500-bereik.  Uitgesloten van dit rapport zijn 502 Bad Gateway en 504 Gateway Timeout response codes. |

## <a name="see-also"></a>Zie ook
* [Overzicht van Azure CDN](cdn-overview.md)
* [Realtime statistieken in Microsoft Azure CDN](cdn-real-time-stats.md)
* [StandaardHTTP-gedrag overschrijven met de rules-engine](cdn-rules-engine.md)
* [Geavanceerde HTTP-rapporten](cdn-advanced-http-reports.md)

