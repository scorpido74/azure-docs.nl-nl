---
title: Functie-app-bronimplementatie naar Azure automatiseren
description: Meer informatie over het maken van een Azure Resource Manager-sjabloon waarmee uw functie-app wordt geïmplementeerd.
ms.assetid: d20743e3-aab6-442c-a836-9bcea09bfd32
ms.topic: conceptual
ms.date: 04/03/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 48d98d6fef896f9288be88824a62fa1c8179217f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276892"
---
# <a name="automate-resource-deployment-for-your-function-app-in-azure-functions"></a>Resourceimplementatie voor uw functie-app automatiseren in Azure-functies

U een Azure Resource Manager-sjabloon gebruiken om een functie-app te implementeren. In dit artikel worden de vereiste resources en parameters beschreven om dit te doen. Mogelijk moet u extra resources implementeren, afhankelijk van de [triggers en bindingen](functions-triggers-bindings.md) in uw functie-app.

Zie [Azure Resource Manager-sjablonen samenstellen](../azure-resource-manager/templates/template-syntax.md) voor meer informatie over het maken van sjablonen.

Zie voor voorbeeldsjablonen:
- [Functie-app op verbruiksplan]
- [Functie-app op Azure App Service-abonnement]

## <a name="required-resources"></a>Vereiste resources

Een Azure-functie-implementatie bestaat meestal uit deze bronnen:

| Resource                                                                           | Vereiste | Verwijzing naar syntaxis en eigenschappen                                                         |   |
|------------------------------------------------------------------------------------|-------------|-----------------------------------------------------------------------------------------|---|
| Een functie-app                                                                     | Vereist    | [Microsoft.Web/sites](/azure/templates/microsoft.web/sites)                             |   |
| Een [Azure Storage-account](../storage/index.yml)                                   | Vereist    | [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts) |   |
| Een [component Application Insights](../azure-monitor/app/app-insights-overview.md) | Optioneel    | [Microsoft.Insights/componenten](/azure/templates/microsoft.insights/components)         |   |
| Een [hostingplan](./functions-scale.md)                                             | Optioneel<sup>1</sup>    | [Microsoft.Web/serverfarms](/azure/templates/microsoft.web/serverfarms)                 |   |

<sup>1.</sup> Een hostingplan is alleen vereist wanneer u ervoor kiest om uw functie-app uit te voeren op een [Premium-abonnement](./functions-premium-plan.md) (in preview) of op een [App Service-abonnement.](../app-service/overview-hosting-plans.md)

> [!TIP]
> Hoewel dit niet nodig is, wordt het ten zeerste aanbevolen om Application Insights voor uw app te configureren.

<a name="storage"></a>
### <a name="storage-account"></a>Storage-account

Voor een functie-app is een Azure-opslagaccount vereist. U hebt een account voor algemeen gebruik nodig dat blobs, tabellen, wachtrijen en bestanden ondersteunt. Zie Azure [Functions-opslagaccountvereisten](storage-considerations.md#storage-account-requirements)voor meer informatie .

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2019-04-01",
    "location": "[resourceGroup().location]",
    "kind": "StorageV2",
    "sku": {
        "name": "[parameters('storageAccountType')]"
    }
}
```

Bovendien moet de `AzureWebJobsStorage` eigenschap worden opgegeven als een app-instelling in de siteconfiguratie. Als de functie-app geen Toepassingsinzichten gebruikt voor `AzureWebJobsDashboard` controle, moet deze ook worden opgegeven als app-instelling.

De runtime azure-functies gebruikt de `AzureWebJobsStorage` verbindingstekenreeks om interne wachtrijen te maken.  Wanneer Application Insights niet is ingeschakeld, `AzureWebJobsDashboard` gebruikt de runtime de verbindingstekenreeks om u aan te melden bij Azure Table-opslag en het tabblad **Monitor** in de portal van stroom te voorzien.

Deze eigenschappen zijn `appSettings` opgegeven in `siteConfig` de verzameling in het object:

```json
"appSettings": [
    {
        "name": "AzureWebJobsStorage",
        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
    },
    {
        "name": "AzureWebJobsDashboard",
        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
    }
]
```

### <a name="application-insights"></a>Application Insights

Application Insights wordt aanbevolen voor het bewaken van uw functie-apps. De Application Insights-bron wordt gedefinieerd met het type **Microsoft.Insights/components** en het soort **web:**

```json
        {
            "apiVersion": "2015-05-01",
            "name": "[variables('appInsightsName')]",
            "type": "Microsoft.Insights/components",
            "kind": "web",
            "location": "[resourceGroup().location]",
            "tags": {
                "[concat('hidden-link:', resourceGroup().id, '/providers/Microsoft.Web/sites/', variables('functionAppName'))]": "Resource"
            },
            "properties": {
                "Application_Type": "web",
                "ApplicationId": "[variables('appInsightsName')]"
            }
        },
