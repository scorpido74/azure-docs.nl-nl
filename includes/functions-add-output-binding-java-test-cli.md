---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 6a359cdd44cc0c0cfbd93bd23b69a67a641c7fbb
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80673284"
---
## <a name="update-the-tests"></a>De tests bijwerken

Omdat de archetype ook een reeks tests maakt, moet u deze tests bijwerken voor het afhandelen van `msg` de nieuwe para `run` meter in de methode handtekening.  

Blader naar de locatie van de test code onder _src/test/java_, open het *functie. java* -project bestand en vervang de regel met code onder `//Invoke` met de volgende code.

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/test/java/com/function/FunctionTest.java" range="48-50":::