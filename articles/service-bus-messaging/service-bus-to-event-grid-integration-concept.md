---
title: Overzicht integratie Azure Service Bus met Azure Event Grid | Microsoft Docs
description: In dit artikel vindt u een beschrijving van de manier waarop Azure Service Bus-berichten worden geïntegreerd met Azure Event Grid.
services: service-bus-messaging
documentationcenter: .net
author: axisc
editor: spelluru
ms.assetid: f99766cb-8f4b-4baf-b061-4b1e2ae570e4
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: conceptual
ms.date: 01/27/2020
ms.author: aschhab
ms.openlocfilehash: 1e514e2856afae4ff6f877bb193935da1bc5d623
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76773482"
---
# <a name="azure-service-bus-to-event-grid-integration-overview"></a>Overzicht integratie Azure Service Bus met Azure Event Grid

Azure Service Bus heeft een nieuwe integratie met Azure Event Grid geïntroduceerd. Het belangrijkste scenario van deze functie is dat Service Bus-wachtrijen of -abonnementen met een laag volume aan berichten geen ontvanger nodig hebben die continu berichten opvraagt. 

Service Bus kan nu gebeurtenissen naar Event Grid verzenden als er zich berichten in een wachtrij of abonnement bevinden zonder dat er ontvangers zijn. U kunt Event Grid-abonnementen maken voor uw Service Bus-naamruimten, naar deze gebeurtenissen luisteren en erop reageren door een ontvanger te starten. Met deze functie kunt u Service Bus gebruiken in reactieve programmeermodellen.

Als u deze functie wilt inschakelen, hebt u het volgende nodig:

* Een Service Bus Premium-naamruimte met ten minste één Service Bus-wachtrij of een Service Bus-onderwerp met ten minste één abonnement.
* Inzender-toegang tot de Service Bus-naamruimte.
* Bovendien moet u een abonnement op Event Grid voor de Service Bus-naamruimte hebben. Het abonnement ontvangt de melding van Event Grid dat er berichten klaarstaan. Typische abonnees zijn mogelijk de functie Logic Apps van Azure App Service, Azure Functions of een webhook die contact opneemt met een web-app. De abonnee verwerkt vervolgens de berichten. 

![19][]


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="verify-that-you-have-contributor-access"></a>Controleren of u toegang op Inzender-niveau hebt
Ga naar de naamruimte van uw servicebus en selecteer **Vervolgens Toegangsbeheer (IAM)** en selecteer het tabblad **Roltoewijzingen.** Controleer of u de inzender toegang hebt tot de naamruimte. 

### <a name="events-and-event-schemas"></a>Gebeurtenissen en gebeurtenisschema's

Service Bus gaat vandaag gebeurtenissen verzenden voor twee scenario's:

