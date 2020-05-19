---
title: Service Bus-wacht rijen en-onderwerpen als gebeurtenis-handlers voor Azure Event Grid gebeurtenissen
description: Hierin wordt beschreven hoe u Service Bus-wacht rijen en-onderwerpen kunt gebruiken als gebeurtenis-handlers voor Azure Event Grid-gebeurtenissen.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: spelluru
ms.openlocfilehash: 201d3203d845ce84207d103750709fe2ff93f022
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83598575"
---
# <a name="service-bus-queues-and-topics-as-event-handlers-for-azure-event-grid-events"></a>Service Bus-wacht rijen en-onderwerpen als gebeurtenis-handlers voor Azure Event Grid gebeurtenissen
Een gebeurtenis-handler is de plaats waar de gebeurtenis wordt verzonden. De handler heeft een aantal verdere acties nodig om de gebeurtenis te verwerken. Verschillende Azure-Services worden automatisch geconfigureerd voor het afhandelen van gebeurtenissen en **Azure service bus** is een hiervan. 

U kunt een service wachtrij of onderwerp als een handler voor gebeurtenissen uit Event Grid gebruiken. 

## <a name="service-bus-queues"></a>Service Bus-wachtrijen
U kunt gebeurtenissen in Event Grid rechtstreeks naar Service Bus wacht rijen routeren voor gebruik in buffer-of opdracht & controle scenario's in bedrijfs toepassingen.

Selecteer in de Azure Portal tijdens het maken van een gebeurtenis abonnement **Service Bus wachtrij** als eindpunt type en klik vervolgens op **Selecteer een eind punt** om een service bus wachtrij te kiezen.

### <a name="using-cli-to-add-a-service-bus-queue-handler"></a>CLI gebruiken om een Service Bus-wachtrij-handler toe te voegen

Voor Azure CLI wordt het volgende voor beeld geabonneerd en wordt een event grid-onderwerp verbonden met een Service Bus wachtrij:

```azurecli-interactive
az eventgrid event-subscription create \
    --name <my-event-subscription> \
    --source-resource-id /subscriptions/{SubID}/resourceGroups/{RG}/providers/Microsoft.EventGrid/topics/topic1 \
    --endpoint-type servicebusqueue \
    --endpoint /subscriptions/{SubID}/resourceGroups/TestRG/providers/Microsoft.ServiceBus/namespaces/ns1/queues/queue1
```

## <a name="service-bus-topics"></a>Service Bus onderwerpen

U kunt gebeurtenissen in Event Grid rechtstreeks door sturen naar Service Bus-onderwerpen om Azure-systeem gebeurtenissen te verwerken met Service Bus-onderwerpen, of voor opdracht & bericht scenario's voor het beheren van berichten.

Selecteer in de Azure Portal tijdens het maken van een gebeurtenis abonnement **Service Bus onderwerp** als eindpunt type en klik vervolgens op **selecteren en eind punt** om een service bus onderwerp te kiezen.

### <a name="using-cli-to-add-a-service-bus-topic-handler"></a>CLI gebruiken om een Service Bus-onderwerpassistent toe te voegen

Voor Azure CLI wordt het volgende voor beeld geabonneerd en wordt een event grid-onderwerp verbonden met een Service Bus wachtrij:

```azurecli-interactive
az eventgrid event-subscription create \
    --name <my-event-subscription> \
    --source-resource-id /subscriptions/{SubID}/resourceGroups/{RG}/providers/Microsoft.EventGrid/topics/topic1 \
    --endpoint-type servicebustopic \
    --endpoint /subscriptions/{SubID}/resourceGroups/TestRG/providers/Microsoft.ServiceBus/namespaces/ns1/topics/topic1
```

## <a name="message-properties"></a>Bericht eigenschappen
Als u een **Service Bus onderwerp of wachtrij** als gebeurtenis-handler voor gebeurtenissen van Event grid gebruikt, stelt u de volgende bericht koppen in: 

| Naam van eigenschap | Beschrijving |
| ------------- | ----------- | 
| AEG-abonnements naam | De naam van het gebeurtenis abonnement. |
| AEG-levering | <p>Het aantal pogingen dat is gedaan voor de gebeurtenis.</p> <p>Voor beeld: "1"</p> |
| AEG-gebeurtenis-type | <p>Het type gebeurtenis.</p><p> Voor beeld: ' micro soft. storage. blobCreated '</p> | 
| AEG-meta gegevens-versie | <p>De meta gegevens versie van de gebeurtenis.</p> <p>Voor beeld: "1".</p><p> Voor **Event grid-gebeurtenis schema**vertegenwoordigt deze eigenschap de versie van de meta gegevens en voor het **Cloud-gebeurtenis schema**, het vertegenwoordigt de **specificatie versie**. </p>|
| AEG-gegevens versie | <p>De gegevens versie van de gebeurtenis.</p><p>Voor beeld: "1".</p><p>Voor **Event grid-gebeurtenis schema**vertegenwoordigt deze eigenschap de gegevens versie en voor het **Cloud-gebeurtenis schema**, maar dit is niet van toepassing.</p> |

## <a name="message-headers"></a>Bericht koppen
Bij het verzenden van een gebeurtenis naar een Service Bus wachtrij of onderwerp als een brokerd bericht, `messageid` is het brokered-bericht **de gebeurtenis-id**.

De gebeurtenis-ID wordt gehandhaafd over de herlevering van de gebeurtenis, zodat u dubbele leveringen kunt voor komen door **Duplicaten detectie** in te scha kelen op de service bus-entiteit. Het is raadzaam om de duur van de duplicaten detectie in te scha kelen op de Service Bus entiteit voor de TTL (time-to-Live) van de gebeurtenis of de maximale nieuwe duur, afhankelijk van wat langer is.

## <a name="next-steps"></a>Volgende stappen
Zie het artikel over [gebeurtenis-handlers](event-handlers.md) voor een lijst met ondersteunde gebeurtenis-handlers. 
