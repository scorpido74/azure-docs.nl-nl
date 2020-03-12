---
title: Onbewerkte HTTP-logboeken Azure CDN
description: In dit artikel worden de Azure CDN RAW-Logboeken beschreven.
services: cdn
author: asudbring
manager: KumudD
ms.service: azure-cdn
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2020
ms.author: allensu
ms.openlocfilehash: 79ced4df8df12bf2ef1fbe0075b53d02fafb2aff
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2020
ms.locfileid: "79129823"
---
# <a name="azure-cdn-http-raw-logs"></a>Onbewerkte HTTP-logboeken Azure CDN
Onbewerkte logboeken bieden uitgebreide informatie over bewerkingen en fouten die belang rijk zijn voor controle en probleem oplossing. Onbewerkte logboeken verschillen van activiteiten Logboeken. Met activiteiten logboeken krijgt u inzicht in de bewerkingen die worden uitgevoerd op Azure-resources. Onbewerkte logboeken bieden een overzicht van de bewerkingen van uw resource.

> [!IMPORTANT]
> De functie voor onbewerkte HTTP-Logboeken is beschikbaar voor Azure CDN van micro soft.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint. 

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com).

## <a name="configuration"></a>Configuratie

RAW-logboeken voor uw Azure CDN van het micro soft-profiel configureren: 

1. Selecteer in het menu Azure Portal **alle Resources** >>  **\<uw CDN-Profiel >** .

2. Onder **bewaking**selecteert u **instellingen voor diagnostische gegevens**.

3. Selecteer **+ Diagnostische instelling toevoegen**.

    ![Instelling voor CDN-diagnose](./media/cdn-raw-logs/raw-logs-01.png)

    > [!IMPORTANT]
    > Onbewerkte logboeken zijn alleen beschikbaar in het profiel niveau, terwijl geaggregeerde HTTP-status code logboeken beschikbaar zijn op het niveau van het eind punt.

4. Voer onder **Diagnostische instellingen**een naam in voor de diagnostische instelling onder **naam van diagnostische instellingen**.

5. Selecteer het **logboek** en stel de Bewaar periode in dagen in.

6. Selecteer de **doel gegevens**. Bestemmings opties zijn:
    * **Verzenden naar Log Analytics**
        * Selecteer het **abonnement** en de **log Analytics-werk ruimte**.
    * **Archiveren naar een opslag account**
        * Selecteer het **abonnement** en het **opslag account**.
    * **Streamen naar een Event Hub**
        * Selecteer het **abonnement**, de **Event hub-naam ruimte**, de naam van de **Event hub (optioneel)** en de naam van het **Event hub-beleid**.

    ![Instelling voor CDN-diagnose](./media/cdn-raw-logs/raw-logs-02.png)

7. Selecteer **Opslaan**.

## <a name="raw-logs-properties"></a>Eigenschappen van onbewerkte logboeken

Azure CDN van de micro soft-service biedt momenteel onbewerkte Logboeken (batched per uur). RAW-logboeken bieden afzonderlijke API-aanvragen voor elke vermelding met het volgende schema: 

| Eigenschap              | Beschrijving                                                                                                                                                                                          |
|-----------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| TrackingReference     | De unieke verwijzings reeks die een aanvraag identificeert die wordt geleverd door de voor deur, ook verzonden als X-Azure-ref-header naar de client. Vereist voor het zoeken naar details in de logboeken van de toegang voor een specifieke aanvraag. |
| httpMethod            | De HTTP-methode die door de aanvraag wordt gebruikt.                                                                                                                                                                     |
| HttpVersion           | Het type van de aanvraag of verbinding.                                                                                                                                                                   |
| RequestUri            | De URI van de ontvangen aanvraag.                                                                                                                                                                         |
| RequestBytes          | De grootte van het HTTP-aanvraag bericht in bytes, met inbegrip van de aanvraag headers en de hoofd tekst van de aanvraag.                                                                                                   |
| ResponseBytes         | Bytes dat als reactie door de back-endserver wordt verzonden.                                                                                                                                                    |
| User agent             | Het browser type dat door de client wordt gebruikt.                                                                                                                                                               |
| ClientIp              | Het IP-adres van de client die de aanvraag heeft ingediend.                                                                                                                                                  |
| TimeTaken             | De duur van de actie in milliseconden.                                                                                                                                            |
| SecurityProtocol      | De TLS/SSL-protocol versie die wordt gebruikt door de aanvraag of null als er geen versleuteling is.                                                                                                                           |
| Eindpunt              | De CDN-eindpunt-host is geconfigureerd onder het bovenliggende CDN-profiel.                                                                                                                                   |
| Hostnaam van back-end     | De naam van de back-end-host of de oorsprong waar aanvragen worden verzonden.                                                                                                                                |
| Verzonden naar het oorspronkelijke schild | Als dit het geval is, betekent dit dat de aanvraag is beantwoord van de oorspronkelijke schild in de cache in plaats van de Edge-pop. Schild van oorsprong is een bovenliggende cache die wordt gebruikt om de verhouding van de cache treffers te verbeteren.                                       |
| Http status code        | De HTTP-status code die is geretourneerd door de proxy.                                                                                                                                                        |
| HttpStatusDetails     | De resulterende status van de aanvraag. De betekenis van deze teken reeks waarde is te vinden in een status verwijzings tabel.                                                                                              |
| Keuzemenu                   | De rand pop, die op de aanvraag van de gebruiker reageerde. De afkortingen van Pop's zijn luchthaven codes van hun respectieve metro lijnen.                                                                                   |
| Cache status          | Geeft aan of het object is geretourneerd uit de cache of afkomstig is van de oorsprong.                                                                                                             |

## <a name="next-steps"></a>Volgende stappen
In dit artikel hebt u HTTP RAW-logboeken ingeschakeld voor de micro soft CDN-service.

Zie voor meer informatie over Azure CDN en de andere Azure-Services die in dit artikel worden genoemd:

* [Analyseren](cdn-log-analysis.md) Gebruiks patronen Azure CDN.

* Meer informatie over [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/overview).

* [Log Analytics configureren in azure monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal).