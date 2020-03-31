---
title: Gebeurtenisbronnen azure-gebeurtenisraster
description: Azure-abonnementen, Container Registry, aangepaste onderwerpen, Event Hubs, IoT Hub, Key Vault, Media Services, resourcegroepen, Service Bus, Storage, Maps, App Configuration, Signal R, Machine Learning.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: spelluru
ms.openlocfilehash: d4a426ea1432d0266b7ae9344afefe8ddac1d030
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264997"
---
# <a name="event-sources-in-azure-event-grid"></a>Gebeurtenisbronnen in Azure Event Grid

Een gebeurtenisbron is waar de gebeurtenis plaatsvindt. Verschillende Azure-services worden automatisch geconfigureerd om gebeurtenissen te verzenden. U ook aangepaste toepassingen maken die gebeurtenissen verzenden. Aangepaste toepassingen hoeven niet te worden gehost in Azure om gebeurtenisraster te gebruiken voor gebeurtenisdistributie.

In dit artikel vindt u links naar inhoud voor elke gebeurtenisbron.

## <a name="azure-subscriptions"></a>Azure-abonnementen

U abonneren op azure-abonnementen gebeurtenissen om te reageren op wijzigingen in resources in een Azure-abonnement.

|Titel |Beschrijving  |
|---------|---------|
| [Zelfstudie: Azure Automation with Event Grid en Microsoft Teams](ensure-tags-exists-on-new-virtual-machines.md) |Maak een virtuele machine, die een gebeurtenis verzendt. De gebeurtenis activeert een automation-runbook die de virtuele machine tagt en activeert een bericht dat naar een Microsoft Teams-kanaal wordt verzonden. |
| [How to: abonneren op evenementen via portal](subscribe-through-portal.md) | Gebruik de portal om u te abonneren op gebeurtenissen voor een Azure-abonnement. |
| [Azure CLI: u abonneren op gebeurtenissen voor een Azure-abonnement](./scripts/event-grid-cli-azure-subscription.md) |Voorbeeldscript waarmee een Event Grid-abonnement op een Azure-abonnement wordt gemaakt en gebeurtenissen naar een WebHook worden verzendt. |
| [PowerShell: u abonneren op gebeurtenissen voor een Azure-abonnement](./scripts/event-grid-powershell-azure-subscription.md)| Voorbeeldscript waarmee een Event Grid-abonnement op een Azure-abonnement wordt gemaakt en gebeurtenissen naar een WebHook worden verzendt. |
| [Gebeurtenisschema](event-schema-subscriptions.md) | Hiermee worden velden weergegeven in Azure-abonnementsgebeurtenissen. |

## <a name="container-registry"></a>Container Registry

Abonneer u op gebeurtenissen in het containerregister om te reageren op wijzigingen in afbeeldingen.

