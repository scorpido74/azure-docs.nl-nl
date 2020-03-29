---
title: Azure Service Bus-bronnen maken met sjablonen
description: Azure Resource Manager-sjablonen gebruiken om de creatie van Service Bus-bronnen te automatiseren
services: service-bus-messaging
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: 24f6a207-0fa4-49cf-8a58-963f9e2fd655
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 09/11/2018
ms.author: spelluru
ms.openlocfilehash: 9bc784ee57b9bde393408cbefa9a197aebc59b08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76264455"
---
# <a name="create-service-bus-resources-using-azure-resource-manager-templates"></a>Servicebusbronnen maken met Azure Resource Manager-sjablonen

In dit artikel wordt beschreven hoe u Service Bus-resources maken en implementeren met Azure Resource Manager-sjablonen, PowerShell en de resourceprovider servicebus.

Met Azure Resource Manager-sjablonen u de resources definiëren die moeten worden geïmplementeerd voor een oplossing en parameters en variabelen opgeven waarmee u waarden voor verschillende omgevingen invoeren. De sjabloon is geschreven in JSON en bestaat uit expressies die u gebruiken om waarden voor uw implementatie te construeren. Zie [structuur en syntaxis van Azure Resource Manager-sjablonen](../azure-resource-manager/templates/template-syntax.md)voor gedetailleerde informatie over het schrijven van Azure Resource Manager-sjablonen en een bespreking van de sjabloonindeling.

> [!NOTE]
> De voorbeelden in dit artikel laten zien hoe u Azure Resource Manager gebruiken om een naamruimte en berichtenentiteit (wachtrij) van servicebus te maken. Ga voor andere sjabloonvoorbeelden naar de [azure quickstartsjablonengalerie][Azure Quickstart Templates gallery] en zoek naar **Servicebus**.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="service-bus-resource-manager-templates"></a>Sjablonen servicebusbronbeheer

Deze Azure Resource Manager-sjablonen voor servicebus zijn beschikbaar voor download en implementatie. Klik op de volgende koppelingen voor meer informatie over elke koppelingen naar de sjablonen op GitHub:

* [Een Service Bus-naamruimte maken](service-bus-resource-manager-namespace.md)
* [Een naamruimte voor servicebus maken met wachtrij](service-bus-resource-manager-namespace-queue.md)
* [Een naamruimte voor servicebus maken met onderwerp en abonnement](service-bus-resource-manager-namespace-topic.md)
* [Een naamruimte voor servicebus maken met wachtrij- en autorisatieregel](service-bus-resource-manager-namespace-auth-rule.md)
* [Een naamruimte voor servicebus maken met onderwerp, abonnement en regel](service-bus-resource-manager-namespace-topic-with-rule.md)

## <a name="deploy-with-powershell"></a>Implementeren met PowerShell

In de volgende procedure wordt beschreven hoe u PowerShell gebruiken om een Azure Resource Manager-sjabloon te implementeren waarmee een naamruimte voor servicebus standaardlaag en een wachtrij in die naamruimte wordt gemaakt. Dit voorbeeld is gebaseerd op de [naamruimte van een servicebus maken met wachtrijsjabloon.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-servicebus-create-queue) De geschatte workflow is als volgt:

1. Installeer PowerShell.
2. Maak de sjabloon en (optioneel) een parameterbestand.
3. Log in PowerShell in bij uw Azure-account.
4. Maak een nieuwe resourcegroep als deze niet bestaat.
5. Test de implementatie.
6. Stel desgewenst de implementatiemodus in.
7. Implementeer de sjabloon.

Zie Resources implementeren met Azure Resource [Manager-sjablonen][Deploy resources with Azure Resource Manager templates]voor volledige informatie over het implementeren van Azure Resource Manager-sjablonen.

### <a name="install-powershell"></a>PowerShell installeren

