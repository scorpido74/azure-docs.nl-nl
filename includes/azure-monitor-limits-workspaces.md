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
ms.openlocfilehash: 5d0c43fbcc1c59c3281f412aad96a3942a5c79b1
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/18/2019
ms.locfileid: "70392938"
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

| Categorie | Limieten | Opmerkingen |
|:---|:---|:---|
| Maximum aantal records geretourneerd door een logboek query | 10.000 | Verminder de resultaten met behulp van het query bereik, het tijds bereik en filters in de query. |


**Data Collector-API**

| Categorie | Limieten | Opmerkingen |
|:---|:---|:---|
| Maximum grootte voor één bericht | 30 MB | Grotere volumes in meerdere berichten splitsen. |
| Maximale grootte voor veld waarden  | 32 KB | Velden die langer zijn dan 32 KB worden afgebroken. |

**Zoek-API**

| Categorie | Limieten | Opmerkingen |
|:---|:---|:---|
| Maximum aantal records dat wordt geretourneerd in één query | 500,000 | |
| Maximale grootte van geretourneerde gegevens | 64.000.000 bytes (~ 61 MiB)| |
| Maximale uitvoerings tijd van de query | 10 minuten | Zie [time-outs](https://dev.loganalytics.io/documentation/Using-the-API/Timeouts) voor meer informatie.  |
| Maximum aantal aanvragen | 200 aanvragen per 30 seconden per AAD-gebruiker of IP-adres van de client | Zie [frequentie limieten](https://dev.loganalytics.io/documentation/Using-the-API/Limits) voor meer informatie. |

**Algemene limieten voor werk ruimten**

| Categorie | Limieten | Opmerkingen |
|:---|:---|:---|
| Maximum aantal kolommen in een tabel         | 500 | |
| Maximum aantal tekens voor de kolom naam | 500 | |
| Regio's op capaciteit | US - west-centraal | U kunt momenteel geen nieuwe werk ruimte maken in deze regio, omdat deze de limiet voor tijdelijke capaciteit heeft. Deze limiet is gepland voor eind oktober 2019. |
| Gegevensexport | Momenteel niet beschikbaar | Gebruik Azure function-of Logic-app om gegevens samen te voegen en te exporteren. | 

>[!NOTE]
>Afhankelijk van hoe lang u Log Analytics hebt gebruikt, hebt u mogelijk toegang tot verouderde prijs categorieën. Meer informatie over [log Analytics verouderde prijs categorieën](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#legacy-pricing-tiers). 