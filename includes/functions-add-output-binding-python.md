---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/23/2019
ms.author: glenga
ms.openlocfilehash: 8bdd8b9d900cc50fdeb34ff7d233ac4d7e17a45c
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/05/2020
ms.locfileid: "78191016"
---
Werkt *HttpExample\\\_\_init\_\_.py* bij om overeen te komen met de volgende code en voeg de parameter `msg` toe aan de functiedefinitie en `msg.set(name)` onder de instructie `if name:`.

:::code language="python" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/__init__.py":::

De parameter `msg` is een instantie van de [`azure.functions.InputStream class`](/python/api/azure-functions/azure.functions.httprequest). De methode `set` schrijft een tekenreeksbericht naar de wachtrij, in dit geval de naam die aan de functie is doorgegeven in de URL-querytekenreeks.
