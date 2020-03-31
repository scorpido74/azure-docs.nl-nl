---
title: Web-app inrichten met Azure-cache voor Redis
description: Gebruik de sjabloon Azure Resource Manager om de web-app te implementeren met Azure Cache voor Redis.
services: app-service
author: yegu-ms
ms.service: app-service
ms.topic: conceptual
ms.date: 01/06/2017
ms.author: yegu
ms.openlocfilehash: 11c854491ab030394eb61964979cb04a5a4b489b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75433383"
---
# <a name="create-a-web-app-plus-azure-cache-for-redis-using-a-template"></a>Een web-app plus Azure-cache voor Redis maken met behulp van een sjabloon

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

In dit onderwerp leert u hoe u een Azure Resource Manager-sjabloon maakt waarmee een Azure Web App met Azure Cache voor Redis wordt geïmplementeerd. U leert hoe u definiëren welke resources worden geïmplementeerd en hoe u parameters definieert die zijn opgegeven wanneer de implementatie wordt uitgevoerd. U kunt deze sjabloon gebruiken voor uw eigen implementaties of de sjabloon aanpassen aan uw eisen.

Zie [Azure Resource Manager-sjablonen ontwerpen](../azure-resource-manager/templates/template-syntax.md)voor meer informatie over het maken van sjablonen. Zie [Microsoft.Cache-brontypen](/azure/templates/microsoft.cache/allversions)voor meer informatie over de syntaxis en eigenschappen van JSON voor cachebronnen.

Zie [Web-app met Azure-cache voor Redis-sjabloon voor](https://github.com/Azure/azure-quickstart-templates/blob/master/201-web-app-with-redis-cache/azuredeploy.json)de volledige sjabloon .

## <a name="what-you-will-deploy"></a>Wat u gaat inzetten
In deze sjabloon implementeert u het:

* Azure Web App
* Azure Cache voor Redis

Klik op de volgende knop om de implementatie automatisch uit te voeren:

[![Implementeren naar Azure](./media/cache-web-app-arm-with-redis-cache-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-web-app-with-redis-cache%2Fazuredeploy.json)

## <a name="parameters-to-specify"></a>Parameters op te geven
[!INCLUDE [app-service-web-deploy-web-parameters](../../includes/app-service-web-deploy-web-parameters.md)]

[!INCLUDE [cache-deploy-parameters](../../includes/cache-deploy-parameters.md)]

## <a name="variables-for-names"></a>Variabelen voor namen
Deze sjabloon gebruikt variabelen om namen voor de resources te construeren. Het maakt gebruik van de [uniekeString-functie](../azure-resource-manager/templates/template-functions-string.md#uniquestring) om een waarde te construeren op basis van de brongroep-id.

    "variables": {
      "hostingPlanName": "[concat('hostingplan', uniqueString(resourceGroup().id))]",
      "webSiteName": "[concat('webSite', uniqueString(resourceGroup().id))]",
      "cacheName": "[concat('cache', uniqueString(resourceGroup().id))]"
    },


## <a name="resources-to-deploy"></a>Resources om te implementeren
[!INCLUDE [app-service-web-deploy-web-host](../../includes/app-service-web-deploy-web-host.md)]

### <a name="azure-cache-for-redis"></a>Azure Cache voor Redis
Hiermee maakt u de Azure-cache voor Redis die wordt gebruikt met de web-app. De naam van de cache is opgegeven in de variabele **cacheName.**

De sjabloon maakt de cache op dezelfde locatie als de resourcegroep.

    {
      "name": "[variables('cacheName')]",
      "type": "Microsoft.Cache/Redis",
      "location": "[resourceGroup().location]",
      "apiVersion": "2015-08-01",
      "dependsOn": [ ],
      "tags": {
        "displayName": "cache"
      },
      "properties": {
        "sku": {
          "name": "[parameters('cacheSKUName')]",
          "family": "[parameters('cacheSKUFamily')]",
          "capacity": "[parameters('cacheSKUCapacity')]"
        }
      }
    }


### <a name="web-app"></a>Web-app
Hiermee maakt u de web-app met de naam die is opgegeven in de variabele **webSiteName.**

Merk op dat de web-app is geconfigureerd met eigenschappen voor app-instelling waarmee deze kan werken met de Azure-cache voor Redis. Deze app-instellingen worden dynamisch gemaakt op basis van waarden die tijdens de implementatie worden verstrekt.

    {
      "apiVersion": "2015-08-01",
      "name": "[variables('webSiteName')]",
      "type": "Microsoft.Web/sites",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.Web/serverFarms/', variables('hostingPlanName'))]",
        "[concat('Microsoft.Cache/Redis/', variables('cacheName'))]"
      ],
      "tags": {
        "[concat('hidden-related:', resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', variables('hostingPlanName'))]": "empty",
        "displayName": "Website"
      },
      "properties": {
        "name": "[variables('webSiteName')]",
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"
      },
      "resources": [
        {
          "apiVersion": "2015-08-01",
          "type": "config",
          "name": "appsettings",
          "dependsOn": [
            "[concat('Microsoft.Web/Sites/', variables('webSiteName'))]",
            "[concat('Microsoft.Cache/Redis/', variables('cacheName'))]"
          ],
          "properties": {
            "CacheConnection": "[concat(variables('cacheName'),'.redis.cache.windows.net,abortConnect=false,ssl=true,password=', listKeys(resourceId('Microsoft.Cache/Redis', variables('cacheName')), '2015-08-01').primaryKey)]"
          }
        }
      ]
    }

## <a name="commands-to-run-deployment"></a>Opdrachten om implementatie uit te voeren
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell
    New-AzResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-with-redis-cache/azuredeploy.json -ResourceGroupName ExampleDeployGroup

### <a name="azure-cli"></a>Azure-CLI
    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-with-redis-cache/azuredeploy.json -g ExampleDeployGroup
