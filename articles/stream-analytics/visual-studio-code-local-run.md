---
title: Een Azure Stream Analytics-taak lokaal testen met voorbeeldgegevens met Visual Studio Code
description: In dit artikel wordt beschreven hoe u query's lokaal testen met voorbeeldgegevens met Azure Stream Analytics Tools voor Visual Studio Code.
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 11/10/2019
ms.topic: conceptual
ms.openlocfilehash: c29d0d9ecd856ee9611df21d23b1b2b763e24652
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75486467"
---
# <a name="test-stream-analytics-queries-locally-with-sample-data-using-visual-studio-code"></a>Query's van TestStream Analytics lokaal met voorbeeldgegevens met Behulp van Visual Studio Code

U Azure Stream Analytics-hulpprogramma's voor Visual Studio Code gebruiken om uw Stream Analytics-taken lokaal te testen met voorbeeldgegevens. U de queryresultaten vinden in JSON-bestanden in de map **LocalRunOutputs** van uw project.

## <a name="prerequisites"></a>Vereisten

* Installeer [.NET core SDK](https://dotnet.microsoft.com/download) en start Visual Studio Code opnieuw.

* Gebruik deze [snelle start](quick-create-vs-code.md) om te leren hoe u een Stream Analytics-taak maakt met Behulp van Visual Studio Code.

## <a name="prepare-sample-data"></a>Voorbeeldgegevens voorbereiden

U moet eerst voorbeeldinvoergegevensbestanden voorbereiden. Als u al een aantal voorbeeldgegevensbestanden op uw machine hebt, u deze stap overslaan en naar de volgende stap gaan.

1. Klik vanaf de bovenste regel **op Voorbeeld van gegevens** in uw invoerconfiguratiebestand. Sommige invoergegevens worden opgehaald via IoT Hub en weergegeven in het voorbeeldvenster. Houd er rekening mee dat dit een tijdje kan duren.

2. Zodra de gegevens zijn weergegeven, klikt u op **Opslaan om** de gegevens op te slaan in een lokaal bestand.

 ![Voorbeeld van live-invoer bekijken](./media/quick-create-vs-code/preview-live-input.png)

## <a name="define-a-local-input"></a>Een lokale invoer definiëren

1. Klik **op input.json** onder de map Invoer in uw Stream Analytics-project. Selecteer vervolgens **Lokale invoer toevoegen** vanaf de bovenste regel.

    ![Lokale invoer van project toevoegen](./media/quick-create-vs-code/add-input-from-project.png)

    U **Ctrl+Shift+P** ook gebruiken om het opdrachtpalet te openen en ASA in te **voeren: Invoer toevoegen**.

   ![Stream Analytics-invoer toevoegen in VS-code](./media/quick-create-vs-code/add-input.png)

2. Selecteer **Lokale invoer**.

    ![Lokale ASA-invoer toevoegen in Visual Studio-code](./media/vscode-local-run/add-local-input.png)

3. Selecteer **+ Nieuwe lokale invoer**.

    ![Een nieuwe ASA-lokale invoer toevoegen in Visual Studio-code](./media/vscode-local-run/add-new-local-input.png)

4. Voer dezelfde invoeralias in die u in uw query hebt gebruikt.

    ![Een nieuwe ASA-lokale invoeralias toevoegen](./media/vscode-local-run/new-local-input-alias.png)

5. Voer in het nieuw gegenereerde **bestand LocalInput_Input.json** het bestandspad in waar uw lokale gegevensbestand zich bevindt.

    ![Lokaal bestandspad invoeren in Visual Studio](./media/vscode-local-run/local-file-path.png)

6. Selecteer **Voorbeeldgegevens** om een voorbeeld van de invoergegevens te bekijken. Het serialisatietype voor uw gegevens wordt automatisch gedetecteerd als de JSON of CSV. Gebruik de kiezer om uw gegevens weer te geven in **tabel-** of **raw-indeling.** De volgende tabel is een voorbeeld van gegevens in de **tabelnotatie:**

     ![Lokale gegevens in tabelindeling bekijken](./media/vscode-local-run/local-file-preview-table.png)

    De volgende tabel is een voorbeeld van gegevens in de **Raw-indeling:**

    ![Lokale gegevens bekijken in raw-indeling](./media/vscode-local-run/local-file-preview-raw.png)

## <a name="run-queries-locally"></a>Query's lokaal uitvoeren

Ga terug naar uw queryeditor en selecteer **Lokaal uitvoeren**. Selecteer vervolgens **Lokale invoer gebruiken** in de vervolgkeuzelijst.

![Lokaal uitvoeren selecteren in de queryeditor](./media/vscode-local-run/run-locally.png)

![Lokale invoer gebruiken](./media/vscode-local-run/run-locally-use-local-input.png)

Het resultaat wordt weergegeven in het rechtervenster. U op **Uitvoeren** klikken om opnieuw te testen. U ook **Openen in map** selecteren om de resultaatbestanden in verkenner te bekijken en deze verder te openen met andere hulpprogramma's. Houd er rekening mee dat de resultaatbestanden alleen beschikbaar zijn in JSON-indeling.

![Lokaal runresultaat weergeven](./media/vscode-local-run/run-locally-result.png)

## <a name="next-steps"></a>Volgende stappen

* [Azure Stream Analytics-taken lokaal testen met live-invoer met Visual Studio Code](visual-studio-code-local-run-live-input.md)

* [Azure Stream Analytics-taken verkennen met Visual Studio Code (Preview)](visual-studio-code-explore-jobs.md)
