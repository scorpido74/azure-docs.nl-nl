---
title: Azure Service Bus-Messa ging-entiteiten onderbreken
description: In dit artikel wordt uitgelegd hoe u Azure Service Bus bericht entiteiten (wacht rijen, onderwerpen en abonnementen) tijdelijk kunt onderbreken en opnieuw activeren.
ms.topic: article
ms.date: 09/29/2020
ms.openlocfilehash: f89e17e494cc777691b7f7ca47538cd29114d2dc
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/30/2020
ms.locfileid: "91575227"
---
# <a name="suspend-and-reactivate-messaging-entities-disable"></a>Messa ging-entiteiten onderbreken en opnieuw activeren (uitschakelen)

Wacht rijen, onderwerpen en abonnementen kunnen tijdelijk worden onderbroken. Met opschorting wordt de entiteit ingesteld op een uitgeschakelde status waarin alle berichten in de opslag worden bewaard. Berichten kunnen echter niet worden verwijderd of toegevoegd, en de bijbehorende protocol bewerkingen veroorzaken fouten.

Het blok keren van een entiteit wordt doorgaans uitgevoerd als urgente administratieve redenen. Eén scenario is het implementeren van een defecte ontvanger die berichten uit de wachtrij aflevert, de verwerking mislukt en de berichten nog niet correct voltooit en verwijdert. Als dat gedrag wordt vastgesteld, kan de wachtrij worden uitgeschakeld voor ontvangst, totdat de gecorrigeerde code wordt geïmplementeerd en het verdere verlies van gegevens dat door de fout code wordt veroorzaakt, wordt voor komen.

Een opschorting of heractivering kan worden uitgevoerd door de gebruiker of door het systeem. Het systeem schorst alleen entiteiten als gevolg van accent grave administratieve redenen, zoals de bestedings limiet van het abonnement. Entiteiten waarvoor een systeem is uitgeschakeld, kunnen niet opnieuw worden geactiveerd door de gebruiker, maar worden hersteld wanneer de oorzaak van de onderbreking is opgelost.

## <a name="queue-status"></a>Wachtrij status 
De statussen die kunnen worden ingesteld voor een wachtrij zijn:

-   **Actief**: de wachtrij is actief.
-   **Uitgeschakeld**: de wachtrij is onderbroken. Het is gelijk aan het instellen van zowel **SendDisabled** als **ReceiveDisabled**. 
-   **SendDisabled**: de wachtrij is gedeeltelijk onderbroken, waarbij ontvangst is toegestaan.
-   **ReceiveDisabled**: de wachtrij is gedeeltelijk onderbroken, terwijl verzenden is toegestaan.

### <a name="change-the-queue-status-in-the-azure-portal"></a>Wijzig de status van de wachtrij in de Azure Portal: 

1. Navigeer in het Azure Portal naar uw Service Bus naam ruimte. 
1. Selecteer de wachtrij waarvan u de status wilt wijzigen. U ziet wacht rijen in het onderste deel venster in het midden. 
1. Op de pagina **Service Bus wachtrij** ziet u de huidige status van de wachtrij als een Hyper link. Als het **overzicht** niet is geselecteerd in het linkermenu, selecteert u dit om de status van de wachtrij weer te geven. Selecteer de huidige status van de wachtrij om deze te wijzigen. 

    :::image type="content" source="./media/entity-suspend/select-state.png" alt-text="Status van de wachtrij selecteren":::
4. Selecteer de nieuwe status voor de wachtrij en selecteer **OK**. 

    :::image type="content" source="./media/entity-suspend/entity-state-change.png" alt-text="Status van de wachtrij selecteren":::
    
De portal staat alleen volledig uitschakelen van wacht rijen toe. U kunt de verzend-en ontvangst bewerkingen ook afzonderlijk uitschakelen met behulp van de Service Bus [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) -api's in de .NET Framework SDK of met een Azure Resource Manager-sjabloon via Azure CLI of Azure PowerShell.

### <a name="change-the-queue-status-using-azure-powershell"></a>De wachtrij status wijzigen met behulp van Azure PowerShell
De Power shell-opdracht voor het uitschakelen van een wachtrij wordt weer gegeven in het volgende voor beeld. De opdracht voor opnieuw activeren is gelijk aan de instelling `Status` **actief**.

```powershell
$q = Get-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue

$q.Status = "Disabled"

Set-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue -QueueObj $q
```

## <a name="topic-status"></a>Onderwerps status
Het wijzigen van de onderwerps status in de Azure Portal is vergelijkbaar met het wijzigen van de status van een wachtrij. Wanneer u de huidige status van het onderwerp selecteert, ziet u de volgende pagina waarmee u de status kunt wijzigen. 

:::image type="content" source="./media/entity-suspend/topic-state-change.png" alt-text="Status van de wachtrij selecteren":::

De statussen die kunnen worden ingesteld voor een onderwerp zijn:
- **Actief**: het onderwerp is actief.
- **Uitgeschakeld**: het onderwerp is onderbroken.
- **SendDisabled**: hetzelfde effect als **uitgeschakeld**.

## <a name="subscription-status"></a>Abonnements status
Het wijzigen van de abonnements status in de Azure Portal is vergelijkbaar met het wijzigen van de status van een onderwerp of wachtrij. Wanneer u de huidige status van het abonnement selecteert, ziet u de volgende pagina waarmee u de status kunt wijzigen. 

:::image type="content" source="./media/entity-suspend/subscription-state-change.png" alt-text="Status van de wachtrij selecteren":::

De statussen die kunnen worden ingesteld voor een onderwerp zijn:
- **Actief**: het onderwerp is actief.
- **Uitgeschakeld**: het onderwerp is onderbroken.
- **ReceiveDisabled**: hetzelfde effect als **uitgeschakeld**.

## <a name="other-statuses"></a>Andere statussen
De opsomming [EntityStatus](/dotnet/api/microsoft.servicebus.messaging.entitystatus) definieert ook een set overgangs statussen die alleen door het systeem kan worden ingesteld. 


## <a name="next-steps"></a>Volgende stappen

Zie de volgende onderwerpen voor meer informatie over Service Bus Messa ging:

* [Service Bus-wachtrijen, -onderwerpen en -abonnementen](service-bus-queues-topics-subscriptions.md)
* [Aan de slag met Service Bus-wachtrijen](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus-onderwerpen en -abonnementen gebruiken](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/entity-suspend/entity-state-change.png

