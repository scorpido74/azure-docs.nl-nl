---
title: Opslag wachtrij als gebeurtenis-handler voor Azure Event Grid gebeurtenissen
description: Hierin wordt beschreven hoe u Azure Storage-wacht rijen kunt gebruiken als gebeurtenis-handlers voor Azure Event Grid-gebeurtenissen.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: spelluru
ms.openlocfilehash: 9b767caa1041f865d8e15cd57796b186f7a4a6bb
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83598540"
---
# <a name="storage-queue-as-an-event-handler-for-azure-event-grid-events"></a>Opslag wachtrij als gebeurtenis-handler voor Azure Event Grid gebeurtenissen
Een gebeurtenis-handler is de plaats waar de gebeurtenis wordt verzonden. De handler heeft een aantal verdere acties nodig om de gebeurtenis te verwerken. Verschillende Azure-Services worden automatisch geconfigureerd voor het afhandelen van gebeurtenissen en **Azure Queue Storage** is een hiervan. 

Gebruik **Queue Storage** om gebeurtenissen te ontvangen die moeten worden opgehaald. U kunt Queue Storage gebruiken wanneer u een langlopend proces hebt dat te lang duurt om te reageren. Door gebeurtenissen te verzenden naar de wachtrij opslag, kan de app op basis van een eigen planning gebeurtenissen ophalen en verwerken.

## <a name="tutorials"></a>Zelfstudies
Raadpleeg de volgende zelf studie voor een voor beeld van het gebruik van wachtrij opslag als een gebeurtenis-handler. 

|Titel  |Beschrijving  |
|---------|---------|
| [Snelstartgids: aangepaste gebeurtenissen naar Azure Queue-opslag door sturen met Azure CLI en Event Grid](custom-event-to-queue-storage.md) | Hierin wordt beschreven hoe u aangepaste gebeurtenissen naar een wachtrij opslag verzendt. |

## <a name="next-steps"></a>Volgende stappen
Zie het artikel over [gebeurtenis-handlers](event-handlers.md) voor een lijst met ondersteunde gebeurtenis-handlers. 
