---
title: De Azure Portal gebruiken om Service Bus onderwerpen en abonnementen te maken
description: 'Quick Start: in deze Snelstartgids leert u hoe u een Service Bus onderwerp en abonnementen op dit onderwerp maakt met behulp van de Azure Portal.'
services: service-bus-messaging
author: spelluru
manager: timlt
ms.service: service-bus-messaging
ms.topic: quickstart
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: a1e330a62a58daaa3fb2a2e8758d14791a3208c1
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/17/2020
ms.locfileid: "76260817"
---
# <a name="quickstart-use-the-azure-portal-to-create-a-service-bus-topic-and-subscriptions-to-the-topic"></a>Snelstartgids: gebruik de Azure Portal om een Service Bus onderwerp en abonnementen te maken voor het onderwerp
In deze Quick Start gebruikt u de Azure Portal om een Service Bus onderwerp te maken en vervolgens abonnementen te maken op dit onderwerp. 

## <a name="what-are-service-bus-topics-and-subscriptions"></a>Wat zijn Service Bus-onderwerpen en -abonnementen?
Service Bus-onderwerpen en -abonnementen bieden ondersteuning voor een berichtencommunicatiemodel op basis van *publiceren/abonneren*. Wanneer u gebruikmaakt van onderwerpen en abonnementen, communiceren onderdelen van een gedistribueerde toepassing niet direct met elkaar. In plaats daarvan wisselen ze berichten uit via een onderwerp dat als intermediair fungeert.

![TopicConcepts](./media/service-bus-java-how-to-use-topics-subscriptions/sb-topics-01.png)

In tegens telling tot Service Bus wachtrijen, waarbij elk bericht wordt verwerkt door één gebruiker, bieden onderwerpen en abonnementen een een-op-veel communicatie vorm, met behulp van het patroon publiceren/abonneren. Het is mogelijk meerdere abonnementen voor een onderwerp te registreren. Wanneer een bericht naar een onderwerp wordt verzonden, wordt dit vervolgens beschikbaar gemaakt voor elk abonnement, waar het vervolgens zelfstandig wordt afgehandeld/verwerkt. Een abonnement op een onderwerp lijkt op een virtuele wachtrij die kopieën van de berichten ontvangen die naar het onderwerp zijn verzonden. U kunt optioneel filter regels registreren voor een onderwerp op basis van per abonnement, waarmee u kunt filteren of beperken welke berichten voor een onderwerp worden ontvangen door welk onderwerp abonnementen.

Met Service Bus-onderwerpen en-abonnementen kunt u schalen om een groot aantal berichten over een groot aantal gebruikers en toepassingen te verwerken.

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

[!INCLUDE [service-bus-create-topics-three-subscriptions-portal](../../includes/service-bus-create-topics-three-subscriptions-portal.md)]

> [!NOTE]
> U kunt Service Bus-resources beheren met [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Met de Service Bus Explorer kunnen gebruikers verbinding maken met een Service Bus naam ruimte en de Messa ging-entiteiten op een eenvoudige manier beheren. Het hulp programma biedt geavanceerde functies zoals de functionaliteit voor importeren/exporteren of de mogelijkheid om onderwerp, wacht rijen, abonnementen, relay-Services, Notification hubs en Events hubs te testen. 

## <a name="next-steps"></a>Volgende stappen
Zie het volgende artikel voor meer informatie over het verzenden van berichten naar een onderwerp en het ontvangen van berichten via een abonnement: Selecteer de programmeer taal in de inhouds opgave. 

> [!div class="nextstepaction"]
> [Berichten publiceren en abonneren](service-bus-dotnet-how-to-use-topics-subscriptions.md)
