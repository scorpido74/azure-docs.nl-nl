---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 8573a915c572885b53437843b114aa987be4c66b
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198446"
---
Fouten die zijn opgetreden in een Azure Functions kunnen afkomstig zijn van een van de volgende oorsprongen:

- Gebruik van ingebouwde Azure Functions [Triggers en bindingen](..\articles\azure-functions\functions-triggers-bindings.md)
- Aanroepen naar Api's van onderliggende Azure-Services
- Aanroepen naar REST-eind punten
- Aanroepen van client Bibliotheken, pakketten of Api's van derden

De volgende procedures voor het afhandelen van fouten zijn belang rijk om verlies van gegevens of gemiste berichten te voor komen. De aanbevolen procedures voor het afhandelen van fouten zijn onder andere de volgende acties:

- [Application Insights inschakelen](../articles/azure-functions/functions-monitoring.md)
- [Gestructureerde fout afhandeling gebruiken](#use-structured-error-handling)
- [Ontwerpen voor idempotentie](../articles/azure-functions/functions-idempotent.md)
- [Beleid voor opnieuw proberen implementeren](../articles/azure-functions/functions-reliable-event-processing.md) (indien van toepassing)

### <a name="use-structured-error-handling"></a>Gestructureerde fout afhandeling gebruiken

Het vastleggen en publiceren van fouten is van cruciaal belang voor het controleren van de status van uw toepassing. Het bovenste niveau van een functie code moet een try/catch-blok bevatten. In het blok catch kunt u fouten vastleggen en publiceren.

### <a name="retry-support"></a>Ondersteuning voor opnieuw proberen

De volgende triggers hebben ingebouwde ondersteuning voor nieuwe pogingen:

* [Azure Blob Storage](../articles/azure-functions/functions-bindings-storage-blob.md)
* [Azure-wachtrij opslag](../articles/azure-functions/functions-bindings-storage-queue.md)
* [Azure Service Bus (wachtrij/onderwerp)](../articles/azure-functions/functions-bindings-service-bus.md)

Deze triggers worden standaard Maxi maal vijf keer aangevraagd. Na de vijfde nieuwe poging is de Azure Queue-opslag en Azure Service Bus triggers een bericht naar een [verontreinigde wachtrij](..\articles\azure-functions\functions-bindings-storage-queue.md#trigger---poison-messages)schrijven.

U moet het beleid voor opnieuw proberen hand matig implementeren voor alle andere triggers of bindingen typen. Hand matige implementaties kunnen informatie over het schrijven van fouten bevatten in een [wachtrij voor verontreinigde berichten](..\articles\azure-functions\functions-bindings-storage-blob-trigger.md#poison-blobs). Als u naar een verontreinigde wachtrij schrijft, hebt u de mogelijkheid om bewerkingen op een later tijdstip uit te voeren. Deze benadering is hetzelfde als de methode die wordt gebruikt door de Blob Storage-trigger.
