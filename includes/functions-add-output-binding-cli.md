---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 121f6ffa5c1a7c903e59be8a5bc3e1e1db0834fc
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80673364"
---
## <a name="add-an-output-binding-definition-to-the-function"></a>Een definitie van een uitvoer binding toevoegen aan de functie

Hoewel een functie slechts één trigger kan hebben, kan deze meerdere invoer-en uitvoer bindingen hebben, waarmee u verbinding kunt maken met andere Azure-Services en-resources zonder aangepaste integratie code te schrijven. 

::: zone pivot="programming-language-python,programming-language-javascript,programming-language-powershell,programming-language-typescript"  
U declareert deze bindingen in het bestand *Function. json* in de map function. In de vorige Snelstartgids bevat het bestand *Function. json* in de map *HttpExample* twee bindingen in de `bindings` verzameling:  
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript"  
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-JavaScript/function.json" range="2-18":::  
::: zone-end

::: zone pivot="programming-language-python"  
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json" range="2-18":::  
::: zone-end

::: zone pivot="programming-language-powershell"  
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/function.json" range="2-18":::
::: zone-end  

::: zone pivot="programming-language-python,programming-language-javascript, programming-language-powershell, programming-language-typescript"  
Elke binding heeft ten minste een type, een richting en een naam. In het bovenstaande voor beeld is de eerste binding van het `httpTrigger` type met de `in`richting. Hiermee geeft `in` u in de richting de naam op van een invoer parameter die wordt verzonden naar de functie wanneer deze wordt aangeroepen door de trigger. `name`  
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript"  
De tweede binding in de verzameling heeft de `res`naam. Deze `http` binding is een uitvoer binding (`out`) die wordt gebruikt voor het schrijven van het HTTP-antwoord. 

Als u wilt schrijven naar een Azure Storage wachtrij van deze functie, `out` voegt u een `queue` binding van het `msg`type met de naam toe, zoals wordt weer gegeven in de onderstaande code:

:::code language="json" source="~/functions-docs-javascript/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-python"  
De tweede binding in de verzameling is van het `http` type met de `out`richting, in dat geval het `name` speciale `$return` element geeft aan dat deze binding de retour waarde van de functie gebruikt in plaats van een invoer parameter op te geven.

Als u wilt schrijven naar een Azure Storage wachtrij van deze functie, `out` voegt u een `queue` binding van het `msg`type met de naam toe, zoals wordt weer gegeven in de onderstaande code:

:::code language="json" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-powershell"  
De tweede binding in de verzameling heeft de `res`naam. Deze `http` binding is een uitvoer binding (`out`) die wordt gebruikt voor het schrijven van het HTTP-antwoord. 

Als u wilt schrijven naar een Azure Storage wachtrij van deze functie, `out` voegt u een `queue` binding van het `msg`type met de naam toe, zoals wordt weer gegeven in de onderstaande code:

:::code language="json" source="~/functions-docs-powershell/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-python,programming-language-javascript,programming-language-powershell,programming-language-typescript"  
In dit geval `msg` wordt de functie als uitvoer argument gegeven. Voor een `queue` type moet u ook de naam van de wachtrij opgeven in `queueName` en de *naam* van de Azure Storage verbinding (van *Local. settings. json*) invoeren in `connection`. 
::: zone-end  
