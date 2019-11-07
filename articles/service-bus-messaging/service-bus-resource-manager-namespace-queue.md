---
title: 'Snelstartgids: Azure Service Bus naam ruimte en wachtrij maken met Azure Resource Manager sjabloon'
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
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 11/05/2019
ms.author: spelluru
ms.openlocfilehash: 40aeff225cfa6d499ebdfae6736e0684b2a27244
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73718791"
---
# <a name="quickstart-create-a-service-bus-namespace-and-a-queue-using-an-azure-resource-manager-template"></a>Snelstartgids: een Service Bus naam ruimte en een wachtrij maken met behulp van een Azure Resource Manager sjabloon

In dit artikel wordt beschreven hoe u een Azure Resource Manager sjabloon gebruikt waarmee een Service Bus naam ruimte en een wachtrij binnen die naam ruimte wordt gemaakt. In het artikel wordt uitgelegd hoe u kunt opgeven welke resources worden geïmplementeerd en hoe para meters moeten worden gedefinieerd die worden opgegeven wanneer de implementatie wordt uitgevoerd. U kunt deze sjabloon gebruiken voor uw eigen implementaties of de sjabloon aanpassen aan uw eisen.

Zie [Azure Resource Manager sjablonen ontwerpen][Authoring Azure Resource Manager templates]voor meer informatie over het maken van sjablonen.

Voor de volledige sjabloon, zie de [Service Bus naam ruimte en wachtrij sjabloon][Service Bus namespace and queue template] op github.

> [!NOTE]
> De volgende Azure Resource Manager sjablonen zijn beschikbaar voor downloaden en implementeren.
> 
> * [Een Service Bus naam ruimte maken met een wachtrij-en autorisatie regel](service-bus-resource-manager-namespace-auth-rule.md)
> * [Een Service Bus naam ruimte met een onderwerp en een abonnement maken](service-bus-resource-manager-namespace-topic.md)
> * [Een Service Bus naam ruimte maken](service-bus-resource-manager-namespace.md)
> * [Een Service Bus naam ruimte maken met een onderwerp, een abonnement en een regel](service-bus-resource-manager-namespace-topic-with-rule.md)
> 
> Als u de meest recente sjablonen wilt controleren, gaat u naar de galerie met [Azure Quick Start sjablonen][Azure Quickstart Templates] en zoekt u naar **Service Bus**.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="what-will-you-deploy"></a>Wat wilt u implementeren?

Met deze sjabloon implementeert u een Service Bus naam ruimte met een wachtrij.

[Service bus-wacht rijen](service-bus-queues-topics-subscriptions.md#queues) bieden de levering van het FIFO-bericht (First in, first out) aan een of meer concurrerende consumenten.

Klik op de volgende knop om de implementatie automatisch uit te voeren:

[![Implementeren in Azure](./media/service-bus-resource-manager-namespace-queue/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-queue%2Fazuredeploy.json)

## <a name="parameters"></a>Parameters

Met Azure Resource Manager kunt u parameters definiëren voor waarden die u wilt opgeven wanneer de sjabloon wordt geïmplementeerd. De sjabloon bevat een sectie met de naam `Parameters` die alle parameter waarden bevat. U moet een para meter definiëren voor die waarden die variëren op basis van het project dat u implementeert of op basis van de omgeving waarin u implementeert. Definieer geen para meters voor waarden die altijd hetzelfde blijven. De waarde van elke parameter wordt gebruikt in de sjabloon voor het definiëren van de resources die worden geïmplementeerd.

De sjabloon definieert de volgende parameters.

### <a name="servicebusnamespacename"></a>serviceBusNamespaceName
De naam van de Service Bus naam ruimte die moet worden gemaakt.

```json
"serviceBusNamespaceName": {
"type": "string",
"metadata": { 
    "description": "Name of the Service Bus namespace" 
    }
}
```

### <a name="servicebusqueuename"></a>serviceBusQueueName
De naam van de wachtrij die in de Service Bus naam ruimte is gemaakt.

```json
"serviceBusQueueName": {
"type": "string"
}
```

### <a name="servicebusapiversion"></a>serviceBusApiVersion
De API-versie van de Service Bus van de sjabloon.

```json
"serviceBusApiVersion": { 
       "type": "string", 
       "defaultValue": "2017-04-01", 
       "metadata": { 
           "description": "Service Bus ApiVersion used by the template" 
       }
```

## <a name="resources-to-deploy"></a>Resources om te implementeren
Hiermee maakt u een standaard Service Bus naam ruimte van het type **Messa ging**, met een wachtrij.

```json
{
    "resources": [{
        "apiVersion": "2017-04-01",
        "name": "[parameters('serviceBusNamespaceName')]",
        "type": "Microsoft.ServiceBus/namespaces",
        "location": "[parameters('location')]",
        "sku": {
            "name": "Standard"
        },
        "properties": {},
        "resources": [{
            "apiVersion": "[variables('sbVersion')]",
            "name": "[parameters('serviceBusQueueName')]",
            "type": "Queues",
            "dependsOn": [
                "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
            ],
            "properties": {
                "path": "[parameters('serviceBusQueueName')]"
            }
        }]
    }]
}
```

Zie [naam ruimten](/azure/templates/microsoft.servicebus/namespaces) en [wacht rijen](/azure/templates/microsoft.servicebus/namespaces/queues)voor de JSON-syntaxis en-eigenschappen.

## <a name="commands-to-run-deployment"></a>Opdrachten om implementatie uit te voeren
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell

```powershell
New-AzResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-queue/azuredeploy.json>
```

## <a name="azure-cli"></a>Azure CLI

```azurecli
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-queue/azuredeploy.json>
```

## <a name="next-steps"></a>Volgende stappen
Zie het volgende onderwerp waarin wordt uitgelegd hoe u een autorisatie regel maakt voor de naam ruimte/wachtrij: [een service bus-autorisatie regel voor een naam ruimte en een wachtrij maken met behulp van een Azure Resource Manager sjabloon](service-bus-resource-manager-namespace-auth-rule.md)

Meer informatie over het beheren van deze resources door de volgende artikelen te bekijken:

* [Service Bus met PowerShell beheren](service-bus-manage-with-ps.md)
* [Service Bus-resources beheren met de Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/releases)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Service Bus namespace and queue template]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Learn more about Service Bus queues]: service-bus-queues-topics-subscriptions.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md
