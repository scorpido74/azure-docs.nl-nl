---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 07/05/2019
ms.author: glenga
ms.openlocfilehash: 5e1a2622df0038141dd5cb05237f93d5e33e0bfb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78190909"
---
In een c#-klassebibliotheekproject worden de bindingen gedefinieerd als bindende kenmerken voor de functiemethode. Het *functie.json-bestand* dat door Functions vereist is, wordt vervolgens automatisch gegenereerd op basis van deze kenmerken.

Open het *HttpExample.cs* projectbestand en voeg `Run` de volgende parameter toe aan de methodedefinitie:

:::code language="csharp" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-cli/HttpExample.cs" range="17":::

De `msg` parameter `ICollector<T>` is een type dat een verzameling berichten vertegenwoordigt die naar een uitvoerbinding zijn geschreven wanneer de functie is voltooid. In dit geval is de uitvoer `outqueue`een opslagwachtrij met de naam . De verbindingstekenreeks voor het opslagaccount `StorageAccountAttribute`wordt ingesteld door het . Dit kenmerk geeft de instelling aan die de tekenreeks Opslagaccountverbinding bevat en kan worden toegepast op klasse-, methode- of parameterniveau. In dit geval `StorageAccountAttribute` u weglaten omdat u het standaardopslagaccount al gebruikt.

De definitie van de methode Uitvoeren moet er nu als volgt uitzien:  

:::code language="csharp" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-cli/HttpExample.cs" range="14-18":::
