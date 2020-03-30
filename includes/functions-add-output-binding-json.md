---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/23/2019
ms.author: glenga
ms.openlocfilehash: 8530f4469a0c25f3c32e652e2b0752c51c28ff3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78191015"
---
Bindingskenmerken worden direct gedefinieerd in het function.json-bestand. Afhankelijk van het bindingstype kunnen extra eigenschappen nodig zijn. De [configuratie van de wachtrijuitvoer](../articles/azure-functions/functions-bindings-storage-queue-output.md#configuration) beschrijft de velden die nodig zijn voor een Azure Storage-wachtrijbinding. De extensie maakt het eenvoudig om bindingen toe te voegen aan het function.json-bestand. 

Als u een binding wilt maken, klikt u `function.json` met de rechtermuisknop (Ctrl+klik op macOS) het bestand in de HttpTrigger-map en kiest u **Binding toevoegen...**. Volg de aanwijzingen om de volgende bindende eigenschappen voor de nieuwe binding te definiëren:

| Vraag | Waarde | Beschrijving |
| -------- | ----- | ----------- |
| **Bindingsrichting selecteren** | `out` | De binding is een outputbinding. |
| **Selecteer binding met richting...** | `Azure Queue Storage` | De binding is een Azure Storage-wachtrijbinding. |
| **De naam die wordt gebruikt om deze binding in uw code te identificeren** | `msg` | Naam die de bindende parameter waarnaar in uw code wordt verwezen identificeert. |
| **De wachtrij waarnaar het bericht wordt verzonden** | `outqueue` | De naam van de wachtrij waar de binding naar schrijft. Wanneer de *queueName* niet bestaat, wordt deze met de binding bij het eerste gebruik gemaakt. |
| **Selecteer instelling in 'local.setting.json'** | `AzureWebJobsStorage` | De naam van een toepassingsinstelling die de verbindingstekenreeks voor het opslagaccount bevat. De `AzureWebJobsStorage` instelling bevat de verbindingstekenreeks voor het opslagaccount dat u met de functie-app hebt gemaakt. |

Er wordt een `bindings` binding toegevoegd aan de array in uw function.json, die er als volgt uit moet zien:

:::code language="son" source="~/functions-docs-javascript/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="18-24":::