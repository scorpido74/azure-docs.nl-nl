---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 02/09/2020
ms.author: glenga
ms.openlocfilehash: 749b733039e89421ac33ef76a11f3291b296e718
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673203"
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

::: zone pivot="programming-language-java"
```
mvn clean package 
mvn azure-functions:run
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
> Als HttpExample niet wordt weergegeven zoals hieronder wordt weergegeven, hebt u de host waarschijnlijk van buiten de hoofdmap van het project gestart. Gebruik in dat geval **Ctrl**+**C** om de host te stoppen, naar de hoofdmap van het project te navigeren en de vorige opdracht opnieuw uit te voeren.

Kopieer de URL `HttpExample` van uw functie vanuit deze uitvoer `?name=<your-name>`naar een browser `http://localhost:7071/api/HttpExample?name=Functions`en sluit de querytekenreeks toe, waardoor de volledige URL zo leuk is . De browser moet een `Hello Functions`bericht weergeven als:

![Resultaat van de functie lokaal in de browser wordt uitgevoerd](./media/functions-run-function-test-local-cli/function-test-local-browser.png)

De terminal waarin u uw project hebt gestart, toont ook de uitvoer van logboeken terwijl u aanvragen doet.

Wanneer u klaar bent, gebruikt `y` u **Ctrl**+**C** en kiest u ervoor om de hosthost van de functies te stoppen.