---
title: Azure Stream Analytics query's lokaal testen met Visual Studio code (preview)
description: In dit artikel wordt beschreven hoe u query's lokaal test met Azure Stream Analytics-Hulpprogram Ma's voor Visual Studio code.
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 05/15/2019
ms.topic: conceptual
ms.openlocfilehash: 751cdf50fccc654dfab06b4d18428531312d08e6
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/29/2019
ms.locfileid: "71673029"
---
# <a name="test-stream-analytics-queries-locally-with-visual-studio-code"></a>Stream Analytics query's lokaal testen met Visual Studio code

U kunt Azure Stream Analytics-hulpprogram ma's voor Visual Studio code gebruiken om uw Stream Analytics-taken lokaal te testen met voorbeeld gegevens.

Gebruik deze [Quick](quick-create-vs-code.md) start om te leren hoe u met Visual Studio Code een stream Analytics taak maakt.

## <a name="prerequisites"></a>Vereisten
* Installeer de [.net core-SDK](https://dotnet.microsoft.com/download).
* Start Visual Studio code opnieuw.
 
## <a name="run-queries-locally"></a>Query's lokaal uitvoeren

U kunt de Azure Stream Analytics extensie voor Visual Studio code gebruiken om uw Stream Analytics-taken lokaal te testen met voorbeeld gegevens.

1. Wanneer u de Stream Analytics taak hebt gemaakt, gebruikt u **CTRL + SHIFT + P** om het opdracht palet te openen. Vervolgens typt en selecteert u **ASA: Invoer @ no__t-0 toevoegen.

    ![ASA-invoer toevoegen in Visual Studio code](./media/vscode-local-run/add-input.png)

2. Selecteer **lokale invoer**.

    ![Lokale invoer van ASA toevoegen in Visual Studio code](./media/vscode-local-run/add-local-input.png)

3. Selecteer **+ nieuwe lokale invoer**.

    ![Een nieuwe lokale invoer van ASA toevoegen in Visual Studio code](./media/vscode-local-run/add-new-local-input.png)

4. Voer dezelfde invoer alias in die u in uw query hebt gebruikt.

    ![Een nieuwe lokale invoer-alias voor ASA toevoegen](./media/vscode-local-run/new-local-input-alias.png)

5. Voer in het bestand **LocalInput_DefaultLocalStream. json** het bestandspad in waar uw lokale gegevens bestand zich bevindt.

    ![Lokaal bestandspad invoeren in Visual Studio](./media/vscode-local-run/local-file-path.png)

6. Ga terug naar de query-editor en selecteer **lokaal uitvoeren**.

    ![Selecteer lokaal uitvoeren in de query-editor](./media/vscode-local-run/run-locally.png)

## <a name="next-steps"></a>Volgende stappen

* [Een Azure Stream Analytics Cloud taak maken in Visual Studio code (preview)](quick-create-vs-code.md)

* [Azure Stream Analytics-taken verkennen met Visual Studio code (preview)](vscode-explore-jobs.md)
