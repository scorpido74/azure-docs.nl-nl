---
title: Terugkerende taken debuggen in Azure Data Lake Analytics
description: Meer informatie over het gebruik van Azure Data Lake Tools voor Visual Studio om een abnormale terugkerende taak te debuggen.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 05/20/2018
ms.openlocfilehash: 5a2935d559a967151c5bdc01c4b0806fe52179b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60629711"
---
# <a name="troubleshoot-an-abnormal-recurring-job"></a>Problemen met een abnormale terugkerende taak oplossen

In dit artikel ziet u hoe u [Azure Data Lake Tools voor Visual Studio kunt](https://aka.ms/adltoolsvs) gebruiken om problemen met terugkerende taken op te lossen. Meer informatie over pijplijn- en terugkerende taken vindt u in het [Azure Data Lake- en Azure HDInsight-blog](https://blogs.msdn.microsoft.com/azuredatalake/2017/09/19/managing-pipeline-recurring-jobs-in-azure-data-lake-analytics-made-easy/).

Terugkerende taken delen meestal dezelfde querylogica en vergelijkbare invoergegevens. Stel je bijvoorbeeld voor dat je elke maandagochtend om 8.00 uur een terugkerende taak hebt. om de wekelijkse actieve gebruiker van vorige week te tellen. De scripts voor deze taken delen één scriptsjabloon die de querylogica bevat. De ingangen voor deze taken zijn de gebruiksgegevens van vorige week. Het delen van dezelfde querylogica en vergelijkbare invoer betekent meestal dat de prestaties van deze taken vergelijkbaar en stabiel zijn. Als een van uw terugkerende taken plotseling abnormaal presteert, mislukt of veel vertraagt, wilt u misschien:

- Bekijk de statistiekenrapporten voor de vorige uitvoeringen van de terugkerende taak om te zien wat er is gebeurd.
- Vergelijk de abnormale baan met een normale om erachter te komen wat er is veranderd.

**Met de bijbehorende vacatureweergave** in Azure Data Lake Tools voor Visual Studio u de voortgang van het oplossen van problemen met beide aanvragen versnellen.

## <a name="step-1-find-recurring-jobs-and-open-related-job-view"></a>Stap 1: Terugkerende taken zoeken en gerelateerde taakweergave openen

Als u Gerelateerde taakweergave wilt gebruiken om een terugkerend taakprobleem op te lossen, moet u eerst de terugkerende taak in Visual Studio vinden en vervolgens Gerelateerde taakweergave openen.

### <a name="case-1-you-have-the-url-for-the-recurring-job"></a>Case 1: Je hebt de URL voor de terugkerende taak

Via **Tools** > **Data Lake** > **Job View**u de taak-URL plakken om Taakweergave te openen in Visual Studio. Selecteer **Gerelateerde taken weergeven** om gerelateerde taakweergave te openen.

![Koppeling Gerelateerde taken weergeven in Data Lake Analytics-hulpprogramma's](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/view-related-job.png)
 
### <a name="case-2-you-have-the-pipeline-for-the-recurring-job-but-not-the-url"></a>Case 2: Je hebt de pijplijn voor de terugkerende taak, maar niet de URL

In Visual Studio u pipelinebrowser openen via Server Explorer > uw Azure Data Lake Analytics-account > **Pijplijnen.** (Als u dit knooppunt niet vinden in Server Explorer, [downloadt u de nieuwste plug-in](https://aka.ms/adltoolsvs).) 

![Het knooppunt Pijplijnen selecteren](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/pipeline-browser.png)

In Pipeline Browser worden alle pijplijnen voor het Data Lake Analytics-account links weergegeven. U de pijplijnen uitbreiden om alle terugkerende taken te vinden en vervolgens de pijplijn selecteren die problemen heeft. Gerelateerde jobweergave wordt rechts geopend.

![Een pijplijn selecteren en gerelateerde taakweergave openen](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-view.png)

## <a name="step-2-analyze-a-statistics-report"></a>Stap 2: Een statistiekenrapport analyseren

Een samenvatting en een statistiekenrapport worden boven aan de gerelateerde taakweergave weergegeven. Daar vindt u de mogelijke oorzaak van het probleem. 

1.  In het rapport geeft de X-as de tijd voor het indienen van vacatures weer. Gebruik het om de abnormale baan te vinden.
2.  Gebruik het proces in het volgende diagram om statistieken te controleren en inzicht te krijgen in het probleem en de mogelijke oplossingen.

![Procesdiagram voor het controleren van statistieken](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-metrics-debugging-flow.png)

## <a name="step-3-compare-the-abnormal-job-to-a-normal-job"></a>Stap 3: Vergelijk de abnormale baan met een normale baan

U alle ingediende terugkerende taken vinden via de takenlijst onder in gerelateerde taakweergave. Om meer inzichten en mogelijke oplossingen te vinden, klikt u met de rechtermuisknop op de abnormale taak. Gebruik de weergave Taakdiff om de abnormale taak te vergelijken met een vorige normale taak.

![Snelmenu voor het vergelijken van taken](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/compare-job.png)

Let op de grote verschillen tussen deze twee banen. Deze verschillen zijn waarschijnlijk de oorzaak van de prestaties problemen. Als u verder wilt controleren, gebruikt u de stappen in het volgende diagram:

![Procesdiagram voor het controleren van verschillen tussen taken](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-diff-debugging-flow.png)

## <a name="next-steps"></a>Volgende stappen

* [Problemen met gegevensscheeftrekken oplossen](data-lake-analytics-data-lake-tools-data-skew-solutions.md)
* [Foutopsporing door de gebruiker gedefinieerde C#-code voor mislukte U-SQL-taken](data-lake-analytics-debug-u-sql-jobs.md)