```

Daarnaast moet de instrumentatiesleutel worden verstrekt aan `APPINSIGHTS_INSTRUMENTATIONKEY` de functie-app met behulp van de toepassingsinstelling. Deze eigenschap is `appSettings` opgegeven in `siteConfig` de verzameling in het object:

```json
"appSettings": [
    {
        "name": "APPINSIGHTS_INSTRUMENTATIONKEY",
        "value": "[reference(resourceId('microsoft.insights/components/', variables('appInsightsName')), '2015-05-01').InstrumentationKey]"
    }
]
```

### <a name="hosting-plan"></a>Hostingplan

De definitie van het hostingplan varieert en kan een van de volgende zijn:
* [Verbruiksplan](#consumption) (standaard)
* [Premium-abonnement](#premium) (in preview)
* [App-serviceplan](#app-service-plan)

### <a name="function-app"></a>Function App

De functie-app resource wordt gedefinieerd met behulp van een bron van het type **Microsoft.Web /sites** en natura **functionapp:**

```json
{
    "apiVersion": "2015-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "[resourceId('Microsoft.Insights/components', variables('appInsightsName'))]"
    ]
}
```

> [!IMPORTANT]
> Als u expliciet een hostingplan definieert, is een extra item nodig in de array dependsOn:`"[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"`

Een functie-app moet de volgende toepassingsinstellingen bevatten:

| Naam van instelling                 | Beschrijving                                                                               | Voorbeeldwaarden                        |
|------------------------------|-------------------------------------------------------------------------------------------|---------------------------------------|
| AzureWebJobsStorage          | Een verbindingstekenreeks met een opslagaccount die de runtime van functies gebruikt voor interne wachtrijen | Zie [Opslagaccount](#storage)       |
| FUNCTIONS_EXTENSION_VERSION  | De versie van de runtime van Azure-functies                                                | `~2`                                  |
| FUNCTIONS_WORKER_RUNTIME     | De taalstack die moet worden gebruikt voor functies in deze app                                   | `dotnet`, `node` `java`, `python`, of`powershell` |
| WEBSITE_NODE_DEFAULT_VERSION | Alleen nodig als `node` u de taalstapel gebruikt, geeft u de versie op die moet worden gebruikt              | `10.14.1`                             |

Deze eigenschappen worden `appSettings` opgegeven in `siteConfig` de verzameling in de eigenschap:

```json
"properties": {
    "siteConfig": {
        "appSettings": [
            {
                "name": "AzureWebJobsStorage",
                "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
            },
            {
                "name": "FUNCTIONS_WORKER_RUNTIME",
                "value": "node"
            },
            {
                "name": "WEBSITE_NODE_DEFAULT_VERSION",
                "value": "10.14.1"
            },
            {
                "name": "FUNCTIONS_EXTENSION_VERSION",
                "value": "~2"
            }
        ]
    }
}
```

<a name="consumption"></a>

## <a name="deploy-on-consumption-plan"></a>Implementeren op verbruiksplan

In het verbruiksplan wordt automatisch rekenkracht toegewezen wanneer uw code wordt uitgevoerd, worden deze zo nodig uitgeschaald om de belasting te verwerken en wordt vervolgens geschaald wanneer de code niet wordt uitgevoerd. U hoeft niet te betalen voor niet-actieve VM's en u hoeft niet van tevoren capaciteit te reserveren. Zie [Azure Functions schalen en hosten](functions-scale.md#consumption-plan)voor meer informatie.

Zie [Functie-app op het beheerplan voor]een voorbeeld van Azure Resource Manager.For a sample Azure Resource Manager template, see Function app on Consumption plan .

### <a name="create-a-consumption-plan"></a>Een verbruiksplan maken

Een verbruiksplan hoeft niet te worden gedefinieerd. Een wordt automatisch gemaakt of geselecteerd op basis van een regio wanneer u de functie-app bron zelf maakt.

Het verbruiksplan is een speciaal type "serverfarm"-bron. Voor Windows u dit `Dynamic` opgeven met `computeMode` `sku` behulp van de waarde voor de eigenschappen en de eigenschappen:

```json
{  
   "type":"Microsoft.Web/serverfarms",
   "apiVersion":"2016-09-01",
   "name":"[variables('hostingPlanName')]",
   "location":"[resourceGroup().location]",
   "properties":{  
      "name":"[variables('hostingPlanName')]",
      "computeMode":"Dynamic"
   },
   "sku":{  
      "name":"Y1",
      "tier":"Dynamic",
      "size":"Y1",
      "family":"Y",
      "capacity":0
   }
}
```

> [!NOTE]
> Het verbruiksplan kan niet expliciet worden gedefinieerd voor Linux. Het wordt automatisch gemaakt.

Als u uw verbruiksplan expliciet definieert, `serverFarmId` moet u de eigenschap in de app zo instellen dat deze verwijst naar de resource-id van het plan. U moet ervoor zorgen dat `dependsOn` de functie-app ook een instelling voor het plan heeft.

### <a name="create-a-function-app"></a>Een functie-app maken

#### <a name="windows"></a>Windows

Voor Windows vereist een verbruiksplan twee extra `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` `WEBSITE_CONTENTSHARE`instellingen in de siteconfiguratie: en . Deze eigenschappen configureren het opslagaccount en bestandspad waar de functie-appcode en -configuratie zijn opgeslagen.

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "WEBSITE_CONTENTSHARE",
                    "value": "[toLower(variables('functionAppName'))]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~2"
                }
            ]
        }
    }
}
```

