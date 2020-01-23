---
title: Gebeurtenis-handlers van Azure Event Grid
description: Hierin worden ondersteunde gebeurtenis-handlers voor Azure Event Grid beschreven. De handler heeft een aantal verdere acties nodig om de gebeurtenis te verwerken.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: 3cef32cd204e8bd4b21353cf66575a721315b387
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2020
ms.locfileid: "76511311"
---
# <a name="event-handlers-in-azure-event-grid"></a>Gebeurtenis-handlers in Azure Event Grid

Een gebeurtenis-handler is de plaats waar de gebeurtenis wordt verzonden. De handler heeft een aantal verdere acties nodig om de gebeurtenis te verwerken. Verschillende Azure-Services worden automatisch geconfigureerd voor het verwerken van gebeurtenissen. U kunt ook een webhook gebruiken voor het verwerken van gebeurtenissen. De webhook hoeft niet te worden gehost in azure voor het verwerken van gebeurtenissen. Event Grid ondersteunt alleen HTTPS-webhook-eind punten.

Dit artikel bevat koppelingen naar inhoud voor elke gebeurtenis-handler.

## <a name="azure-automation"></a>Azure Automation

Gebruik Azure Automation om gebeurtenissen met geautomatiseerde runbooks te verwerken.

|Titel  |Beschrijving  |
|---------|---------|
|[Zelf studie: Azure Automation met Event Grid en micro soft teams](ensure-tags-exists-on-new-virtual-machines.md) |Maak een virtuele machine die een gebeurtenis verzendt. De gebeurtenis activeert een Automation-runbook waarmee de virtuele machine wordt gecodeerd en een bericht wordt geactiveerd dat naar een micro soft teams-kanaal wordt verzonden. |

## <a name="azure-functions"></a>Azure Functions

Gebruik Azure Functions voor serverloze reacties op gebeurtenissen.

