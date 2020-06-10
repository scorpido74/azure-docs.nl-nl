---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 02/09/2020
ms.author: glenga
ms.openlocfilehash: 6944d2e6a8f762e62c14f6f3fa3f600a9b3c333e
ms.sourcegitcommit: 1692e86772217fcd36d34914e4fb4868d145687b
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/29/2020
ms.locfileid: "84195037"
---
## <a name="run-the-function-locally"></a>De functie lokaal uitvoeren

Voer uw functie uit door de lokale Azure Functions runtime host te starten vanuit de map *LocalFunctionProj*:

::: zone pivot="programming-language-csharp,programming-language-powershell,programming-language-javascript,programming-language-python"
```
func host start
```
::: zone-end

::: zone pivot="programming-language-typescript"
```
npm install
npm start
```
::: zone-end

::: zone pivot="programming-language-java"
```
mvn clean package 
mvn azure-functions:run
```
::: zone-end

Naar het einde van de uitvoer moeten de volgende regels worden weergegeven: 

<pre>
...

Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

Http Functions:

        HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
...

</pre>

>[!NOTE]  
> Als HttpExample niet verschijnt zoals hieronder weergegeven, hebt u waarschijnlijk de host gestart van buiten de hoofdmap van het project. In dat geval gebruikt u **Ctrl**+**C** om de host te stoppen, gaat u naar de hoofdmap van het project en voert u de vorige opdracht opnieuw uit.

Kopieer de URL van uw `HttpExample`-functie van deze uitvoer naar een browser en voeg de query tekenreeks toe `?name=<your-name>`, waardoor de volledige URL verschijnt, zoals `http://localhost:7071/api/HttpExample?name=Functions`. In de browser moet een bericht als het volgende weergeven `Hello Functions`:

![Resultaat van de functie lokaal uitgevoerd in de browser](./media/functions-run-function-test-local-cli/function-test-local-browser.png)

De terminal waarin u uw project hebt gestart, toont ook de logboek uitvoer wanneer u aanvragen doet.

Wanneer u klaar bent, gebruikt u **Ctrl**+**C** en kiest u `y` om de functiehost te stoppen.
