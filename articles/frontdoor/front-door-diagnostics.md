---
title: Metrische gegevens en logboeken bewaken in de Azure front-deur-service | Microsoft Docs
description: In dit artikel worden de verschillende metrische gegevens en toegangs logboeken beschreven die worden ondersteund door de Azure front-deur service
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
ms.openlocfilehash: 229706ff91b776363d3e9de080e02cee5edf9c77
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/30/2019
ms.locfileid: "71677897"
---
# <a name="monitoring-metrics-and-logs-in-azure-front-door-service"></a>Metrische gegevens en logboeken bewaken in de Azure front-deur-service

Met behulp van de Azure front-deur service kunt u bronnen op de volgende manieren controleren:

- **Metrische gegevens**. Azure front-deur heeft momenteel zeven metrische gegevens om prestatie meter items weer te geven.
- **Logboeken**. Met activiteiten en Diagnostische logboeken kunnen prestaties, toegang en andere gegevens worden opgeslagen of verbruikt vanuit een resource voor bewakings doeleinden.

### <a name="metrics"></a>Metrische gegevens

Metrische gegevens zijn een functie voor bepaalde Azure-resources waarmee u prestatie meter items in de portal kunt weer geven. Hieronder vindt u de beschik bare metrische gegevens voor de voor deur:

| Gegevens | De naam van de metrische gegevens weergeven | Eenheid | Dimensies | Description |
| --- | --- | --- | --- | --- |
| RequestCount | Aantal aanvragen | Count | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Het aantal client aanvragen dat door de voor deur wordt geleverd.  |
| RequestSize | Aanvraag grootte | Bytes | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Het aantal bytes dat is verzonden als aanvragen van clients naar de voor deur. |
| ResponseSize | Grootte van antwoord | Bytes | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Het aantal bytes dat is verzonden als reacties van de voor deur naar clients. |
| TotalLatency | Totale latentie | Milliseconden | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | De tijd die wordt berekend op basis van de aanvraag van de client die wordt ontvangen door de voor deur totdat de client de laatste reactie van de voor deur heeft bevestigd. |
| BackendRequestCount | Aantal back-aanvragen | Count | HttpStatus</br>HttpStatusGroup</br>Back-end | Het aantal aanvragen dat van de voor deur naar de back-end wordt verzonden. |
| BackendRequestLatency | Latentie van back-upaanvraag | Milliseconden | Back-end | De tijd die wordt berekend vanaf het moment dat de aanvraag door de voor deur naar de back-end is verzonden tot de voor deur de laatste reactie byte van de back-end heeft ontvangen. |
| BackendHealthPercentage | Back-status percentage | Percent | Back-end</br>BackendPool | Het percentage geslaagde status tests van front-deur naar back-end. |
| WebApplicationFirewallRequestCount | Aantal aanvragen voor Web Application firewall | Count | policyName</br>RuleName</br>Action | Het aantal client aanvragen dat is verwerkt door de beveiligingslaag van de toepassingslaag. |

## <a name="activity-log"></a>Activiteiten logboeken

Activiteiten logboeken bevatten informatie over de bewerkingen die worden uitgevoerd op de voor deur van de service. Ze bepalen ook de What, wie en wanneer voor schrijf bewerkingen (put, post of Delete) die op de voor deur worden uitgevoerd.

>[!NOTE]
>Activiteiten logboeken bevatten geen lees bewerkingen (Get). Ze bevatten ook geen bewerkingen die u uitvoert met behulp van de Azure Portal of de oorspronkelijke beheer-API.

U krijgt toegang tot activiteiten Logboeken in uw front-deur service of alle logboeken van uw Azure-resources in Azure Monitor. Activiteitenlogboeken weergeven:

1. Selecteer uw voor deur-exemplaar.
2. Selecteer **activiteiten logboek**.

    ![Activiteitenlogboek](./media/front-door-diagnostics/activity-log.png)

3. Kies een filter bereik en selecteer vervolgens **Toep assen**.

## <a name="diagnostic-logging"></a>Diagnostische logboeken
Diagnostische logboeken bieden uitgebreide informatie over bewerkingen en fouten die belang rijk zijn voor controle en probleem oplossing. Diagnostische logboeken verschillen van activiteiten Logboeken.

Activiteiten logboeken bieden inzicht in de bewerkingen die worden uitgevoerd op Azure-resources. Diagnostische logboeken bieden inzicht in bewerkingen die door uw resource worden uitgevoerd. Zie [Azure monitor Diagnostische logboeken](../azure-monitor/platform/resource-logs-overview.md)voor meer informatie.

![Diagnostische logboeken](./media/front-door-diagnostics/diagnostic-log.png)

Diagnostische logboeken configureren voor de voor deur-service:

1. Selecteer uw Azure front-deur service.

2. Kies **Diagnostische instellingen**.

3. Selecteer **diagnostische gegevens inschakelen**. Archiveer Diagnostische logboeken Samen met metrische gegevens naar een opslag account, stream ze naar een Event Hub of stuur ze naar Azure Monitor-Logboeken.

De voor deur-service biedt momenteel Diagnostische logboeken (batched per uur). Diagnostische logboeken bieden afzonderlijke API-aanvragen voor elke vermelding met het volgende schema:

| Eigenschap  | Description |
| ------------- | ------------- |
| ClientIp | Het IP-adres van de client die de aanvraag heeft ingediend. |
| ClientPort | De IP-poort van de client die de aanvraag heeft ingediend. |
| httpMethod | De HTTP-methode die door de aanvraag wordt gebruikt. |
| Http status code | De HTTP-status code die is geretourneerd door de proxy. |
| HttpStatusDetails | De resulterende status van de aanvraag. De betekenis van deze teken reeks waarde is te vinden in een status verwijzings tabel. |
| httpVersion | Het type van de aanvraag of verbinding. |
| RequestBytes | De grootte van het HTTP-aanvraag bericht in bytes, met inbegrip van de aanvraag headers en de hoofd tekst van de aanvraag. |
| RequestUri | De URI van de ontvangen aanvraag. |
| ResponseBytes | Bytes dat als reactie door de back-endserver wordt verzonden.  |
| RoutingRuleName | De naam van de routerings regel die de aanvraag heeft gevonden. |
| SecurityProtocol | De TLS/SSL-protocol versie die wordt gebruikt door de aanvraag of null als er geen versleuteling is. |
| TimeTaken | De duur van de actie in milliseconden. |
| User agent | Het browser type dat door de client wordt gebruikt |
| TrackingReference | De unieke verwijzings reeks die een aanvraag identificeert die wordt geleverd door de voor deur, ook verzonden als X-Azure-ref-header naar de client. Vereist voor het zoeken naar details in de logboeken van de toegang voor een specifieke aanvraag. |

## <a name="next-steps"></a>Volgende stappen

- [Een voor deur profiel maken](quickstart-create-front-door.md)
- [De werking van de voor deur](front-door-routing-architecture.md)
