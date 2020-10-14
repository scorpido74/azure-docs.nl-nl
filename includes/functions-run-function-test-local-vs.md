---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/06/2020
ms.author: glenga
ms.openlocfilehash: b4b2409928b6a4196738c7cc6c7040e781d34686
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/05/2020
ms.locfileid: "80056641"
---
1. Als u de functie wilt uitvoeren, drukt u op F5 in Visual Studio. Mogelijk moet u ook een firewall-uitzondering maken, zodat de hulpprogramma's HTTP-aanvragen kunnen afhandelen. Er worden geen autorisatieniveaus afgedwongen wanneer u een functie lokaal uitvoert.

2. Kopieer de URL van uw functie vanuit de uitvoer van de Azure Functions-runtime.

    ![Lokale Azure-runtime](./media/functions-run-function-test-local-vs/functions-debug-local-vs.png)

3. Plak de URL van de HTTP-aanvraag in de adresbalk van uw browser. Voeg de queryreeks `?name=<YOUR_NAME>` toe aan de URL en voer de aanvraag uit. In de afbeelding hieronder ziet u de reactie in de browser op de lokale GET-aanvraag die door de functie wordt geretourneerd: 

    ![De reactie van de lokale host van de functie in de browser](./media/functions-run-function-test-local-vs/functions-run-browser-local-vs.png)

4. Druk op Shift + F5 in VS Code als u het opsporen van fouten wilt stoppen.