Wanneer u Azure Functions als de handler gebruikt, gebruikt u de Event Grid-trigger in plaats van algemene HTTP-triggers. Functie-triggers van Event Grid worden namelijk automatisch gevalideerd. Bij gebruik van algemene HTTP-triggers moet u een [validatie-antwoord](security-authentication.md#webhook-event-delivery) implementeren.

|Titel  |Beschrijving  |
|---------|---------|
| [Quick Start: gebeurtenissen met functie afhandelen](custom-event-to-function.md) | Hiermee wordt een aangepaste gebeurtenis verzonden naar een functie voor verwerking. |
| [Event Grid trigger voor Azure Functions](../azure-functions/functions-bindings-event-grid.md) | Overzicht van het gebruik van de Event Grid trigger in functions. |
| [Zelf studie: het formaat van geüploade afbeeldingen automatisch wijzigen met behulp van Event Grid](resize-images-on-storage-blob-upload-event.md) | Gebruikers uploaden afbeeldingen via web-app naar het opslag account. Wanneer een opslag-BLOB wordt gemaakt, verzendt Event Grid een gebeurtenis naar de functie-app, waarmee het formaat van de geüploade afbeelding wordt gewijzigd. |
| [Zelf studie: stream big data naar een Data Warehouse](event-grid-event-hubs-integration.md) | Wanneer Event Hubs een opname bestand maakt, wordt Event Grid een gebeurtenis naar een functie-app verzonden. De app haalt het opname bestand op en migreert gegevens naar een Data Warehouse. |
| [Zelf studie: Azure Service Bus voor voor beelden van Azure Event Grid-integratie](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid verzendt berichten van Service Bus onderwerp naar de functie app en de logische app. |

## <a name="event-hubs"></a>Event Hubs

Gebruik Event Hubs wanneer uw oplossing sneller gebeurtenissen ontvangt dan de gebeurtenissen kan verwerken. Uw toepassing verwerkt de gebeurtenissen van Event Hubs op basis van het eigen schema. U kunt uw gebeurtenis verwerking schalen om de binnenkomende gebeurtenissen te verwerken.

Event Hubs kan fungeren als een gebeurtenis bron of gebeurtenis-handler. In het volgende artikel ziet u hoe u Event Hubs als handler gebruikt.

|Titel  |Beschrijving  |
|---------|---------|
| [Snelstartgids: aangepaste gebeurtenissen door sturen naar Azure Event Hubs met Azure CLI en Event Grid](custom-event-to-eventhub.md) | Hiermee wordt een aangepaste gebeurtenis verzonden naar een Event Hub voor verwerking door een toepassing. |
| [Resource Manager-sjabloon: aangepast onderwerp en Event Hubs eind punt](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| Een resource manager-sjabloon voor het maken van een abonnement voor een aangepast onderwerp. Er worden gebeurtenissen naar een Azure-Event Hubs verzonden. |

Zie [Event hubs source](event-sources.md#event-hubs)(Engelstalig) voor voor beelden van Event hubs als bron.

## <a name="hybrid-connections"></a>Hybride verbindingen

Gebruik Azure Relay Hybride verbindingen om gebeurtenissen te verzenden naar toepassingen die zich binnen een bedrijfs netwerk bevinden en geen openbaar toegankelijk eind punt hebben.

|Titel  |Beschrijving  |
|---------|---------|
| [Zelf studie: gebeurtenissen verzenden naar hybride verbinding](custom-event-to-hybrid-connection.md) | Hiermee wordt een aangepaste gebeurtenis verzonden naar een bestaande hybride verbinding voor verwerking door een listener-toepassing. |

## <a name="logic-apps"></a>Logische apps

Gebruik Logic Apps om bedrijfs processen te automatiseren voor het reageren op gebeurtenissen.

|Titel  |Beschrijving  |
|---------|---------|
| [Zelf studie: wijzigingen van virtuele machines bewaken met Azure Event Grid en Logic Apps](monitor-virtual-machine-changes-event-grid-logic-app.md) | Een logische app controleert wijzigingen aan een virtuele machine en stuurt e-mails over die wijzigingen. |
| [Zelf studie: e-mail meldingen over Azure IoT Hub-gebeurtenissen verzenden met Logic Apps](publish-iot-hub-events-to-logic-apps.md) | Een logische app verzendt een e-mail melding wanneer een apparaat wordt toegevoegd aan uw IoT-hub. |
| [Zelf studie: Azure Service Bus voor voor beelden van Azure Event Grid-integratie](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid verzendt berichten van Service Bus onderwerp naar de functie app en de logische app. |

## <a name="service-bus"></a>Service Bus

### <a name="service-bus-queues"></a>Service Bus-wachtrijen

U kunt gebeurtenissen in Event Grid rechtstreeks naar Service Bus wacht rijen routeren voor gebruik in buffer-of opdracht & controle scenario's in bedrijfs toepassingen.

Selecteer in de Azure Portal tijdens het maken van een gebeurtenis abonnement ' Service Bus wachtrij ' als eindpunt type en klik vervolgens op Selecteer een eind punt om een Service Bus wachtrij te kiezen.

#### <a name="using-cli-to-add-a-service-bus-queue-handler"></a>CLI gebruiken om een Service Bus-wachtrij-handler toe te voegen

Voor Azure CLI wordt het volgende voor beeld geabonneerd en wordt een event grid-onderwerp verbonden met een Service Bus wachtrij:

```azurecli-interactive
# If you haven't already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid event-subscription create \
    --name <my-event-subscription> \
    --source-resource-id /subscriptions/{SubID}/resourceGroups/{RG}/providers/Microsoft.EventGrid/topics/topic1 \
    --endpoint-type servicebusqueue \
    --endpoint /subscriptions/{SubID}/resourceGroups/TestRG/providers/Microsoft.ServiceBus/namespaces/ns1/queues/queue1
```

### <a name="service-bus-topics"></a>Service Bus-onderwerpen

U kunt gebeurtenissen in Event Grid rechtstreeks naar Service Bus onderwerpen routeren om Azure-systeem gebeurtenissen te verwerken met Service Bus-onderwerpen, of voor opdracht & bericht scenario's voor het beheren van berichten.

Selecteer in de Azure Portal tijdens het maken van een gebeurtenis abonnement ' Service Bus onderwerp ' als eindpunt type en klik vervolgens op ' selecteren en eind punt ' om een Service Bus onderwerp te kiezen.

#### <a name="using-cli-to-add-a-service-bus-topic-handler"></a>CLI gebruiken om een Service Bus-onderwerpassistent toe te voegen

Voor Azure CLI wordt het volgende voor beeld geabonneerd en wordt een event grid-onderwerp verbonden met een Service Bus wachtrij:

```azurecli-interactive
# If you haven't already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid event-subscription create \
    --name <my-event-subscription> \
    --source-resource-id /subscriptions/{SubID}/resourceGroups/{RG}/providers/Microsoft.EventGrid/topics/topic1 \
    --endpoint-type servicebustopic \
    --endpoint /subscriptions/{SubID}/resourceGroups/TestRG/providers/Microsoft.ServiceBus/namespaces/ns1/topics/topic1
```

## <a name="queue-storage"></a>Queue Storage

Gebruik Queue Storage om gebeurtenissen te ontvangen die moeten worden opgehaald. U kunt Queue Storage gebruiken wanneer u een langlopend proces hebt dat te lang duurt om te reageren. Door gebeurtenissen te verzenden naar de wachtrij opslag, kan de app op basis van een eigen planning gebeurtenissen ophalen en verwerken.

|Titel  |Beschrijving  |
|---------|---------|
| [Snelstartgids: aangepaste gebeurtenissen naar Azure Queue-opslag door sturen met Azure CLI en Event Grid](custom-event-to-queue-storage.md) | Hierin wordt beschreven hoe u aangepaste gebeurtenissen naar een wachtrij opslag verzendt. |

## <a name="webhooks"></a>WebHooks

Gebruik webhooks voor aanpas bare eind punten die reageren op gebeurtenissen.

|Titel  |Beschrijving  |
|---------|---------|
| Snelstartgids: aangepaste gebeurtenissen maken en routeren met- [Azure cli](custom-event-quickstart.md), [Power shell](custom-event-quickstart-powershell.md)en [Portal](custom-event-quickstart-portal.md). | Laat zien hoe u aangepaste gebeurtenissen naar een webhook verzendt. |
| Snelstartgids: Blob Storage-gebeurtenissen door sturen naar een aangepast webeindpunt met- [Azure cli](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json), [Power shell](../storage/blobs/storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fevent-grid%2ftoc.json)en [Portal](blob-event-quickstart-portal.md). | Toont hoe Blob Storage-gebeurtenissen naar een webhook worden verzonden. |
| [Snelstartgids: container register gebeurtenissen verzenden](../container-registry/container-registry-event-grid-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Laat zien hoe u met Azure CLI Container Registry-gebeurtenissen kunt verzenden. |
| [Overzicht: gebeurtenissen ontvangen naar een HTTP-eind punt](receive-events.md) | Hierin wordt beschreven hoe u een HTTP-eind punt kunt valideren om gebeurtenissen van een gebeurtenis abonnement te ontvangen en gebeurtenissen te ontvangen en te deserialiseren. |

## <a name="next-steps"></a>Volgende stappen

* Zie [Een inleiding tot Event Grid](overview.md) voor een inleiding tot Event Grid.
* Als u wilt snel aan de slag met Event Grid, Zie [aangepaste gebeurtenissen maken en routeren met Azure Event Grid](custom-event-quickstart.md).