Installeer Azure PowerShell door de instructies te volgen in [Aan de slag met Azure PowerShell](/powershell/azure/get-started-azureps).

### <a name="create-a-template"></a>Een sjabloon maken

Kloon de repository of kopieer de sjabloon [201-servicebus-create-queue](https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/azuredeploy.json) van GitHub:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "serviceBusNamespaceName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Service Bus namespace"
      }
    },
    "serviceBusQueueName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Queue"
      }
    }
  },
  "variables": {
    "defaultSASKeyName": "RootManageSharedAccessKey",
    "authRuleResourceId": "[resourceId('Microsoft.ServiceBus/namespaces/authorizationRules', parameters('serviceBusNamespaceName'), variables('defaultSASKeyName'))]",
    "sbVersion": "2017-04-01"
  },
  "resources": [
    {
      "apiVersion": "2017-04-01",
      "name": "[parameters('serviceBusNamespaceName')]",
      "type": "Microsoft.ServiceBus/Namespaces",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard"
      },
      "properties": {},
      "resources": [
        {
          "apiVersion": "2017-04-01",
          "name": "[parameters('serviceBusQueueName')]",
          "type": "Queues",
          "dependsOn": [
            "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
          ],
          "properties": {
            "lockDuration": "PT5M",
            "maxSizeInMegabytes": "1024",
            "requiresDuplicateDetection": "false",
            "requiresSession": "false",
            "defaultMessageTimeToLive": "P10675199DT2H48M5.4775807S",
            "deadLetteringOnMessageExpiration": "false",
            "duplicateDetectionHistoryTimeWindow": "PT10M",
            "maxDeliveryCount": "10",
            "autoDeleteOnIdle": "P10675199DT2H48M5.4775807S",
            "enablePartitioning": "false",
            "enableExpress": "false"
          }
        }
      ]
    }
  ],
  "outputs": {
    "NamespaceConnectionString": {
      "type": "string",
      "value": "[listkeys(variables('authRuleResourceId'), variables('sbVersion')).primaryConnectionString]"
    },
    "SharedAccessPolicyPrimaryKey": {
      "type": "string",
      "value": "[listkeys(variables('authRuleResourceId'), variables('sbVersion')).primaryKey]"
    }
  }
}
```

### <a name="create-a-parameters-file-optional"></a>Een parametersbestand maken (optioneel)

Als u een optioneel parametersbestand wilt gebruiken, kopieert u het bestand [201-servicebus-create-queue.](https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/azuredeploy.parameters.json) Vervang de `serviceBusNamespaceName` waarde van de naam van de naamruimte servicebus die u `serviceBusQueueName` in deze implementatie wilt maken en vervang de waarde van de naam van de wachtrij die u wilt maken.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "serviceBusNamespaceName": {
            "value": "<myNamespaceName>"
        },
        "serviceBusQueueName": {
            "value": "<myQueueName>"
        },
        "serviceBusApiVersion": {
            "value": "2017-04-01"
        }
    }
}
```

Zie het artikel [Parameters voor](../azure-resource-manager/templates/parameter-files.md) meer informatie.

### <a name="log-in-to-azure-and-set-the-azure-subscription"></a>Inloggen bij Azure en het Azure-abonnement instellen

Voer vanuit een PowerShell-prompt de volgende opdracht uit:

```powershell
Connect-AzAccount
```

U wordt gevraagd zich aan te melden bij uw Azure-account. Voer na het aanmelden de volgende opdracht uit om uw beschikbare abonnementen weer te geven:

```powershell
Get-AzSubscription
```

Met deze opdracht wordt een lijst met beschikbare Azure-abonnementen geretourneerd. Kies een abonnement voor de huidige sessie door de volgende opdracht uit te voeren. Vervang `<YourSubscriptionId>` de GUID voor het Azure-abonnement dat u wilt gebruiken:

```powershell
Set-AzContext -SubscriptionID <YourSubscriptionId>
```

