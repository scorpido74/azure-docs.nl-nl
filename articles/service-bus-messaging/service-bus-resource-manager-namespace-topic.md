---
title: Het naamruimteonderwerp Azure Service Bus maken met een sjabloon
description: 'Snelstart: een naamruimte voor servicebus maken met onderwerp en abonnement met azure resourcemanager-sjabloon'
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
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: c94b670a33f7640d2d6f428287b3ba0fab766bc5
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384871"
---
# <a name="quickstart-create-a-service-bus-namespace-with-topic-and-subscription-using-an-azure-resource-manager-template"></a>Snelstart: een naamruimte voor servicebus maken met onderwerp en abonnement met behulp van een Azure Resource Manager-sjabloon

In dit artikel ziet u hoe u een Azure Resource Manager-sjabloon gebruikt waarmee een naamruimte voor servicebus en een onderwerp en abonnement in die naamruimte worden gemaakt. In het artikel wordt uitgelegd hoe u opgeven welke resources worden geïmplementeerd en hoe parameters kunnen worden gedefinieerd die zijn opgegeven wanneer de implementatie wordt uitgevoerd. U kunt deze sjabloon gebruiken voor uw eigen implementaties of de sjabloon aanpassen aan uw eisen

Zie [Azure Resource Manager-sjablonen samenstellen][Authoring Azure Resource Manager templates] voor meer informatie over het maken van sjablonen.

Zie de [naamruimte Servicebus met onderwerp en abonnementssjabloon][Service Bus namespace with topic and subscription] voor de volledige sjabloon.

> [!NOTE]
> De volgende Azure Resource Manager-sjablonen zijn beschikbaar voor download en implementatie.
> 
> * [Een Service Bus-naamruimte maken](service-bus-resource-manager-namespace.md)
> * [Een naamruimte voor servicebus maken met wachtrij](service-bus-resource-manager-namespace-queue.md)
> * [Een naamruimte voor servicebus maken met wachtrij- en autorisatieregel](service-bus-resource-manager-namespace-auth-rule.md)
> * [Een naamruimte voor servicebus maken met onderwerp, abonnement en regel](service-bus-resource-manager-namespace-topic-with-rule.md)
> 
> Als u wilt controleren of u de nieuwste sjablonen wilt controleren, gaat u naar de azure [quickstartsjablonengalerie][Azure Quickstart Templates] en zoekt u naar **Servicebus**.

## <a name="what-do-you-deploy"></a>Wat zet je in?

Met deze sjabloon implementeert u een servicebusnaamruimte met onderwerp en abonnement.

[Service Bus onderwerpen en abonnementen](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) bieden een een-op-veel vorm van communicatie, in een publiceren / *abonneren* patroon.

Klik op de volgende knop om de implementatie automatisch uit te voeren:

[![Implementeren naar Azure](./media/service-bus-resource-manager-namespace-topic/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-topic-and-subscription%2Fazuredeploy.json)

## <a name="parameters"></a>Parameters

Met Azure Resource Manager kunt u parameters definiëren voor waarden die u wilt opgeven wanneer de sjabloon wordt geïmplementeerd. De sjabloon bevat `Parameters` een sectie die wordt aangeroepen en die alle parameterwaarden bevat. Definieer een parameter voor de waarden die variëren op basis van het project dat u implementeert of op basis van de omgeving waarop u implementeert. Definieer geen parameters voor waarden die altijd hetzelfde blijven. De waarde van elke parameter wordt gebruikt in de sjabloon voor het definiëren van de resources die worden geïmplementeerd.

De sjabloon definieert de volgende parameters:

### <a name="servicebusnamespacename"></a>serviceBusNamespaceName

De naam van de naamruimte servicebus om te maken.

```json
"serviceBusNamespaceName": {
"type": "string"
}
```

### <a name="servicebustopicname"></a>serviceBusTopicName

De naam van het onderwerp dat is gemaakt in de naamruimte servicebus.

```json
"serviceBusTopicName": {
"type": "string"
}
```

### <a name="servicebussubscriptionname"></a>serviceBusAbonnementNaam

De naam van het abonnement dat is gemaakt in de naamruimte servicebus.

```json
"serviceBusSubscriptionName": {
"type": "string"
}
```

### <a name="servicebusapiversion"></a>serviceBusApiVersie

De API-versie van de sjabloon servicebus.

```json
"serviceBusApiVersion": { 
       "type": "string", 
       "defaultValue": "2017-04-01", 
       "metadata": { 
           "description": "Service Bus ApiVersion used by the template" 
       }
```

## <a name="resources-to-deploy"></a>Resources om te implementeren

Hiermee maakt u een standaard naamruimte van de servicebus van het type **Messaging,** met onderwerp en abonnement.

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

Zie [naamruimten](/azure/templates/microsoft.servicebus/namespaces), [onderwerpen](/azure/templates/microsoft.servicebus/namespaces/topics)en [abonnementen](/azure/templates/microsoft.servicebus/namespaces/topics/subscriptions)voor syntaxis en eigenschappen van JSON .

## <a name="commands-to-run-deployment"></a>Opdrachten om implementatie uit te voeren

[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell

```powershell-interactive
New-AzureResourceGroupDeployment -Name \<deployment-name\> -ResourceGroupName \<resource-group-name\> -TemplateUri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-and-subscription/azuredeploy.json>
```

## <a name="azure-cli"></a>Azure-CLI

```azurecli-interactive
az group deployment create \<my-resource-group\> --name \<my-deployment-name\> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-and-subscription/azuredeploy.json>
```

## <a name="next-steps"></a>Volgende stappen

Nu u resources hebt gemaakt en geïmplementeerd met Azure Resource Manager, leest u hoe u deze bronnen beheren door deze artikelen te bekijken:

* [Service Bus met PowerShell beheren](service-bus-manage-with-ps.md)
* [Servicebusbronnen beheren met de Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/releases)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/templates/template-syntax.md
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Learn more about Service Bus topics and subscriptions]: service-bus-queues-topics-subscriptions.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md
[Service Bus namespace with topic and subscription]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-topic-and-subscription/
