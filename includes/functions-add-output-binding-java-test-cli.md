---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 6a359cdd44cc0c0cfbd93bd23b69a67a641c7fbb
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673284"
---
## <a name="update-the-tests"></a>De tests bijwerken

Omdat het archetype ook een reeks tests maakt, moet u `msg` deze `run` tests bijwerken om de nieuwe parameter in de methodehandtekening te verwerken.  

Blader naar de locatie van uw testcode onder _src/test/java,_ open het *projectbestand Function.java* en vervang de coderegel onder `//Invoke` door de volgende code.

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/test/java/com/function/FunctionTest.java" range="48-50":::