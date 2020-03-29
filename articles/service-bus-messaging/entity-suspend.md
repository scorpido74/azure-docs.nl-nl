---
title: Azure Service Bus - berichtenentiteiten opschorten
description: In dit artikel wordt uitgelegd hoe u azure servicebus-berichtentiteiten (wachtrijen, onderwerpen en abonnementen) tijdelijk opschorten en opnieuw activeren.
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
ms.openlocfilehash: 7386932f19eee064926184eb17f5e92e30add98e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76760382"
---
# <a name="suspend-and-reactivate-messaging-entities-disable"></a>Berichtenentiteiten onderbreken en opnieuw activeren (uitschakelen)

Wachtrijen, onderwerpen en abonnementen kunnen tijdelijk worden opgeschort. Met opschorting wordt de entiteit uitgeschakeld waarin alle berichten worden opgeslagen. Berichten kunnen echter niet worden verwijderd of toegevoegd en de desbetreffende protocolbewerkingen leveren fouten op.

Het opschorten van een entiteit gebeurt meestal om dringende administratieve redenen. Een scenario is het hebben geïmplementeerd van een defecte ontvanger die berichten uit de wachtrij haalt, de verwerking mislukt en toch de berichten onjuist voltooit en verwijdert. Als dat gedrag wordt gediagnosticeerd, kan de wachtrij worden uitgeschakeld voor ontvangst totdat de gecorrigeerde code is geïmplementeerd en verder gegevensverlies veroorzaakt door de defecte code kan worden voorkomen.

Een opschorting of reactivering kan worden uitgevoerd door de gebruiker of door het systeem. Het systeem schort alleen entiteiten op vanwege ernstige administratieve redenen, zoals het bereiken van de abonnementsbestedingslimiet. Entiteiten met een systeemuitgeschakelde entiteiten kunnen niet opnieuw worden geactiveerd door de gebruiker, maar worden hersteld wanneer de oorzaak van de opschorting is verholpen.

In de portal maakt de sectie **Eigenschappen** voor de desbetreffende entiteit het wijzigen van de status mogelijk; in de volgende schermafbeelding wordt de schakelaar voor een wachtrij weergegeven:

![][1]

Het portaal maakt alleen het volledig uitschakelen van wachtrijen mogelijk. U de bewerkingen voor verzenden en ontvangen ook afzonderlijk uitschakelen met behulp van de API's voor [ServiceBus NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) in de .NET Framework SDK of met een Azure Resource Manager-sjabloon via Azure CLI of Azure PowerShell.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="suspension-states"></a>Schorsingsstaten

De statussen die kunnen worden ingesteld voor een wachtrij zijn:

-   **Actief**: de wachtrij is actief.
-   **Uitgeschakeld**: de wachtrij is opgeschort.
-   **SendDisabled**: De wachtrij is gedeeltelijk opgeschort, met ontvangen is toegestaan.
-   **OntvangenUitgeschakeld**: De wachtrij is gedeeltelijk opgeschort, waarbij verzenden is toegestaan.

Voor abonnementen en onderwerpen kunnen alleen **Actief** en **uitgeschakeld** worden ingesteld.

De [entiteitStatus](/dotnet/api/microsoft.servicebus.messaging.entitystatus) opsomming definieert ook een set van overgangsstaten die alleen kan worden ingesteld door het systeem. De opdracht PowerShell om een wachtrij uit te schakelen wordt in het volgende voorbeeld weergegeven. De opdracht Reactivering is `Status` gelijkwaardig en stelt in op **Actief**.

```powershell
$q = Get-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue

$q.Status = "Disabled"

Set-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue -QueueObj $q
```

## <a name="next-steps"></a>Volgende stappen

Zie de volgende onderwerpen voor meer informatie over berichten over Service Bus:

* [Service Bus-wachtrijen, -onderwerpen en -abonnementen](service-bus-queues-topics-subscriptions.md)
* [Aan de slag met Service Bus-wachtrijen](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus-onderwerpen en -abonnementen gebruiken](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/entity-suspend/queue-disable.png

