---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/18/2020
ms.author: glenga
ms.openlocfilehash: c27f3fea6d2dd8b891220ba06b375b33e6cd950b
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673501"
---
In een Java-project worden de bindingen gedefinieerd als bindende annotaties op de functiemethode. Het *bestand function.json* wordt vervolgens automatisch gegenereerd op basis van deze annotaties.

Blader naar de locatie van uw functiecode onder _src/main/java,_ open het *projectbestand Function.java* en voeg de volgende parameter toe aan de `run` methodedefinitie:

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="20-21":::

De `msg` parameter [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.outputbinding) is een type dat een verzameling tekenreeksen vertegenwoordigt die als berichten naar een uitvoerbinding worden geschreven wanneer de functie is voltooid. In dit geval is de uitvoer `outqueue`een opslagwachtrij met de naam . De verbindingstekenreeks voor het opslagaccount `connection` wordt ingesteld door de methode. In plaats van de verbindingstekenreeks zelf, passeert u de toepassingsinstelling die de tekenreeks Opslagaccountverbinding bevat.

De `run` methodedefinitie moet er nu uitzien als het volgende voorbeeld:  

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="16-22":::