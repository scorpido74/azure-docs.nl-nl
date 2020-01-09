---
title: Azure-cache implementeren voor redis met Azure Resource Manager
description: Meer informatie over het gebruik van een Azure Resource Manager sjabloon voor het implementeren van een Azure-cache voor redis-resource. Er worden sjablonen gegeven voor veelvoorkomende scenario's.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 01/23/2017
ms.openlocfilehash: 787edf662aa3a34e167db61b0a89dfc5c2944219
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75412412"
---
# <a name="create-an-azure-cache-for-redis-using-a-template"></a>Een Azure-cache voor redis maken met behulp van een sjabloon

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

In dit onderwerp leert u hoe u een Azure Resource Manager sjabloon maakt waarmee een Azure-cache voor redis wordt geïmplementeerd. De cache kan worden gebruikt met een bestaand opslag account om diagnostische gegevens te bewaren. U leert ook hoe u definieert welke resources worden geïmplementeerd en hoe u para meters definieert die worden opgegeven wanneer de implementatie wordt uitgevoerd. U kunt deze sjabloon gebruiken voor uw eigen implementaties of de sjabloon aanpassen aan uw eisen.

Op dit moment worden de diagnostische instellingen gedeeld voor alle caches in dezelfde regio voor een abonnement. Het bijwerken van een cache in de regio is van invloed op alle andere caches in de regio.

Zie [Azure Resource Manager sjablonen ontwerpen](../azure-resource-manager/templates/template-syntax.md)voor meer informatie over het maken van sjablonen. Zie [resource typen van micro soft. cache](/azure/templates/microsoft.cache/allversions)voor meer informatie over de JSON-syntaxis en eigenschappen voor cache resource typen.

Zie [Azure cache for redis-sjabloon](https://github.com/Azure/azure-quickstart-templates/blob/master/101-redis-cache/azuredeploy.json)voor de volledige sjabloon.

> [!NOTE]
> Resource Manager-sjablonen voor de nieuwe [Premium-laag](cache-premium-tier-intro.md) zijn beschikbaar. 
> 
> * [Een Premium Azure-cache maken voor redis met clustering](https://azure.microsoft.com/resources/templates/201-redis-premium-cluster-diagnostics/)
> * [Premium Azure-cache maken voor redis met gegevens persistentie](https://azure.microsoft.com/resources/templates/201-redis-premium-persistence/)
> * [Premium-Redis Cache die zijn geïmplementeerd in een Virtual Network maken](https://azure.microsoft.com/resources/templates/201-redis-premium-vnet/)
> 
> Zie [Azure Quick](https://azure.microsoft.com/documentation/templates/) start-sjablonen en zoek naar `Azure Cache for Redis`om te controleren of er nieuwe sjablonen zijn.
> 
> 

## <a name="what-you-will-deploy"></a>Wat u gaat implementeren
In deze sjabloon implementeert u een Azure-cache voor redis die gebruikmaakt van een bestaand opslag account voor diagnostische gegevens.

Klik op de volgende knop om de implementatie automatisch uit te voeren:

[![Implementeren in Azure](./media/cache-redis-cache-arm-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-redis-cache%2Fazuredeploy.json)

## <a name="parameters"></a>Parameters
Met Azure Resource Manager kunt u parameters definiëren voor waarden die u wilt opgeven wanneer de sjabloon wordt geïmplementeerd. De sjabloon bevat een sectie met de naam para meters die alle parameter waarden bevat.
U moet een parameter definiëren voor de waarden die variëren op basis van het project dat u wilt implementeren of op basis van de omgeving waarin u gaat implementeren. Definieer geen parameters voor waarden die altijd hetzelfde blijven. De waarde van elke parameter wordt gebruikt in de sjabloon voor het definiëren van de resources die worden geïmplementeerd. 

[!INCLUDE [app-service-web-deploy-redis-parameters](../../includes/cache-deploy-parameters.md)]

### <a name="rediscachelocation"></a>redisCacheLocation
De locatie van de Azure-cache voor redis. Voor de beste prestaties gebruikt u dezelfde locatie als de app die wordt gebruikt voor de cache.

    "redisCacheLocation": {
      "type": "string"
    }

### <a name="existingdiagnosticsstorageaccountname"></a>existingDiagnosticsStorageAccountName
De naam van het bestaande opslag account dat moet worden gebruikt voor diagnostische gegevens. 

    "existingDiagnosticsStorageAccountName": {
      "type": "string"
    }

### <a name="enablenonsslport"></a>enableNonSslPort
Een Booleaanse waarde die aangeeft of toegang via niet-SSL-poorten is toegestaan.

    "enableNonSslPort": {
      "type": "bool"
    }

### <a name="diagnosticsstatus"></a>diagnosticsStatus
Een waarde die aangeeft of diagnostische gegevens zijn ingeschakeld. Gebruiken in-of uitschakelen.

    "diagnosticsStatus": {
      "type": "string",
      "defaultValue": "ON",
      "allowedValues": [
            "ON",
            "OFF"
        ]
    }

## <a name="resources-to-deploy"></a>Resources om te implementeren
### <a name="azure-cache-for-redis"></a>Azure Cache voor Redis
Hiermee maakt u het Azure-cache geheugen voor redis.

    {
      "apiVersion": "2015-08-01",
      "name": "[parameters('redisCacheName')]",
      "type": "Microsoft.Cache/Redis",
      "location": "[parameters('redisCacheLocation')]",
      "properties": {
        "enableNonSslPort": "[parameters('enableNonSslPort')]",
        "sku": {
          "capacity": "[parameters('redisCacheCapacity')]",
          "family": "[parameters('redisCacheFamily')]",
          "name": "[parameters('redisCacheSKU')]"
        }
      },
      "resources": [
        {
          "apiVersion": "2017-05-01-preview",
          "type": "Microsoft.Cache/redis/providers/diagnosticsettings",
          "name": "[concat(parameters('redisCacheName'), '/Microsoft.Insights/service')]",
          "location": "[parameters('redisCacheLocation')]",
          "dependsOn": [
            "[concat('Microsoft.Cache/Redis/', parameters('redisCacheName'))]"
          ],
          "properties": {
            "status": "[parameters('diagnosticsStatus')]",
            "storageAccountName": "[parameters('existingDiagnosticsStorageAccountName')]"
          }
        }
      ]
    }

## <a name="commands-to-run-deployment"></a>Opdrachten om implementatie uit te voeren
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell

    New-AzResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-redis-cache/azuredeploy.json -ResourceGroupName ExampleDeployGroup -redisCacheName ExampleCache

### <a name="azure-cli"></a>Azure-CLI
    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-redis-cache/azuredeploy.json -g ExampleDeployGroup
