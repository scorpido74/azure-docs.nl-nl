---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 121f6ffa5c1a7c903e59be8a5bc3e1e1db0834fc
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673364"
---
## <a name="add-an-output-binding-definition-to-the-function"></a>Een uitvoerbindingsdefinitie toevoegen aan de functie

Hoewel een functie slechts één trigger kan hebben, kan deze meerdere invoer- en uitvoerbindingen hebben, waarmee u verbinding maken met andere Azure-services en -bronnen zonder aangepaste integratiecode te schrijven. 

::: zone pivot="programming-language-python,programming-language-javascript,programming-language-powershell,programming-language-typescript"  
U declareert deze bindingen in het *bestand function.json* in uw functiemap. Van de vorige quickstart bevat het bestand *function.json* in de `bindings` *map HttpExample* twee bindingen in de verzameling:  
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
Elke binding heeft ten minste een type, een richting en een naam. In het bovenstaande voorbeeld is de `httpTrigger` eerste `in`binding van type met de richting . Hiermee `in` geeft u voor de richting de naam op van een invoerparameter die naar de functie wordt verzonden wanneer deze door de trigger wordt aangeroepen. `name`  
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript"  
De tweede binding in `res`de collectie wordt genoemd. Deze `http` binding is een`out`uitvoerbinding ( ) die wordt gebruikt om het HTTP-antwoord te schrijven. 

Als u vanuit deze functie naar een `out` Azure `queue` Storage-wachtrij `msg`wilt schrijven, voegt u een tekstbinding toe met de naam , zoals in de onderstaande code wordt weergegeven:

:::code language="json" source="~/functions-docs-javascript/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-python"  
De tweede binding in de `http` collectie `out`is van type `name` met `$return` de richting , in welk geval de speciale van aangeeft dat deze binding de retourwaarde van de functie gebruikt in plaats van het verstrekken van een invoerparameter.

Als u vanuit deze functie naar een `out` Azure `queue` Storage-wachtrij `msg`wilt schrijven, voegt u een tekstbinding toe met de naam , zoals in de onderstaande code wordt weergegeven:

:::code language="json" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-powershell"  
De tweede binding in `res`de collectie wordt genoemd. Deze `http` binding is een`out`uitvoerbinding ( ) die wordt gebruikt om het HTTP-antwoord te schrijven. 

Als u vanuit deze functie naar een `out` Azure `queue` Storage-wachtrij `msg`wilt schrijven, voegt u een tekstbinding toe met de naam , zoals in de onderstaande code wordt weergegeven:

:::code language="json" source="~/functions-docs-powershell/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-python,programming-language-javascript,programming-language-powershell,programming-language-typescript"  
In dit `msg` geval wordt gegeven aan de functie als een output argument. Voor `queue` een type moet u ook de `queueName` naam van de wachtrij opgeven en de *naam* van de `connection`Azure Storage-verbinding (van *local.settings.json)* opgeven in . 
::: zone-end  
