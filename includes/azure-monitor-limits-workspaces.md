---
title: bestand opnemen
description: bestand opnemen
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 02/07/2019
ms.author: robb
ms.custom: include file
ms.openlocfilehash: 97f265d76ac70891e9cefc0ef6651e439706ed23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334846"
---
**Volume en bewaring van gegevensverzameling** 

| Laag | Limiet per dag | Bewaartijd van gegevens | Opmerking |
|:---|:---|:---|:---|
| Huidige prijscategorie per GB<br>(geïntroduceerd in april 2018) | Geen limiet | 30 - 730 dagen | Gegevensbewaring na 31 dagen is beschikbaar voor extra kosten. Meer informatie over azure monitor-prijzen. |
| Legacy Free-lagen<br>(geïntroduceerd in april 2016) | 500 MB | 7 dagen | Wanneer uw werkruimte de limiet van 500 MB per dag bereikt, wordt de opname van gegevens aan het begin van de volgende dag gestopt en hervat. Een dag is gebaseerd op UTC. Houd er rekening mee dat gegevens die door Azure Security Center worden verzameld, niet zijn opgenomen in deze limiet van 500 MB per dag en nog steeds boven deze limiet worden verzameld.  |
| Legacy Standalone per GB-laag<br>(geïntroduceerd in april 2016) | Geen limiet | 30 tot 730 dagen | Gegevensbewaring na 31 dagen is beschikbaar voor extra kosten. Meer informatie over azure monitor-prijzen. |
| Legacy Per Node (OMS)<br>(geïntroduceerd in april 2016) | Geen limiet | 30 tot 730 dagen | Gegevensbewaring na 31 dagen is beschikbaar voor extra kosten. Meer informatie over azure monitor-prijzen. |
| Legacy-standaardlaag | Geen limiet | 30 dagen  | Retentie kan niet worden aangepast |
| Legacy Premium-laag | Geen limiet | 365 dagen  | Retentie kan niet worden aangepast |

**Aantal werkruimten per abonnement.**

| Prijscategorie    | Werkruimtelimiet | Opmerkingen
|:---|:---|:---|
| Gratis laag  | 10 | Deze limiet kan niet worden verhoogd. |
| Alle andere lagen | Geen limiet | U wordt beperkt door het aantal resources binnen een resourcegroep en het aantal resourcegroepen per abonnement. |

**Azure-portal**

| Categorie | Limiet | Opmerkingen |
|:---|:---|:---|
| Maximale records die worden geretourneerd door een logboekquery | 10.000 | Verminder de resultaten met querybereik, tijdbereik en filters in de query. |


**API voor gegevensverzamelaar**

| Categorie | Limiet | Opmerkingen |
|:---|:---|:---|
| Maximale grootte voor één bericht | 30 MB | Splits grotere volumes op in meerdere berichten. |
| Maximale grootte voor veldwaarden  | 32 KB | Velden die langer zijn dan 32 KB worden afgebroken. |

**API voor zoeken**

| Categorie | Limiet | Opmerkingen |
|:---|:---|:---|
| Maximale records geretourneerd in één query | 500.000 | |
| Maximale grootte van geretourneerde gegevens | 64.000.000 bytes (~61 MiB)| |
| Maximale gebruikstijd van query's | 10 minuten | Zie [Time-outs](https://dev.loganalytics.io/documentation/Using-the-API/Timeouts) voor meer informatie.  |
| Maximum aanvraagtarief | 200 aanvragen per 30 seconden per AAD-gebruiker of client IP-adres | Zie [Tarieflimieten](https://dev.loganalytics.io/documentation/Using-the-API/Limits) voor meer informatie. |

**Algemene werkruimtelimieten**

| Categorie | Limiet | Opmerkingen |
|:---|:---|:---|
| Maximale kolommen in een tabel         | 500 | |
| Maximumtekens voor kolomnaam | 500 | |
| Gegevensexport | Momenteel niet beschikbaar | Gebruik de azure-functie of logische app om gegevens te verzamelen en te exporteren. | 

**Volumesnelheid voor gegevensopname**


Azure Monitor is een grootschalige dataservice die duizenden klanten bedient die elke maand terabytes aan gegevens verzenden. De standaardlimiet voor opnamevolumesnelheid voor gegevens die vanuit Azure-bronnen worden verzonden met [diagnostische instellingen](../articles/azure-monitor/platform/diagnostic-settings.md) is ongeveer **6 GB/min** per werkruimte. Dit is een geschatte waarde, omdat de werkelijke grootte kan variëren tussen gegevenstypen, afhankelijk van de loglengte en de compressieverhouding. Deze limiet is niet van toepassing op gegevens die worden verzonden vanuit agents of [Data Collector API.](../articles/azure-monitor/platform/data-collector-api.md)

Als u gegevens met een hoger tarief naar één werkruimte verzendt, worden sommige gegevens verwijderd en wordt elke 6 uur een gebeurtenis naar de *bewerkingstabel* in uw werkruimte verzonden terwijl de drempelwaarde nog steeds wordt overschreden. Als uw opnamevolume de tarieflimiet blijft overschrijden of als u verwacht deze binnenkort te bereiken, u een verhoging van uw werkruimte aanvragen door een ondersteuningsverzoek te openen.
 
Als u een melding wilt ontvangen over een dergelijke gebeurtenis in uw werkruimte, maakt u een [logboekwaarschuwingsregel](../articles/azure-monitor/platform/alerts-log.md) met de volgende query met waarschuwingslogica op basis van het aantal resultatenrasp dan nul.

``` Kusto
Operation
|where OperationCategory == "Ingestion"
|where Detail startswith "The rate of data crossed the threshold"
``` 


>[!NOTE]
>Afhankelijk van hoe lang u Log Analytics al gebruikt, hebt u mogelijk toegang tot oudere prijsniveaus. Meer informatie over [prijsniveaus van Logboekanalyse voor oude prijzen](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#legacy-pricing-tiers). 