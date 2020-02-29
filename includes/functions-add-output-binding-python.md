---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/23/2019
ms.author: glenga
ms.openlocfilehash: 8bdd8b9d900cc50fdeb34ff7d233ac4d7e17a45c
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78191016"
---
Werk *HttpExample\\\_\_init\_\_. py* met de volgende code en voeg de `msg`-para meter toe aan de functie definitie en `msg.set(name)` onder de `if name:`-instructie.

:::code language="python" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/__init__.py":::

De para meter `msg` is een exemplaar van de [`azure.functions.InputStream class`](/python/api/azure-functions/azure.functions.httprequest). De `set` methode schrijft een teken reeks bericht naar de wachtrij, in dit geval de naam die is door gegeven aan de functie in de URL-query teken reeks.
