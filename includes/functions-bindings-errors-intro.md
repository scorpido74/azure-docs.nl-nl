---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 629de079f7cc7d95d10f8ff951a47b8b8fc62dad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77474108"
---
Fouten die in een Azure-functie worden aangekaart, kunnen afkomstig zijn van een van de volgende oorsprongen:

- Gebruik van ingebouwde Azure Functions [triggers en bindingen](..\articles\azure-functions\functions-triggers-bindings.md)
- Oproepen naar API's van onderliggende Azure-services
- Oproepen naar REST-eindpunten
- Oproepen naar clientbibliotheken, pakketten of API's van derden

Het volgen van solide foutenafhandelingspraktijken is belangrijk om verlies van gegevens of gemiste berichten te voorkomen. Aanbevolen foutenafhandelingsprocedures omvatten de volgende acties:

- [Application Insights inschakelen](../articles/azure-functions/functions-monitoring.md)
- [Gestructureerde foutafhandeling gebruiken](#use-structured-error-handling)
- [Ontwerp voor idempotentie](../articles/azure-functions/functions-idempotent.md)
- [Beleid voor nieuwe try implementeren](../articles/azure-functions/functions-reliable-event-processing.md) (indien van toepassing)

### <a name="use-structured-error-handling"></a>Gestructureerde foutafhandeling gebruiken

Het vastleggen en publiceren van fouten is essentieel om de status van uw toepassing te controleren. Het bovenste niveau van een functiecode moet een try/catch-blok bevatten. In het catch block u fouten vastleggen en publiceren.

### <a name="retry-support"></a>Ondersteuning opnieuw proberen

De volgende triggers hebben ingebouwde ondersteuning voor opnieuw proberen:

* [Azure Blob-opslag](../articles/azure-functions/functions-bindings-storage-blob.md)
* [Azure-wachtrijopslag](../articles/azure-functions/functions-bindings-storage-queue.md)
* [Azure Service Bus (wachtrij/onderwerp)](../articles/azure-functions/functions-bindings-service-bus.md)

Standaard proberen deze triggers aanvragen tot vijf keer opnieuw. Na de vijfde poging schrijven zowel de Azure Queue-opslag als de azure-servicebus-triggers een bericht naar een [gifwachtrij.](..\articles\azure-functions\functions-bindings-storage-queue-trigger.md#poison-messages)

U moet handmatig beleid opnieuw proberen voor andere triggers of bindingentypen. Handmatige implementaties kunnen het schrijven van foutinformatie naar een [wachtrij voor gifberichten omvatten.](..\articles\azure-functions\functions-bindings-storage-blob-trigger.md#poison-blobs) Door te schrijven naar een gif wachtrij, heb je de mogelijkheid om operaties opnieuw te proberen op een later tijdstip. Deze benadering is dezelfde die wordt gebruikt door de Blob-opslagtrigger.
