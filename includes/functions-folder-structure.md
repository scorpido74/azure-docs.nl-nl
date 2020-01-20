---
title: bestand opnemen
description: bestand opnemen
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/12/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: ea7396117935c13698a8c6cc6ef4029cc82b90bc
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/19/2020
ms.locfileid: "76279421"
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

Het bestand [host. json](../articles/azure-functions/functions-host-json.md) bevat runtime-specifieke configuraties en bevindt zich in de hoofdmap van de functie-app. Een *bin* -map bevat pakketten en andere bibliotheek bestanden die de functie-app nodig heeft. Zie de taalspecifieke vereisten voor een functie-app-project:

* [C#klassebibliotheek (. csproj)](../articles/azure-functions/functions-dotnet-class-library.md#functions-class-library-project)
* [C# script (.csx)](../articles/azure-functions/functions-reference-csharp.md#folder-structure)
* [F#Schriften](../articles/azure-functions/functions-reference-fsharp.md#folder-structure)
* [Java](../articles/azure-functions/functions-reference-java.md#folder-structure)
* [JavaScript](../articles/azure-functions/functions-reference-node.md#folder-structure)
