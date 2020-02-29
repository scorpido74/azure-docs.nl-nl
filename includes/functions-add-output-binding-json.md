---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/23/2019
ms.author: glenga
ms.openlocfilehash: 8530f4469a0c25f3c32e652e2b0752c51c28ff3f
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78191015"
---
Bindings kenmerken worden rechtstreeks in het bestand function. json gedefinieerd. Afhankelijk van het bindings type zijn er mogelijk aanvullende eigenschappen vereist. De [uitvoer configuratie](../articles/azure-functions/functions-bindings-storage-queue-output.md#configuration) van de wachtrij beschrijft de velden die vereist zijn voor een Azure Storage binding van de wachtrij. Met deze extensie kunt u eenvoudig bindingen toevoegen aan het bestand function. json. 

Als u een binding wilt maken, klikt u met de rechter muisknop (CTRL + klik op macOS) het `function.json` bestand in de map http trigger en kiest u **binding toevoegen...** . Volg de aanwijzingen om de volgende bindings eigenschappen te definiëren voor de nieuwe binding:

| Vraag | Waarde | Beschrijving |
| -------- | ----- | ----------- |
| **Bindings richting selecteren** | `out` | De binding is een uitvoer binding. |
| **Binding met richting selecteren...** | `Azure Queue Storage` | De binding is een Azure Storage wachtrij binding. |
| **De naam die wordt gebruikt om deze binding in uw code aan te duiden** | `msg` | Naam die de bindings parameter identificeert waarnaar in uw code wordt verwezen. |
| **De wachtrij waarnaar het bericht wordt verzonden** | `outqueue` | De naam van de wachtrij waarnaar de binding wordt geschreven. Wanneer de *wachtrij* naam niet bestaat, wordt deze door de binding gemaakt bij het eerste gebruik. |
| **Selecteer de instelling in ' lokaal. instelling. json '** | `AzureWebJobsStorage` | De naam van een toepassings instelling die de connection string voor het opslag account bevat. De instelling `AzureWebJobsStorage` bevat de connection string voor het opslag account dat u hebt gemaakt met de functie-app. |

Een binding wordt toegevoegd aan de `bindings` matrix in uw function. json, die er als volgt uitziet:

:::code language="son" source="~/functions-docs-javascript/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="18-24":::