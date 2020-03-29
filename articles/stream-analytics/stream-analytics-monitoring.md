---
title: Inzicht in taakbewaking in Azure Stream Analytics
description: In dit artikel wordt beschreven hoe u Azure Stream Analytics-taken in de Azure-portal controleren.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2018
ms.custom: seodec18
ms.openlocfilehash: 4e9f90035816269d2d41781be34d0d8080628b12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75431657"
---
# <a name="understand-stream-analytics-job-monitoring-and-how-to-monitor-queries"></a>Inzicht in de taakbewaking van Stream Analytics en hoe u query's controleren

## <a name="introduction-the-monitor-page"></a>Inleiding: de monitorpagina
De Azure-portal geeft belangrijke prestatiestatistieken weer die kunnen worden gebruikt om uw query- en taakprestaties te bewaken en op te lossen. Als u deze statistieken wilt bekijken, bladert u naar de streamanalytics-taak waarvoor u wilt statistieken bekijken en bekijkt u de sectie **Controle** op de pagina Overzicht.  

![Koppeling naar taakbewaking van Stream Analytics](./media/stream-analytics-monitoring/02-stream-analytics-monitoring-block.png)

Het venster wordt weergegeven zoals weergegeven:

![Dashboard voor taakbewaking van Stream Analytics](./media/stream-analytics-monitoring/01-stream-analytics-monitoring.png)  

## <a name="metrics-available-for-stream-analytics"></a>Statistieken beschikbaar voor Stream Analytics
| Gegevens                 | Definitie                               |
| ---------------------- | ---------------------------------------- |
| Backlogged Invoergebeurtenissen       | Aantal invoergebeurtenissen dat is geback-verstopte. Een niet-nulwaarde voor deze statistiek houdt in dat uw taak het aantal binnenkomende gebeurtenissen niet kan bijhouden. Als deze waarde langzaam toeneemt of consequent niet-nul is, moet u uw taak uitschalen. U meer informatie krijgen door naar [Streaming-eenheden begrijpen en aanpassen.](stream-analytics-streaming-unit-consumption.md) |
| Fouten in gegevensconversie | Aantal uitvoergebeurtenissen dat niet kon worden geconverteerd naar het verwachte uitvoerschema. Foutbeleid kan worden gewijzigd in 'Drop' om gebeurtenissen te laten vallen die dit scenario tegenkomen. |
| Gebeurtenissen voor vroege invoer       | Gebeurtenissen waarvan de toepassingstijdstempel meer dan 5 minuten vdagis dan hun aankomsttijd. |
| Mislukte functieaanvragen | Aantal mislukte Azure Machine Learning-functieaanroepen (indien aanwezig). |
| Functiegebeurtenissen        | Aantal gebeurtenissen dat naar de Azure Machine Learning-functie wordt verzonden (indien aanwezig). |
| Functieaanvragen      | Aantal oproepen naar de Azure Machine Learning-functie (indien aanwezig). |
| Fouten in de deserialisatie van invoer       | Aantal invoergebeurtenissen dat niet kan worden gedeserialiseerd.  |
| Gebeurtenisbytes voor invoer      | Hoeveelheid gegevens die zijn ontvangen door de taak Stream Analytics, in bytes. Dit kan worden gebruikt om te valideren of gebeurtenissen naar de invoerbron worden verzonden. |
| Invoergebeurtenissen           | Aantal records dat is gedeserialiseerd uit de invoergebeurtenissen. Deze telling omvat geen inkomende gebeurtenissen die resulteren in deserialisatiefouten. |
| Ontvangen invoerbronnen       | Aantal berichten dat door de taak is ontvangen. Voor Gebeurtenishub is een bericht één EventData. Voor Blob is een bericht één blob. Houd er rekening mee dat invoerbronnen worden geteld voordat u deserialisatie. Als er deserialisatiefouten zijn, kunnen invoerbronnen groter zijn dan invoergebeurtenissen. Anders kan het minder dan of gelijk zijn aan invoergebeurtenissen, omdat elk bericht meerdere gebeurtenissen kan bevatten. |
| Gebeurtenissen voor late invoer      | Gebeurtenissen die later zijn aangekomen dan het geconfigureerde venster voor late aankomsttolerantie. Meer informatie over overwegingen voor [gebeurtenisvolgorde van Azure Stream Analytics](stream-analytics-out-of-order-and-late-events.md) . |
| Out-of-order-evenementen    | Aantal gebeurtenissen die buiten de orde zijn ontvangen en die zijn verwijderd of een aangepaste tijdstempel hebben gekregen, op basis van het bestelbeleid voor evenementen. Dit kan worden beïnvloed door de configuratie van de instelling Foute tolerantie. |
| Uitvoergebeurtenissen          | Hoeveelheid gegevens die door de taak Stream Analytics naar het uitvoerdoel wordt verzonden, in aantal gebeurtenissen. |
| Runtime-fouten         | Totaal aantal fouten in verband met queryverwerking (met uitzondering van gevonden fouten tijdens het innemen van gebeurtenissen of het uitzetten van resultaten) |
| SU % Gebruik       | Het gebruik van de streamingeenheid(en) die is toegewezen aan een taak via het tabblad Schalen van de taak. Als deze indicator 80% of hoger is, is de kans groot dat de verwerking van gebeurtenissen wordt vertraagd of gestopt met het boeken van vooruitgang. |
| Watermerk vertraging       | De maximale vertraging van het watermerk voor alle partities van alle uitgangen in de taak. |

U deze statistieken gebruiken om [de prestaties van uw Stream Analytics-taak](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor)te controleren. 

## <a name="customizing-monitoring-in-the-azure-portal"></a>Controle aanpassen in de Azure-portal
U het type grafiek, weergegeven statistieken en tijdbereik aanpassen in de instellingen van grafiekbewerken bewerken. Zie Monitoring [aanpassen voor](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md)meer informatie.

  ![Tijdgrafiek van Stream Analytics-querymonitor](./media/stream-analytics-monitoring/08-stream-analytics-monitoring.png)  


## <a name="latest-output"></a>Laatste uitvoer
Een ander interessant gegevenspunt om uw taak te controleren is de tijd van de laatste uitvoer, weergegeven op de pagina Overzicht.
Deze tijd is de toepassingstijd (d.w.z. de tijdstempel van de gebeurtenisgegevens) van de recentste output van uw baan.

## <a name="get-help"></a>Help opvragen
Voor verdere hulp kunt u mogelijk terecht op het [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Volgende stappen
* [Inleiding tot Azure Stream Analytics](stream-analytics-introduction.md)
* [Aan de slag met Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Azure Stream Analytics-taken schalen](stream-analytics-scale-jobs.md)
* [Naslaggids voor Azure Stream Analytics Query](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [REST API-naslaggids voor Azure Stream Analytics Management](https://msdn.microsoft.com/library/azure/dn835031.aspx)
