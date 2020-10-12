---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 07/05/2019
ms.author: glenga
ms.openlocfilehash: 5e1a2622df0038141dd5cb05237f93d5e33e0bfb
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/05/2020
ms.locfileid: "78190909"
---
In een bibliotheekproject van de C#-klasse worden de bindingen gedefinieerd als bindingseigenschappen in de functiemethode. Het bestand *function.json* dat wordt vereist door Functions, wordt automatisch gegenereerd op basis van deze kenmerken.

Open het projectbestand *HttpExample.cs* en voeg de volgende parameter toe aan de methodedefinitie `Run`:

:::code language="csharp" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-cli/HttpExample.cs" range="17":::

De parameter `msg` is een type `ICollector<T>` die een verzameling berichten vertegenwoordigt die worden geschreven naar een uitvoerbinding wanneer de functie is voltooid. In dit geval is de uitvoer een opslagwachtrij met de naam `outqueue`. De verbindingsreeks voor het Storage-account wordt ingesteld door de `StorageAccountAttribute`. Dit kenmerk geeft de instelling aan die de verbindingsreeks van het Storage-account bevat en kan worden toegepast op het niveau van de klasse, methode of parameter. In dit geval kunt u `StorageAccountAttribute` weglaten, omdat u dit al gebruikt in het standaardopslagaccount.

De uitvoermethodedefinitie moet er nu als volgt uitzien:  

:::code language="csharp" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-cli/HttpExample.cs" range="14-18":::
