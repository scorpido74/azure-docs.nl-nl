---
title: Azure Stream Analytics-taken weergeven in Visual Studio
description: Meer informatie over het weergeven, starten en stoppen, testen van verbindingen, het controleren van resultaten en het exporteren van uw Azure Stream Analytics-taken met Visual Studio.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 07/10/2018
ms.openlocfilehash: ad35ed342dfd40b98d61919749479ec5612cdd4f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75369623"
---
# <a name="use-visual-studio-to-view-azure-stream-analytics-jobs"></a>Visual Studio gebruiken om Azure Stream Analytics-taken weer te geven

Azure Stream Analytics-hulpprogramma's voor Visual Studio maken het voor ontwikkelaars gemakkelijk om hun Stream Analytics-taken rechtstreeks vanuit de IDE te beheren. Met Azure Stream Analytics-hulpprogramma's u het als nog stellen:
- [Nieuwe banen creëren](stream-analytics-quick-create-vs.md)
- [Taken starten,](stream-analytics-monitor-jobs-use-vs.md) stoppen en controleren
- Taakresultaten controleren
- Bestaande taken exporteren naar een project
- Invoer- en uitvoerverbindingen testen
- [Query's lokaal uitvoeren](stream-analytics-vs-tools-local-run.md)

Meer informatie over het [installeren van Azure Stream Analytics-hulpprogramma's voor Visual Studio](stream-analytics-tools-for-visual-studio-install.md).

## <a name="explore-the-job-view"></a>Bekijk de taakweergave

U de taakweergave gebruiken om te communiceren met Azure Stream Analytics-taken vanuit Visual Studio.

### <a name="open-the-job-view"></a>De taakweergave openen

1. Selecteer in **Server Explorer** **Stream Analytics-taken** en selecteer **Vernieuwen**. Uw taak moet worden weergegeven onder **Stream Analytics-taken**.

    ![Lijst met Stream Analytics-serververkenners](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-list-jobs-01.png)

2. Vouw uw taakknooppunt uit en dubbelklik op het knooppunt **Taakweergave** om een taakweergave te openen.
    
   ![Uitgebreid taakknooppunt](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-job-view-01.png)

### <a name="start-and-stop-jobs"></a>Taken starten en stoppen

Azure Stream Analytics-taken kunnen volledig worden beheerd vanuit de taakweergave in Visual Studio. Gebruik de besturingselementen om een taak te starten, te stoppen of te verwijderen.
    
   ![Werkbesturingselementen voor Stream Analytics](./media/stream-analytics-vs-tools/azure-stream-analytics-job-view-controls.png)

## <a name="check-job-results"></a>Taakresultaten controleren

Stream Analytics-hulpprogramma's voor Visual Studio ondersteunen momenteel uitvoervoorbeeld voor Azure Data Lake Storage en blob-opslag. Als u het resultaat wilt weergeven, dubbelklikt u op het uitvoerknooppunt van het taakdiagram in **taakweergave** en voert u de juiste referenties in.

   ![Stream Analytics-taakblob-uitvoer](./media/stream-analytics-vs-tools/stream-analytics-blob-preview.png)

## <a name="export-jobs-to-a-project"></a>Taken exporteren naar een project

Er zijn twee manieren waarop u een bestaande taak naar een project exporteren.

1. Klik in **Server Explorer**onder het knooppunt Vacatures van Stream Analytics met de rechtermuisknop op het taakknooppunt. Selecteer **Exporteren naar een nieuw streamanalyticsproject**.
    
   ![Taak exporteren naar project](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-export-job-01.png)
    
    Het gegenereerde project wordt weergegeven in **Solution Explorer**.
    
   ![Solution Explorer](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-export-job-02.png)

2. Selecteer **Project genereren**in de taakweergave .
    
   ![Project genereren vanuit taakweergave](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-export-job-03.png)

## <a name="test-connections"></a>Testverbindingen

Invoer- en uitvoerverbindingen kunnen worden getest vanuit de **taakweergave** door een optie te selecteren in de vervolgkeuzelijst **Testverbinding.**

   ![Vervolgkeuzelijst Verbinding testen](./media/stream-analytics-vs-tools/stream-analytics-test-connection-dropdown.png)

De resultaten **van de testverbinding** worden weergegeven in het venster **Uitvoer.**

   ![Verbindingsresultaten testen](./media/stream-analytics-vs-tools/stream-analytics-test-connection-results.png)

## <a name="next-steps"></a>Volgende stappen

* [Azure Stream Analytics-taken bewaken en beheren met Visual Studio](stream-analytics-monitor-jobs-use-vs.md)
* [Snelstart: een functie Stream Analytics maken met Visual Studio](stream-analytics-quick-create-vs.md)
* [Zelfstudie: Een Azure Stream Analytics-taak met CI/CD implementeren met behulp van Azure Pipelines](stream-analytics-tools-visual-studio-cicd-vsts.md)
* [Continue integratie en ontwikkeling met Stream Analytics-hulpprogramma’s](stream-analytics-tools-for-visual-studio-cicd.md)
