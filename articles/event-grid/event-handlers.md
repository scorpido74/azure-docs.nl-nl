---
title: Gebeurtenishandlers voor Azure Event Grid
description: Beschrijft ondersteunde gebeurtenishandlers voor Azure Event Grid. Azure Automation, Functions, Event Hubs, Hybrid Connections, Logic Apps, Service Bus, Queue Storage, Webhooks.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: 7ea00d663264e902c1818f7a4684e90eccd97b28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265049"
---
# <a name="event-handlers-in-azure-event-grid"></a>Gebeurtenishandlers in Azure Event Grid

Een gebeurtenishandler is de plaats waar de gebeurtenis wordt verzonden. De handler onderneemt verdere actie om de gebeurtenis te verwerken. Verschillende Azure-services worden automatisch geconfigureerd om gebeurtenissen te verwerken. U ook elke WebHook gebruiken voor het afhandelen van gebeurtenissen. De WebHook hoeft niet te worden gehost in Azure om gebeurtenissen te verwerken. Event Grid ondersteunt alleen HTTPS WebHook-eindpunten.

In dit artikel vindt u koppelingen naar inhoud voor elke gebeurtenishandler.

## <a name="azure-automation"></a>Azure Automation

Gebruik Azure Automation om gebeurtenissen te verwerken met geautomatiseerde runbooks.

|Titel  |Beschrijving  |
|---------|---------|
|[Zelfstudie: Azure Automation with Event Grid en Microsoft Teams](ensure-tags-exists-on-new-virtual-machines.md) |Maak een virtuele machine, die een gebeurtenis verzendt. De gebeurtenis activeert een automation-runbook die de virtuele machine tagt en activeert een bericht dat naar een Microsoft Teams-kanaal wordt verzonden. |

## <a name="azure-functions"></a>Azure Functions

Gebruik Azure-functies voor serverloze respons op gebeurtenissen.

