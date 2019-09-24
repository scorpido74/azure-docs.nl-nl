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
ms.openlocfilehash: 58a741b369231a353a6b8e282a6e604a63a5727d
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/24/2019
ms.locfileid: "71210197"
---
**Volume en retentie van gegevens verzameling** 

| Laag | Limiet per dag | Bewaartijd van gegevens | Opmerking |
|:---|:---|:---|:---|
| Prijs categorie huidige per GB<br>(geïntroduceerd in april 2018) | Geen limiet | 30-730 dagen | Het bewaren van gegevens is meer dan 31 dagen beschikbaar voor extra kosten. Meer informatie over Azure Monitor prijzen. |
| Verouderde gratis lagen<br>(geïntroduceerd in april 2016) | 500 MB | 7 dagen | Als uw werk ruimte de limiet van 500 MB per dag bereikt, worden gegevens opname gestopt en hervat aan het begin van de volgende dag. Een dag is gebaseerd op UTC. Houd er rekening mee dat de gegevens die worden verzameld door Azure Security Center, niet zijn opgenomen in de limiet van 500 MB per dag en nog steeds worden verzameld boven deze limiet.  |
| Verouderde standalone per GB-laag<br>(geïntroduceerd in april 2016) | Geen limiet | 30 tot 730 dagen | Het bewaren van gegevens is meer dan 31 dagen beschikbaar voor extra kosten. Meer informatie over Azure Monitor prijzen. |
| Verouderd per knoop punt (OMS)<br>(geïntroduceerd in april 2016) | Geen limiet | 30 tot 730 dagen | Het bewaren van gegevens is meer dan 31 dagen beschikbaar voor extra kosten. Meer informatie over Azure Monitor prijzen. |
| Verouderde Standard-laag | Geen limiet | 30 dagen  | Bewaren kan niet worden aangepast |
| Verouderde Premium-laag | Geen limiet | 365 dagen  | Bewaren kan niet worden aangepast |

**Aantal werk ruimten per abonnement.**

| Prijscategorie    | Limiet van de werk ruimte | Opmerkingen
|:---|:---|:---|
| Free-laag  | 10 | Deze limiet kan niet worden verhoogd. |
| Alle andere lagen | Geen limiet | U bent beperkt tot het aantal resources in een resource groep en het aantal resource groepen per abonnement. |

**Azure-portal**

| Category | Limieten | Opmerkingen |
|:---|:---|:---|
| Maximum aantal records geretourneerd door een logboek query | 10.000 | Verminder de resultaten met behulp van het query bereik, het tijds bereik en filters in de query. |


**Data Collector-API**

| Category | Limieten | Opmerkingen |
|:---|:---|:---|
| Maximum grootte voor één bericht | 30 MB | Grotere volumes in meerdere berichten splitsen. |
| Maximale grootte voor veld waarden  | 32 KB | Velden die langer zijn dan 32 KB worden afgebroken. |

**Zoek-API**

| Category | Limieten | Opmerkingen |
|:---|:---|:---|
| Maximum aantal records dat wordt geretourneerd in één query | 500,000 | |
| Maximale grootte van geretourneerde gegevens | 64.000.000 bytes (~ 61 MiB)| |
| Maximale uitvoerings tijd van de query | 10 minuten | Zie [time-outs](https://dev.loganalytics.io/documentation/Using-the-API/Timeouts) voor meer informatie.  |
| Maximum aantal aanvragen | 200 aanvragen per 30 seconden per AAD-gebruiker of IP-adres van de client | Zie [frequentie limieten](https://dev.loganalytics.io/documentation/Using-the-API/Limits) voor meer informatie. |

**Algemene limieten voor werk ruimten**

| Category | Limieten | Opmerkingen |
|:---|:---|:---|
| Maximum aantal kolommen in een tabel         | 500 | |
| Maximum aantal tekens voor de kolom naam | 500 | |
| Regio's op capaciteit | US - west-centraal | U kunt momenteel geen nieuwe werk ruimte maken in deze regio, omdat deze de limiet voor tijdelijke capaciteit heeft. Deze limiet is gepland voor eind oktober 2019. |
| Gegevensexport | Momenteel niet beschikbaar | Gebruik Azure function-of Logic-app om gegevens samen te voegen en te exporteren. | 

**Gegevens opname frequentie**

Azure Monitor is een grootschalige gegevens service waarmee duizenden klanten elke maand terabytes aan gegevens verzenden in een groei tempo. De standaard drempel voor opname snelheden is ingesteld op **500 MB/min** per werk ruimte. Als u gegevens met een hoger snelheid naar één werk ruimte verzendt, worden sommige gegevens verwijderd en wordt er om de 6 uur een gebeurtenis verzonden naar de *bewerkings* tabel in uw werk ruimte, terwijl de drempel waarde blijft overschreden. Als uw opname volume de frequentie limiet blijft overschrijden of als u verwacht dat deze kort te bereiken, kunt u een verhoging van uw werk ruimte aanvragen door een ondersteunings aanvraag te openen.
 
Als u een melding wilt ontvangen over een dergelijke gebeurtenis in uw werk ruimte, maakt u een [waarschuwings regel](../articles/azure-monitor/platform/alerts-log.md) voor het logboek met behulp van de volgende query met de logica van een waarschuwing op basis van het aantal resultaten dat is gelukt dan nul.

``` Kusto
Operation
|where OperationCategory == "Ingestion"
|where Detail startswith "The rate of data crossed the threshold"
``` 


>[!NOTE]
>Afhankelijk van hoe lang u Log Analytics hebt gebruikt, hebt u mogelijk toegang tot verouderde prijs categorieën. Meer informatie over [log Analytics verouderde prijs categorieën](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#legacy-pricing-tiers). 