#### <a name="linux"></a>Linux

Op Linux moet de `kind` functie-app `functionapp,linux`zijn ingesteld op `reserved` , `true`en het moet de eigenschap ingesteld op:

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp,linux",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountName'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~2"
                }
            ]
        },
        "reserved": true
    }
}
```



<a name="premium"></a>

## <a name="deploy-on-premium-plan"></a>Implementeren op Premium-abonnement

Het Premium-abonnement biedt dezelfde schaalvergroting als het verbruiksplan, maar bevat speciale resources en extra mogelijkheden. Zie [Azure Functions Premium Plan](./functions-premium-plan.md)voor meer informatie.

### <a name="create-a-premium-plan"></a>Een Premium-abonnement maken

Een Premium-abonnement is een speciaal type "serverfarm"-bron. U dit opgeven `EP1` `EP2`door `EP3` een `Name` van beide `sku` te gebruiken, of voor de eigenschapswaarde in het [beschrijvingsobject](https://docs.microsoft.com/azure/templates/microsoft.web/2018-02-01/serverfarms#skudescription-object).

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2018-02-01",
    "name": "[parameters('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "properties": {
        "name": "[parameters('hostingPlanName')]",
        "workerSize": "[parameters('workerSize')]",
        "workerSizeId": "[parameters('workerSizeId')]",
        "numberOfWorkers": "[parameters('numberOfWorkers')]",
        "hostingEnvironment": "[parameters('hostingEnvironment')]",
        "maximumElasticWorkerCount": "20"
    },
    "sku": {
        "Tier": "ElasticPremium",
        "Name": "EP1"
    }
}
```

### <a name="create-a-function-app"></a>Een functie-app maken

Een functie-app op een `serverFarmId` Premium-abonnement moet de eigenschap hebben ingesteld op de resource-id van het plan dat eerder is gemaakt. Bovendien vereist een Premium-abonnement twee extra instellingen `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` `WEBSITE_CONTENTSHARE`in de siteconfiguratie: en . Deze eigenschappen configureren het opslagaccount en bestandspad waar de functie-appcode en -configuratie zijn opgeslagen.

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",            
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "WEBSITE_CONTENTSHARE",
                    "value": "[toLower(variables('functionAppName'))]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~2"
                }
            ]
        }
    }
}
```


<a name="app-service-plan"></a>

## <a name="deploy-on-app-service-plan"></a>Implementeren op App-serviceplan

In het App Service-abonnement wordt uw functie-app uitgevoerd op speciale VM's op Basic-, Standaard- en Premium SKU's, vergelijkbaar met web-apps. Zie het uitgebreide overzicht van azure [app service-abonnementen voor](../app-service/overview-hosting-plans.md)meer informatie over de werking van het App Service-abonnement.

Zie [Functie-app op Azure App Service-abonnement]voor een voorbeeld van Azure Resource Manager-sjabloon .

### <a name="create-an-app-service-plan"></a>Een App Service-plan maken

Een App Service-abonnement wordt gedefinieerd door een resource 'serverfarm'.

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2018-02-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "S1",
        "tier": "Standard",
        "size": "S1",
        "family": "S",
        "capacity": 1
    }
}
```

