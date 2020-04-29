---
title: Azure Service Bus-Messa ging-entiteiten onderbreken
description: In dit artikel wordt uitgelegd hoe u Azure Service Bus bericht entiteiten (wacht rijen, onderwerpen en abonnementen) tijdelijk kunt onderbreken en opnieuw activeren.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76760382"
---
# <a name="suspend-and-reactivate-messaging-entities-disable"></a>Messa ging-entiteiten onderbreken en opnieuw activeren (uitschakelen)

Wacht rijen, onderwerpen en abonnementen kunnen tijdelijk worden onderbroken. Met opschorting wordt de entiteit ingesteld op een uitgeschakelde status waarin alle berichten in de opslag worden bewaard. Berichten kunnen echter niet worden verwijderd of toegevoegd, en de bijbehorende protocol bewerkingen veroorzaken fouten.

Het blok keren van een entiteit wordt doorgaans uitgevoerd als urgente administratieve redenen. Eén scenario is het implementeren van een defecte ontvanger die berichten uit de wachtrij aflevert, de verwerking mislukt en de berichten nog niet correct voltooit en verwijdert. Als dat gedrag wordt vastgesteld, kan de wachtrij worden uitgeschakeld voor ontvangst, totdat de gecorrigeerde code wordt geïmplementeerd en het verdere verlies van gegevens dat door de fout code wordt veroorzaakt, wordt voor komen.

Een opschorting of heractivering kan worden uitgevoerd door de gebruiker of door het systeem. Het systeem schorst alleen entiteiten als gevolg van accent grave administratieve redenen, zoals de bestedings limiet van het abonnement. Entiteiten waarvoor een systeem is uitgeschakeld, kunnen niet opnieuw worden geactiveerd door de gebruiker, maar worden hersteld wanneer de oorzaak van de onderbreking is opgelost.

In de portal kunt u in het gedeelte **Eigenschappen** voor de respectieve entiteit de status wijzigen. op de volgende scherm afbeelding ziet u de wissel knop voor een wachtrij:

![][1]

De portal staat alleen volledig uitschakelen van wacht rijen toe. U kunt de verzend-en ontvangst bewerkingen ook afzonderlijk uitschakelen met behulp van de Service Bus [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) -api's in de .NET Framework SDK of met een Azure Resource Manager-sjabloon via Azure CLI of Azure PowerShell.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="suspension-states"></a>Blokkerings statussen

De statussen die kunnen worden ingesteld voor een wachtrij zijn:

-   **Actief**: de wachtrij is actief.
-   **Uitgeschakeld**: de wachtrij is onderbroken.
-   **SendDisabled**: de wachtrij is gedeeltelijk onderbroken, waarbij ontvangst is toegestaan.
-   **ReceiveDisabled**: de wachtrij is gedeeltelijk onderbroken, terwijl verzenden is toegestaan.

Voor abonnementen en onderwerpen kan alleen **actief** en **uitgeschakeld** worden ingesteld.

De opsomming [EntityStatus](/dotnet/api/microsoft.servicebus.messaging.entitystatus) definieert ook een set overgangs statussen die alleen door het systeem kan worden ingesteld. De Power shell-opdracht voor het uitschakelen van een wachtrij wordt weer gegeven in het volgende voor beeld. De opdracht voor opnieuw activeren is gelijk aan `Status` de instelling **actief**.

```powershell
$q = Get-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue

$q.Status = "Disabled"

Set-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue -QueueObj $q
```

## <a name="next-steps"></a>Volgende stappen

Zie de volgende onderwerpen voor meer informatie over Service Bus Messa ging:

* [Service Bus-wachtrijen, -onderwerpen en -abonnementen](service-bus-queues-topics-subscriptions.md)
* [Aan de slag met Service Bus-wachtrijen](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus-onderwerpen en -abonnementen gebruiken](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/entity-suspend/queue-disable.png

