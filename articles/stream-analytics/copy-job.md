---
title: Azure Stream Analytics-taken kopiëren of een back-up maken
description: In dit artikel wordt beschreven hoe u een Azure Stream Analytics-taak kopiëren of een back-up maken.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: 5c8f770855dd8d19a9d313f1b79f9bf8da4b2393
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75771492"
---
# <a name="copy-or-back-up-azure-stream-analytics-jobs"></a>Azure Stream Analytics-taken kopiëren of een back-up maken

U uw geïmplementeerde Azure Stream Analytics-taken kopiëren of een back-up maken met Visual Studio Code of Visual Studio. 

## <a name="before-you-begin"></a>Voordat u begint
* Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/)aan.

* Meld u aan bij [Azure Portal](https://portal.azure.com/).

* Installeer [Azure Stream Analytics-extensie voor Visual Studio Code](quick-create-vs-code.md#install-the-azure-stream-analytics-tools-extension) of Azure Stream [Analytics-hulpprogramma's voor Visual Studio.](quick-create-vs-code.md#install-the-azure-stream-analytics-tools-extension)  

## <a name="visual-studio-code"></a>Visual Studio Code

1. Klik op het **Azure-pictogram** op de activiteitsbalk van de Visual Studio-code en vouw vervolgens het knooppunt **Stream Analytics** uit. Uw vacatures moeten onder uw abonnementen worden weergegeven.

   ![Open Stream Analytics Explorer](./media/vscode-explore-jobs/open-explorer.png)

2. Als u een taak wilt exporteren naar een lokaal project, zoekt u de taak die u wilt exporteren in de **Stream Analytics Explorer** in Visual Studio Code. Selecteer vervolgens een map voor uw project.

    ![ASA-taak exporteren in Visual Studio Code](./media/vscode-explore-jobs/export-job.png)

    Het project wordt geëxporteerd naar de map die u selecteert en aan uw huidige werkruimte wordt toegevoegd.

    ![ASA-taak exporteren in Visual Studio Code](./media/stream-analytics-manage-job/copy-backup-stream-analytics-jobs.png)

3. Als u de taak wilt publiceren naar een andere regio of een back-up met een andere naam, selecteert u **Selecteren uit uw abonnementen om te publiceren** in de queryeditor (\*.asaql) en volgt u de instructies.

    ![Publiceren naar Azure in Visual Studio-code](./media/quick-create-vs-code/submit-job.png)

## <a name="visual-studio"></a>Visual Studio

1. Volg de [taak Een geïmplementeerde Azure Stream Analytics-taak exporteren naar een projectinstructies](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-vs-tools#export-jobs-to-a-project).

2. Open \*het asaql-bestand in de queryeditor, selecteer **Verzenden naar Azure** in de scripteditor en volg de instructies om de taak naar een andere regio of back-up te publiceren met een nieuwe naam.

## <a name="next-steps"></a>Volgende stappen

* [Snelstart: een functie Stream Analytics maken met Visual Studio Code](quick-create-vs-code.md)
* [Snelstart: een functie Stream Analytics maken met Visual Studio](stream-analytics-quick-create-vs.md)
* [Een Azure Stream Analytics-taak met CI/CD implementeren met behulp van Azure Pipelines](stream-analytics-tools-visual-studio-cicd-vsts.md)
