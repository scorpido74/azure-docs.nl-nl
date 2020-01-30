---
title: Azure Event Grid gebeurtenis bronnen
description: In dit artikel worden de ondersteunde gebeurtenis bronnen voor Azure Event Grid beschreven. Een gebeurtenis bron is de plaats waar de gebeurtenis plaatsvindt.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: spelluru
ms.openlocfilehash: 3a52d906d958fdfd1d7e09c6229b12af3204bc52
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2020
ms.locfileid: "76846381"
---
# <a name="event-sources-in-azure-event-grid"></a>Gebeurtenis bronnen in Azure Event Grid

Een gebeurtenis bron is de plaats waar de gebeurtenis plaatsvindt. Verschillende Azure-Services worden automatisch geconfigureerd voor het verzenden van gebeurtenissen. U kunt ook aangepaste toepassingen maken die gebeurtenissen verzenden. Aangepaste toepassingen hoeven niet te worden gehost in azure om Event Grid te gebruiken voor gebeurtenis distributie.

Dit artikel bevat koppelingen naar inhoud voor elke bron van de gebeurtenis.

## <a name="azure-subscriptions"></a>Azure-abonnementen

Abonneer u op Azure-abonnementen om te reageren op wijzigingen in resources in een Azure-abonnement.

|Titel |Beschrijving  |
|---------|---------|
| [Zelf studie: Azure Automation met Event Grid en micro soft teams](ensure-tags-exists-on-new-virtual-machines.md) |Maak een virtuele machine die een gebeurtenis verzendt. De gebeurtenis activeert een Automation-runbook waarmee de virtuele machine wordt gecodeerd en een bericht wordt geactiveerd dat naar een micro soft teams-kanaal wordt verzonden. |
| [Procedure: abonneren op gebeurtenissen via de portal](subscribe-through-portal.md) | Gebruik de portal om u te abonneren op gebeurtenissen voor een Azure-abonnement. |
| [Azure CLI: abonneren op gebeurtenissen voor een Azure-abonnement](./scripts/event-grid-cli-azure-subscription.md) |Voorbeeld script voor het maken van een Event Grid-abonnement op een Azure-abonnement en het verzenden van gebeurtenissen naar een webhook. |
| [Power shell: abonneren op gebeurtenissen voor een Azure-abonnement](./scripts/event-grid-powershell-azure-subscription.md)| Voorbeeld script voor het maken van een Event Grid-abonnement op een Azure-abonnement en het verzenden van gebeurtenissen naar een webhook. |
| [Gebeurtenis schema](event-schema-subscriptions.md) | Hiermee worden velden in azure-abonnements gebeurtenissen weer gegeven. |

## <a name="container-registry"></a>Container Registry

Abonneer u op Container Registry gebeurtenissen om te reageren op wijzigingen in installatie kopieën.

