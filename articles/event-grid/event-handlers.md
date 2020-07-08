---
title: Gebeurtenis-handlers van Azure Event Grid
description: Hierin worden ondersteunde gebeurtenis-handlers voor Azure Event Grid beschreven. Azure Automation, functions, Event Hubs, Hybride verbindingen, Logic Apps, Service Bus, Queue Storage, webhooks.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: c29526bdee5ad70b3acb23117ee003877f52a55e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "83592454"
---
# <a name="event-handlers-in-azure-event-grid"></a>Gebeurtenis-handlers in Azure Event Grid
Een gebeurtenis-handler is de plaats waar de gebeurtenis wordt verzonden. De handler heeft een aantal verdere acties nodig om de gebeurtenis te verwerken. Verschillende Azure-Services worden automatisch geconfigureerd voor het verwerken van gebeurtenissen. U kunt ook een webhook gebruiken voor het verwerken van gebeurtenissen. De webhook hoeft niet te worden gehost in azure voor het verwerken van gebeurtenissen. Event Grid ondersteunt alleen HTTPS-webhook-eind punten.

## <a name="supported-event-handlers"></a>Ondersteunde gebeurtenis-handlers
Dit zijn de ondersteunde gebeurtenis-handlers: 

- [Webhooks](handler-webhooks.md). Azure Automation runbooks en Logic Apps worden ondersteund via webhooks. 
- [Azure functions](handler-functions.md)
- [Event hubs](handler-event-hubs.md)
- [Hybride verbindingen doorgeven](handler-relay-hybrid-connections.md)
- [Service Bus-wacht rijen en-onderwerpen](handler-service-bus.md)
- [Opslagwachtrijen](handler-storage-queues.md)

## <a name="next-steps"></a>Volgende stappen
- Zie [Een inleiding tot Event Grid](overview.md) voor een inleiding tot Event Grid.
