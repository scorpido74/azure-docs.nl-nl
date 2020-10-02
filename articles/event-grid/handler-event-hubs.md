---
title: Event hub als gebeurtenis-handler voor Azure Event Grid gebeurtenissen
description: Hierin wordt beschreven hoe u Event hubs kunt gebruiken als gebeurtenis-handlers voor Azure Event Grid-gebeurtenissen.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 4fb8027290831dbfed4b6d202d61d2d689274828
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/01/2020
ms.locfileid: "91629621"
---
# <a name="event-hub-as-an-event-handler-for-azure-event-grid-events"></a>Event hub als gebeurtenis-handler voor Azure Event Grid gebeurtenissen
Een gebeurtenis-handler is de plaats waar de gebeurtenis wordt verzonden. De handler heeft een actie nodig om de gebeurtenis te verwerken. Verschillende Azure-Services worden automatisch geconfigureerd voor het afhandelen van gebeurtenissen en **Azure Event hubs** is een hiervan. 

Gebruik **Event hubs** wanneer uw oplossing gebeurtenissen van Event grid sneller ontvangt dan de gebeurtenissen kan verwerken. Zodra de gebeurtenissen zich in een Event Hub bevinden, kan uw toepassing gebeurtenissen uit de Event Hub op basis van een eigen planning verwerken. U kunt uw gebeurtenis verwerking schalen om de binnenkomende gebeurtenissen te verwerken.

## <a name="tutorials"></a>Zelfstudies
Zie de volgende voorbeelden: 

|Titel  |Beschrijving  |
|---------|---------|
| [Snelstartgids: aangepaste gebeurtenissen naar Azure Event Hubs door sturen met Azure CLI](custom-event-to-eventhub.md) | Hiermee wordt een aangepaste gebeurtenis verzonden naar een Event Hub voor verwerking door een toepassing. |
| [Resource Manager-sjabloon: een Event Grid aangepast onderwerp maken en gebeurtenissen naar een Event Hub verzenden](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| Een resource manager-sjabloon voor het maken van een abonnement voor een aangepast onderwerp. Er worden gebeurtenissen naar een Azure-Event Hubs verzonden. |

## <a name="message-properties"></a>Bericht eigenschappen
Als u een **Event hub** als gebeurtenis-handler gebruikt voor gebeurtenissen uit Event grid, zijn dit de eigenschappen die u ontvangt in de bericht koppen: 

| Naam van eigenschap | Beschrijving |
| ------------- | ----------- | 
| AEG-abonnements naam | De naam van het gebeurtenis abonnement. |
| AEG-levering | <p>Het aantal pogingen dat is gedaan voor de gebeurtenis.</p> <p>Voor beeld: "1"</p> |
| AEG-gebeurtenis-type | <p>Het type gebeurtenis.</p><p> Voor beeld: ' micro soft. storage. blobCreated '</p> | 
| AEG-meta gegevens-versie | <p>De meta gegevens versie van de gebeurtenis.</p> <p>Voor beeld: "1".</p><p> Voor **Event grid-gebeurtenis schema**vertegenwoordigt deze eigenschap de versie van de meta gegevens en voor het **Cloud-gebeurtenis schema**, het vertegenwoordigt de **specificatie versie**. </p>|
| AEG-gegevens versie | <p>De gegevens versie van de gebeurtenis.</p><p>Voor beeld: "1".</p><p>Voor **Event grid-gebeurtenis schema**vertegenwoordigt deze eigenschap de gegevens versie en voor het **Cloud-gebeurtenis schema**, maar dit is niet van toepassing.</p> |
| AEG-uitvoer-gebeurtenis-id | ID van de Event Grid gebeurtenis. |

## <a name="rest-examples-for-put"></a>REST-voor beelden (voor PUT)


### <a name="event-hub"></a>Event Hub

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "EventHub",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUBS NAMESPACE NAME>/eventhubs/<EVENT HUB NAME>"
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

### <a name="event-hub---delivery-with-managed-identity"></a>Event hub-levering met beheerde identiteit

```json
{
    "properties": {
        "deliveryWithResourceIdentity": 
        {
            "identity": 
            {
                "type": "SystemAssigned"
            },
            "destination": 
            {
                "endpointType": "EventHub",
                "properties": 
                {
                    "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUBS NAMESPACE NAME>/eventhubs/<EVENT HUB NAME>"
                }
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

## <a name="next-steps"></a>Volgende stappen
Zie het artikel over [gebeurtenis-handlers](event-handlers.md) voor een lijst met ondersteunde gebeurtenis-handlers. 