* [ActiveMessagesWithNoListenersAvailable](#active-messages-available-event)
* DeadletterMessagesAvailable

Bovendien worden de standaard Event Grid-beveiliging en [verificatiemechanismen](https://docs.microsoft.com/azure/event-grid/security-authentication) gebruikt.

Zie [Azure Event Grid-gebeurtenisschema's](https://docs.microsoft.com/azure/event-grid/event-schema) voor meer informatie.

#### <a name="active-messages-available-event"></a>De gebeurtenis Actieve berichten beschikbaar

Deze gebeurtenis wordt gegenereerd als er actieve berichten in een wachtrij of abonnement zijn, en er geen ontvangers luisteren.

Het schema voor deze gebeurtenis is als volgt:

```JSON
{
  "topic": "/subscriptions/<subscription id>/resourcegroups/DemoGroup/providers/Microsoft.ServiceBus/namespaces/<YOUR SERVICE BUS NAMESPACE WILL SHOW HERE>",
  "subject": "topics/<service bus topic>/subscriptions/<service bus subscription>",
  "eventType": "Microsoft.ServiceBus.ActiveMessagesAvailableWithNoListeners",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://YOUR-SERVICE-BUS-NAMESPACE-WILL-SHOW-HERE.servicebus.windows.net/TOPIC-NAME/subscriptions/SUBSCRIPTIONNAME/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}
```

#### <a name="dead-letter-messages-available-event"></a>De gebeurtenis Berichten beschikbaar in de wachtrij voor onbestelbare berichten

U krijgt minimaal een gebeurtenis per wachtrij voor onbestelbare berichten, waarin zich berichten bevinden maar waar geen actieve ontvangers zijn.

Het schema voor deze gebeurtenis is als volgt:

```JSON
[{
  "topic": "/subscriptions/<subscription id>/resourcegroups/DemoGroup/providers/Microsoft.ServiceBus/namespaces/<YOUR SERVICE BUS NAMESPACE WILL SHOW HERE>",
  "subject": "topics/<service bus topic>/subscriptions/<service bus subscription>",
  "eventType": "Microsoft.ServiceBus.DeadletterMessagesAvailableWithNoListener",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://YOUR-SERVICE-BUS-NAMESPACE-WILL-SHOW-HERE.servicebus.windows.net/TOPIC-NAME/subscriptions/SUBSCRIPTIONNAME/$deadletterqueue/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="how-many-events-are-emitted-and-how-often"></a>Hoeveel gebeurtenissen worden er verzonden en hoe vaak?

Als u meerdere wachtrijen en onderwerpen of abonnementen in de naamruimte hebt, krijgt u minimaal één gebeurtenis per wachtrij en één per abonnement. De gebeurtenissen worden onmiddellijk uitgezonden als de Service Bus-entiteit geen berichten bevat en er een nieuw bericht arriveert. Of de gebeurtenissen worden elke twee minuten uitgezonden, tenzij Service Bus een actieve ontvanger detecteert. De gebeurtenissen worden niet onderbroken als u door berichten bladert.

Standaard verzendt Service Bus gebeurtenissen voor alle entiteiten in de naamruimte. Als u alleen voor specifieke entiteiten gebeurtenissen wilt ophalen, raadpleegt u de volgende sectie.

### <a name="use-filters-to-limit-where-you-get-events-from"></a>Filters gebruiken om te beperken waarvandaan u gebeurtenissen ophaalt

Als u gebeurtenissen bijvoorbeeld alleen van één wachtrij of één abonnement binnen uw naamruimte wilt ontvangen, kunt u de filters *Begint met* of *Eindigt op* van Event Grid gebruiken. In sommige interfaces worden deze filters *voorvoegselfilter* en *achtervoegselfilter* genoemd. Als u gebeurtenissen voor meerdere maar niet alle wachtrijen en abonnementen wilt ontvangen, kunt u meerdere verschillende Event Grid-abonnementen maken en voor elk ervan een filter opgeven.

## <a name="create-event-grid-subscriptions-for-service-bus-namespaces"></a>Event Grid-abonnementen maken voor Service Bus-naamruimten

U kunt op drie verschillende manieren Event Grid-abonnementen voor Service Bus-naamruimten maken:

* In de Azure Portal
* In [Azure CLI](#azure-cli-instructions)
* In [PowerShell](#powershell-instructions)

## <a name="azure-portal-instructions"></a>Instructies voor Azure Portal

Ga als volgt te werk als u een nieuw Event Grid-abonnement wilt maken:
1. Ga in Azure Portal naar uw naamruimte.
2. Selecteer in het linkerdeelvenster de optie **Event Grid**. 
3. Selecteer **Gebeurtenisabonnement**.  

   In de volgende afbeelding wordt een naamruimte weergegeven met een Event Grid-abonnement:

   ![Event Grid-abonnementen](./media/service-bus-to-event-grid-integration-concept/sbtoeventgridportal.png)

   In de volgende afbeelding wordt getoond hoe u zich kunt abonneren op een functie of webhook zonder specifieke filters:

   ![21][]

## <a name="azure-cli-instructions"></a>Instructies voor Azure CLI

Controleer eerst of Azure CLI versie 2.0 of hoger is geïnstalleerd. [Download het installatieprogramma](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Selecteer **Windows + X**en open een nieuwe PowerShell-console met beheerdersmachtigingen. U kunt ook een opdrachtshell in Azure Portal gebruiken.

Voer de volgende code uit:

 ```azurecli-interactive
az login

az account set -s "<Azure subscription name>"

namespaceid=$(az resource show --namespace Microsoft.ServiceBus --resource-type namespaces --name "<service bus namespace>" --resource-group "<resource group that contains the service bus namespace>" --query id --output tsv

az eventgrid event-subscription create --resource-id $namespaceid --name "<YOUR EVENT GRID SUBSCRIPTION NAME (CAN BE ANY NOT EXISTING)>" --endpoint "<your_function_url>" --subject-ends-with "<YOUR SERVICE BUS SUBSCRIPTION NAME>"
```

Als u BASH gebruikt 

## <a name="powershell-instructions"></a>Instructies voor PowerShell

Controleer of u Azure PowerShell hebt geïnstalleerd. [Download het installatieprogramma](https://docs.microsoft.com/powershell/azure/install-Az-ps). Selecteer **Windows+X** en open een nieuwe PowerShell-console met beheerdersmachtigingen. U kunt ook een opdrachtshell in Azure Portal gebruiken.

```powershell-interactive
Connect-AzAccount

Select-AzSubscription -SubscriptionName "<YOUR SUBSCRIPTION NAME>"

# This might be installed already
Install-Module Az.ServiceBus

$NSID = (Get-AzServiceBusNamespace -ResourceGroupName "<YOUR RESOURCE GROUP NAME>" -Na
mespaceName "<YOUR NAMESPACE NAME>").Id

New-AzEVentGridSubscription -EventSubscriptionName "<YOUR EVENT GRID SUBSCRIPTION NAME (CAN BE ANY NOT EXISTING)>" -ResourceId $NSID -Endpoint "<YOUR FUNCTION URL>” -SubjectEndsWith "<YOUR SERVICE BUS SUBSCRIPTION NAME>"
```

U kunt nu kennismaken met de andere instelopties of testen of er gebeurtenissen stromen.

## <a name="next-steps"></a>Volgende stappen

* [Voorbeelden](service-bus-to-event-grid-integration-example.md) ophalen van Service Bus en Event Grid.
* Meer informatie over [Event Grid](https://docs.microsoft.com/azure/event-grid/).
* Meer informatie over [Azure Functions](https://docs.microsoft.com/azure/azure-functions/).
* Meer informatie over [Logic Apps](https://docs.microsoft.com/azure/logic-apps/).
* Meer informatie over [Service Bus](https://docs.microsoft.com/azure/service-bus/).

[1]: ./media/service-bus-to-event-grid-integration-concept/sbtoeventgrid1.png
[19]: ./media/service-bus-to-event-grid-integration-concept/sbtoeventgriddiagram.png
[8]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid8.png
[9]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid9.png
[20]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal.png
[21]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal2.png