### <a name="set-the-resource-group"></a>De resourcegroep instellen

Als u geen bestaande resourcegroep hebt, maakt u een nieuwe resourcegroep met de opdracht **Nieuw-AzResourceGroep.** Geef de naam op van de resourcegroep en locatie die u wilt gebruiken. Bijvoorbeeld:

```powershell
New-AzResourceGroup -Name MyDemoRG -Location "West US"
```

Als dit is gelukt, wordt een overzicht van de nieuwe resourcegroep weergegeven.

```powershell
ResourceGroupName : MyDemoRG
Location          : westus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/<GUID>/resourceGroups/MyDemoRG
```

### <a name="test-the-deployment"></a>De implementatie testen

Valideer uw `Test-AzResourceGroupDeployment` implementatie door de cmdlet uit te voeren. Geef bij het testen van de implementatie parameters op die precies zo zijn als bij het uitvoeren van de implementatie.

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

### <a name="create-the-deployment"></a>De implementatie maken

Als u de nieuwe `New-AzResourceGroupDeployment` implementatie wilt maken, voert u de cmdlet uit en geeft u de benodigde parameters op wanneer daarom wordt gevraagd. De parameters omvatten een naam voor uw implementatie, de naam van uw resourcegroep en het pad of de URL naar het sjabloonbestand. Als de parameter **Modus** niet is opgegeven, wordt de standaardwaarde **incrementeel** gebruikt. Zie [Incrementele en volledige implementaties](../azure-resource-manager/templates/deployment-modes.md)voor meer informatie.

Met de volgende opdracht wordt u gevraagd naar de drie parameters in het PowerShell-venster:

```powershell
New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

Als u in plaats daarvan een parametersbestand wilt opgeven, gebruikt u de volgende opdracht:

```powershell
New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
```

U ook inlineparameters gebruiken wanneer u de implementatiecmdlet uitvoert. De opdracht is als volgt:

```powershell
New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -parameterName "parameterValue"
```

Als u een volledige implementatie [wilt](../azure-resource-manager/templates/deployment-modes.md) uitvoeren, stelt u de parameter **Modus** in op **Voltooien:**

```powershell
New-AzResourceGroupDeployment -Name MyDemoDeployment -Mode Complete -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

### <a name="verify-the-deployment"></a>De implementatie controleren
Als de resources met succes worden geïmplementeerd, wordt een overzicht van de implementatie weergegeven in het PowerShell-venster:

```powershell
DeploymentName    : MyDemoDeployment
ResourceGroupName : MyDemoRG
ProvisioningState : Succeeded
Timestamp         : 4/19/2017 10:38:30 PM
Mode              : Incremental
TemplateLink      :
Parameters        :
                    Name             Type                       Value
                    ===============  =========================  ==========
                    serviceBusNamespaceName  String             <namespaceName>
                    serviceBusQueueName  String                 <queueName>
                    serviceBusApiVersion  String                2017-04-01

```

## <a name="next-steps"></a>Volgende stappen
U hebt nu de basiswerkstroom en opdrachten voor het implementeren van een Azure Resource Manager-sjabloon gezien. Ga voor meer gedetailleerde informatie naar de volgende links:

* [Overzicht van Azure Resource Manager][Azure Resource Manager overview]
* [Resources implementeren met Resource Manager-sjablonen en Azure PowerShell][Deploy resources with Azure Resource Manager templates]
* [Azure Resource Manager-sjablonen maken](../azure-resource-manager/templates/template-syntax.md)
* [Microsoft.ServiceBus-brontypen](/azure/templates/microsoft.servicebus/allversions)

[Azure Resource Manager overview]: ../azure-resource-manager/management/overview.md
[Deploy resources with Azure Resource Manager templates]: ../azure-resource-manager/templates/deploy-powershell.md
[Azure Quickstart Templates gallery]: https://azure.microsoft.com/documentation/templates/?term=service+bus
