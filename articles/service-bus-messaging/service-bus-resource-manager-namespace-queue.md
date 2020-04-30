---
title: Azure Service Bus naam ruimte en wachtrij maken met behulp van Azure-sjabloon
description: 'Snelstartgids: een Service Bus naam ruimte en een wachtrij maken met Azure Resource Manager sjabloon'
services: service-bus-messaging
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: a6bfb5fd-7b98-4588-8aa1-9d5f91b599b6
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: quickstart
ms.custom: subject-armqs
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 03/30/2020
ms.author: spelluru
ms.openlocfilehash: b08253104eeb61f6bb09fde507473d235a996494
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80422621"
---
# <a name="quickstart-create-a-service-bus-namespace-and-a-queue-using-an-azure-resource-manager-template"></a>Snelstartgids: een Service Bus naam ruimte en een wachtrij maken met behulp van een Azure Resource Manager sjabloon

In dit artikel wordt beschreven hoe u een Azure Resource Manager sjabloon gebruikt waarmee een Service Bus naam ruimte en een wachtrij binnen die naam ruimte wordt gemaakt. In het artikel wordt uitgelegd hoe u kunt opgeven welke resources worden geïmplementeerd en hoe para meters moeten worden gedefinieerd die worden opgegeven wanneer de implementatie wordt uitgevoerd. U kunt deze sjabloon gebruiken voor uw eigen implementaties of de sjabloon aanpassen aan uw eisen.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Als u nog geen abonnement op Azure hebt, [Maak dan een gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Geen

## <a name="create-a-service-bus-namespace-and-a-queue"></a>Een Service Bus naam ruimte en een wachtrij maken

### <a name="review-the-template"></a>De sjabloon controleren

De sjabloon die in deze Quick Start wordt gebruikt, is afkomstig uit [Azure Quick](https://azure.microsoft.com/resources/templates/201-servicebus-create-queue)start-sjablonen.

:::code language="json" source="~/quickstart-templates/201-servicebus-create-queue/azuredeploy.json" range="1-75" highlight="31-63":::

De resources die in de sjabloon zijn gedefinieerd, zijn onder andere:

- [**Micro soft. ServiceBus/naam ruimten**](/azure/templates/microsoft.servicebus/namespaces)
- [**Micro soft. ServiceBus/naam ruimten/wacht rijen**](/azure/templates/microsoft.servicebus/namespaces/queues)

> [!NOTE]
> De volgende Azure Resource Manager sjablonen zijn beschikbaar voor downloaden en implementeren.
>
> * [Een Service Bus naam ruimte maken met een wachtrij-en autorisatie regel](service-bus-resource-manager-namespace-auth-rule.md)
> * [Een Service Bus naam ruimte met een onderwerp en een abonnement maken](service-bus-resource-manager-namespace-topic.md)
> * [Een Service Bus-naamruimte maken](service-bus-resource-manager-namespace.md)
> * [Een Service Bus naam ruimte maken met een onderwerp, een abonnement en een regel](service-bus-resource-manager-namespace-topic-with-rule.md)

U kunt meer uit de [sjablonen van Azure Quick](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Servicebus&pageNumber=1&sort=Popular) start vinden

### <a name="deploy-the-template"></a>De sjabloon implementeren

Met deze sjabloon implementeert u een Service Bus naam ruimte met een wachtrij.

[Service bus-wacht rijen](service-bus-queues-topics-subscriptions.md#queues) bieden de levering van het FIFO-bericht (First in, first out) aan een of meer concurrerende consumenten.

Als u de implementatie automatisch wilt uitvoeren, klikt u op de volgende knop: Maak een nieuwe resource groep voor de implementatie, zodat u deze later eenvoudig kunt opschonen.

[![Implementeren in Azure](./media/service-bus-resource-manager-namespace-queue/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-queue%2Fazuredeploy.json)

## <a name="verify-the-deployment"></a>De implementatie controleren

1. Selecteer bovenaan **meldingen** om de status van de implementatie te bekijken. Wacht tot de implementatie is geslaagd. Selecteer vervolgens **naar resource groep** in het meldings bericht om naar de pagina voor de resource groep met de naam ruimte service bus te gaan. 

    ![Melding van implementatie](./media/service-bus-resource-manager-namespace-queue/notification.png)
2. Controleer of uw Service Bus naam ruimte wordt weer geven in de lijst met resources. 

    ![Resource groep-naam ruimte](./media/service-bus-resource-manager-namespace-queue/resource-group-namespace.png)
3. Selecteer de naam ruimte in de lijst om de pagina **Service Bus naam ruimte** weer te geven. 

## <a name="cleanup-resources"></a>Resources opruimen

1. Navigeer in het Azure Portal naar de pagina **resource groep** voor uw resource groep.
2. Selecteer **Resourcegroep verwijderen** uit de taakbalk. 
3. Typ de naam van de resource groep en selecteer **verwijderen**. 

    ![Resource groep-verwijderen](./media/service-bus-resource-manager-namespace-queue/resource-group-delete.png)

## <a name="next-steps"></a>Volgende stappen

Zie het volgende onderwerp waarin wordt uitgelegd hoe u een autorisatie regel maakt voor de naam ruimte/wachtrij:

[Een Service Bus autorisatie regel voor een naam ruimte en een wachtrij maken met behulp van een Azure Resource Manager sjabloon](service-bus-resource-manager-namespace-auth-rule.md)

Meer informatie over het beheren van deze resources door de volgende artikelen te bekijken:

* [Service Bus met PowerShell beheren](service-bus-manage-with-ps.md)
* [Service Bus-resources beheren met de Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/releases)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/templates/template-syntax.md
[Service Bus namespace and queue template]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Learn more about Service Bus queues]: service-bus-queues-topics-subscriptions.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md
