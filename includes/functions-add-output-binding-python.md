---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/23/2019
ms.author: glenga
ms.openlocfilehash: 8bdd8b9d900cc50fdeb34ff7d233ac4d7e17a45c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78191016"
---
Update *HttpExample\\\_\_\_\_init .py* om de `msg` volgende code aan te passen, waarbij de parameter wordt toegevoegd aan de functiedefinitie en `msg.set(name)` onder de `if name:` instructie.

:::code language="python" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/__init__.py":::

De `msg` parameter is een [`azure.functions.InputStream class`](/python/api/azure-functions/azure.functions.httprequest)instantie van de . De `set` methode schrijft een tekenreeksbericht naar de wachtrij, in dit geval wordt de naam doorgegeven aan de functie in de URL-querytekenreeks.
