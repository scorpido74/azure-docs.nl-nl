---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 02/09/2020
ms.author: glenga
ms.openlocfilehash: d53c41752d57a27ebea9bd60f7e723dab1e7308a
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78190840"
---
## <a name="run-the-function-locally"></a>De functie lokaal uitvoeren

Voer uw functie uit door de lokale Azure Functions runtime host te starten vanuit de map *LocalFunctionProj* :

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

Naar het einde van de uitvoer moeten de volgende regels worden weer gegeven: 

<pre>
...

Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

Http Functions:

        HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
...

</pre>

>[!NOTE]  
> Als HttpExample niet wordt weer gegeven zoals hieronder weer gegeven, start u de host waarschijnlijk vanuit de map *HttpExample* . In dat geval gebruikt u **Ctrl**+**C** om de host te stoppen, gaat u naar de map van het bovenliggende *LocalFunctionProj* en voert u de vorige opdracht opnieuw uit.

Kopieer de URL van uw `HttpExample`-functie van deze uitvoer naar een browser en voeg de query reeks toe `?name=<your-name>`, waardoor de volledige URL, zoals `http://localhost:7071/api/HttpExample?name=Functions`. In de browser moet een bericht als `Hello Functions`worden weer gegeven:

![Resultaat van de functie lokaal uitgevoerd in de browser](./media/functions-run-function-test-local-cli/function-test-local-browser.png)

De Terminal waarin u `func start` hebt uitgevoerd, toont ook de logboek uitvoer wanneer u aanvragen doet.

Wanneer u klaar bent, gebruikt u **Ctrl**+**C** en kiest u `y` om de functions-host te stoppen.