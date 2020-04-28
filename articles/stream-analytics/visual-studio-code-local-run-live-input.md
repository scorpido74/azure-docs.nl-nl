---
title: Azure Stream Analytics query's lokaal testen op invoer van live streams met behulp van Visual Studio code
description: In dit artikel wordt beschreven hoe u query's lokaal kunt testen met behulp van Azure Stream Analytics-Hulpprogram Ma's voor Visual Studio code.
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 11/14/2019
ms.topic: conceptual
ms.openlocfilehash: 34ce91a1385f951847abeedd3a6b526d3a07af35
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75660848"
---
# <a name="test-stream-analytics-queries-locally-against-live-stream-input-by-using-visual-studio-code"></a>Stream Analytics query's lokaal testen op invoer van live streams met behulp van Visual Studio code

U kunt Azure Stream Analytics-Hulpprogram Ma's voor Visual Studio code gebruiken om uw Stream Analytics-taken lokaal te testen op de Live stroom invoer. De invoer kan afkomstig zijn uit een bron, zoals Azure Event Hubs of Azure IoT Hub. De uitvoer resultaten worden als JSON-bestanden verzonden naar een map in uw project met de naam **LocalRunOutputs**.

## <a name="prerequisites"></a>Vereisten

* Installeer de [.net core SDK](https://dotnet.microsoft.com/download) en Start Visual Studio code opnieuw.

* In [deze Quick](quick-create-vs-code.md) start leert u hoe u een stream Analytics-taak kunt maken met behulp van Visual Studio code.

## <a name="define-a-live-stream-input"></a>Een live stream-invoer definiëren

1. Klik met de rechter muisknop op de map met **invoer** gegevens in uw stream Analytics-project. Selecteer vervolgens **ASA: invoer toevoegen** in het context menu.

   ![Invoer toevoegen vanuit de map met invoer](./media/quick-create-vs-code/add-input-from-inputs-folder.png)

   U kunt ook **CTRL + SHIFT + P** selecteren om het opdracht palet te openen en **ASA: invoer toevoegen**.

   ![Stream Analytics invoer toevoegen in Visual Studio code](./media/quick-create-vs-code/add-input.png)

2. Kies een invoer bron type in de vervolg keuzelijst.

   ![IoT hub selecteren als de optie invoer](./media/quick-create-vs-code/iot-hub.png)

3. Als u de invoer van het opdracht palet hebt toegevoegd, kiest u het Stream Analytics query script waarmee de invoer wordt gebruikt. Deze moet automatisch worden ingevuld met het bestandspad naar **myASAproj. asaql**.

   ![Een Stream Analytics script selecteren in Visual Studio code](./media/quick-create-vs-code/asa-script.png)

4. Kies **selecteren in uw Azure-abonnementen** in de vervolg keuzelijst.

    ![Selecteren uit abonnementen](./media/quick-create-vs-code/add-input-select-subscription.png)

5. Configureer het zojuist gegenereerde JSON-bestand. U kunt de functie code lens gebruiken om een teken reeks op te geven, een selectie uit een vervolg keuzelijst te selecteren of de tekst rechtstreeks in het bestand te wijzigen. Op de volgende scherm afbeelding ziet u een voor beeld **van uw abonnementen** .

   ![Invoer in Visual Studio code configureren](./media/quick-create-vs-code/configure-input.png)

## <a name="preview-input"></a>Preview-invoer

Om ervoor te zorgen dat de invoer gegevens afkomstig zijn, selecteert u **voorbeeld gegevens** in uw Live-invoer configuratie bestand vanaf de bovenste regel. Sommige invoer gegevens zijn afkomstig van een IoT-hub en worden weer gegeven in het voorbeeld venster. Het kan een paar seconden duren voordat het voor beeld wordt weer gegeven.

 ![Live-invoer preview](./media/quick-create-vs-code/preview-live-input.png)

## <a name="run-queries-locally"></a>Query's lokaal uitvoeren

Ga terug naar de query-editor en selecteer **lokaal uitvoeren**. Selecteer vervolgens **Live-invoer gebruiken** uit de vervolg keuzelijst.

![Selecteer lokaal uitvoeren in de query-editor](./media/vscode-local-run/run-locally.png)

![Selecteer Live invoer gebruiken](./media/vscode-local-run-live-input/run-locally-use-live-input.png)

Het resultaat wordt weer gegeven in het rechter venster en elke 3 seconden vernieuwd. U kunt **uitvoeren** selecteren om opnieuw te testen. U kunt ook **openen in map** selecteren om de resultaat bestanden in Verkenner te bekijken en te openen met Visual Studio code of een hulp programma zoals Excel. Houd er rekening mee dat de resultaten bestanden alleen beschikbaar zijn in JSON-indeling.

De standaard waarde voor de taak voor het maken van de uitvoer is ingesteld op **nu**. U kunt de tijd aanpassen door de knop **Start tijd** van de uitvoer te selecteren in het resultaat venster.

![Resultaat van lokale uitvoering weer geven](./media/vscode-local-run-live-input/vscode-livetesting.gif)

## <a name="next-steps"></a>Volgende stappen

* [Azure Stream Analytics-taken verkennen met Visual Studio code (preview)](visual-studio-code-explore-jobs.md)

* [CI/CD-pijp lijnen instellen met behulp van het NPM-pakket](setup-cicd-vs-code.md)
