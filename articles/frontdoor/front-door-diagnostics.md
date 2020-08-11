---
title: Metrische gegevens en logboeken bewaken in azure front-deur | Microsoft Docs
description: In dit artikel worden de verschillende metrische gegevens en toegangs logboeken beschreven die door Azure front-deur worden ondersteund
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/18/2018
ms.author: sharadag
ms.openlocfilehash: 249b2406f048709fd7e4f76f8272b3158708e5bb
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/10/2020
ms.locfileid: "88056428"
---
# <a name="monitoring-metrics-and-logs-in-azure-front-door"></a>Metrische gegevens en logboeken bewaken in azure front deur

Met behulp van de voor deur van Azure kunt u resources op de volgende manieren controleren:

- **Metrische gegevens**. Azure front-deur heeft momenteel zeven metrische gegevens om prestatie meter items weer te geven.
- **Logboeken**. Met activiteiten en Diagnostische logboeken kunnen prestaties, toegang en andere gegevens worden opgeslagen of verbruikt vanuit een resource voor bewakings doeleinden.

### <a name="metrics"></a>Metrische gegevens

Metrische gegevens zijn een functie voor bepaalde Azure-resources waarmee u prestatie meter items in de portal kunt weer geven. Hieronder vindt u de beschik bare metrische gegevens voor de voor deur:

| Gegevens | Weergave naam voor metrische gegevens | Eenheid | Dimensies | Beschrijving |
| --- | --- | --- | --- | --- |
| RequestCount | Aantal aanvragen | Aantal | Http status</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Het aantal client aanvragen dat door de voor deur wordt geleverd.  |
| RequestSize | Aanvraag grootte | Bytes | Http status</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Het aantal bytes dat is verzonden als aanvragen van clients naar de voor deur. |
| ResponseSize | Grootte van antwoord | Bytes | Http status</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Het aantal bytes dat is verzonden als reacties van de voor deur naar clients. |
| TotalLatency | Totale latentie | Milliseconden | Http status</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | De tijd die wordt berekend op basis van de aanvraag van de client die wordt ontvangen door de voor deur totdat de client de laatste reactie van de voor deur heeft bevestigd. |
| BackendRequestCount | Aantal back-aanvragen | Aantal | Http status</br>HttpStatusGroup</br>Back-end | Het aantal aanvragen dat van de voor deur naar de back-end wordt verzonden. |
| BackendRequestLatency | Latentie van back-upaanvraag | Milliseconden | Back-end | De tijd die wordt berekend vanaf het moment dat de aanvraag door de voor deur naar de back-end is verzonden tot de voor deur de laatste reactie byte van de back-end heeft ontvangen. |
| BackendHealthPercentage | Back-status percentage | Percentage | Back-end</br>Hosts | Het percentage geslaagde status tests van front-deur naar back-end. |
| WebApplicationFirewallRequestCount | Aantal aanvragen voor Web Application firewall | Aantal | PolicyName</br>RuleName</br>Bewerking | Het aantal client aanvragen dat is verwerkt door de beveiligingslaag van de toepassingslaag. |

## <a name="activity-logs"></a><a name="activity-log"></a>Activiteiten logboeken

Activiteiten logboeken bevatten informatie over de bewerkingen die op de voor deur worden uitgevoerd. Ze bepalen ook de What, wie en wanneer er schrijf bewerkingen (put, post of Delete) op de voor deur worden uitgevoerd.

>[!NOTE]
>Activiteiten logboeken bevatten geen lees bewerkingen (Get). Ze bevatten ook geen bewerkingen die u uitvoert met behulp van de Azure Portal of de oorspronkelijke beheer-API.

U krijgt toegang tot activiteiten Logboeken in uw voor deur of in alle logboeken van uw Azure-resources in Azure Monitor. Activiteitenlogboeken weergeven:

1. Selecteer uw voor deur-exemplaar.
2. Selecteer **Activiteitenlogboek**.

    ![Activiteitenlogboek](./media/front-door-diagnostics/activity-log.png)

3. Kies een filter bereik en selecteer vervolgens **Toep assen**.

## <a name="diagnostic-logs"></a><a name="diagnostic-logging"></a>Diagnostische logboeken
Diagnostische logboeken bieden uitgebreide informatie over bewerkingen en fouten die belang rijk zijn voor controle en probleem oplossing. Diagnostische logboeken verschillen van activiteiten Logboeken.

Activiteiten logboeken bieden inzicht in de bewerkingen die worden uitgevoerd op Azure-resources. Diagnostische logboeken bieden inzicht in bewerkingen die door uw resource worden uitgevoerd. Zie [Azure monitor Diagnostische logboeken](../azure-monitor/platform/platform-logs-overview.md)voor meer informatie.

![Diagnostische logboeken](./media/front-door-diagnostics/diagnostic-log.png)

Diagnostische logboeken voor uw front-deur configureren:

1. Selecteer uw Azure-voor deur.

