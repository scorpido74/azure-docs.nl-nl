---
title: De Azure-portal gebruiken om servicebus-onderwerpen en -abonnementen te maken
description: 'Snelstart: in deze quickstart leert u hoe u een ServiceBus-onderwerp en abonnementen op dat onderwerp maakt met behulp van de Azure-portal.'
services: service-bus-messaging
author: spelluru
manager: timlt
ms.service: service-bus-messaging
ms.topic: quickstart
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: a1e330a62a58daaa3fb2a2e8758d14791a3208c1
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "76260817"
---
# <a name="quickstart-use-the-azure-portal-to-create-a-service-bus-topic-and-subscriptions-to-the-topic"></a>Snelstart: de Azure-portal gebruiken om een servicebusonderwerp en abonnementen op het onderwerp te maken
In deze quickstart gebruikt u de Azure-portal om een servicebusonderwerp te maken en vervolgens abonnementen op dat onderwerp te maken. 

## <a name="what-are-service-bus-topics-and-subscriptions"></a>Wat zijn Service Bus-onderwerpen en -abonnementen?
Service Bus-onderwerpen en -abonnementen bieden ondersteuning voor een berichtencommunicatiemodel op basis van *publiceren/abonneren*. Wanneer u gebruikmaakt van onderwerpen en abonnementen, communiceren onderdelen van een gedistribueerde toepassing niet direct met elkaar. In plaats daarvan wisselen ze berichten uit via een onderwerp dat als intermediair fungeert.

![TopicConcepts](./media/service-bus-java-how-to-use-topics-subscriptions/sb-topics-01.png)

In tegenstelling tot wachtrijen voor ServiceBus, waarin elk bericht door één enkele consument wordt verwerkt, bieden onderwerpen en abonnementen een één-op-veel communicatievorm, met behulp van een publicatie-/abonneerpatroon. Het is mogelijk meerdere abonnementen voor een onderwerp te registreren. Wanneer een bericht naar een onderwerp wordt verzonden, wordt dit vervolgens beschikbaar gemaakt voor elk abonnement, waar het vervolgens zelfstandig wordt afgehandeld/verwerkt. Een abonnement op een onderwerp lijkt op een virtuele wachtrij die kopieën van de berichten ontvangen die naar het onderwerp zijn verzonden. U optioneel filterregels voor een onderwerp per abonnement registreren, zodat u filteren of beperken welke berichten naar een onderwerp worden ontvangen door welke onderwerpabonnementen.

Service Bus-onderwerpen en -abonnementen stellen u in staat om te schalen om een groot aantal berichten te verwerken over een groot aantal gebruikers en toepassingen.

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

[!INCLUDE [service-bus-create-topics-three-subscriptions-portal](../../includes/service-bus-create-topics-three-subscriptions-portal.md)]

> [!NOTE]
> U servicebusbronnen beheren met [Service Bus Explorer.](https://github.com/paolosalvatori/ServiceBusExplorer/) Met de Service Bus Explorer kunnen gebruikers eenvoudig verbinding maken met een naamruimte van een ServiceBus en berichtenentiteiten beheren. De tool biedt geavanceerde functies zoals import/export functionaliteit of de mogelijkheid om onderwerp, wachtrijen, abonnementen, relay services, meldinghubs en evenementenhubs te testen. 

## <a name="next-steps"></a>Volgende stappen
Zie het volgende artikel: selecteer de programmeertaal in de TOC om te leren hoe u berichten naar een onderwerp verzenden en deze berichten via een abonnement ontvangen. 

> [!div class="nextstepaction"]
> [Publiceren en abonneren op berichten](service-bus-dotnet-how-to-use-topics-subscriptions.md)
