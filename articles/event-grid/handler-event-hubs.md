---
title: Event hub als gebeurtenis-handler voor Azure Event Grid gebeurtenissen
description: Hierin wordt beschreven hoe u Event hubs kunt gebruiken als gebeurtenis-handlers voor Azure Event Grid-gebeurtenissen.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: spelluru
ms.openlocfilehash: c9ce4e7da51005dcb06c9df420d80f4d2c7b93e9
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83598365"
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
Als u een **Event hub** als gebeurtenis-handler voor gebeurtenissen van Event grid gebruikt, stelt u de volgende bericht koppen in: 

| Naam van eigenschap | Beschrijving |
| ------------- | ----------- | 
| AEG-abonnements naam | De naam van het gebeurtenis abonnement. |
| AEG-levering | <p>Het aantal pogingen dat is gedaan voor de gebeurtenis.</p> <p>Voor beeld: "1"</p> |
| AEG-gebeurtenis-type | <p>Het type gebeurtenis.</p><p> Voor beeld: ' micro soft. storage. blobCreated '</p> | 
| AEG-meta gegevens-versie | <p>De meta gegevens versie van de gebeurtenis.</p> <p>Voor beeld: "1".</p><p> Voor **Event grid-gebeurtenis schema**vertegenwoordigt deze eigenschap de versie van de meta gegevens en voor het **Cloud-gebeurtenis schema**, het vertegenwoordigt de **specificatie versie**. </p>|
| AEG-gegevens versie | <p>De gegevens versie van de gebeurtenis.</p><p>Voor beeld: "1".</p><p>Voor **Event grid-gebeurtenis schema**vertegenwoordigt deze eigenschap de gegevens versie en voor het **Cloud-gebeurtenis schema**, maar dit is niet van toepassing.</p> |
| AEG-uitvoer-gebeurtenis-id | ID van de Event Grid gebeurtenis. |


## <a name="next-steps"></a>Volgende stappen
Zie het artikel over [gebeurtenis-handlers](event-handlers.md) voor een lijst met ondersteunde gebeurtenis-handlers. 