|Titel |Beschrijving  |
|---------|---------|
| [Snelstart: containerregistergebeurtenissen verzenden](../container-registry/container-registry-event-grid-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Hier ziet u hoe u Azure CLI gebruikt om containerregistergebeurtenissen te verzenden. |
| [Gebeurtenisschema](event-schema-container-registry.md) | Hiermee worden velden weergegeven in containerregistergebeurtenissen. |

## <a name="custom-topics"></a>Aangepaste onderwerpen

Abonneer u op aangepaste onderwerpen om te reageren op toepassingsgebeurtenissen.

|Titel  |Beschrijving  |
|---------|---------|
| [Snelstart: aangepaste gebeurtenissen maken en routeren met Azure CLI](custom-event-quickstart.md) | Hier wordt uitgelegd hoe u Azure CLI gebruikt om aangepaste gebeurtenissen te verzenden. |
| [Snelstart: aangepaste gebeurtenissen maken en routeren met Azure PowerShell](custom-event-quickstart-powershell.md) | Hier wordt uitgelegd hoe u Azure PowerShell gebruiken om aangepaste gebeurtenissen te verzenden. |
| [Snelstart: aangepaste gebeurtenissen maken en routeren met de Azure-portal](custom-event-quickstart-portal.md) | Laat zien hoe u de portal gebruiken om aangepaste gebeurtenissen te verzenden. |
| [Snelstart: aangepaste gebeurtenissen routeren naar Azure Queue-opslag](custom-event-to-queue-storage.md) | Beschrijft hoe u aangepaste gebeurtenissen naar een wachtrijopslag verzendt. |
| [How to: post op aangepast onderwerp](post-to-custom-topic.md) | Laat zien hoe je een evenement plaatst in een aangepast onderwerp. |
| [Azure CLI: aangepaste gebeurtenisrasteronderwerp maken](./scripts/event-grid-cli-create-custom-topic.md)|Voorbeeldscript dat een aangepast onderwerp maakt. Het script haalt het eindpunt en een sleutel op.|
| [Azure CLI: u abonneren op gebeurtenissen voor een aangepast onderwerp](./scripts/event-grid-cli-subscribe-custom-topic.md)|Voorbeeldscript waarmee een abonnement wordt gemaakt voor een aangepast onderwerp. Het stuurt gebeurtenissen naar een WebHook.|
| [PowerShell: aangepaste gebeurtenisraster-onderwerp maken](./scripts/event-grid-powershell-create-custom-topic.md)|Voorbeeldscript dat een aangepast onderwerp maakt. Het script haalt het eindpunt en een sleutel op.|
| [PowerShell: abonneer je op evenementen voor een aangepast onderwerp](./scripts/event-grid-powershell-subscribe-custom-topic.md)|Voorbeeldscript waarmee een abonnement wordt gemaakt voor een aangepast onderwerp. Het stuurt gebeurtenissen naar een WebHook.|
| [Resource Manager-sjabloon: aangepast onderwerp en WebHook-eindpunt](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid) | Een resourcemanagersjabloon waarmee een aangepast onderwerp en een aangepast abonnement voor dat aangepaste onderwerp wordt gemaakt. Het stuurt gebeurtenissen naar een WebHook. |
|
| [Resource Manager-sjabloon: aangepast onderwerp en eindpunt van gebeurtenishubs](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| Een resourcemanagersjabloon waarmee een abonnement wordt gemaakt voor een aangepast onderwerp. Het verzendt gebeurtenissen naar een Azure Event Hubs. |
| [Gebeurtenisschema](event-schema.md) | Hiermee worden velden weergegeven in aangepaste gebeurtenissen. |

## <a name="event-hubs"></a>Event Hubs

Abonneer u op gebeurtenissen op Event Hubs om te reageren op gebeurtenissen in het vastleggen van bestanden. Gebeurtenishubs kunnen fungeren als gebeurtenisbron of gebeurtenishandler. In de volgende artikelen wordt uitgelegd hoe u gebeurtenishubs als bron gebruiken.

|Titel  |Beschrijving  |
|---------|---------|
| [Zelfstudie: big data streamen naar een datawarehouse](event-grid-event-hubs-integration.md) | Wanneer Gebeurtenishubs een Capture-bestand maakt, stuurt gebeurtenisraster een gebeurtenis naar een functie-app. De app haalt het capture-bestand op en migreert gegevens naar een gegevensmagazijn. |
| [Gebeurtenisschema](event-schema-event-hubs.md) | Hiermee worden velden weergegeven in gebeurtenissen op gebeurtenishubs. |

Zie [Gebeurtenishubs handler](event-handlers.md#event-hubs)voor voorbeelden van gebeurtenishubs als handler.

## <a name="iot-hub"></a>IoT Hub

Abonneer u op IoT Hub-gebeurtenissen om te reageren op gebeurtenissen die zijn gemaakt, verwijderd, verbonden, losgekoppeld en telemetrie gebeurtenissen.

|Titel  |Beschrijving  |
|---------|---------|
| [E-mailmeldingen over gebeurtenissen van Azure IoT Hub verzenden met Logic Apps](publish-iot-hub-events-to-logic-apps.md) | Een logische app stuurt een meldingsmail telkens wanneer een apparaat aan uw IoT-hub wordt toegevoegd. |
| [Reageren op IoT Hub-gebeurtenissen door gebeurtenisraster te gebruiken om acties te activeren](../iot-hub/iot-hub-event-grid.md) | Overzicht van de integratie van IoT Hub met Event Grid. |
| [Gebeurtenisschema](event-schema-iot-hub.md) | Hiermee worden velden weergegeven in IoT Hub-gebeurtenissen. |
| [Aangesloten bestelapparaat en verbinding tussen apparaten](../iot-hub/iot-hub-how-to-order-connection-state-events.md) | Hier ziet u hoe u statusgebeurtenissen voor apparaatverbindingen ordenen. |

## <a name="key-vault-preview"></a>Key Vault (voorbeeld)

Key Vault-integratie met Event Grid is momenteel in preview. 

Abonneer u op Key Vault-gebeurtenissen om op de hoogte te worden gesteld wanneer een geheim bijna verloopt, een geheim verloopt of een geheim een nieuwe versie beschikbaar heeft. 

|Titel  |Beschrijving  |
|---------|---------|
| [Key Vault-gebeurtenissen bewaken met Azure Event Grid](../key-vault/event-grid-overview.md) | Overzicht van de integratie van Key Vault met Event Grid. |
| [Zelfstudie: Key Vault-gebeurtenissen maken en bewaken met gebeurtenisraster](../key-vault/event-grid-tutorial.md) | Meer informatie over het instellen van Event Grid-meldingen voor Key Vault. |
| [Gebeurtenisschema](event-schema-key-vault.md) | Hiermee worden velden weergegeven in Key Vault-gebeurtenissen. |

## <a name="media-services"></a>Media Services

Abonneer u op gebeurtenissen in Media Services om te reageren op gebeurtenissen in de status van een taak.

|Titel  |Beschrijving  |
|---------|---------|
| [Overzicht: reageren op mediaservices-evenementen](../media-services/latest/reacting-to-media-services-events.md) | Overzicht van de integratie van Media Services met Event Grid. |
| [Zelfstudie: Azure Media Services-gebeurtenissen routeren naar een aangepast webeindpunt met CLI](../media-services/latest/job-state-events-cli-how-to.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Laat zien hoe u gebeurtenissen vanuit Media Services verzendt. |
| [Gebeurtenisschema](../media-services/latest/media-services-event-schemas.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Hiermee worden velden weergegeven in gebeurtenissen van Media Services. |

## <a name="resource-groups"></a>Resourcegroepen

Abonneer u op resourcegroepgebeurtenissen om te reageren op wijzigingen in resources in een resourcegroep.

|Titel  |Beschrijving  |
|---------|---------|
| [Zelfstudie: wijzigingen in virtuele machines controleren met Azure Event Grid en Logic Apps](monitor-virtual-machine-changes-event-grid-logic-app.md) | Een logische app controleert wijzigingen in een virtuele machine en stuurt e-mails over deze wijzigingen. |
| [Azure CLI: u abonneren op gebeurtenissen voor een resourcegroep](./scripts/event-grid-cli-resource-group.md)| Voorbeeldscript dat zich abonneert op gebeurtenissen voor een resourcegroep. Het stuurt gebeurtenissen naar een WebHook. |
| [Azure CLI: u abonneren op gebeurtenissen voor een resourcegroep en filteren op een resource](./scripts/event-grid-cli-resource-group-filter.md) | Voorbeeldscript dat zich abonneert op gebeurtenissen voor een resourcegroep en gebeurtenissen filtert voor één resource. |
| [PowerShell: abonneer u op gebeurtenissen voor een resourcegroep](./scripts/event-grid-powershell-resource-group.md) | Voorbeeldscript dat zich abonneert op gebeurtenissen voor een resourcegroep. Het stuurt gebeurtenissen naar een WebHook. |
| [PowerShell: abonneer u op gebeurtenissen voor een resourcegroep en filter voor een resource](./scripts/event-grid-powershell-resource-group-filter.md) | Voorbeeldscript dat zich abonneert op gebeurtenissen voor een resourcegroep en gebeurtenissen filtert voor één resource. |
| [Resource Manager-sjabloon: resourceabonnement](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-resource-events-to-webhook) | U abonneert zich op gebeurtenissen voor een Azure-abonnement of resourcegroep. Het stuurt gebeurtenissen naar een WebHook. |
| [Gebeurtenisschema](event-schema-resource-groups.md) | Hiermee worden velden weergegeven in resourcegroepgebeurtenissen. |

## <a name="service-bus"></a>Service Bus

Abonneer u op Service Bus-gebeurtenissen om te reageren op berichten zonder actieve listener.

|Titel  |Beschrijving  |
|---------|---------|
| [Zelfstudie: voorbeelden van azure servicebus naar Azure Event Grid-integratie](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid stuurt berichten vanuit het onderwerp Service Bus naar de functie-app en logische app. |
| [Overzicht: Azure Service Bus to Event Grid-integratie](../service-bus-messaging/service-bus-to-event-grid-integration-concept.md) | Overzicht van de integratie van Service Bus met Event Grid. |
| [Gebeurtenisschema](event-schema-service-bus.md) | Hiermee worden velden weergegeven in servicebus-gebeurtenissen. |

## <a name="storage"></a>Storage

Abonneer u op Blob Storage-gebeurtenissen om te reageren op blob-gebeurtenissen en verwijderde gebeurtenissen.

>[!NOTE]
> Alleen opslagaccounts van soort **StorageV2 (v2 voor algemeen gebruik)** en **BlobStorage** ondersteunen gebeurtenis-integratie. **Opslag (algemene toepassing v1)** biedt *geen* ondersteuning voor integratie met Event Grid.

|Titel  |Beschrijving  |
|---------|---------|
| [Snelstart: blob-opslaggebeurtenissen routeren naar een aangepast webeindpunt met Azure CLI](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Hier ziet u hoe u Azure CLI gebruikt om blob-opslaggebeurtenissen naar een WebHook te verzenden. |
| [Snelstart: blob-opslaggebeurtenissen routeren naar een aangepast webeindpunt met PowerShell](../storage/blobs/storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Hier ziet u hoe u Azure PowerShell gebruiken om blob-opslaggebeurtenissen naar een WebHook te verzenden. |
| [Snelstart: Blob-opslaggebeurtenissen maken en routeren met de Azure-portal](blob-event-quickstart-portal.md) | Laat zien hoe u de portal gebruikt om blob-opslaggebeurtenissen naar een WebHook te verzenden. |
| [Azure CLI: u abonneren op gebeurtenissen voor een Blob-opslagaccount](./scripts/event-grid-cli-blob.md) | Voorbeeldscript dat zich abonneert op een gebeurtenis voor een Blob-opslagaccount. Het stuurt de gebeurtenis naar een WebHook. |
| [PowerShell: abonneer u op gebeurtenissen voor een Blob-opslagaccount](./scripts/event-grid-powershell-blob.md) | Voorbeeldscript dat zich abonneert op een gebeurtenis voor een Blob-opslagaccount. Het stuurt de gebeurtenis naar een WebHook. |
| [Resourcemanager-sjabloon: Blob-opslag en -abonnement maken](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-subscription-and-storage) | Hiermee implementeert u een Azure Blob-opslagaccount en abonneert u zich op gebeurtenissen van die opslagaccount. Het stuurt gebeurtenissen naar een WebHook. |
| [Overzicht: reageren op blob-opslaggebeurtenissen](../storage/blobs/storage-blob-event-overview.md) | Overzicht van de integratie van Blob-opslag met gebeurtenisraster. |
| [Gebeurtenisschema](event-schema-blob-storage.md) | Hiermee worden velden weergegeven in blobopslaggebeurtenissen. |

## <a name="maps"></a>Kaarten
Abonneer u op Azure Maps-gebeurtenissen om te reageren op geofence-gebeurtenissen. Een toepassing kan bijvoorbeeld een e-mailmelding leveren wanneer een apparaat een geofence binnenkomt of verlaat.

|Titel  |Beschrijving  |
|---------|---------|
| [Reageren op Azure Maps-gebeurtenissen met gebeurtenisraster](../azure-maps/azure-maps-event-grid-integration.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Overzicht van de integratie van Azure Maps met Event Grid. |
| [Zelfstudie: Een geofence instellen](../azure-maps/tutorial-geofence.md?toc=%2fazure%2fevent-grid%2ftoc.json) | In deze zelfstudie doorloopt u de basisstappen voor het instellen van geofence met behulp van Azure Maps. U gebruikt Azure Event Grid om de geofence-resultaten te streamen en een melding in te stellen op basis van de geofence-resultaten. |
| [Gebeurtenisschema](event-schema-azure-maps.md) | Hiermee worden velden weergegeven in Azure Maps-gebeurtenissen. |

## <a name="app-configuration"></a>App Configuration
Abonneer u op azure-configuratiegebeurtenissen voor de configuratie van azure-apps om te reageren op gebeurtenissen met betrekking tot wijzigingen in de sleutelwaarde.

|Titel | Beschrijving |
|---------|---------|
| [Reageren op gebeurtenissen voor Azure App-configuratie met gebeurtenisraster](../azure-app-configuration/concept-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Overzicht van de integratie van Azure App Configuration met Event Grid. |
| [Snelstart: azure-app-configuratiegebeurtenissen routeren naar een aangepast webeindpunt met Azure CLI](../azure-app-configuration/howto-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Hier ziet u hoe u Azure CLI gebruikt om Azure App Configuration-gebeurtenissen naar een WebHook te verzenden. |
| [Gebeurtenisschema](event-schema-app-configuration.md) | Hiermee worden velden weergegeven in Azure App-configuratiegebeurtenissen. |

## <a name="azure-signalr"></a>Azure SignalR
Abonneer u op Azure SignalR Service-gebeurtenissen om te reageren op gebeurtenissen in de clientverbinding.

|Titel | Beschrijving |
|---------|---------|
| [Reageren op Azure SignalR Service-gebeurtenissen met gebeurtenisraster](../azure-signalr/signalr-concept-event-grid-integration.md) | Overzicht van de integratie van Azure SignalR Service met Event Grid. |
| [Azure SignalR Service-gebeurtenissen verzenden naar gebeurtenisraster](../azure-signalr/signalr-howto-event-grid-integration.md) | Hier ziet u hoe u Azure SignalR Service-gebeurtenissen naar een toepassing verzendt via gebeurtenisraster. |
| [Gebeurtenisschema](event-schema-azure-signalr.md) | Hiermee worden velden weergegeven in Azure SignalR Service-gebeurtenissen. |

## <a name="azure-machine-learning"></a>Azure Machine Learning

Abonneer u op Azure Machine Learning-werkruimtegebeurtenissen om te reageren op modelregistratie.

| Titel | Beschrijving |
| ----- | ----- |
| [Azure Machine Learning-gebeurtenissen gebruiken](../machine-learning/concept-event-grid-integration.md) | Overzicht van de integratie van Azure Machine Learning met Event Grid. |
| [Azure Event Grid-gebeurtenisschema voor Azure Machine Learning](event-schema-machine-learning.md) | Hiermee worden velden weergegeven in de Azure Machine Learning-gebeurtenissen. |

## <a name="next-steps"></a>Volgende stappen

* Zie [Een inleiding tot Event Grid](overview.md) voor een inleiding tot Event Grid.
* Zie [Aangepaste gebeurtenissen maken en routeren met Azure Event Grid](custom-event-quickstart.md)om snel aan de slag te gaan met gebeurtenisraster.
