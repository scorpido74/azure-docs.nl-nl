---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: a33cc604d45d51a63a73ea6ed58abc67269437d7
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673156"
---
::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python" 
Voeg een functie toe aan uw project `--name` met behulp van de volgende opdracht, waarbij `--template` het argument de unieke naam van uw functie (HttpExample) is en het argument de trigger van de functie (HTTP) opgeeft. 

```
func new --name HttpExample --template "HTTP trigger"
```  
::: zone-end  
::: zone pivot="programming-language-csharp"
`func new`hiermee wordt een HttpExample.cs codebestand aanmaakt.
::: zone-end
::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell"
`func new`hiermee wordt een submap gemaakt die overeenkomt met de functienaam die een codebestand bevat dat geschikt is voor de gekozen taal van het project en een configuratiebestand met de naam *function.json*.
::: zone-end