Als u uw app op Linux `kind` wilt `Linux`uitvoeren, moet u ook de app instellen op:

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2018-02-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "kind": "Linux",
    "sku": {
        "name": "S1",
        "tier": "Standard",
        "size": "S1",
        "family": "S",
        "capacity": 1
    }
}
```

### <a name="create-a-function-app"></a>Een functie-app maken

Een functie-app op een App `serverFarmId` Service-abonnement moet de eigenschap hebben ingesteld op de resource-id van het plan dat eerder is gemaakt.

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~2"
                }
            ]
        }
    }
}
```

Linux-apps moeten `linuxFxVersion` ook `siteConfig`een eigenschap bevatten onder . Als u alleen code implementeert, wordt de waarde hiervoor bepaald door de gewenste runtimestack:

| Stack            | Voorbeeldwaarde                                         |
|------------------|-------------------------------------------------------|
| Python           | `DOCKER|microsoft/azure-functions-python3.6:2.0`      |
| JavaScript       | `DOCKER|microsoft/azure-functions-node8:2.0`          |
| .NET             | `DOCKER|microsoft/azure-functions-dotnet-core2.0:2.0` |

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~2"
                }
            ],
            "linuxFxVersion": "DOCKER|microsoft/azure-functions-node8:2.0"
        }
    }
}
```

Als u [een aangepaste containerafbeelding implementeert,](./functions-create-function-linux-custom-image.md) `linuxFxVersion` moet u deze opgeven met en een configuratie opnemen waarmee uw afbeelding kan worden getrokken, zoals in [Web App voor containers.](/azure/app-service/containers) Ook ingesteld `WEBSITES_ENABLE_APP_SERVICE_STORAGE` `false`op , omdat uw app-inhoud wordt geleverd in de container zelf:

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~2"
                },
                {
                    "name": "DOCKER_REGISTRY_SERVER_URL",
                    "value": "[parameters('dockerRegistryUrl')]"
                },
                {
                    "name": "DOCKER_REGISTRY_SERVER_USERNAME",
                    "value": "[parameters('dockerRegistryUsername')]"
                },
                {
                    "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
                    "value": "[parameters('dockerRegistryPassword')]"
                },
                {
                    "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
                    "value": "false"
                }
            ],
            "linuxFxVersion": "DOCKER|myacr.azurecr.io/myimage:mytag"
        }
    }
}
```

## <a name="customizing-a-deployment"></a>Een implementatie aanpassen

Een functie-app heeft veel onderliggende bronnen die u gebruiken in uw implementatie, waaronder app-instellingen en opties voor bronbeheer. U er ook voor kiezen om de **onderliggende bronbron source** te verwijderen en in plaats daarvan een andere [implementatieoptie](functions-continuous-deployment.md) te gebruiken.

> [!IMPORTANT]
> Als u uw toepassing met Azure Resource Manager wilt implementeren, is het belangrijk om te begrijpen hoe resources in Azure worden geïmplementeerd. In het volgende voorbeeld worden configuraties op het hoogste niveau toegepast met behulp van **siteConfig**. Het is belangrijk om deze configuraties op een topniveau in te stellen, omdat ze informatie overbrengen naar de runtime en implementatie-engine voor functies. Informatie op het hoogste niveau is vereist voordat de **onderliggende bronbesturingselementen/webbron** worden toegepast. Hoewel het mogelijk is om deze instellingen te configureren in de **bron voor config/appSettings** op kinderniveau, moet in sommige gevallen uw functie-app worden geïmplementeerd *voordat* **config/appSettings** wordt toegepast. Wanneer u bijvoorbeeld functies met [Logic Apps](../logic-apps/index.yml)gebruikt, zijn uw functies afhankelijk van een andere bron.

