---
title: Abonnement en regel van ServiceBus-onderwerp maken met Azure-sjabloon
description: Een naamruimte voor servicebus maken met onderwerp, abonnement en regel met azure resourcebeheersjabloon
services: service-bus-messaging
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: 9e0aaf58-0214-4bca-bd00-d29c08f9b1bc
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 11/27/2019
ms.author: spelluru
ms.openlocfilehash: 6cbaf447dfcf06ae11f2282d7d847978297af8b8
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384888"
---
# <a name="create-a-service-bus-namespace-with-topic-subscription-and-rule-using-an-azure-resource-manager-template"></a>Een naamruimte voor servicebus maken met onderwerp, abonnement en regel met behulp van een Azure Resource Manager-sjabloon

In dit artikel ziet u hoe u een Azure Resource Manager-sjabloon gebruikt waarmee een naamruimte voor servicebus met een onderwerp, abonnement en regel (filter) wordt gemaakt. In het artikel wordt uitgelegd hoe u opgeven welke resources worden geïmplementeerd en hoe parameters kunnen worden gedefinieerd die zijn opgegeven wanneer de implementatie wordt uitgevoerd. U kunt deze sjabloon gebruiken voor uw eigen implementaties of de sjabloon aanpassen aan uw eisen

Zie [Azure Resource Manager-sjablonen samenstellen][Authoring Azure Resource Manager templates] voor meer informatie over het maken van sjablonen.

Zie [Aanbevolen naamgevingsconventies voor Azure-bronnen voor][Recommended naming conventions for Azure resources]meer informatie over de praktijk en patronen op azure-bronnen.

Zie de [naamruimte Servicebus met onderwerp, abonnement en regel][Service Bus namespace with topic, subscription, and rule] voor de volledige sjabloon.

> [!NOTE]
> De volgende Azure Resource Manager-sjablonen zijn beschikbaar voor download en implementatie.
> 
> * [Een naamruimte voor servicebus maken met wachtrij- en autorisatieregel](service-bus-resource-manager-namespace-auth-rule.md)
> * [Een naamruimte voor servicebus maken met wachtrij](service-bus-resource-manager-namespace-queue.md)
> * [Een Service Bus-naamruimte maken](service-bus-resource-manager-namespace.md)
> * [Een naamruimte voor servicebus maken met onderwerp en abonnement](service-bus-resource-manager-namespace-topic.md)
> 
> Als u wilt controleren op de nieuwste sjablonen, gaat u naar de azure [quickstartsjablonengalerie][Azure Quickstart Templates] en zoekt u naar ServiceBus.

## <a name="what-do-you-deploy"></a>Wat zet je in?

Met deze sjabloon implementeert u een naamruimte servicebus met onderwerp, abonnement en regel (filter).

[Service Bus onderwerpen en abonnementen](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) bieden een een-op-veel vorm van communicatie, in een publiceren / *abonneren* patroon. Bij het gebruik van onderwerpen en abonnementen communiceren onderdelen van een gedistribueerde toepassing niet rechtstreeks met elkaar, maar wisselen ze berichten uit via onderwerp dat als tussenpersoon fungeert. Een abonnement op een onderwerp lijkt op een virtuele wachtrij die kopieën ontvangt van berichten die naar het onderwerp zijn verzonden. Met een filter op abonnement u opgeven welke berichten die naar een onderwerp worden verzonden, binnen een specifiek onderwerpabonnement moeten worden weergegeven.

## <a name="what-are-rules-filters"></a>Wat zijn regels (filters)?

In veel scenario's moeten berichten met specifieke kenmerken op verschillende manieren worden verwerkt. Als u deze aangepaste verwerking wilt inschakelen, u abonnementen configureren om berichten met specifieke eigenschappen te zoeken en vervolgens wijzigingen aan deze eigenschappen uit te voeren. Hoewel Service Bus-abonnementen alle berichten zien die naar het onderwerp worden verzonden, u alleen een subset van deze berichten kopiëren naar de virtuele abonnementswachtrij. Het wordt bereikt met behulp van abonnementsfilters. Zie [Regels en acties](service-bus-queues-topics-subscriptions.md#rules-and-actions)voor meer informatie over regels (filters).

