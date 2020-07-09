---
title: Azure function als gebeurtenis-handler voor Azure Event Grid gebeurtenissen
description: Hierin wordt beschreven hoe u Azure functions kunt gebruiken als gebeurtenis-handlers voor Event Grid-gebeurtenissen.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 8e48949bb5fecdf370fdf23146209ad757ffa062
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86105758"
---
# <a name="azure-function-as-an-event-handler-for-event-grid-events"></a>Azure function als gebeurtenis-handler voor Event Grid gebeurtenissen

Een gebeurtenis-handler is de plaats waar de gebeurtenis wordt verzonden. De handler heeft een actie nodig om de gebeurtenis te verwerken. Verschillende Azure-Services worden automatisch geconfigureerd voor het afhandelen van gebeurtenissen en **Azure functions** is een hiervan. 

Gebruik **Azure functions** in een serverloze architectuur om te reageren op gebeurtenissen van Event grid. Wanneer u een Azure-functie als handler gebruikt, gebruikt u de trigger Event Grid in plaats van de algemene HTTP-trigger. Event Grid triggers worden door Event Grid automatisch gevalideerd. Bij algemene HTTP-triggers moet u het [validatie antwoord](webhook-event-delivery.md) zelf implementeren.

Zie [Event grid trigger voor Azure functions](../azure-functions/functions-bindings-event-grid.md) voor een overzicht van het gebruik van de Event grid trigger in functions voor meer informatie.

## <a name="tutorials"></a>Zelfstudies

|Titel  |Beschrijving  |
|---------|---------|
| [Quick Start: gebeurtenissen met functie afhandelen](custom-event-to-function.md) | Hiermee wordt een aangepaste gebeurtenis verzonden naar een functie voor verwerking. |
| [Zelf studie: het formaat van geüploade afbeeldingen automatisch wijzigen met behulp van Event Grid](resize-images-on-storage-blob-upload-event.md) | Gebruikers uploaden afbeeldingen via web-app naar het opslag account. Wanneer een opslag-BLOB wordt gemaakt, verzendt Event Grid een gebeurtenis naar de functie-app, waarmee het formaat van de geüploade afbeelding wordt gewijzigd. |
| [Zelf studie: stream big data naar een Data Warehouse](event-grid-event-hubs-integration.md) | Wanneer Event Hubs een opname bestand maakt, wordt Event Grid een gebeurtenis naar een functie-app verzonden. De app haalt het opname bestand op en migreert gegevens naar een Data Warehouse. |
| [Zelf studie: Azure Service Bus voor voor beelden van Azure Event Grid-integratie](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid verzendt berichten van Service Bus onderwerp naar een functie-app en een logische app. |

## <a name="rest-example-for-put"></a>REST-voor beeld (voor PUT)

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "AzureFunction",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Web/sites/<FUNCTION APP NAME>/functions/<FUNCTION NAME>",
                "maxEventsPerBatch": 1,
                "preferredBatchSizeInKilobytes": 64
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

## <a name="next-steps"></a>Volgende stappen
Zie het artikel over [gebeurtenis-handlers](event-handlers.md) voor een lijst met ondersteunde gebeurtenis-handlers. 
