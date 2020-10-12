---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 02/19/2020
ms.author: glenga
ms.openlocfilehash: 753a3d16792e0dbaffe318cd188ea3f0d40bd5cb
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/05/2020
ms.locfileid: "78191017"
---
Voeg code toe die gebruikmaakt van de cmdlet `Push-OutputBinding` om tekst te schrijven naar de wachtrij met de uitvoerbinding `msg`. Voeg deze code toe voordat u de status Ok in de instructie `if` instelt.

:::code language="powershell" range="18-19" source="~/functions-docs-powershell/functions-add-output-binding-storage-queue-cli/HttpExample/run.ps1":::

Op dit moment moet uw functie er als volgt uit zien:

:::code language="powershell" source="~/functions-docs-powershell/functions-add-output-binding-storage-queue-cli/HttpExample/run.ps1":::