Klik op de volgende knop om de implementatie automatisch uit te voeren:

[![Implementeren naar Azure](./media/service-bus-resource-manager-namespace-topic/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-topic-subscription-rule%2Fazuredeploy.json)

## <a name="parameters"></a>Parameters

Definieer met Azure Resource Manager parameters voor waarden die u wilt opgeven wanneer de sjabloon wordt geïmplementeerd. De sjabloon bevat een sectie met de naam `Parameters` die alle parameterwaarden bevat. Definieer een parameter voor de waarden die variëren op basis van het project dat u implementeert of op basis van de omgeving die u implementeert. Definieer geen parameters voor waarden die altijd hetzelfde blijven. De waarde van elke parameter wordt gebruikt in de sjabloon voor het definiëren van de resources die worden geïmplementeerd.

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

### <a name="servicebusrulename"></a>serviceBusRuleName

De naam van de regel(filter) die is gemaakt in de naamruimte servicebus.

```json
   "serviceBusRuleName": {
   "type": "string",
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

Hiermee maakt u een standaard naamruimte van de ServiceBus van het type **Messaging,** met onderwerp en abonnement en regels.

```json
 "resources": [{
        "apiVersion": "[variables('sbVersion')]",
        "name": "[parameters('serviceBusNamespaceName')]",
        "type": "Microsoft.ServiceBus/Namespaces",
        "location": "[variables('location')]",
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
                "path": "[parameters('serviceBusTopicName')]"
            },
            "resources": [{
                "apiVersion": "[variables('sbVersion')]",
                "name": "[parameters('serviceBusSubscriptionName')]",
                "type": "Subscriptions",
                "dependsOn": [
                    "[parameters('serviceBusTopicName')]"
                ],
                "properties": {},
                "resources": [{
                    "apiVersion": "[variables('sbVersion')]",
                    "name": "[parameters('serviceBusRuleName')]",
                    "type": "Rules",
                    "dependsOn": [
                        "[parameters('serviceBusSubscriptionName')]"
                    ],
                    "properties": {
                        "filterType": "SqlFilter",
                        "sqlFilter": {
                            "sqlExpression": "StoreName = 'Store1'",
                            "requiresPreprocessing": "false"
                        },
                        "action": {
                            "sqlExpression": "set FilterTag = 'true'"
                        }
                    }
                }]
            }]
        }]
    }]
```

Zie [naamruimten](/azure/templates/microsoft.servicebus/namespaces), [onderwerpen](/azure/templates/microsoft.servicebus/namespaces/topics), [abonnementen](/azure/templates/microsoft.servicebus/namespaces/topics/subscriptions)en [regels](/azure/templates/microsoft.servicebus/namespaces/topics/subscriptions/rules)voor syntaxis en eigenschappen van JSON .

## <a name="commands-to-run-deployment"></a>Opdrachten om implementatie uit te voeren

[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell

```powershell-interactive
New-AzureResourceGroupDeployment -Name \<deployment-name\> -ResourceGroupName \<resource-group-name\> -TemplateUri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-subscription-rule/azuredeploy.json>
```

## <a name="azure-cli"></a>Azure-CLI

```azurecli-interactive
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-subscription-rule/azuredeploy.json>
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het beheren van deze bronnen door deze artikelen te bekijken:

* [Azure-servicebus beheren](service-bus-management-libraries.md)
* [Service Bus met PowerShell beheren](service-bus-manage-with-ps.md)
* [Servicebusbronnen beheren met de Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/releases)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/templates/template-syntax.md
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Learn more about Service Bus topics and subscriptions]: service-bus-queues-topics-subscriptions.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md
[Recommended naming conventions for Azure resources]: /azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging
[Service Bus namespace with topic, subscription, and rule]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-topic-subscription-rule/
[Service Bus queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
