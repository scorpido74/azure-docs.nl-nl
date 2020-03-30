---
title: Azure Stream Analytics-query's lokaal testen op basis van live stream-invoer met Behulp van Visual Studio Code
description: In dit artikel wordt beschreven hoe u query's lokaal testen op basis van invoer van live streams met Azure Stream Analytics Tools voor Visual Studio Code.
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 11/14/2019
ms.topic: conceptual
ms.openlocfilehash: 34ce91a1385f951847abeedd3a6b526d3a07af35
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75660848"
---
# <a name="test-stream-analytics-queries-locally-against-live-stream-input-by-using-visual-studio-code"></a>Test Stream Analytics-query's lokaal tegen live stream-invoer met behulp van Visual Studio Code

U Azure Stream Analytics Tools voor Visual Studio Code gebruiken om uw Stream Analytics-taken lokaal te testen op basis van live stream-invoer. De invoer kan afkomstig zijn van een bron zoals Azure Event Hubs of Azure IoT Hub. De uitvoerresultaten worden verzonden als JSON-bestanden naar een map in uw project met de naam **LocalRunOutputs.**

## <a name="prerequisites"></a>Vereisten

* Installeer de [.NET Core SDK](https://dotnet.microsoft.com/download) en start Visual Studio Code opnieuw.

* Gebruik [deze snelle start](quick-create-vs-code.md) om te leren hoe u een Stream Analytics-taak maakt met Behulp van Visual Studio Code.

## <a name="define-a-live-stream-input"></a>Een live stream-invoer definiëren

1. Klik met de rechtermuisknop op de map **Invoer** in uw Stream Analytics-project. Selecteer vervolgens **ASA: Invoer toevoegen** in het contextmenu.

   ![Invoer toevoegen vanuit de map Invoer](./media/quick-create-vs-code/add-input-from-inputs-folder.png)

   U **ctrl+Shift+P** ook selecteren om het opdrachtpalet te openen en **ASA invoeren: Invoer toevoegen**.

   ![Stream Analytics-invoer toevoegen in Visual Studio Code](./media/quick-create-vs-code/add-input.png)

2. Kies een invoerbrontype in de vervolgkeuzelijst.

   ![IoT-hub selecteren als invoeroptie](./media/quick-create-vs-code/iot-hub.png)

3. Als u de invoer uit het opdrachtpalet hebt toegevoegd, kiest u het queryscript Stream Analytics dat de invoer gebruikt. Het moet automatisch worden gevuld met het bestandspad naar **myASAproj.asaql**.

   ![Een Stream Analytics-script selecteren in Visual Studio Code](./media/quick-create-vs-code/asa-script.png)

4. Kies **Selecteren in uw Azure-abonnementen** in het vervolgkeuzemenu.

    ![Kiezen uit abonnementen](./media/quick-create-vs-code/add-input-select-subscription.png)

5. Configureer het nieuw gegenereerde JSON-bestand. U de functie CodeLens gebruiken om u te helpen een tekenreeks in te voeren, uit een vervolgkeuzelijst te kiezen of de tekst rechtstreeks in het bestand te wijzigen. In de volgende schermafbeelding wordt **Selecteren uit uw abonnementen** als voorbeeld weergegeven.

   ![Invoer configureren in Visual Studio Code](./media/quick-create-vs-code/configure-input.png)

## <a name="preview-input"></a>Voorvertoning van invoer

Als u ervoor wilt zorgen dat de invoergegevens binnenkomen, selecteert u **Voorbeeldgegevens** in uw configuratiebestand voor liveinvoer vanaf de bovenste regel. Sommige invoergegevens zijn afkomstig van een IoT-hub en worden weergegeven in het voorbeeldvenster. Het kan enkele seconden duren voordat de preview wordt weergegeven.

 ![Voorbeeld van live-invoer bekijken](./media/quick-create-vs-code/preview-live-input.png)

## <a name="run-queries-locally"></a>Query's lokaal uitvoeren

Ga terug naar uw queryeditor en selecteer **Lokaal uitvoeren**. Selecteer vervolgens **Live-invoer gebruiken** in de vervolgkeuzelijst.

![Selecteer 'Lokaal uitvoeren' in de queryeditor](./media/vscode-local-run/run-locally.png)

![Selecteer 'Live-invoer gebruiken'](./media/vscode-local-run-live-input/run-locally-use-live-input.png)

Het resultaat wordt weergegeven in het rechtervenster en elke 3 seconden vernieuwd. U **Uitvoeren** selecteren om opnieuw te testen. U ook **Openen in map** selecteren om de resultaatbestanden in Verkenner te bekijken en deze te openen met Visual Studio Code of een tool als Excel. Houd er rekening mee dat de resultaatbestanden alleen beschikbaar zijn in JSON-indeling.

De standaardtijd voor het maken van de uitvoer taak is ingesteld op **Nu**. U de tijd aanpassen door de knop **Starttijd uitvoer** in het resultaatvenster te selecteren.

![Lokaal runresultaat weergeven](./media/vscode-local-run-live-input/vscode-livetesting.gif)

## <a name="next-steps"></a>Volgende stappen

* [Azure Stream Analytics-taken verkennen met Visual Studio Code (voorbeeld)](visual-studio-code-explore-jobs.md)

* [CI/CD-pijplijnen instellen met het npm-pakket](setup-cicd-vs-code.md)
