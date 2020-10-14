---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/18/2020
ms.author: glenga
ms.openlocfilehash: f4f109a6da0cb75555f9aadfa2fff31b8aa3107d
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/05/2020
ms.locfileid: "80673483"
---
U kunt nu de nieuwe parameter, `msg`, gebruiken om naar de uitvoerbinding van uw functiecode te schrijven. Voordat u het antwoord krijgt dat de bewerking is geslaagd, voegt u de volgende regel code toe om de waarde van `name` aan de `msg`-uitvoerbinding toe te voegen.

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="33":::

Als u een uitvoerbinding gebruikt, hoeft u niet de Azure Storage SDK-code voor verificatie te gebruiken, een wachtrijverwijzing op te halen of gegevens te schrijven. Deze taken worden voor u verwerkt via Functions-runtime en Queue Storage-uitvoerbinding.

De methode `run` moet er nu uitzien als in het volgende voorbeeld:

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="16-37":::