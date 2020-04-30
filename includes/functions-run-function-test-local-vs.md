---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/06/2020
ms.author: glenga
ms.openlocfilehash: b4b2409928b6a4196738c7cc6c7040e781d34686
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80056641"
---
1. Als u de functie wilt uitvoeren, drukt u op F5 in Visual Studio. Mogelijk moet u een firewall-uitzonde ring inschakelen zodat de hulpprogram ma's HTTP-aanvragen kunnen afhandelen. Autorisatie niveaus worden nooit afgedwongen wanneer u een functie lokaal uitvoert.

2. Kopieer de URL van uw functie vanuit de uitvoer van de Azure Functions-runtime.

    ![Lokale Azure-runtime](./media/functions-run-function-test-local-vs/functions-debug-local-vs.png)

3. Plak de URL van de HTTP-aanvraag in de adresbalk van uw browser. Voeg de query reeks `?name=<YOUR_NAME>` toe aan deze URL en voer de aanvraag uit. In de volgende afbeelding ziet u het antwoord in de browser op de lokale GET-aanvraag die wordt geretourneerd door de functie: 

    ![De reactie van de lokale host van de functie in de browser](./media/functions-run-function-test-local-vs/functions-run-browser-local-vs.png)

4. Als u het fout opsporingsprogramma wilt stoppen, drukt u op SHIFT + F5 in Visual Studio.