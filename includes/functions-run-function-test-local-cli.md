---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 02/09/2020
ms.author: glenga
ms.openlocfilehash: 749b733039e89421ac33ef76a11f3291b296e718
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80673203"
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

::: zone pivot="programming-language-java"
```
mvn clean package 
mvn azure-functions:run
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
> Als HttpExample niet wordt weer gegeven zoals hieronder weer gegeven, hebt u waarschijnlijk de host gestart van buiten de hoofdmap van het project. In dat geval gebruikt u **CTRL**+**C** om de host te stoppen, navigeert u naar de hoofdmap van het project en voert u de vorige opdracht opnieuw uit.

Kopieer de URL van uw `HttpExample` functie van deze uitvoer naar een browser en voeg de query reeks `?name=<your-name>`toe, zodat u de volledige `http://localhost:7071/api/HttpExample?name=Functions`URL kunt gebruiken. In de browser moet een bericht als `Hello Functions`volgt worden weer gegeven:

![Resultaat van de functie lokaal uitgevoerd in de browser](./media/functions-run-function-test-local-cli/function-test-local-browser.png)

De Terminal waarin u uw project hebt gestart, toont ook de logboek uitvoer wanneer u aanvragen doet.

Wanneer u klaar bent, gebruikt u **CTRL**+**C** en `y` klikt u op de functie host stoppen.