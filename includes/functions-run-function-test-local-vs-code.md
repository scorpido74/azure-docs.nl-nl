---
title: bestand opnemen
description: bestand opnemen
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/31/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: ebcbe7e8af7e01a9b7e75282be4e4f4ceb80b806
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/19/2020
ms.locfileid: "76279612"
---
## <a name="run-the-function-locally"></a>De functie lokaal uitvoeren

Met Azure Functions Core-hulpprogramma's kunt u een Azure Functions-project uitvoeren op uw lokale ontwikkelcomputer.

1. U kunt de functie testen door een onderbrekingspunt in de functiecode in te stellen en op F5 te drukken om het functie-appproject te starten. De uitvoer van Core Tools wordt weergegeven in het deelvenster **Terminal**.

1. Kopieer het URL-eindpunt van de door HTTP getriggerde functie in het deelvenster **Terminal**. 

    ![Lokale Azure-uitvoer](./media/functions-run-function-test-local-vs-code/functions-vscode-f5.png)

1. Plak de URL van de HTTP-aanvraag in de adresbalk van uw browser. Voeg de queryreeks `?name=<yourname>` toe aan de URL en voer de aanvraag uit. De uitvoering wordt onderbroken als het onderbrekingspunt wordt bereikt.

1. Hieronder ziet u het antwoord op de GET-aanvraag weergegeven in de browser als u met de uitvoering doorgaat:

    ![De reactie van de lokale host van de functie in de browser](./media/functions-run-function-test-local-vs-code/functions-test-local-browser.png)

1. Als u wilt stoppen met fouten opsporen, drukt u op Shift + F5.
