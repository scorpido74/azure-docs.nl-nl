---
title: Dubbele berichtdetectie van Azure Service Bus | Microsoft Documenten
description: In dit artikel wordt uitgelegd hoe u duplicaten detecteren in Azure Service Bus-berichten. Het dubbele bericht kan worden genegeerd en verwijderd.
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
ms.openlocfilehash: c109b9fd310a09e5eb4c6d18cc3536e4d8069c0b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76760365"
---
# <a name="duplicate-detection"></a>Detectie van duplicaten

Als een toepassing mislukt als gevolg van een fatale fout onmiddellijk nadat het een bericht verzendt, en de opnieuw gestarte toepassingsinstantie ten onrechte van mening is dat de eerdere berichtbezorging niet heeft plaatsgevonden, zorgt een volgend bericht ervoor dat hetzelfde bericht twee keer in het systeem wordt weergegeven.

Het is ook mogelijk dat een fout op client- of netwerkniveau een moment eerder optreedt en dat een verzonden bericht in de wachtrij wordt vastgelegd, waarbij de bevestiging niet met succes wordt teruggestuurd naar de client. Dit scenario laat de klant twijfelen over de uitkomst van de verzendbewerking.

Dubbele detectie neemt de twijfel uit deze situaties door de afzender in te schakelen hetzelfde bericht opnieuw te verzenden, en de wachtrij of het onderwerp verwijdert dubbele kopieën.

Door dubbele detectie in te schakelen, u de door de toepassing gecontroleerde *MessageId* bijhouden van alle berichten die in een wachtrij of onderwerp tijdens een bepaald tijdvenster worden verzonden. Als er een nieuw bericht wordt verzonden met *MessageId* dat tijdens het tijdvenster is geregistreerd, wordt het bericht gerapporteerd als geaccepteerd (de verzendbewerking slaagt), maar wordt het nieuw verzonden bericht onmiddellijk genegeerd en verwijderd. Er worden geen andere delen van het bericht dan de *MessageId* in aanmerking genomen.

Toepassingsbeheer van de id is essentieel, omdat alleen dat de toepassing in staat stelt om de *MessageId* te koppelen aan een bedrijfsprocescontext waaruit deze voorspelbaar kan worden gereconstrueerd wanneer een fout optreedt.

Voor een bedrijfsproces waarin meerdere berichten worden verzonden tijdens de afhandeling van bepaalde toepassingscontext, kan de *MessageId* een samenstelling zijn van het context-id op toepassingsniveau, zoals een inkoopordernummer, en het onderwerp van het bericht, bijvoorbeeld **12345.2017/betaling**.

De *MessageId* kan altijd een GUID zijn, maar het verankeren van de id naar het bedrijfsproces levert voorspelbare herhaalbaarheid op, wat gewenst is om de dubbele detectiefunctie effectief te gebruiken.

> [!NOTE]
> Als de dubbele detectie is ingeschakeld en de sessie-id of partitiesleutel niet is ingesteld, wordt de bericht-id gebruikt als de partitiesleutel. Als de bericht-id ook niet is ingesteld, genereren .NET- en AMQP-bibliotheken automatisch een bericht-id voor het bericht. Zie [Partitiesleutels gebruiken voor](service-bus-partitioning.md#use-of-partition-keys)meer informatie .

## <a name="enable-duplicate-detection"></a>Dubbele detectie inschakelen

In de portal is de functie ingeschakeld tijdens het maken van entiteiten met het selectievakje **Dubbele detectie inschakelen,** dat standaard is uitgeschakeld. De instelling voor het maken van nieuwe onderwerpen is gelijkwaardig.

![][1]

> [!IMPORTANT]
> U dubbele detectie niet inschakelen/uitschakelen nadat de wachtrij is gemaakt. U dit alleen doen op het moment dat u de wachtrij maakt. 

Programmatisch stelt u de vlag in met de eigenschap [QueueDescription.requiresDuplicateDetection](/dotnet/api/microsoft.servicebus.messaging.queuedescription.requiresduplicatedetection#Microsoft_ServiceBus_Messaging_QueueDescription_RequiresDuplicateDetection) op het volledige framework .NET API. Met de Azure Resource Manager API wordt de waarde ingesteld met de [eigenschap queueProperties.requiresDuplicateDetection.](/azure/templates/microsoft.servicebus/namespaces/queues#property-values)

De dubbele detectietijdgeschiedenis is standaard 30 seconden voor wachtrijen en onderwerpen, met een maximale waarde van zeven dagen. U deze instelling wijzigen in het venster wachtrij- en onderwerpeigenschappen in de Azure-portal.

![][2]

Programmatisch u de grootte van het dubbele detectievenster configureren waarin bericht-id's worden behouden, met behulp van de eigenschap [QueueDescription.DuplicateDetectionHistoryTimeWindow](/dotnet/api/microsoft.servicebus.messaging.queuedescription.duplicatedetectionhistorytimewindow#Microsoft_ServiceBus_Messaging_QueueDescription_DuplicateDetectionHistoryTimeWindow) met de volledige .NET Framework API. Met de Azure Resource Manager API wordt de waarde ingesteld met de eigenschap [queueProperties.duplicateHistoryTimeWindow.](/azure/templates/microsoft.servicebus/namespaces/queues#property-values)

Dubbele detectie inschakelen en de grootte van het venster hebben rechtstreeks invloed op de doorvoer van de wachtrij (en onderwerp), omdat alle opgenomen bericht-id's moeten worden gekoppeld aan de nieuw ingediende bericht-id.

Als u het venster klein houdt, moeten er minder berichten-id's worden bewaard en afgestemd en wordt de doorvoer minder beïnvloed. Voor entiteiten met een hoge doorvoer die dubbele detectie vereisen, moet u het venster zo klein mogelijk houden.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende onderwerpen voor meer informatie over berichten over Service Bus:

* [Service Bus-wachtrijen, -onderwerpen en -abonnementen](service-bus-queues-topics-subscriptions.md)
* [Aan de slag met Service Bus-wachtrijen](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus-onderwerpen en -abonnementen gebruiken](service-bus-dotnet-how-to-use-topics-subscriptions.md)

In scenario's waarin clientcode een bericht met dezelfde *MessageId* niet opnieuw kan indienen als voorheen, is het belangrijk om berichten te ontwerpen die veilig opnieuw kunnen worden verwerkt. Deze [blogpost over idempotentie](https://particular.net/blog/what-does-idempotent-mean) beschrijft verschillende technieken voor hoe dat te doen.

[1]: ./media/duplicate-detection/create-queue.png
[2]: ./media/duplicate-detection/queue-prop.png
