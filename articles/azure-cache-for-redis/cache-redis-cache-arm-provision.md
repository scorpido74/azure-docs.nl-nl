---
title: Azure-cache voor Redis implementeren met Azure Resource Manager
description: Meer informatie over het gebruik van een Azure Resource Manager-sjabloon om een Azure Cache voor Redis-bron te implementeren. Sjablonen zijn beschikbaar voor veelvoorkomende scenario's.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 01/23/2017
ms.openlocfilehash: 787edf662aa3a34e167db61b0a89dfc5c2944219
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75412412"
---
# <a name="create-an-azure-cache-for-redis-using-a-template"></a>Een Azure-cache voor Redis maken met behulp van een sjabloon

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

In dit onderwerp leert u hoe u een Azure Resource Manager-sjabloon maakt waarmee een Azure-cache voor Redis wordt geïmplementeerd. De cache kan worden gebruikt met een bestaand opslagaccount om diagnostische gegevens te bewaren. U leert ook hoe u definiëren welke resources worden geïmplementeerd en hoe u parameters definieert die zijn opgegeven wanneer de implementatie wordt uitgevoerd. U kunt deze sjabloon gebruiken voor uw eigen implementaties of de sjabloon aanpassen aan uw eisen.

Momenteel worden diagnostische instellingen gedeeld voor alle caches in dezelfde regio voor een abonnement. Het bijwerken van één cache in de regio heeft invloed op alle andere caches in de regio.

Zie [Azure Resource Manager-sjablonen ontwerpen](../azure-resource-manager/templates/template-syntax.md)voor meer informatie over het maken van sjablonen. Zie [Microsoft.Cache-brontypen](/azure/templates/microsoft.cache/allversions)voor meer informatie over de syntaxis en eigenschappen van JSON voor cachebronnen.

Zie Azure Cache [for Redis-sjabloon voor](https://github.com/Azure/azure-quickstart-templates/blob/master/101-redis-cache/azuredeploy.json)de volledige sjabloon .

> [!NOTE]
> Resourcebeheersjablonen voor de nieuwe [Premium-laag](cache-premium-tier-intro.md) zijn beschikbaar. 
> 
> * [Een Premium Azure-cache voor Redis maken met clustering](https://azure.microsoft.com/resources/templates/201-redis-premium-cluster-diagnostics/)
> * [Premium Azure-cache voor Redis maken met gegevenspersistentie](https://azure.microsoft.com/resources/templates/201-redis-premium-persistence/)
> * [Premium Redis-cache maken geïmplementeerd in een virtueel netwerk](https://azure.microsoft.com/resources/templates/201-redis-premium-vnet/)
> 
> Zie [Azure Quickstart-sjablonen](https://azure.microsoft.com/documentation/templates/) en zoek `Azure Cache for Redis`naar .
> 
> 

## <a name="what-you-will-deploy"></a>Wat u gaat inzetten
In deze sjabloon implementeert u een Azure-cache voor Redis die een bestaand opslagaccount gebruikt voor diagnostische gegevens.

Klik op de volgende knop om de implementatie automatisch uit te voeren:

[![Implementeren naar Azure](./media/cache-redis-cache-arm-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-redis-cache%2Fazuredeploy.json)

## <a name="parameters"></a>Parameters
Met Azure Resource Manager kunt u parameters definiëren voor waarden die u wilt opgeven wanneer de sjabloon wordt geïmplementeerd. De sjabloon bevat een sectie met de naam Parameters die alle parameterwaarden bevat.
U moet een parameter definiëren voor de waarden die variëren op basis van het project dat u wilt implementeren of op basis van de omgeving waarin u gaat implementeren. Definieer geen parameters voor waarden die altijd hetzelfde blijven. De waarde van elke parameter wordt gebruikt in de sjabloon voor het definiëren van de resources die worden geïmplementeerd. 

[!INCLUDE [app-service-web-deploy-redis-parameters](../../includes/cache-deploy-parameters.md)]

### <a name="rediscachelocation"></a>redisCacheLocatie
De locatie van de Azure-cache voor Redis. Gebruik voor de beste prestaties dezelfde locatie als de app die met de cache moet worden gebruikt.

    "redisCacheLocation": {
      "type": "string"
    }

### <a name="existingdiagnosticsstorageaccountname"></a>bestaandeDiagnosticsStorageAccountName
De naam van het bestaande opslagaccount dat moet worden gebruikt voor diagnose. 

    "existingDiagnosticsStorageAccountName": {
      "type": "string"
    }

### <a name="enablenonsslport"></a>enableNonSslPort
Een booleaanse waarde die aangeeft of toegang via niet-SSL-poorten moet worden toegestaan.

    "enableNonSslPort": {
      "type": "bool"
    }

### <a name="diagnosticsstatus"></a>diagnostiekStatus
Een waarde die aangeeft of diagnostische gegevens zijn ingeschakeld. Aan of UIT gebruiken.

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
Hiermee maakt u de Azure-cache voor Redis.

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
