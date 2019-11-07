---
title: 'Quick Start: een abonnement op de Azure Service Bus naam ruimte maken met behulp van Azure Resource Manager sjabloon'
description: 'Snelstartgids: een Service Bus naam ruimte met een onderwerp en een abonnement maken met behulp van Azure Resource Manager sjabloon'
services: service-bus-messaging
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: d3d55200-5c60-4b5f-822d-59974cafff0e
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: quickstart
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 11/05/2019
ms.author: spelluru
ms.openlocfilehash: a43b945ce11736b16e8e11edbe988a3c48f62d51
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73718781"
---
# <a name="quickstart-create-a-service-bus-namespace-with-topic-and-subscription-using-an-azure-resource-manager-template"></a>Snelstartgids: een Service Bus naam ruimte met een onderwerp en een abonnement maken met behulp van een Azure Resource Manager sjabloon

In dit artikel wordt beschreven hoe u een Azure Resource Manager sjabloon gebruikt waarmee u een Service Bus naam ruimte en een onderwerp en een abonnement binnen die naam ruimte maakt. In het artikel wordt uitgelegd hoe u kunt opgeven welke resources worden geïmplementeerd en hoe para meters moeten worden gedefinieerd die worden opgegeven wanneer de implementatie wordt uitgevoerd. U kunt deze sjabloon gebruiken voor uw eigen implementaties of de sjabloon aanpassen aan uw eisen

Zie [Azure Resource Manager sjablonen ontwerpen][Authoring Azure Resource Manager templates]voor meer informatie over het maken van sjablonen.

Zie voor de volledige sjabloon de [Service Bus naam ruimte met onderwerp en abonnements][Service Bus namespace with topic and subscription] sjabloon.

> [!NOTE]
> De volgende Azure Resource Manager sjablonen zijn beschikbaar voor downloaden en implementeren.
> 
> * [Een Service Bus naam ruimte maken](service-bus-resource-manager-namespace.md)
> * [Een Service Bus naam ruimte maken met wachtrij](service-bus-resource-manager-namespace-queue.md)
> * [Een Service Bus naam ruimte maken met een wachtrij-en autorisatie regel](service-bus-resource-manager-namespace-auth-rule.md)
> * [Een Service Bus naam ruimte maken met een onderwerp, een abonnement en een regel](service-bus-resource-manager-namespace-topic-with-rule.md)
> 
> Als u de meest recente sjablonen wilt controleren, gaat u naar de galerie met [Azure Quick Start sjablonen][Azure Quickstart Templates] en zoekt u naar **Service Bus**.
> 
> 

## <a name="what-do-you-deploy"></a>Wat gaat u implementeren?

Met deze sjabloon implementeert u een Service Bus naam ruimte met een onderwerp en een abonnement.

[Service Bus-onderwerpen en-abonnementen](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) bieden een een-op-veel communicatie vorm, in een patroon voor *publiceren/abonneren* .

Klik op de volgende knop om de implementatie automatisch uit te voeren:

[![Implementeren in Azure](./media/service-bus-resource-manager-namespace-topic/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-topic-and-subscription%2Fazuredeploy.json)

## <a name="parameters"></a>Parameters

Met Azure Resource Manager kunt u parameters definiëren voor waarden die u wilt opgeven wanneer de sjabloon wordt geïmplementeerd. De sjabloon bevat een sectie met de naam `Parameters` die alle parameter waarden bevat. Definieer een para meter voor die waarden die variëren op basis van het project dat u implementeert of op basis van de omgeving waarin u de implementatie uitvoert. Definieer geen parameters voor waarden die altijd hetzelfde blijven. De waarde van elke parameter wordt gebruikt in de sjabloon voor het definiëren van de resources die worden geïmplementeerd.

De sjabloon definieert de volgende para meters:

### <a name="servicebusnamespacename"></a>serviceBusNamespaceName
De naam van de Service Bus naam ruimte die moet worden gemaakt.

```json
"serviceBusNamespaceName": {
"type": "string"
}
```

### <a name="servicebustopicname"></a>serviceBusTopicName
De naam van het onderwerp dat in de Service Bus naam ruimte is gemaakt.

```json
"serviceBusTopicName": {
"type": "string"
}
```

### <a name="servicebussubscriptionname"></a>serviceBusSubscriptionName
De naam van het abonnement dat in de naam ruimte van de Service Bus is gemaakt.

```json
"serviceBusSubscriptionName": {
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
Hiermee maakt u een standaard Service Bus naam ruimte van het type **berichten**, met onderwerp en abonnement.

```json
"resources": [{
        "apiVersion": "[variables('sbVersion')]",
        "name": "[parameters('serviceBusNamespaceName')]",
        "type": "Microsoft.ServiceBus/Namespaces",
        "location": "[variables('location')]",
        "kind": "Messaging",
        "sku": {
            "name": "Standard",
        },
        "resources": [{
            "apiVersion": "[variables('sbVersion')]",
            "name": "[parameters('serviceBusTopicName')]",
            "type": "Topics",
            "dependsOn": [
                "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
            ],
            "properties": {
                "path": "[parameters('serviceBusTopicName')]",
            },
            "resources": [{
                "apiVersion": "[variables('sbVersion')]",
                "name": "[parameters('serviceBusSubscriptionName')]",
                "type": "Subscriptions",
                "dependsOn": [
                    "[parameters('serviceBusTopicName')]"
                ],
                "properties": {}
            }]
        }]
    }]
```

Voor de JSON-syntaxis en-eigenschappen raadpleegt u [naam ruimten](/azure/templates/microsoft.servicebus/namespaces), [onderwerpen](/azure/templates/microsoft.servicebus/namespaces/topics)en [abonnementen](/azure/templates/microsoft.servicebus/namespaces/topics/subscriptions).

## <a name="commands-to-run-deployment"></a>Opdrachten om implementatie uit te voeren
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell
```powershell
New-AzureResourceGroupDeployment -Name \<deployment-name\> -ResourceGroupName \<resource-group-name\> -TemplateUri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-and-subscription/azuredeploy.json>
```

## <a name="azure-cli"></a>Azure CLI
```azurecli
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-and-subscription/azuredeploy.json>
```

## <a name="next-steps"></a>Volgende stappen
Nu u resources hebt gemaakt en geïmplementeerd met behulp van Azure Resource Manager, leert u hoe u deze resources kunt beheren door de volgende artikelen te bekijken:

* [Service Bus met PowerShell beheren](service-bus-manage-with-ps.md)
* [Service Bus-resources beheren met de Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/releases)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Learn more about Service Bus topics and subscriptions]: service-bus-queues-topics-subscriptions.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md
[Service Bus namespace with topic and subscription]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-topic-and-subscription/
