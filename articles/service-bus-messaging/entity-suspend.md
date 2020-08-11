---
title: Azure Service Bus-Messa ging-entiteiten onderbreken
description: In dit artikel wordt uitgelegd hoe u Azure Service Bus bericht entiteiten (wacht rijen, onderwerpen en abonnementen) tijdelijk kunt onderbreken en opnieuw activeren.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 2dad0b774f271ed719ca09b1e749559d5e1868bd
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/11/2020
ms.locfileid: "88078854"
---
# <a name="suspend-and-reactivate-messaging-entities-disable"></a>Messa ging-entiteiten onderbreken en opnieuw activeren (uitschakelen)

Wacht rijen, onderwerpen en abonnementen kunnen tijdelijk worden onderbroken. Met opschorting wordt de entiteit ingesteld op een uitgeschakelde status waarin alle berichten in de opslag worden bewaard. Berichten kunnen echter niet worden verwijderd of toegevoegd, en de bijbehorende protocol bewerkingen veroorzaken fouten.

Het blok keren van een entiteit wordt doorgaans uitgevoerd als urgente administratieve redenen. Eén scenario is het implementeren van een defecte ontvanger die berichten uit de wachtrij aflevert, de verwerking mislukt en de berichten nog niet correct voltooit en verwijdert. Als dat gedrag wordt vastgesteld, kan de wachtrij worden uitgeschakeld voor ontvangst, totdat de gecorrigeerde code wordt geïmplementeerd en het verdere verlies van gegevens dat door de fout code wordt veroorzaakt, wordt voor komen.

Een opschorting of heractivering kan worden uitgevoerd door de gebruiker of door het systeem. Het systeem schorst alleen entiteiten als gevolg van accent grave administratieve redenen, zoals de bestedings limiet van het abonnement. Entiteiten waarvoor een systeem is uitgeschakeld, kunnen niet opnieuw worden geactiveerd door de gebruiker, maar worden hersteld wanneer de oorzaak van de onderbreking is opgelost.

In de portal kunt u de status wijzigen in de sectie **overzicht** van de respectieve entiteit. de huidige status wordt weer gegeven onder **status** als een Hyper link.

In de volgende scherm afbeelding ziet u de beschik bare statussen waarmee de entiteit kan worden gewijzigd door de Hyper link te selecteren: 

![Scherm afbeelding van de functie Service Bus in overzicht om de optie voor de entiteits status te wijzigen.][1]

De portal staat alleen volledig uitschakelen van wacht rijen toe. U kunt de verzend-en ontvangst bewerkingen ook afzonderlijk uitschakelen met behulp van de Service Bus [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) -api's in de .NET Framework SDK of met een Azure Resource Manager-sjabloon via Azure CLI of Azure PowerShell.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="suspension-states"></a>Blokkerings statussen

De statussen die kunnen worden ingesteld voor een wachtrij zijn:

-   **Actief**: de wachtrij is actief.
-   **Uitgeschakeld**: de wachtrij is onderbroken.
-   **SendDisabled**: de wachtrij is gedeeltelijk onderbroken, waarbij ontvangst is toegestaan.
-   **ReceiveDisabled**: de wachtrij is gedeeltelijk onderbroken, terwijl verzenden is toegestaan.

Voor abonnementen en onderwerpen kan alleen **actief** en **uitgeschakeld** worden ingesteld.

De opsomming [EntityStatus](/dotnet/api/microsoft.servicebus.messaging.entitystatus) definieert ook een set overgangs statussen die alleen door het systeem kan worden ingesteld. De Power shell-opdracht voor het uitschakelen van een wachtrij wordt weer gegeven in het volgende voor beeld. De opdracht voor opnieuw activeren is gelijk aan de instelling `Status` **actief**.

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

[1]: ./media/entity-suspend/entity-state-change.png

