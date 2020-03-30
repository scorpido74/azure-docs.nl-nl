---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/06/2020
ms.author: glenga
ms.openlocfilehash: b4b2409928b6a4196738c7cc6c7040e781d34686
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80056641"
---
1. Druk op F5 in Visual Studio om uw functie uit te voeren. Mogelijk moet u een firewalluitzondering inschakelen, zodat de hulpprogramma's HTTP-aanvragen kunnen afhandelen. Autorisatieniveaus worden nooit afgedwongen wanneer u een functie lokaal uitvoert.

2. Kopieer de URL van uw functie vanuit de uitvoer van de Azure Functions-runtime.

    ![Lokale Azure-runtime](./media/functions-run-function-test-local-vs/functions-debug-local-vs.png)

3. Plak de URL van de HTTP-aanvraag in de adresbalk van uw browser. Sluit de querytekenreeks `?name=<YOUR_NAME>` toe aan deze URL en voer het verzoek uit. In de volgende afbeelding wordt het antwoord in de browser weergegeven op de lokale GET-aanvraag die door de functie wordt geretourneerd: 

    ![De reactie van de lokale host van de functie in de browser](./media/functions-run-function-test-local-vs/functions-run-browser-local-vs.png)

4. Als u foutopsporing wilt stoppen, drukt u op Shift+F5 in Visual Studio.