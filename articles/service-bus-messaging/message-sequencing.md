---
title: Volgorde van Azure Service Bus-berichten en tijdstempels | Microsoft Documenten
description: In dit artikel wordt uitgelegd hoe u de volgorde en het bestellen (met tijdstempels) van Azure Service Bus-berichten behouden.
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: 54d774c00fa650cb9608f46cc07b9d899709eaa5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261656"
---
# <a name="message-sequencing-and-timestamps"></a>Berichtvolgorde en -timestamps

Sequencing en timestamping zijn twee functies die altijd zijn ingeschakeld op alle servicebusentiteiten en surface via de [eigenschappen SequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber) en [EnqueuedTimeUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedtimeutc) van ontvangen of doorbladerde berichten.

Voor die gevallen waarin de absolute volgorde van berichten aanzienlijk is en/of waarin een consument een betrouwbare unieke identificatie voor berichten nodig heeft, stempelt de makelaar berichten met een gap-free, toenemend volgordenummer ten opzichte van de wachtrij of het onderwerp. Voor verdeelde entiteiten wordt het volgnummer ten opzichte van de partitie uitgegeven.

De **sequencenumber-waarde** is een uniek 64-bits geheel getal dat is toegewezen aan een bericht dat wordt geaccepteerd en opgeslagen door de makelaar en fungeert als interne id. Voor partitieentiteiten weerspiegelen de bovenste 16 bits de partitie-id. Volgnummers rollen naar nul wanneer het bereik van 48/64-bits is uitgeput.

Het volgnummer kan worden vertrouwd als een unieke id omdat het wordt toegewezen door een centrale en neutrale autoriteit en niet door clients. Het vertegenwoordigt ook de ware volgorde van aankomst, en is nauwkeuriger dan een tijdstempel als een bestelcriterium, omdat tijdstempels mogelijk niet een voldoende hoge resolutie hebben bij extreme berichtsnelheden en onderhevig kunnen zijn aan (hoe minimaal) de klok scheef loopt in situaties waarin de makelaar eigendomsovergangen tussen knooppunten.

De absolute aankomstorder is bijvoorbeeld van belang in bedrijfsscenario's waarin een beperkt aantal aangeboden goederen op basis van wie het eerst komt, het eerst maalt, zolang de levering strekt; de kaartverkoop is daar een voorbeeld van.

De tijdstempelmogelijkheid fungeert als een neutrale en betrouwbare autoriteit die de UTC-aankomsttijd van een bericht nauwkeurig vastlegt, weergegeven in de eigenschap **EnqueuedTimeUtc.** De waarde is handig als een bedrijfsscenario afhankelijk is van deadlines, zoals of een werkitem op een bepaalde datum voor middernacht is ingediend, maar de verwerking ver achterloopt op de wachtrijachterstand.

## <a name="scheduled-messages"></a>Geplande berichten

U kunt berichten verzenden naar een wachtrij of onderwerp voor vertraagde verwerking, bijvoorbeeld om te plannen dat een taak op een bepaald moment beschikbaar komt voor verwerking door een systeem. Deze mogelijkheid realiseert een betrouwbare gedistribueerde time-based planner.

Geplande berichten worden pas in de wachtrij weergegeven als de gedefinieerde wachtrijtijd is vastgelegd. Voor die tijd kunnen geplande berichten worden geannuleerd. Annulering verwijdert het bericht.

U berichten plannen door de eigenschap [ScheduledEnqueueTimeUtc](/dotnet/api/microsoft.azure.servicebus.message.scheduledenqueuetimeutc) in te stellen wanneer u een bericht verzendt via het reguliere verzendpad of expliciet met de [ScheduleMessageAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.schedulemessageasync#Microsoft_Azure_ServiceBus_QueueClient_ScheduleMessageAsync_Microsoft_Azure_ServiceBus_Message_System_DateTimeOffset_) API. De laatste retourneert onmiddellijk het geplande bericht **SequenceNumber**, die u later gebruiken om het geplande bericht te annuleren indien nodig. Geplande berichten en hun volgordenummers kunnen ook worden gedetecteerd met behulp van [het browsen door berichten.](message-browsing.md)

Het **volgnummer** voor een gepland bericht is alleen geldig wanneer het bericht zich in deze status bevindt. Als het bericht wordt overgezet naar de actieve status, wordt het bericht toegevoegd aan de wachtrij alsof het op het huidige moment in de wachtrij is geplaatst, waaronder het toewijzen van een nieuw **sequencenummer**.

Omdat de functie is verankerd in afzonderlijke berichten en berichten slechts één keer in de wachtrij kunnen worden geplaatst, ondersteunt Service Bus geen terugkerende schema's voor berichten.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende onderwerpen voor meer informatie over berichten over Service Bus:

* [Service Bus-wachtrijen, -onderwerpen en -abonnementen](service-bus-queues-topics-subscriptions.md)
* [Aan de slag met Service Bus-wachtrijen](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus-onderwerpen en -abonnementen gebruiken](service-bus-dotnet-how-to-use-topics-subscriptions.md)