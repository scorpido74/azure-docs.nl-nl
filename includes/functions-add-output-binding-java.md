---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/18/2020
ms.author: glenga
ms.openlocfilehash: c27f3fea6d2dd8b891220ba06b375b33e6cd950b
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80673501"
---
In een Java-project worden de bindingen gedefinieerd als bindings aantekeningen voor de functie methode. Het bestand *Function. json* wordt vervolgens automatisch gegenereerd op basis van deze aantekeningen.

Blader naar de locatie van de functie code onder _src/main/Java_, open het *functie. java* -project bestand en voeg de volgende para meter toe `run` aan de methode definitie:

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="20-21":::

De `msg` para meter is [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.outputbinding) een type dat een verzameling teken reeksen vertegenwoordigt die als berichten naar een uitvoer binding worden geschreven wanneer de functie is voltooid. In dit geval is de uitvoer een opslag wachtrij met de `outqueue`naam. De connection string voor het opslag account is ingesteld met de `connection` methode. In plaats van de connection string zelf, geeft u de toepassings instelling door die het opslag account bevat connection string.

De `run` definitie van de methode moet er nu uitzien als in het volgende voor beeld:  

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="16-22":::