Wanneer u Azure Functions als de handler gebruikt, gebruikt u de Event Grid-trigger in plaats van algemene HTTP-triggers. Functie-triggers van Event Grid worden namelijk automatisch gevalideerd. Bij gebruik van algemene HTTP-triggers moet u een [validatie-antwoord](security-authentication.md#webhook-event-delivery) implementeren.

|Titel  |Beschrijving  |
|---------|---------|
| [Snelstart: gebeurtenissen afhandelen met functie](custom-event-to-function.md) | Hiermee stuurt u een aangepaste gebeurtenis naar een functie voor verwerking. |
| [Trigger voor gebeurtenisraster voor Azure-functies](../azure-functions/functions-bindings-event-grid.md) | Overzicht van het gebruik van de trigger van gebeurtenisraster in functies. |
| [Zelfstudie: het wijzigen van het formaat van geüploade afbeeldingen automatiseren met gebeurtenisraster](resize-images-on-storage-blob-upload-event.md) | Gebruikers uploaden afbeeldingen via de web-app naar een opslagaccount. Wanneer een opslagblob wordt gemaakt, stuurt gebeurtenisraster een gebeurtenis naar de functie-app, waarmee het formaat van de geüploade afbeelding wordt gewijzigd. |
| [Zelfstudie: big data streamen naar een datawarehouse](event-grid-event-hubs-integration.md) | Wanneer Gebeurtenishubs een Capture-bestand maakt, stuurt gebeurtenisraster een gebeurtenis naar een functie-app. De app haalt het capture-bestand op en migreert gegevens naar een gegevensmagazijn. |
| [Zelfstudie: voorbeelden van azure servicebus naar Azure Event Grid-integratie](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid stuurt berichten vanuit het onderwerp Service Bus naar de functie-app en logische app. |

## <a name="event-hubs"></a>Event Hubs

Gebruik Event Hubs wanneer uw oplossing gebeurtenissen sneller opgang krijgt dan wanneer de gebeurtenissen kunnen worden verwerkt. Uw toepassing verwerkt de gebeurtenissen vanuit Event Hubs in het eigen schema. U uw gebeurtenisverwerking schalen om de binnenkomende gebeurtenissen af te handelen.

Gebeurtenishubs kunnen fungeren als gebeurtenisbron of gebeurtenishandler. In het volgende artikel ziet u hoe u gebeurtenishubs als handler gebruiken.

|Titel  |Beschrijving  |
|---------|---------|
| [Snelstart: aangepaste gebeurtenissen routeren naar Azure Event Hubs met Azure CLI en Event Grid](custom-event-to-eventhub.md) | Hiermee verzendt u een aangepaste gebeurtenis naar een gebeurtenishub voor verwerking door een toepassing. |
| [Resource Manager-sjabloon: aangepast onderwerp en eindpunt van gebeurtenishubs](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| Een resourcemanagersjabloon waarmee een abonnement wordt gemaakt voor een aangepast onderwerp. Het verzendt gebeurtenissen naar een Azure Event Hubs. |

Zie [Gebeurtenishubsbron](event-sources.md#event-hubs)voor voorbeelden van gebeurtenishubs als bron .

## <a name="hybrid-connections"></a>Hybride verbindingen

Gebruik Hybride verbindingen voor Azure Relay om gebeurtenissen te verzenden naar toepassingen die zich binnen een bedrijfsnetwerk bevinden en geen openbaar toegankelijk eindpunt hebben.

|Titel  |Beschrijving  |
|---------|---------|
| [Zelfstudie: gebeurtenissen naar hybride verbinding verzenden](custom-event-to-hybrid-connection.md) | Hiermee verzendt u een aangepaste gebeurtenis naar een bestaande hybride verbinding voor verwerking door een listenertoepassing. |

## <a name="logic-apps"></a>Logic Apps

Gebruik Logic Apps om bedrijfsprocessen te automatiseren om op gebeurtenissen te reageren.

|Titel  |Beschrijving  |
|---------|---------|
| [Zelfstudie: wijzigingen in virtuele machines controleren met Azure Event Grid en Logic Apps](monitor-virtual-machine-changes-event-grid-logic-app.md) | Een logische app controleert wijzigingen in een virtuele machine en stuurt e-mails over deze wijzigingen. |
| [Zelfstudie: e-mailmeldingen verzenden over Azure IoT Hub-gebeurtenissen met Logic Apps](publish-iot-hub-events-to-logic-apps.md) | Een logische app stuurt een meldingsmail telkens wanneer een apparaat aan uw IoT-hub wordt toegevoegd. |
| [Zelfstudie: voorbeelden van azure servicebus naar Azure Event Grid-integratie](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid stuurt berichten vanuit het onderwerp Service Bus naar de functie-app en logische app. |

## <a name="service-bus"></a>Service Bus

### <a name="service-bus-queues"></a>Service Bus-wachtrijen

U gebeurtenissen in gebeurtenisraster rechtstreeks naar wachtrijen voor servicebus routeren voor gebruik in buffering of opdracht & controlescenario's in bedrijfstoepassingen.

Selecteer in de Azure-portal tijdens het maken van een gebeurtenisabonnement de optie 'Servicebuswachtrij' als eindpunttype en klik vervolgens op 'selecteer een eindpunt' om een wachtrij voor servicebus te kiezen.

#### <a name="using-cli-to-add-a-service-bus-queue-handler"></a>CLI gebruiken om een servicebuswachtrijhandler toe te voegen

Voor Azure CLI wordt in het volgende voorbeeld een gebeurtenisrasteronderwerp geabonneerd op een wachtrij voor servicebus:

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

U gebeurtenissen in gebeurtenisraster rechtstreeks naar servicebus-onderwerpen routeren om Azure-systeemgebeurtenissen met ServiceBus-onderwerpen te verwerken of om berichtenscenario's voor de opdracht & beheren.

Selecteer in de Azure-portal tijdens het maken van een gebeurtenisabonnement 'Servicebusonderwerp' als eindpunttype en klik vervolgens op 'selecteren en eindpunt' om een servicebusonderwerp te kiezen.

#### <a name="using-cli-to-add-a-service-bus-topic-handler"></a>CLI gebruiken om een servicebusonderwerphandler toe te voegen

Voor Azure CLI wordt in het volgende voorbeeld een gebeurtenisrasteronderwerp geabonneerd op een wachtrij voor servicebus:

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

Gebruik wachtrijopslag om gebeurtenissen te ontvangen die moeten worden ingetrokken. U wachtrijopslag gebruiken wanneer u een lang lopend proces hebt dat te lang duurt om te reageren. Door gebeurtenissen naar wachtrijopslag te verzenden, kan de app gebeurtenissen op eigen planning optrekken en verwerken.

|Titel  |Beschrijving  |
|---------|---------|
| [Snelstart: aangepaste gebeurtenissen doorsturen naar Azure Queue-opslag met Azure CLI en gebeurtenisraster](custom-event-to-queue-storage.md) | Beschrijft hoe u aangepaste gebeurtenissen naar een wachtrijopslag verzendt. |

## <a name="webhooks"></a>WebHooks

Gebruik webhooks voor aanpasbare eindpunten die reageren op gebeurtenissen.

|Titel  |Beschrijving  |
|---------|---------|
| Snelstart: aangepaste gebeurtenissen maken en routeren met - [Azure CLI,](custom-event-quickstart.md) [PowerShell](custom-event-quickstart-powershell.md)en [portal](custom-event-quickstart-portal.md). | Hier ziet u hoe u aangepaste gebeurtenissen naar een WebHook verzendt. |
| Snelstart: routeblob-opslaggebeurtenissen naar een aangepast webeindpunt met - [Azure CLI,](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) [PowerShell](../storage/blobs/storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fevent-grid%2ftoc.json)en [portal](blob-event-quickstart-portal.md). | Hier ziet u hoe u blobopslaggebeurtenissen naar een WebHook verzendt. |
| [Snelstart: containerregistergebeurtenissen verzenden](../container-registry/container-registry-event-grid-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Hier ziet u hoe u Azure CLI gebruikt om containerregistergebeurtenissen te verzenden. |
| [Overzicht: gebeurtenissen ontvangen naar een HTTP-eindpunt](receive-events.md) | Beschrijft hoe u een HTTP-eindpunt valideren om gebeurtenissen van een gebeurtenisabonnement te ontvangen en gebeurtenissen te ontvangen en te deserialiseren. |

## <a name="next-steps"></a>Volgende stappen

* Zie [Een inleiding tot Event Grid](overview.md) voor een inleiding tot Event Grid.
* Zie [Aangepaste gebeurtenissen maken en routeren met Azure Event Grid](custom-event-quickstart.md)om snel aan de slag te gaan met gebeurtenisraster.