|Titel |Beschrijving  |
|---------|---------|
| [Snelstartgids: container register gebeurtenissen verzenden](../container-registry/container-registry-event-grid-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Laat zien hoe u met Azure CLI Container Registry-gebeurtenissen kunt verzenden. |
| [Gebeurtenis schema](event-schema-container-registry.md) | Hiermee worden velden in Container Registry gebeurtenissen weer gegeven. |

## <a name="custom-topics"></a>Aangepaste onderwerpen

Abonneer u op aangepaste onderwerpen om te reageren op toepassings gebeurtenissen.

|Titel  |Beschrijving  |
|---------|---------|
| [Snelstartgids: aangepaste gebeurtenissen maken en routeren met Azure CLI](custom-event-quickstart.md) | Laat zien hoe u Azure CLI gebruikt om aangepaste gebeurtenissen te verzenden. |
| [Snelstartgids: aangepaste gebeurtenissen maken en routeren met Azure PowerShell](custom-event-quickstart-powershell.md) | Laat zien hoe u Azure PowerShell kunt gebruiken om aangepaste gebeurtenissen te verzenden. |
| [Snelstartgids: aangepaste gebeurtenissen maken en routeren met de Azure Portal](custom-event-quickstart-portal.md) | Laat zien hoe u de portal kunt gebruiken om aangepaste gebeurtenissen te verzenden. |
| [Snelstartgids: aangepaste gebeurtenissen naar Azure Queue-opslag routeren](custom-event-to-queue-storage.md) | Hierin wordt beschreven hoe u aangepaste gebeurtenissen naar een wachtrij opslag verzendt. |
| [Procedure: posten naar een aangepast onderwerp](post-to-custom-topic.md) | Laat zien hoe u een gebeurtenis op een aangepast onderwerp plaatst. |
| [Azure CLI: Event Grid aangepast onderwerp maken](./scripts/event-grid-cli-create-custom-topic.md)|Voorbeeld script voor het maken van een aangepast onderwerp. Het script haalt het eind punt en een sleutel op.|
| [Azure CLI: abonneren op gebeurtenissen voor een aangepast onderwerp](./scripts/event-grid-cli-subscribe-custom-topic.md)|Voorbeeld script voor het maken van een abonnement voor een aangepast onderwerp. Er worden gebeurtenissen naar een webhook verzonden.|
| [Power shell: Event Grid aangepast onderwerp maken](./scripts/event-grid-powershell-create-custom-topic.md)|Voorbeeld script voor het maken van een aangepast onderwerp. Het script haalt het eind punt en een sleutel op.|
| [Power shell: abonneren op gebeurtenissen voor een aangepast onderwerp](./scripts/event-grid-powershell-subscribe-custom-topic.md)|Voorbeeld script voor het maken van een abonnement voor een aangepast onderwerp. Er worden gebeurtenissen naar een webhook verzonden.|
| [Resource Manager-sjabloon: aangepast onderwerp en webhook-eind punt](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid) | Een resource manager-sjabloon voor het maken van een aangepast onderwerp en abonnement voor dat aangepaste onderwerp. Er worden gebeurtenissen naar een webhook verzonden. |
|
| [Resource Manager-sjabloon: aangepast onderwerp en Event Hubs eind punt](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| Een resource manager-sjabloon voor het maken van een abonnement voor een aangepast onderwerp. Er worden gebeurtenissen naar een Azure-Event Hubs verzonden. |
| [Gebeurtenis schema](event-schema.md) | Toont velden in aangepaste gebeurtenissen. |

## <a name="event-hubs"></a>Event Hubs

Abonneer u op Event Hubs gebeurtenissen om te reageren op gebeurtenissen van het gebeurtenis bestand. Event Hubs kan fungeren als een gebeurtenis bron of gebeurtenis-handler. In de volgende artikelen ziet u hoe u Event Hubs als een bron gebruikt.

|Titel  |Beschrijving  |
|---------|---------|
| [Zelf studie: stream big data naar een Data Warehouse](event-grid-event-hubs-integration.md) | Wanneer Event Hubs een opname bestand maakt, wordt Event Grid een gebeurtenis naar een functie-app verzonden. De app haalt het opname bestand op en migreert gegevens naar een Data Warehouse. |
| [Gebeurtenis schema](event-schema-event-hubs.md) | Hiermee worden velden in Event Hubs gebeurtenissen weer gegeven. |

Zie [Event hubs handler](event-handlers.md#event-hubs)voor voor beelden van Event hubs als handler.

## <a name="iot-hub"></a>IoT Hub

Abonneer u op IoT Hub-gebeurtenissen om te reageren op apparaten die zijn gemaakt, verwijderd, verbonden, niet-verbonden en telemetrie-gebeurtenissen.

|Titel  |Beschrijving  |
|---------|---------|
| [E-mail meldingen over Azure IoT Hub-gebeurtenissen verzenden met Logic Apps](publish-iot-hub-events-to-logic-apps.md) | Een logische app verzendt een e-mail melding wanneer een apparaat wordt toegevoegd aan uw IoT Hub. |
| [Reageren op IoT Hub gebeurtenissen met behulp van Event Grid om acties te activeren](../iot-hub/iot-hub-event-grid.md) | Overzicht van het integreren van IoT Hub met Event Grid. |
| [Gebeurtenis schema](event-schema-iot-hub.md) | Hiermee worden velden in IoT Hub gebeurtenissen weer gegeven. |
| [De verbinding met het apparaat is verbonden en de verbroken gebeurtenissen van het apparaat](../iot-hub/iot-hub-how-to-order-connection-state-events.md) | Laat zien hoe u gebeurtenissen van de verbindings status van apparaten kunt ordenen. |

## <a name="key-vault-preview"></a>Key Vault (preview-versie)

Key Vault integratie met Event Grid is momenteel beschikbaar als preview-versie. 

Abonneer u op Key Vault gebeurtenissen als u een melding wilt ontvangen wanneer een geheim verloopt, een geheim verloopt of een geheim een nieuwe versie beschikbaar heeft. 

|Titel  |Beschrijving  |
|---------|---------|
| [Key Vault gebeurtenissen bewaken met Azure Event Grid](../key-vault/event-grid-overview.md) | Overzicht van het integreren van Key Vault met Event Grid. |
| [Zelf studie: Key Vault gebeurtenissen maken en bewaken met Event Grid](../key-vault/event-grid-tutorial.md) | Meer informatie over het instellen van Event Grid meldingen voor Key Vault. |
| [Gebeurtenis schema](event-schema-key-vault.md) | Hiermee worden velden in Key Vault gebeurtenissen weer gegeven. |

## <a name="media-services"></a>Media Services

Abonneer u op Media Services gebeurtenissen om te reageren op taak status gebeurtenissen.

|Titel  |Beschrijving  |
|---------|---------|
| [Overzicht: reageren op Media Services gebeurtenissen](../media-services/latest/reacting-to-media-services-events.md) | Overzicht van het integreren van Media Services met Event Grid. |
| [Zelf studie: Azure Media Services gebeurtenissen naar een aangepast eind punt routeren met behulp van CLI](../media-services/latest/job-state-events-cli-how-to.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Toont hoe gebeurtenissen van Media Services worden verzonden. |
| [Gebeurtenis schema](../media-services/latest/media-services-event-schemas.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Hiermee worden velden in Media Services gebeurtenissen weer gegeven. |

## <a name="resource-groups"></a>Resourcegroepen

Abonneer u op de gebeurtenissen van de resource groep om te reageren op wijzigingen in resources in een resource groep.

|Titel  |Beschrijving  |
|---------|---------|
| [Zelf studie: wijzigingen van virtuele machines bewaken met Azure Event Grid en Logic Apps](monitor-virtual-machine-changes-event-grid-logic-app.md) | Een logische app controleert wijzigingen aan een virtuele machine en stuurt e-mails over die wijzigingen. |
| [Azure CLI: abonneren op gebeurtenissen voor een resource groep](./scripts/event-grid-cli-resource-group.md)| Voorbeeld script dat zich abonneert op gebeurtenissen voor een resource groep. Er worden gebeurtenissen naar een webhook verzonden. |
| [Azure CLI: abonneren op gebeurtenissen voor een resource groep en filteren op een resource](./scripts/event-grid-cli-resource-group-filter.md) | Voorbeeld script voor het abonneren op gebeurtenissen voor een resource groep en het filteren van gebeurtenissen voor één resource. |
| [Power shell: abonneren op gebeurtenissen voor een resource groep](./scripts/event-grid-powershell-resource-group.md) | Voorbeeld script dat zich abonneert op gebeurtenissen voor een resource groep. Er worden gebeurtenissen naar een webhook verzonden. |
| [Power shell: abonneren op gebeurtenissen voor een resource groep en filteren op een resource](./scripts/event-grid-powershell-resource-group-filter.md) | Voorbeeld script voor het abonneren op gebeurtenissen voor een resource groep en het filteren van gebeurtenissen voor één resource. |
| [Resource Manager-sjabloon: resource-abonnement](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-resource-events-to-webhook) | Abonneren op gebeurtenissen voor een Azure-abonnement of resource groep. Er worden gebeurtenissen naar een webhook verzonden. |
| [Gebeurtenisschema](event-schema-resource-groups.md) | Hiermee worden velden in de gebeurtenissen van de resource groep weer gegeven. |

## <a name="service-bus"></a>Service Bus

Abonneer u op Service Bus gebeurtenissen om te reageren op berichten zonder actieve listener.

|Titel  |Beschrijving  |
|---------|---------|
| [Zelf studie: Azure Service Bus voor voor beelden van Azure Event Grid-integratie](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid verzendt berichten van Service Bus onderwerp naar de functie app en de logische app. |
| [Overzicht: Azure Service Bus Event Grid-integratie](../service-bus-messaging/service-bus-to-event-grid-integration-concept.md) | Overzicht van het integreren van Service Bus met Event Grid. |
| [Gebeurtenis schema](event-schema-service-bus.md) | Hiermee worden velden in Service Bus gebeurtenissen weer gegeven. |

## <a name="storage"></a>Storage

Abonneer u op Blob Storage-gebeurtenissen om te reageren op gemaakte blobs en verwijderde gebeurtenissen.

>[!NOTE]
> Alleen opslag accounts van het type **StorageV2 (algemeen gebruik v2)** en **BlobStorage** ondersteunings gebeurtenis integratie. **Storage (Genral doel v1)** biedt *geen* ondersteuning voor integratie met Event grid.

|Titel  |Beschrijving  |
|---------|---------|
| [Quick Start: Blob Storage-gebeurtenissen naar een aangepast webeindpunt door sturen met Azure CLI](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Laat zien hoe u met Azure CLI Blob Storage-gebeurtenissen naar een webhook verzendt. |
| [Snelstartgids: Blob Storage-gebeurtenissen door sturen naar een aangepast webeindpunt met Power shell](../storage/blobs/storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Laat zien hoe u Azure PowerShell kunt gebruiken voor het verzenden van Blob Storage-gebeurtenissen naar een webhook. |
| [Snelstartgids: Blob Storage-gebeurtenissen maken en routeren met de Azure Portal](blob-event-quickstart-portal.md) | Laat zien hoe u de portal kunt gebruiken voor het verzenden van Blob Storage-gebeurtenissen naar een webhook. |
| [Azure CLI: abonneren op gebeurtenissen voor een Blob Storage-account](./scripts/event-grid-cli-blob.md) | Voorbeeld script dat zich abonneert op een gebeurtenis voor een Blob Storage-account. Hiermee wordt de gebeurtenis naar een webhook verzonden. |
| [Power shell: abonneren op gebeurtenissen voor een Blob Storage-account](./scripts/event-grid-powershell-blob.md) | Voorbeeld script dat zich abonneert op een gebeurtenis voor een Blob Storage-account. Hiermee wordt de gebeurtenis naar een webhook verzonden. |
| [Resource Manager-sjabloon: Blob-opslag en-abonnement maken](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-subscription-and-storage) | Hiermee implementeert u een Azure Blob-opslagaccount en abonneert u zich op gebeurtenissen van die opslagaccount. Er worden gebeurtenissen naar een webhook verzonden. |
| [Overzicht: reageren op Blob Storage-gebeurtenissen](../storage/blobs/storage-blob-event-overview.md) | Overzicht van het integreren van Blob Storage met Event Grid. |
| [Gebeurtenis schema](event-schema-blob-storage.md) | Hiermee worden velden in Blob Storage gebeurtenissen weer gegeven. |

## <a name="maps"></a>Kaarten
Abonneer u op Azure Maps gebeurtenissen om te reageren op geofence-gebeurtenissen. Een toepassing kan bijvoorbeeld een e-mail melding verzenden wanneer een apparaat binnenkomt of een geofence verlaat.

|Titel  |Beschrijving  |
|---------|---------|
| [Reageren op Azure Maps gebeurtenissen met behulp van Event Grid](../azure-maps/azure-maps-event-grid-integration.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Overzicht van het integreren van Azure Maps met Event Grid. |
| [Zelf studie: een geofence instellen](../azure-maps/tutorial-geofence.md?toc=%2fazure%2fevent-grid%2ftoc.json) | In deze zelfstudie doorloopt u de basisstappen voor het instellen van geofence met behulp van Azure Maps. U gebruikt Azure Event Grid om de geofence-resultaten te streamen en een melding in te stellen op basis van de resultaten van de geofence. |
| [Gebeurtenis schema](event-schema-azure-maps.md) | Hiermee worden velden in Azure Maps gebeurtenissen weer gegeven. |

## <a name="app-configuration"></a>App Configuration
Abonneer u op Azure-app configuratie gebeurtenissen om te reageren op belang rijke wijzigings gebeurtenissen.

|Titel | Beschrijving |
|---------|---------|
| [Reageren op Azure-app configuratie gebeurtenissen met behulp van Event Grid](../azure-app-configuration/concept-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Overzicht van het integreren van Azure-app configuratie met Event Grid. |
| [Snelstartgids: route Azure-app configuratie gebeurtenissen naar een aangepast webeindpunt met Azure CLI](../azure-app-configuration/howto-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Laat zien hoe u met Azure CLI Azure-app configuratie gebeurtenissen naar een webhook verzendt. |
| [Gebeurtenis schema](event-schema-app-configuration.md) | Hiermee worden velden in Azure-app configuratie gebeurtenissen weer gegeven. |

## <a name="azure-signalr"></a>Azure SignalR
Abonneer u op gebeurtenissen van de Azure signalerings service om te reageren op gebeurtenissen van client verbindingen.

|Titel | Beschrijving |
|---------|---------|
| [Reageren op gebeurtenissen van de Azure signalerings service met behulp van Event Grid](../azure-signalr/signalr-concept-event-grid-integration.md) | Overzicht van het integreren van de Azure signalerings service met Event Grid. |
| [Service gebeurtenissen van Azure signalering verzenden naar Event Grid](../azure-signalr/signalr-howto-event-grid-integration.md) | Laat zien hoe u Azure signalerings service-gebeurtenissen verzendt naar een toepassing via Event Grid. |
| [Gebeurtenis schema](event-schema-azure-signalr.md) | Geeft velden weer in gebeurtenissen van de Azure signalerings service. |

## <a name="azure-machine-learning"></a>Azure Machine Learning

Abonneer u op Azure Machine Learning werkruimte gebeurtenissen om te reageren op model registratie.

| Titel | Beschrijving |
| ----- | ----- |
| [Azure Machine Learning gebeurtenissen gebruiken](../machine-learning/concept-event-grid-integration.md) | Overzicht van het integreren van Azure Machine Learning met Event Grid. |
| [Azure Event Grid-gebeurtenis schema voor Azure Machine Learning](event-schema-machine-learning.md) | Hiermee worden velden in de Azure Machine Learning gebeurtenissen weer gegeven. |

## <a name="next-steps"></a>Volgende stappen

* Zie [Een inleiding tot Event Grid](overview.md) voor een inleiding tot Event Grid.
* Als u wilt snel aan de slag met Event Grid, Zie [aangepaste gebeurtenissen maken en routeren met Azure Event Grid](custom-event-quickstart.md).
