---
title: Statistieken en logboeken controleren in Azure Front Door| Microsoft Documenten
description: In dit artikel worden de verschillende statistieken en toegangslogboeken beschreven die Azure Front Door ondersteunt
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
ms.openlocfilehash: b935355cce36a6e26b168db286ab40248f8f0f68
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471724"
---
# <a name="monitoring-metrics-and-logs-in-azure-front-door"></a>Statistieken en logboeken in Azure Front Door bewaken

Door Azure Front Door te gebruiken, u resources op de volgende manieren controleren:

- **Statistieken**. Azure Front Door heeft momenteel zeven statistieken om prestatiemeteritems weer te geven.
- **Logboeken**. Met activiteits- en diagnostische logboeken kunnen prestaties, toegang en andere gegevens worden opgeslagen of verbruikt vanuit een bron voor bewakingsdoeleinden.

### <a name="metrics"></a>Metrische gegevens

Statistieken zijn een functie voor bepaalde Azure-resources waarmee u prestatiemeteritems in de portal weergeven. De volgende zijn beschikbare Front Door metrics:

| Gegevens | Metrische weergavenaam | Eenheid | Dimensies | Beschrijving |
| --- | --- | --- | --- | --- |
| Aantal aanvragen | Aantal aanvragen | Count | HttpStatus</br>HttpStatusgroep</br>ClientRegio</br>ClientLand | Het aantal verzoeken van klanten die door De Voordeur worden gediend.  |
| RequestSize | Aanvraaggrootte | Bytes | HttpStatus</br>HttpStatusgroep</br>ClientRegio</br>ClientLand | Het aantal bytes dat wordt verzonden als aanvragen van clients naar Front Door. |
| ResponseSize | Reactiegrootte | Bytes | HttpStatus</br>HttpStatusgroep</br>ClientRegio</br>ClientLand | Het aantal bytes dat wordt verzonden als antwoorden van Voordeur naar klanten. |
| TotalLatency TotalLatency | Totale latentie | Milliseconden | HttpStatus</br>HttpStatusgroep</br>ClientRegio</br>ClientLand | De tijd berekend op basis van het verzoek van de klant ontvangen door Front Door totdat de klant erkende de laatste reactie byte van Front Door. |
| BackendRequestCount | Aantal back-endaanvragen | Count | HttpStatus</br>HttpStatusgroep</br>Back-end | Het aantal verzoeken dat van Voordeur naar backends wordt verzonden. |
| BackendRequestLatentie | Latentie voor back-endaanvragen | Milliseconden | Back-end | De tijd berekend vanaf het moment dat het verzoek werd verzonden door Front Door naar de backend tot Front Door ontving de laatste reactie byte van de backend. |
| Back-endHealthPercentage | Backend-statuspercentage | Percentage | Back-end</br>BackendPool | Het percentage succesvolle gezondheidssondes van Voordeur tot backends. |
| WebApplicationFirewallRequestCount | Aantal firewallaanvragen voor webtoepassingen | Count | PolicyName</br>RegelNaam</br>Actie | Het aantal clientaanvragen dat wordt verwerkt door de beveiliging van de toepassingslaag van Front Door. |

## <a name="activity-logs"></a><a name="activity-log"></a>Activiteitenlogboeken

Activiteitslogboeken geven informatie over de bewerkingen op De Voordeur. Ze bepalen ook de wat, wie, en wanneer voor een schrijfbewerkingen (zetten, plaatsen of verwijderen) genomen op Front Door.

>[!NOTE]
>Activiteitslogboeken bevatten geen leesbewerkingen (get). Ze bevatten ook geen bewerkingen die u uitvoert met behulp van de Azure-portal of de oorspronkelijke Beheer-API.

Toegang tot activiteitlogboeken in uw voordeur of alle logboeken van uw Azure-resources in Azure Monitor. Activiteitenlogboeken weergeven:

1. Selecteer de instantie Voordeur.
2. Selecteer **Activiteitslogboek**.

    ![Activiteitenlogboek](./media/front-door-diagnostics/activity-log.png)

3. Kies een filterbereik en selecteer **Toepassen**.

## <a name="diagnostic-logs"></a><a name="diagnostic-logging"></a>Diagnostische logboeken
Diagnostische logboeken bieden uitgebreide informatie over bewerkingen en fouten die belangrijk zijn voor controle en probleemoplossing. Diagnostische logboeken verschillen van activiteitslogboeken.

Activiteitslogboeken bieden inzicht in de bewerkingen die zijn uitgevoerd op Azure-resources. Diagnostische logboeken geven inzicht in bewerkingen die uw resource heeft uitgevoerd. Zie [Diagnostische logboeken voor Azure Monitor](../azure-monitor/platform/platform-logs-overview.md)voor meer informatie .

![Diagnostische logboeken](./media/front-door-diagnostics/diagnostic-log.png)

