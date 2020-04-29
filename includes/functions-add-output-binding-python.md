---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/23/2019
ms.author: glenga
ms.openlocfilehash: 8bdd8b9d900cc50fdeb34ff7d233ac4d7e17a45c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "78191016"
---
Werk *\\\_HttpExample\_init\_. py bij met de volgende code door de para meter toe te voegen aan de functie definitie en de\_* `msg.set(name)` `if name:` `msg` instructie.

:::code language="python" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/__init__.py":::

De `msg` para meter is een exemplaar van [`azure.functions.InputStream class`](/python/api/azure-functions/azure.functions.httprequest)de. De `set` methode schrijft een teken reeks bericht naar de wachtrij, in dit geval de naam die is door gegeven aan de functie in de URL-query teken reeks.