```json
{
  "apiVersion": "2015-08-01",
  "name": "[parameters('appName')]",
  "type": "Microsoft.Web/sites",
  "kind": "functionapp",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Web/serverfarms', parameters('appName'))]"
  ],
  "properties": {
     "serverFarmId": "[variables('appServicePlanName')]",
     "siteConfig": {
        "alwaysOn": true,
        "appSettings": [
            {
                "name": "FUNCTIONS_EXTENSION_VERSION",
                "value": "~2"
            },
            {
                "name": "Project",
                "value": "src"
            }
        ]
     }
  },
  "resources": [
     {
        "apiVersion": "2015-08-01",
        "name": "appsettings",
        "type": "config",
        "dependsOn": [
          "[resourceId('Microsoft.Web/Sites', parameters('appName'))]",
          "[resourceId('Microsoft.Web/Sites/sourcecontrols', parameters('appName'), 'web')]",
          "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
        ],
        "properties": {
          "AzureWebJobsStorage": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]",
          "AzureWebJobsDashboard": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]",
          "FUNCTIONS_EXTENSION_VERSION": "~2",
          "FUNCTIONS_WORKER_RUNTIME": "dotnet",
          "Project": "src"
        }
     },
     {
          "apiVersion": "2015-08-01",
          "name": "web",
          "type": "sourcecontrols",
          "dependsOn": [
            "[resourceId('Microsoft.Web/sites/', parameters('appName'))]"
          ],
          "properties": {
            "RepoUrl": "[parameters('sourceCodeRepositoryURL')]",
            "branch": "[parameters('sourceCodeBranch')]",
            "IsManualIntegration": "[parameters('sourceCodeManualIntegration')]"
          }
     }
  ]
}
```
> [!TIP]
> Deze sjabloon maakt gebruik van de waarde van de project-app-instellingen, waarin de basismap wordt ingesteld waarin de Implementatieengine voor functies (Kudu) zoekt naar implementeerbare code. [Project](https://github.com/projectkudu/kudu/wiki/Customizing-deployments#using-app-settings-instead-of-a-deployment-file) In onze repository bevinden onze functies zich in een submap van de **src** map. In het voorgaande voorbeeld stellen we de `src`waarde van de app-instellingen in op . Als uw functies zich in de hoofdmap van uw opslagplaats bevinden of als u niet implementeert vanuit bronbeheer, u deze waarde voor app-instellingen verwijderen.

## <a name="deploy-your-template"></a>De sjabloon implementeren

U een van de volgende manieren gebruiken om uw sjabloon te implementeren:

* [Powershell](../azure-resource-manager/templates/deploy-powershell.md)
* [Azure-CLI](../azure-resource-manager/templates/deploy-cli.md)
* [Azure-portal](../azure-resource-manager/templates/deploy-portal.md)
* [REST-API](../azure-resource-manager/templates/deploy-rest.md)

### <a name="deploy-to-azure-button"></a>De knop Implementeren in Azure

Vervang ```<url-encoded-path-to-azuredeploy-json>``` door een [door de URL gecodeerde](https://www.bing.com/search?q=url+encode) `azuredeploy.json` versie van het ruwe pad van uw bestand in GitHub.

Hier is een voorbeeld dat afwaardering gebruikt:

```markdown
[![Deploy to Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>)
```

Hier is een voorbeeld dat HTML gebruikt:

```html
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"></a>
```

### <a name="deploy-using-powershell"></a>Implementeren met PowerShell

Met de volgende PowerShell-opdrachten wordt een resourcegroep gemaakt en wordt een sjabloon geïmplementeerd waarmee een functie-app met de vereiste resources wordt gemaakt. Als u lokaal wilt worden uitgevoerd, moet [Azure PowerShell](/powershell/azure/install-az-ps) zijn geïnstalleerd. Ren [`Connect-AzAccount`](/powershell/module/az.accounts/connect-azaccount) om je aan te melden.

```powershell
# Register Resource Providers if they're not already registered
Register-AzResourceProvider -ProviderNamespace "microsoft.web"
Register-AzResourceProvider -ProviderNamespace "microsoft.storage"

# Create a resource group for the function app
New-AzResourceGroup -Name "MyResourceGroup" -Location 'West Europe'

# Create the parameters for the file, which for this template is the function app name.
$TemplateParams = @{"appName" = "<function-app-name>"}

# Deploy the template
New-AzResourceGroupDeployment -ResourceGroupName "MyResourceGroup" -TemplateFile template.json -TemplateParameterObject $TemplateParams -Verbose
```

Als u deze implementatie wilt testen, u een [sjabloon als deze](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-function-app-create-dynamic/azuredeploy.json) gebruiken waarmee een functie-app op Windows wordt gemaakt in een verbruiksplan. Vervang `<function-app-name>` door een unieke naam voor uw functie-app.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het ontwikkelen en configureren van Azure-functies.

* [Naslaginformatie over Azure Functions voor ontwikkelaars](functions-reference.md)
* [Instellingen voor Azure-functie-apps configureren](functions-how-to-use-azure-function-app-settings.md)
* [Uw eerste Azure-functie maken](functions-create-first-azure-function.md)

<!-- LINKS -->

[Functie-app op verbruiksplan]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dynamic/azuredeploy.json
[Functie-app op Azure App Service-abonnement]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dedicated/azuredeploy.json
