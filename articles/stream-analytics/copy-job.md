---
title: Azure Stream Analytics-taken kopiëren of een back-up maken
description: In dit artikel wordt beschreven hoe u een Azure Stream Analytics-taak kopieert of een back-up maakt.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 09/11/2019
ms.openlocfilehash: ba63358347cf9722d2cafa35598b9b3b37f49dc3
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93129453"
---
# <a name="copy-or-back-up-azure-stream-analytics-jobs"></a>Azure Stream Analytics-taken kopiëren of een back-up maken

U kunt uw geïmplementeerde Azure Stream Analytics-taken kopiëren of een back-up maken met Visual Studio code of Visual Studio. Als u een taak naar een andere regio kopieert, wordt de laatste uitvoer tijd niet gekopieerd. Daarom kunt u bij het starten van de gekopieerde taak niet gebruiken [**wanneer de laatste keer is gestopt**](./start-job.md#start-options) .

## <a name="before-you-begin"></a>Voordat u begint
* Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) aan.

* Meld u aan bij de [Azure-portal](https://portal.azure.com/).

* Installeer [Azure stream Analytics-extensie voor Visual Studio code](quick-create-visual-studio-code.md#install-the-azure-stream-analytics-tools-extension) of [Azure stream Analytics tools voor Visual Studio](quick-create-visual-studio-code.md#install-the-azure-stream-analytics-tools-extension).  

## <a name="visual-studio-code"></a>Visual Studio Code

1. Klik op het pictogram van **Azure** op de activiteiten balk van Visual Studio en vouw vervolgens **Stream Analytics** knoop punt uit. Uw taken moeten worden weer gegeven onder uw abonnementen.

   ![Stream Analytics Explorer openen](./media/vscode-explore-jobs/open-explorer.png)

2. Als u een taak wilt exporteren naar een lokaal project, zoekt u de taak die u wilt exporteren in de **Stream Analytics Explorer** in Visual Studio code. Selecteer vervolgens een map voor uw project.

    ![De ASA-taak in Visual Studio code zoeken](./media/vscode-explore-jobs/export-job.png)

    Het project wordt geëxporteerd naar de map die u hebt geselecteerd en toegevoegd aan uw huidige werk ruimte.

    ![De taak ASA exporteren in Visual Studio code](./media/stream-analytics-manage-job/copy-backup-stream-analytics-jobs.png)

3. Als u de taak wilt publiceren naar een andere regio of een back-up met een andere naam, selecteert u in **uw abonnementen selecteren om** deze te publiceren in de query-editor ( \* . asaql) en volgt u de instructies.

    ![Publiceren naar Azure in Visual Studio code](./media/quick-create-visual-studio-code/submit-job.png)

## <a name="visual-studio"></a>Visual Studio

1. Volg de [taak een geïmplementeerde Azure stream Analytics exporteren naar een project instructies](./stream-analytics-vs-tools.md#export-jobs-to-a-project).

2. Open het \* . asaql-bestand in de query-editor, selecteer **verzenden naar Azure** in de script editor en volg de instructies voor het publiceren van de taak naar een andere regio of een back-up met een nieuwe naam.

## <a name="next-steps"></a>Volgende stappen

* [Snelstartgids: een Stream Analytics-taak maken met behulp van Visual Studio code](quick-create-visual-studio-code.md)
* [Snelstartgids: een Stream Analytics-taak maken met behulp van Visual Studio](stream-analytics-quick-create-vs.md)
* [Een Azure Stream Analytics-taak met CI/CD implementeren met behulp van Azure Pipelines](stream-analytics-tools-visual-studio-cicd-vsts.md)