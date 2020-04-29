---
title: Dubbele bericht detectie Azure Service Bus | Microsoft Docs
description: In dit artikel wordt uitgelegd hoe u dubbele items kunt detecteren in Azure Service Bus berichten. Het duplicaat bericht kan worden genegeerd en verwijderd.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76760365"
---
# <a name="duplicate-detection"></a>Detectie van duplicaten

Als een toepassing mislukt als gevolg van een onherstelbare fout onmiddellijk nadat een bericht is verzonden en het opnieuw opstarten van de toepassing ten onrechte verstrijkt dat de voorafgaande bericht bezorging niet is uitgevoerd, zorgt een volgende verzen ding ertoe dat hetzelfde bericht twee keer wordt weer gegeven in het systeem.

Het is ook mogelijk dat er een ogen blik geduld op client-of netwerk niveau, en dat een verzonden bericht moet worden doorgevoerd in de wachtrij, waarbij de bevestiging niet naar de client wordt geretourneerd. Dit scenario laat de client onzeker weten wat het resultaat van de verzend bewerking is.

Duplicaten detectie neemt de twijfel uit deze situaties door het inschakelen van de afzender om hetzelfde bericht opnieuw te verzenden. de wachtrij of het onderwerp verwijdert dubbele kopieën.

Het inschakelen van duplicaten detectie helpt bij het bijhouden van de door de toepassing beheerde *MessageId* van alle berichten die worden verzonden naar een wachtrij of onderwerp tijdens een opgegeven periode. Als er een nieuw bericht wordt verzonden met een *MessageId* die tijdens het tijd venster is geregistreerd, wordt het bericht gerapporteerd als geaccepteerd (de verzend bewerking slaagt), maar wordt het zojuist verzonden bericht direct genegeerd en verwijderd. Er wordt geen rekening gehouden met andere onderdelen van het bericht dan de *MessageId* .

Toepassings beheer van de id is essentieel, omdat alleen de toepassing de *MessageId* kan koppelen aan een bedrijfsproces context van waaruit het zoals verwacht kan worden geconstrueerd wanneer er een fout optreedt.

Voor een bedrijfs proces waarbij tijdens de verwerking van bepaalde toepassings context meerdere berichten worden verzonden, kan de *MessageId* een combi natie zijn van de context-id op toepassings niveau, zoals een aankoop ordernummer, en het onderwerp van het bericht, bijvoorbeeld **12345.2017/Payment**.

De *MessageId* kan altijd een bepaalde GUID zijn, maar het verankeren van de id naar het bedrijfs proces levert voorspel bare Herhaal baarheid, die u nodig hebt om de duplicaten detectie functie effectief te benutten.

> [!NOTE]
> Als de duplicaten detectie is ingeschakeld en de sessie-ID of partitie sleutel niet zijn ingesteld, wordt de bericht-ID gebruikt als de partitie sleutel. Als de bericht-ID ook niet is ingesteld, genereert .NET-en AMQP-bibliotheken automatisch een bericht-ID voor het bericht. Zie het [gebruik van partitie sleutels](service-bus-partitioning.md#use-of-partition-keys)voor meer informatie.

## <a name="enable-duplicate-detection"></a>Duplicaten detectie inschakelen

In de portal is de functie ingeschakeld tijdens het maken van de entiteit met het selectie vakje **Duplicaten detectie inschakelen** , die standaard uitgeschakeld is. De instelling voor het maken van nieuwe onderwerpen is gelijkwaardig.

![][1]

> [!IMPORTANT]
> U kunt duplicaten detectie niet in-of uitschakelen nadat de wachtrij is gemaakt. U kunt dit alleen doen op het moment van het maken van de wachtrij. 

Via een programma kunt u de markering instellen met de eigenschap [QueueDescription. requiresDuplicateDetection](/dotnet/api/microsoft.servicebus.messaging.queuedescription.requiresduplicatedetection#Microsoft_ServiceBus_Messaging_QueueDescription_RequiresDuplicateDetection) in de volledige Framework .net API. Met de Azure Resource Manager-API wordt de waarde ingesteld met de eigenschap [queueProperties. requiresDuplicateDetection](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) .

De geschiedenis van de duplicaten detectie tijd wordt standaard ingesteld op 30 seconden voor wacht rijen en onderwerpen, met een maximale waarde van zeven dagen. U kunt deze instelling wijzigen in het venster Eigenschappen van de wachtrij en het onderwerp in de Azure Portal.

![][2]

Via een programma kunt u de grootte van het duplicaten detectie venster configureren waarin bericht-id's worden bewaard, met behulp van de eigenschap [QueueDescription. DuplicateDetectionHistoryTimeWindow](/dotnet/api/microsoft.servicebus.messaging.queuedescription.duplicatedetectionhistorytimewindow#Microsoft_ServiceBus_Messaging_QueueDescription_DuplicateDetectionHistoryTimeWindow) met de volledige .NET Framework-API. Met de Azure Resource Manager-API wordt de waarde ingesteld met de eigenschap [queueProperties. duplicateDetectionHistoryTimeWindow](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) .

Het inschakelen van duplicaten detectie en de grootte van het venster zijn direct van invloed op de door Voer van de wachtrij (en het onderwerp), omdat alle opgenomen bericht-id's moeten overeenkomen met de nieuw ingediende bericht-id.

Als u het venster klein houdt, betekent dit dat er minder bericht-id's moeten worden bewaard en gevonden en dat de door Voer minder wordt beïnvloed. Voor entiteiten met een hoge door Voer waarvoor duplicaten detectie is vereist, moet u het venster zo klein mogelijk laten.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende onderwerpen voor meer informatie over Service Bus Messa ging:

* [Service Bus-wachtrijen, -onderwerpen en -abonnementen](service-bus-queues-topics-subscriptions.md)
* [Aan de slag met Service Bus-wachtrijen](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus-onderwerpen en -abonnementen gebruiken](service-bus-dotnet-how-to-use-topics-subscriptions.md)

In scenario's waarin client code een bericht niet opnieuw kan verzenden met dezelfde *MessageId* als voorheen, is het belang rijk om berichten te ontwerpen die veilig kunnen worden verwerkt. In dit [blog bericht over Idempotence](https://particular.net/blog/what-does-idempotent-mean) worden verschillende technieken beschreven voor het uitvoeren van deze procedure.

[1]: ./media/duplicate-detection/create-queue.png
[2]: ./media/duplicate-detection/queue-prop.png
