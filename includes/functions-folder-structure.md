---
title: bestand opnemen
description: bestand opnemen
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/12/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 5f5f682c8c31c17f1a645bcdacdc78f32bd5c001
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "77205692"
---
De code voor alle functies in een specifieke functie-app bevindt zich in een hoofdmap van het project met een configuratie bestand voor een host en een of meer submappen. Elke submap bevat de code voor een afzonderlijke functie. De mapstructuur wordt weer gegeven in de volgende weer gave:

```
FunctionApp
 | - host.json
 | - MyFirstFunction
 | | - function.json
 | | - ...  
 | - MySecondFunction
 | | - function.json
 | | - ...  
 | - SharedCode
 | - bin
```

In versie 2. x en hoger van de functions-runtime moeten alle functies in de functie-app dezelfde taal stack delen.  

De [host.jsin](../articles/azure-functions/functions-host-json.md) het bestand bevat runtime-specifieke configuraties en bevindt zich in de hoofdmap van de functie-app. Een *bin* -map bevat pakketten en andere bibliotheek bestanden die de functie-app nodig heeft. Zie de taalspecifieke vereisten voor een functie-app-project:

* [C#-klassebibliotheek (. csproj)](../articles/azure-functions/functions-dotnet-class-library.md#functions-class-library-project)
* [C#-script (.csx)](../articles/azure-functions/functions-reference-csharp.md#folder-structure)
* [F # script](../articles/azure-functions/functions-reference-fsharp.md#folder-structure)
* [Java](../articles/azure-functions/functions-reference-java.md#folder-structure)
* [JavaScript](../articles/azure-functions/functions-reference-node.md#folder-structure)
* [Python](../articles/azure-functions/functions-reference-python.md#folder-structure)