2. Kies **Diagnostische instellingen**.

3. Selecteer **Diagnostische gegevens inschakelen**. Archiveer Diagnostische logboeken Samen met metrische gegevens naar een opslag account, stream ze naar een Event Hub of stuur ze naar Azure Monitor-Logboeken.

De voor deur bevat momenteel Diagnostische logboeken (batched per uur). Diagnostische logboeken bieden afzonderlijke API-aanvragen voor elke vermelding met het volgende schema:

| Eigenschap  | Beschrijving |
| ------------- | ------------- |
| BackendHostname | Als de aanvraag wordt doorgestuurd naar een back-end, vertegenwoordigt dit veld de hostnaam van de back-end. Dit veld is leeg als de aanvraag is omgeleid of doorgestuurd naar een regionale cache (wanneer caching is ingeschakeld voor de routerings regel). |
| CacheStatus | In het geval van cache scenario's definieert dit veld de cache treffer/Missing in de POP |
| ClientIp | Het IP-adres van de client die de aanvraag heeft ingediend. Als er een X-doorgestuurd is voor de header in de aanvraag, wordt het client-IP-adres uit hetzelfde gekozen. |
| ClientPort | De IP-poort van de client die de aanvraag heeft ingediend. |
| HttpMethod | De HTTP-methode die door de aanvraag wordt gebruikt. |
| Http status code | De HTTP-status code die is geretourneerd door de proxy. |
| HttpStatusDetails | De resulterende status van de aanvraag. De betekenis van deze teken reeks waarde is te vinden in een status verwijzings tabel. |
| HttpVersion | Het type van de aanvraag of verbinding. |
| POP | De korte naam van de rand waar de aanvraag is gelandd. |
| RequestBytes | De grootte van het HTTP-aanvraag bericht in bytes, met inbegrip van de aanvraag headers en de hoofd tekst van de aanvraag. |
| RequestUri | De URI van de ontvangen aanvraag. |
| ResponseBytes | Bytes dat als reactie door de back-endserver wordt verzonden.  |
| RoutingRuleName | De naam van de routerings regel die de aanvraag heeft gevonden. |
| RulesEngineMatchNames | De namen van de regels die de aanvraag heeft gevonden. |
| Exemplaar | De TLS/SSL-protocol versie die wordt gebruikt door de aanvraag of null als er geen versleuteling is. |
| SentToOriginShield | Boolean-veld dat aangeeft of er een cache-Missing is in de eerste omgeving en dat de aanvraag is verzonden naar de regionale cache. Dit veld negeren als de routerings regel een omleiding is of wanneer caching niet is ingeschakeld. |
| TimeTaken | De tijds duur van de eerste byte van de aanvraag naar de laatste byte van de reactie, in seconden. |
| TrackingReference | De unieke verwijzings reeks die een aanvraag identificeert die wordt geleverd door de voor deur, ook verzonden als X-Azure-ref-header naar de client. Vereist voor het zoeken naar details in de logboeken van de toegang voor een specifieke aanvraag. |
| User agent | Het browser type dat door de client wordt gebruikt. |

**Opmerking:** Voor verschillende routerings configuraties en verkeers gedrag kunnen sommige velden, zoals backendHostname, cacheStatus, sentToOriginShield en POP, worden gereageerd met verschillende waarden. In de onderstaande tabel worden de verschillende waarden uitgelegd. deze velden hebben verschillende scenario's:

| Scenario's | Aantal logboek vermeldingen | POP | BackendHostname | SentToOriginShield | CacheStatus |
| ------------- | ------------- | ------------- | ------------- | ------------- | ------------- |
| Routerings regel zonder caching ingeschakeld | 1 | POP-code van rand | Back-end waar de aanvraag is doorgestuurd | Niet waar | CONFIG_NOCACHE |
| Routerings regel met caching ingeschakeld. Cache treffer aan de rand POP | 1 | POP-code van rand | Leeg | Niet waar | BEZOCHT |
| Routerings regel met caching ingeschakeld. Geen geheugen meer in de POP-context, maar cache is aanwezig in de POP van de bovenliggende cache | 2 | 1. POP-code van rand</br>2. POP-code van bovenliggende cache | 1. POP-hostnaam van bovenliggende cache</br>2. empty | 1. True</br>2. onwaar | 1. MIS</br>2. PARTIAL_HIT |
| Routerings regel met caching ingeschakeld. Cache-missers aan de rand en de bovenliggende cache POP | 2 | 1. POP-code van rand</br>2. POP-code van bovenliggende cache | 1. POP-hostnaam van bovenliggende cache</br>2. back-end waarmee cache kan worden gevuld | 1. True</br>2. onwaar | 1. MIS</br>2. MIS |


## <a name="next-steps"></a>Volgende stappen

- [Een Front Door-profiel maken](quickstart-create-front-door.md)
- [De werking van de voor deur](front-door-routing-architecture.md)
