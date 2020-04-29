---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 07/05/2019
ms.author: glenga
ms.openlocfilehash: 5e1a2622df0038141dd5cb05237f93d5e33e0bfb
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "78190909"
---
In een C#-klassen bibliotheek project worden de bindingen gedefinieerd als bindings kenmerken voor de functie methode. Het bestand *Function. json* dat vereist is voor-functies, wordt vervolgens automatisch gegenereerd op basis van deze kenmerken.

Open het project bestand *HttpExample.cs* en voeg de volgende para meter toe `Run` aan de methode definitie:

:::code language="csharp" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-cli/HttpExample.cs" range="17":::

De `msg` para meter is `ICollector<T>` een type dat een verzameling berichten vertegenwoordigt die naar een uitvoer binding worden geschreven wanneer de functie is voltooid. In dit geval is de uitvoer een opslag wachtrij met de `outqueue`naam. De connection string voor het opslag account is ingesteld door de `StorageAccountAttribute`. Dit kenmerk geeft de instelling aan die het opslag account bevat connection string en kan worden toegepast op het niveau van de klasse, methode of para meter. In dit geval kunt u weglaten `StorageAccountAttribute` omdat u het standaard opslag account al gebruikt.

De definitie van de run-methode moet er nu als volgt uitzien:  

:::code language="csharp" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-cli/HttpExample.cs" range="14-18":::
