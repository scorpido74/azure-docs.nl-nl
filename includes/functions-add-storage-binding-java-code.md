---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/18/2020
ms.author: glenga
ms.openlocfilehash: f4f109a6da0cb75555f9aadfa2fff31b8aa3107d
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673483"
---
U de nieuwe `msg` parameter nu gebruiken om naar de uitvoerbinding van uw functiecode te schrijven. Voeg de volgende coderegel toe voordat de `name` succesrespons `msg` wordt uitgevoerd om de waarde van de uitvoerbinding toe te voegen.

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="33":::

Wanneer u een uitvoerbinding gebruikt, hoeft u de Azure Storage SDK-code niet te gebruiken voor verificatie, het verkrijgen van een wachtrijverwijzing of het schrijven van gegevens. De runtime en queue output binding van functies doen deze taken voor u.

Uw `run` methode moet er nu uitzien als het volgende voorbeeld:

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="16-37":::