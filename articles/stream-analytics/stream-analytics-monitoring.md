---
title: Inzicht in de taak bewaken in Azure Stream Analytics
description: In dit artikel wordt beschreven hoe u Azure Stream Analytics-taken in Azure portal controleren.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2018
ms.custom: seodec18
ms.openlocfilehash: 9432b43498cd48352427bf1ec2709a2d0f13a797
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70172765"
---
# <a name="understand-stream-analytics-job-monitoring-and-how-to-monitor-queries"></a>Informatie over Stream Analytics-taak controleren en bewaken van query 's

## <a name="introduction-the-monitor-page"></a>Inleiding De pagina monitor
De belangrijkste prestatie gegevens voor de Azure Portal-Opper vlakken die kunnen worden gebruikt voor het bewaken en oplossen van de prestaties van query's en taken. Als u wilt deze metrische gegevens zien, bladert u naar de Stream Analytics-taak u geïnteresseerd bent in de metrische gegevens voor zien en bekijk de **bewaking** sectie op de pagina overzicht.  

![Stream Analytics-taak bewaking van koppeling](./media/stream-analytics-monitoring/02-stream-analytics-monitoring-block.png)

Het venster wordt weergegeven, zoals wordt weergegeven:

![Stream Analytics-taak bewaking dashboard](./media/stream-analytics-monitoring/01-stream-analytics-monitoring.png)  

## <a name="metrics-available-for-stream-analytics"></a>Metrische gegevens beschikbaar voor Stream Analytics
| Gegevens                 | Definitie                               |
| ---------------------- | ---------------------------------------- |
| Invoervelden met achterstand       | Het aantal invoer gebeurtenissen die zijn nog moeten worden uitgevoerd. Een waarde die niet gelijk is aan nul voor deze metriek houdt in dat uw taak het aantal binnenkomende gebeurtenissen niet kan blijven gebruiken. Als deze waarde langzaam toeneemt of constant niet-nul is, moet u uw taak uitschalen. U kunt meer informatie vinden door te kijken naar [streaming-eenheden begrijpen en aanpassen](stream-analytics-streaming-unit-consumption.md). |
| Gegevensconversiefouten | Het aantal uitvoergebeurtenissen dat kan niet worden geconverteerd naar het schema van de verwachte uitvoer. Fout beleid kan worden gewijzigd in drop om gebeurtenissen te verwijderen die in dit scenario optreden. |
| Vroege-invoergebeurtenissen       | Gebeurtenissen waarvan het tijds tempel van de toepassing voor meer dan vijf minuten eerder is dan de aankomst tijd. |
| Mislukte functieaanvragen | Aantal mislukte functie van Azure Machine Learning-aanroepen (indien aanwezig). |
| Functiegebeurtenissen        | Aantal gebeurtenissen verzonden naar de Azure Machine Learning-functie (indien aanwezig). |
| Functieaanvragen      | Het aantal aanroepen naar de Azure Machine Learning-functie (indien aanwezig). |
| Fouten in invoerdeserialisatie       | Aantal invoer gebeurtenissen dat niet kan worden gedeserialiseerd.  |
| Invoergebeurtenisbytes      | Hoeveelheid gegevens die zijn ontvangen door de Stream Analytics-taak gemaakt in bytes. Dit kan worden gebruikt om te valideren dat gebeurtenissen worden verzonden naar de invoerbron. |
| Invoergebeurtenissen           | Het aantal records dat uit de invoer gebeurtenissen is gedeserialiseerd. Dit aantal bevat geen binnenkomende gebeurtenissen die leiden tot fouten bij deserialisatie. |
| Ontvangen invoerbronnen       | Het aantal berichten dat is ontvangen door de taak. Voor Event hub is een bericht één Event Data. Voor blob is een bericht één blob. Houd er rekening mee dat invoer bronnen worden geteld vóór deserialisatie. Als er fouten zijn opgetreden bij de serialisatie, kunnen invoer bronnen groter zijn dan invoer gebeurtenissen. Anders kan het kleiner zijn dan of gelijk zijn aan invoer gebeurtenissen, omdat elk bericht meerdere gebeurtenissen kan bevatten. |
| Late invoergebeurtenissen      | Gebeurtenissen die later zijn aangekomen dan het geconfigureerde tolerantie venster voor late aankomst. Meer informatie over het [Azure stream Analytics van overwegingen voor gebeurtenis orders](stream-analytics-out-of-order-and-late-events.md) . |
| Out-van-Order gebeurtenissen    | Het aantal gebeurtenissen ontvangen buiten de volgorde die zijn verwijderd of een gecorrigeerde timestamp, op basis van het beleid voor het bestellen van gebeurtenis gegeven. Dit kan worden beïnvloed door de configuratie van de instelling van de kant van tolerantie venster. |
| Uitvoergebeurtenis          | De hoeveelheid gegevens die door de Stream Analytics-taak wordt verzonden naar de bestemming voor uitvoer, in aantal gebeurtenissen. |
| Runtimefouten         | Totaal aantal fouten met betrekking tot de verwerking van query's (zonder fouten gevonden tijdens het opnemen van gebeurtenissen of het uitvoeren van resultaten) |
| Gebruikspercentage voor Streaming-eenheden       | Het gebruik van de Streaming-eenheid/eenheden toegewezen aan een taak op het tabblad schalen van de taak. Moet deze indicator 80% bereikt of hierboven, is zeer waarschijnlijk dat de verwerking van gebeurtenissen kan worden vertraagd of geen voortgang meer maakt. |
| Watermerkvertraging       | De maximale watermerk vertraging voor alle partities van alle uitvoer van de taak. |

U kunt deze metrische gegevens gebruiken om [de prestaties van uw stream Analytics-taak](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor)te bewaken. 

## <a name="customizing-monitoring-in-the-azure-portal"></a>Bewaking aanpassen in Azure portal
U kunt het type van de grafiek, metrische gegevens die worden weergegeven, aanpassen en tijdsbereik in de instellingen van de grafiek bewerken. Zie voor meer informatie, [over het aanpassen van bewaking](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md).

  ![Stream Analytics query uitvoeren op het diagram met de monitor](./media/stream-analytics-monitoring/08-stream-analytics-monitoring.png)  


## <a name="latest-output"></a>Laatste uitvoer
Een andere interessante gegevenspunt voor het bewaken van uw taak is de tijd van de laatste uitvoer, die wordt weergegeven op de pagina overzicht.
Dit is de tijd van de toepassing (dat wil zeggen de tijd met behulp van de tijdstempel van de gebeurtenisgegevens) van de meest recente uitvoer van uw taak.

## <a name="get-help"></a>Help opvragen
Voor verdere hulp kunt u mogelijk terecht op het [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Volgende stappen
* [Inleiding tot Azure Stream Analytics](stream-analytics-introduction.md)
* [Aan de slag met Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Azure Stream Analytics-taken schalen](stream-analytics-scale-jobs.md)
* [Naslaggids voor Azure Stream Analytics Query](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [REST API-naslaggids voor Azure Stream Analytics Management](https://msdn.microsoft.com/library/azure/dn835031.aspx)