Ga als volgende voor het configureren van diagnostische logboeken voor uw voordeur:

1. Selecteer uw Azure-voordeur.

2. Kies **Diagnostische instellingen**.

3. Selecteer **Diagnostische gegevens inschakelen**. Archiveer diagnostische logboeken samen met statistieken naar een opslagaccount, stream ze naar een gebeurtenishub of stuur ze naar Azure Monitor-logboeken.

Front Door biedt momenteel diagnostische logs (batched per uur). Diagnostische logboeken bieden afzonderlijke API-aanvragen bij elke vermelding met het volgende schema:

| Eigenschap  | Beschrijving |
| ------------- | ------------- |
| BackendHostname | Als de aanvraag is doorgestuurd naar een backend, vertegenwoordigt dit veld de hostnaam van de backend. Dit veld is leeg als de aanvraag is doorgestuurd of doorgestuurd naar een regionale cache (wanneer caching is ingeschakeld voor de routeringsregel). |
| CacheStatus | Voor cachingscenario's definieert dit veld de cachehit/miss op de POP |
| ClientIp | Het IP-adres van de klant die het verzoek heeft ingediend. Als er een X-Forwarded-For-header in de aanvraag is opgenomen, wordt het IP-client uit hetzelfde gekozen. |
| ClientPort | De IP-poort van de client die het verzoek heeft ingediend. |
| HttpMethod | HTTP-methode die door de aanvraag wordt gebruikt. |
| Httpstatuscode | De HTTP-statuscode is geretourneerd van de proxy. |
| HttpStatusDetails | Resulterende status op het verzoek. Betekenis van deze tekenreekswaarde is te vinden in een statusreferentietabel. |
| HttpVersie | Type van de aanvraag of verbinding. |
| POP | Korte naam van de rand waar het verzoek landde. |
| AanvragenBytes | De grootte van het HTTP-aanvraagbericht in bytes, inclusief de aanvraagkoppen en de aanvraaginstantie. |
| RequestUri | URI van het ontvangen verzoek. |
| ResponseBytes | Bytes die door de backendserver als antwoord worden verzonden.  |
| Routeregelnaam | De naam van de routeringsregel die de aanvraag heeft gematcht. |
| SecurityProtocol (SecurityProtocol) | De TLS/SSL-protocolversie die door het verzoek of null wordt gebruikt als er geen versleuteling is. |
| SentToOriginShield | Booleaanse veld vertegenwoordigen als er een cache missen op de eerste omgeving en de aanvraag werd verzonden naar de regionale cache. Negeer dit veld als de routeringsregel een omleiding is of wanneer caching niet is ingeschakeld. |
| Tijdgenomen | De tijdsduur die de actie heeft ondernomen, in milliseconden. |
| TrackingReferentie | De unieke referentietekenreeks die een aanvraag van Front Door identificeert en ook als X-Azure-Ref-header naar de client wordt verzonden. Vereist voor het zoeken naar details in de toegangslogboeken voor een specifiek verzoek. |
| Useragent | Het browsertype dat de client heeft gebruikt. |

**Let op:** Voor verschillende routeringsconfiguraties en verkeersgedrag kunnen sommige velden zoals backendHostname, cacheStatus, sentToOriginShield en POP-veld met verschillende waarden reageren. In de onderstaande tabel worden de verschillende waarden uitgelegd, deze velden hebben voor verschillende scenario's:

| Scenario's | Aantal logboekvermeldingen | POP | BackendHostname | SentToOriginShield | CacheStatus |
| ------------- | ------------- | ------------- | ------------- | ------------- | ------------- |
| Routeringsregel zonder caching ingeschakeld | 1 | Edge POP-code | Backend waar het verzoek is doorgestuurd | False | CONFIG_NOCACHE |
| Routeringsregel met caching ingeschakeld. Cache hit aan de rand POP | 1 | Edge POP-code | Leeg | False | Hit |
| Routeringsregel met caching ingeschakeld. Cache missen bij edge POP, maar cache hit op ouder cache POP | 2 | 1. Edge POP-code</br>2. POP-code voor bovenliggende cache | 1. POP-hostnaam in bovenliggende cache</br>2. Leeg | 1.</br>2. | 1.</br>2. PARTIAL_HIT |
| Routeringsregel met caching ingeschakeld. Cache missen op zowel edge en ouder cache POP | 2 | 1. Edge POP-code</br>2. POP-code voor bovenliggende cache | 1. POP-hostnaam in bovenliggende cache</br>2. Backend waarmee cache kan worden ingevuld | 1.</br>2. | 1.</br>2. |


## <a name="next-steps"></a>Volgende stappen

- [Een voordeurprofiel maken](quickstart-create-front-door.md)
- [Hoe Voordeur werkt](front-door-routing-architecture.md)
