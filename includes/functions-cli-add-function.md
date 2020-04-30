---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: a33cc604d45d51a63a73ea6ed58abc67269437d7
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80673156"
---
::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python" 
Voeg een functie toe aan uw project met behulp van de volgende opdracht `--name` , waarbij het argument de unieke naam van uw functie (HttpExample) `--template` is en het argument de trigger (http) van de functie specificeert. 

```
func new --name HttpExample --template "HTTP trigger"
```  
::: zone-end  
::: zone pivot="programming-language-csharp"
`func new`Hiermee maakt u een HttpExample.cs-code bestand.
::: zone-end
::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell"
`func new`Hiermee maakt u een submap die overeenkomt met de naam van de functie die geschikt is voor de geselecteerde taal van het project en een configuratie bestand met de naam *Function. json*.
::: zone-end