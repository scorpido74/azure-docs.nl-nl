---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 3705f58a37c109ebe0b774603c60e246fc174f25
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80673258"
---
U kunt nu de nieuwe `msg` para meter gebruiken om te schrijven naar de uitvoer binding vanuit uw functie code. Voeg de volgende regel code toe vóór het antwoord van het slagen om de waarde `name` van toe `msg` te voegen aan de uitvoer binding.

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="34":::

Wanneer u een uitvoer binding gebruikt, hoeft u niet de Azure Storage SDK-code te gebruiken voor authenticatie, het ophalen van een wachtrij verwijzing of het schrijven van gegevens. De functies runtime en wachtrij-uitvoer binding voeren deze taken voor u uit.

De `run` methode moet er nu uitzien als in het volgende voor beeld:

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="17-38":::