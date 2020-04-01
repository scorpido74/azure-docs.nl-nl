---
title: Naamruimte en wachtrij voor Azure-servicebus maken met Azure-sjabloon
description: 'Snelstart: naamruimte voor servicebus en een wachtrij maken met azure resourcemanager-sjabloon'
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
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422621"
---
# <a name="quickstart-create-a-service-bus-namespace-and-a-queue-using-an-azure-resource-manager-template"></a>Snelstart: naamruimte voor servicebus en een wachtrij maken met een sjabloon Azure Resource Manager

In dit artikel ziet u hoe u een Azure Resource Manager-sjabloon gebruikt waarmee een naamruimte voor servicebus en een wachtrij in die naamruimte wordt gemaakt. In het artikel wordt uitgelegd hoe u opgeven welke resources worden geïmplementeerd en hoe parameters kunnen worden gedefinieerd die zijn opgegeven wanneer de implementatie wordt uitgevoerd. U kunt deze sjabloon gebruiken voor uw eigen implementaties of de sjabloon aanpassen aan uw eisen.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Geen

## <a name="create-a-service-bus-namespace-and-a-queue"></a>Een naamruimte voor servicebus en een wachtrij maken

### <a name="review-the-template"></a>De sjabloon bekijken

De sjabloon die in deze quickstart wordt gebruikt, is afkomstig van [Azure Quickstart-sjablonen.](https://azure.microsoft.com/resources/templates/201-servicebus-create-queue)

:::code language="json" source="~/quickstart-templates/201-servicebus-create-queue/azuredeploy.json" range="1-75" highlight="31-63":::

De resources die in de sjabloon zijn gedefinieerd, zijn:

- [**Microsoft.ServiceBus/naamruimten**](/azure/templates/microsoft.servicebus/namespaces)
- [**Microsoft.ServiceBus/naamruimten/wachtrijen**](/azure/templates/microsoft.servicebus/namespaces/queues)

> [!NOTE]
> De volgende Azure Resource Manager-sjablonen zijn beschikbaar voor download en implementatie.
>
> * [Een naamruimte voor servicebus maken met wachtrij- en autorisatieregel](service-bus-resource-manager-namespace-auth-rule.md)
> * [Een naamruimte voor servicebus maken met onderwerp en abonnement](service-bus-resource-manager-namespace-topic.md)
> * [Een Service Bus-naamruimte maken](service-bus-resource-manager-namespace.md)
> * [Een naamruimte voor servicebus maken met onderwerp, abonnement en regel](service-bus-resource-manager-namespace-topic-with-rule.md)

U meer sjabloon vinden in [Azure Quickstart-sjablonen](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Servicebus&pageNumber=1&sort=Popular)

### <a name="deploy-the-template"></a>De sjabloon implementeren

Met deze sjabloon implementeert u een naamruimte voor servicebus met een wachtrij.

[Service Bus wachtrijen](service-bus-queues-topics-subscriptions.md#queues) bieden First In, First Out (FIFO) bericht levering aan een of meer concurrerende consumenten.

Als u de implementatie automatisch wilt uitvoeren, klikt u op de volgende knop: Een nieuwe brongroep maken voor de implementatie, zodat u later gemakkelijk worden opgemaakt.

[![Implementeren in Azure](./media/service-bus-resource-manager-namespace-queue/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-queue%2Fazuredeploy.json)

## <a name="verify-the-deployment"></a>De implementatie controleren

1. Selecteer **Meldingen** bovenaan om de status van de implementatie te bekijken. Wacht tot de implementatie slaagt. Selecteer vervolgens **Ga naar resourcegroep** in het meldingsbericht om naar de pagina te navigeren voor de resourcegroep die de naamruimte servicebus bevat. 

    ![Melding vanaf implementatie](./media/service-bus-resource-manager-namespace-queue/notification.png)
2. Controleer of u de naamruimte van uw servicebus ziet in de lijst met bronnen. 

    ![Resourcegroep - naamruimte](./media/service-bus-resource-manager-namespace-queue/resource-group-namespace.png)
3. Selecteer de naamruimte in de lijst om de pagina **Naamruimte servicebus** weer te geven. 

## <a name="cleanup-resources"></a>Resources opruimen

1. Navigeer in de Azure-portal naar de **groepspagina Resource** voor uw resourcegroep.
2. Selecteer **Resourcegroep verwijderen** uit de taakbalk. 
3. Typ de naam van de resourcegroep en selecteer **Verwijderen**. 

    ![Resourcegroep - verwijderen](./media/service-bus-resource-manager-namespace-queue/resource-group-delete.png)

## <a name="next-steps"></a>Volgende stappen

Zie het volgende onderwerp met een autorisatieregel voor de naamruimte/wachtrij:

[Een autorisatieregel servicebus maken voor naamruimte en wachtrij met een sjabloon Azure Resource Manager](service-bus-resource-manager-namespace-auth-rule.md)

Meer informatie over het beheren van deze bronnen door deze artikelen te bekijken:

* [Service Bus met PowerShell beheren](service-bus-manage-with-ps.md)
* [Servicebusbronnen beheren met de Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/releases)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/templates/template-syntax.md
[Service Bus namespace and queue template]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Learn more about Service Bus queues]: service-bus-queues-topics-subscriptions.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md
