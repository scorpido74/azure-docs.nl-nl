---
title: Een autorisatieregel voor servicebus maken met een Azure-sjabloon
description: Een autorisatieregel servicebus maken voor naamruimte en wachtrij met azure resourcebeheersjabloon
services: service-bus-messaging
documentationcenter: .net
author: axisc
manager: timlt
editor: spelluru
ms.assetid: 7f1443a0-5fa8-4d90-8637-1a977ef0b1f0
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 12/20/2019
ms.author: aschhab
ms.openlocfilehash: 1bfb2d2d946a85c1d051315fb29a5a63f7a00871
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384922"
---
# <a name="create-a-service-bus-authorization-rule-for-namespace-and-queue-using-an-azure-resource-manager-template"></a>Een autorisatieregel servicebus maken voor naamruimte en wachtrij met een sjabloon Azure Resource Manager

In dit artikel ziet u hoe u een Azure Resource Manager-sjabloon gebruikt waarmee een [autorisatieregel](service-bus-authentication-and-authorization.md#shared-access-signature) voor naamruimte en wachtrij servicebus wordt gemaakt. In het artikel wordt uitgelegd hoe u opgeven welke resources worden geïmplementeerd en hoe parameters kunnen worden gedefinieerd die zijn opgegeven wanneer de implementatie wordt uitgevoerd. U kunt deze sjabloon gebruiken voor uw eigen implementaties of de sjabloon aanpassen aan uw eisen.

Zie Sjablonen voor Azure Resource Manager ontwerpen voor meer informatie over het maken [van sjablonen.][Authoring Azure Resource Manager templates]

Zie de [machtigingsregelsjabloon Servicebus][Service Bus auth rule template] op GitHub voor de volledige sjabloon.

> [!NOTE]
> De volgende Azure Resource Manager-sjablonen zijn beschikbaar voor download en implementatie.
> 
> * [Een Service Bus-naamruimte maken](service-bus-resource-manager-namespace.md)
> * [Een naamruimte voor servicebus maken met wachtrij](service-bus-resource-manager-namespace-queue.md)
> * [Een naamruimte voor servicebus maken met onderwerp en abonnement](service-bus-resource-manager-namespace-topic.md)
> * [Een naamruimte voor servicebus maken met onderwerp, abonnement en regel](service-bus-resource-manager-namespace-topic-with-rule.md)
> 
> Als u wilt controleren of u de nieuwste sjablonen wilt controleren, gaat u naar de azure [quickstartsjablonengalerie][Azure Quickstart Templates] en zoekt u naar **Servicebus**.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="what-will-you-deploy"></a>Wat wilt u implementeren?

Met deze sjabloon implementeert u een autorisatieregel servicebus voor een naamruimte- en berichtenentiteit (in dit geval een wachtrij).

Deze sjabloon gebruikt [Shared Access Signature (SAS)](service-bus-sas.md) voor verificatie. Met SAS kunnen toepassingen zich verifiëren naar Service Bus met behulp van een toegangssleutel die is geconfigureerd op de naamruimte of op de berichtenentiteit (wachtrij of onderwerp) waaraan specifieke rechten zijn gekoppeld. U deze sleutel vervolgens gebruiken om een SAS-token te genereren dat clients op hun beurt kunnen gebruiken om te verifiëren bij Service Bus.

Klik op de volgende knop om de implementatie automatisch uit te voeren:

[![Implementeren naar Azure](./media/service-bus-resource-manager-namespace-auth-rule/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F301-servicebus-create-authrule-namespace-and-queue%2Fazuredeploy.json)

## <a name="parameters"></a>Parameters

Met Azure Resource Manager kunt u parameters definiëren voor waarden die u wilt opgeven wanneer de sjabloon wordt geïmplementeerd. De sjabloon bevat `Parameters` een sectie die wordt aangeroepen en die alle parameterwaarden bevat. U moet een parameter definiëren voor de waarden die variëren op basis van het project dat u implementeert of op basis van de omgeving waarop u implementeert. Definieer geen parameters voor waarden die altijd hetzelfde blijven. De waarde van elke parameter wordt gebruikt in de sjabloon voor het definiëren van de resources die worden geïmplementeerd.

De sjabloon definieert de volgende parameters.

### <a name="servicebusnamespacename"></a>serviceBusNamespaceName

De naam van de naamruimte servicebus om te maken.

```json
"serviceBusNamespaceName": {
"type": "string"
}
```

### <a name="namespaceauthorizationrulename"></a>naamruimteAutorisatieRegelnaam

De naam van de autorisatieregel voor de naamruimte.

```json
"namespaceAuthorizationRuleName ": {
"type": "string"
}
```

### <a name="servicebusqueuename"></a>serviceBusQueueName

De naam van de wachtrij in de naamruimte servicebus.

```json
"serviceBusQueueName": {
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

Hiermee maakt u een standaardnaamruimte servicebus van het type **Messaging**en een autorisatieregel servicebus voor naamruimte en entiteit.

```json
"resources": [
        {
            "apiVersion": "[variables('sbVersion')]",
            "name": "[parameters('serviceBusNamespaceName')]",
            "type": "Microsoft.ServiceBus/namespaces",
            "location": "[variables('location')]",
            "kind": "Messaging",
            "sku": {
                "name": "Standard",
            },
            "resources": [
                {
                    "apiVersion": "[variables('sbVersion')]",
                    "name": "[parameters('serviceBusQueueName')]",
                    "type": "Queues",
                    "dependsOn": [
                        "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
                    ],
                    "properties": {
                        "path": "[parameters('serviceBusQueueName')]"
                    },
                    "resources": [
                        {
                            "apiVersion": "[variables('sbVersion')]",
                            "name": "[parameters('queueAuthorizationRuleName')]",
                            "type": "authorizationRules",
                            "dependsOn": [
                                "[parameters('serviceBusQueueName')]"
                            ],
                            "properties": {
                                "Rights": ["Listen"]
                            }
                        }
                    ]
                }
            ]
        }, {
            "apiVersion": "[variables('sbVersion')]",
            "name": "[variables('namespaceAuthRuleName')]",
            "type": "Microsoft.ServiceBus/namespaces/authorizationRules",
            "dependsOn": ["[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"],
            "location": "[resourceGroup().location]",
            "properties": {
                "Rights": ["Send"]
            }
        }
    ]
```

Zie [naamruimten](/azure/templates/microsoft.servicebus/namespaces), [wachtrijen](/azure/templates/microsoft.servicebus/namespaces/queues)en [autorisatieregels](/azure/templates/microsoft.servicebus/namespaces/authorizationrules)voor syntaxis en eigenschappen van JSON .

## <a name="commands-to-run-deployment"></a>Opdrachten om implementatie uit te voeren

[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell

```powershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/301-servicebus-create-authrule-namespace-and-queue/azuredeploy.json>
```

## <a name="azure-cli"></a>Azure-CLI

```azurecli-interactive
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/301-servicebus-create-authrule-namespace-and-queue/azuredeploy.json>
```

## <a name="next-steps"></a>Volgende stappen

Nu u resources hebt gemaakt en geïmplementeerd met Azure Resource Manager, leest u hoe u deze bronnen beheren door deze artikelen te bekijken:

* [Service Bus met PowerShell beheren](service-bus-powershell-how-to-provision.md)
* [Servicebusbronnen beheren met de Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/releases)
* [Vereenvoudigde Service Bus-verificatie en -autorisatie](service-bus-authentication-and-authorization.md)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/templates/template-syntax.md
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md
[Service Bus auth rule template]: https://github.com/Azure/azure-quickstart-templates/blob/master/301-servicebus-create-authrule-namespace-and-queue/
