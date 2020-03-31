---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 02/09/2020
ms.author: glenga
ms.openlocfilehash: d53c41752d57a27ebea9bd60f7e723dab1e7308a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78190840"
---
## <a name="run-the-function-locally"></a>De functie lokaal uitvoeren

Voer uw functie uit door de lokale runtime-host van Azure Functions te starten vanuit de map *LocalFunctionProj:*

::: zone pivot="programming-language-csharp,programming-language-powershell,programming-language-javascript,programming-language-python"
```
func start
```
::: zone-end

::: zone pivot="programming-language-typescript"
```
npm install
npm start
```
::: zone-end

Tegen het einde van de uitvoer moeten de volgende regels worden weergegeven: 

<pre>
...

Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

Http Functions:

        HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
...

</pre>

>[!NOTE]  
> Als HttpExample niet wordt weergegeven zoals hieronder wordt weergegeven, hebt u de host waarschijnlijk gestart vanuit de *map HttpExample.* Gebruik in dat geval **Ctrl**+**C** om de host te stoppen, navigeer naar de bovenliggende *map LocalFunctionProj* en voer de vorige opdracht opnieuw uit.

Kopieer de URL `HttpExample` van uw functie vanuit deze uitvoer `?name=<your-name>`naar een browser `http://localhost:7071/api/HttpExample?name=Functions`en sluit de querytekenreeks toe, waardoor de volledige URL zo leuk is . De browser moet een `Hello Functions`bericht weergeven als:

![Resultaat van de functie lokaal in de browser wordt uitgevoerd](./media/functions-run-function-test-local-cli/function-test-local-browser.png)

De terminal waarin `func start` u liep toont ook log uitvoer als u verzoeken.

Wanneer u klaar bent, gebruikt `y` u **Ctrl**+**C** en kiest u ervoor om de hosthost van de functies te stoppen.