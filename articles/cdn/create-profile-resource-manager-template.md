---
title: 'Quick Start: een profiel en een eind punt maken met behulp van Resource Manager-sjablonen'
titleSuffix: Azure Content Delivery Network
description: Meer informatie over het maken van een Azure content delivery-netwerk profiel en-eind punt met behulp van Resource Manager-sjablonen
services: cdn
documentationcenter: ''
author: senthuransivananthan
manager: danielgi
editor: ''
ms.assetid: cbc2ff98-916d-4339-8959-622823c5b772
ms.service: azure-cdn
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 03/05/2019
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: b711a12161bc134bdcbb8c1f3e74f2e5ae06e701
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083143"
---
# <a name="quickstart-create-an-azure-cdn-profile-and-endpoint-using-resource-manager-template"></a>Snelstartgids: een Azure CDN profiel en eind punt maken met Resource Manager-sjabloon

In deze Quick Start implementeert u een Azure Resource Manager-sjabloon met behulp van CLI. De sjabloon die u maakt, implementeert een CDN-profiel en een CDN-eind punt voor de front-endwebserver.
Het duurt ongeveer tien minuten om deze stappen uit te voeren.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prequisites"></a>Prequisites

Voor deze Quick start moet u een webtoepassing hebben om te gebruiken als uw oorsprong. De voor beeld-webtoepassing die in deze Quick Start wordt gebruikt, is geïmplementeerd in https://cdndemo.azurewebsites.net

Zie [een statische HTML-Web-app maken in azure](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-html)voor meer informatie.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Alle resources moeten worden geïmplementeerd in dezelfde resource groep.

Maak de resource groep op de locatie die u selecteert. In dit voor beeld wordt het maken van een resource groep met de naam CDN weer gegeven in de locatie VS-Oost.

```bash
az group create --name cdn --location eastus
```

![Nieuwe resource groep](./media/create-profile-resource-manager-template/cdn-create-resource-group.png)

## <a name="create-the-resource-manager-template"></a>Het Resource Manager-sjabloon maken

In deze stap maakt u een sjabloon bestand dat de resources implementeert.

Hoewel in dit voor beeld een scenario voor een algemene website versnelling wordt door lopen, zijn er veel andere instellingen die kunnen worden geconfigureerd. Deze instellingen zijn beschikbaar in de verwijzing naar de Azure Resource Manager-sjabloon. Zie de verwijzingen voor het [CDN-profiel](https://docs.microsoft.com/azure/templates/microsoft.cdn/2017-10-12/profiles) en het [eind punt](https://docs.microsoft.com/azure/templates/microsoft.cdn/2017-10-12/profiles/endpoints)van het CDN-profiel.

Houd er rekening mee dat micro soft CDN geen ondersteuning biedt voor het wijzigen van de lijst met inhouds typen.

Sla de sjabloon op als **Resource-Manager-CDN. json**.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "cdnProfileSku": {
            "type": "string",
            "allowedValues": [
                "Standard_Microsoft",
                "Standard_Akamai",
                "Standard_Verizon",
                "Premium_Verizon"
            ]
        },
        "endpointOriginHostName": {
            "type": "string"
        }
    },
    "variables": {
        "profile": {
            "name": "[replace(toLower(parameters('cdnProfileSku')), '_', '-')]"
        },
        "endpoint": {
            "name": "[replace(toLower(parameters('endpointOriginHostName')), '.', '-')]",
            "originHostName": "[parameters('endpointOriginHostName')]"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Cdn/profiles",
            "apiVersion": "2017-10-12",
            "location": "[resourceGroup().location]",
            "name": "[variables('profile').name]",
            "sku": {
                "name": "[parameters('cdnProfileSku')]"
            }
        },
        {
            "dependsOn": [
                "[resourceId('Microsoft.Cdn/profiles', variables('profile').name)]"
            ],
            "type": "Microsoft.Cdn/profiles/endpoints",
            "apiVersion": "2017-10-12",
            "location": "[resourceGroup().location]",
            "name": "[concat(variables('profile').name, '/', variables('endpoint').name)]",
            "properties": {
                "hostName": "[concat(variables('endpoint').name, '.azureedge.net')]",
                "originHostHeader": "[variables('endpoint').originHostName]",
                "isHttpAllowed": true,
                "isHttpsAllowed": true,
                "queryStringCachingBehavior": "IgnoreQueryString",
                "origins": [
                    {
                        "name": "[replace(variables('endpoint').originHostName, '.', '-')]",
                        "properties": {
                            "hostName": "[variables('endpoint').originHostName]",
                            "httpPort": 80,
                            "httpsPort": 443
                        }
                    }
                ],
                "contentTypesToCompress": [
                    "application/eot",
                    "application/font",
                    "application/font-sfnt",
                    "application/javascript",
                    "application/json",
                    "application/opentype",
                    "application/otf",
                    "application/pkcs7-mime",
                    "application/truetype",
                    "application/ttf",
                    "application/vnd.ms-fontobject",
                    "application/xhtml+xml",
                    "application/xml",
                    "application/xml+rss",
                    "application/x-font-opentype",
                    "application/x-font-truetype",
                    "application/x-font-ttf",
                    "application/x-httpd-cgi",
                    "application/x-javascript",
                    "application/x-mpegurl",
                    "application/x-opentype",
                    "application/x-otf",
                    "application/x-perl",
                    "application/x-ttf",
                    "font/eot",
                    "font/ttf",
                    "font/otf",
                    "font/opentype",
                    "image/svg+xml",
                    "text/css",
                    "text/csv",
                    "text/html",
                    "text/javascript",
                    "text/js",
                    "text/plain",
                    "text/richtext",
                    "text/tab-separated-values",
                    "text/xml",
                    "text/x-script",
                    "text/x-component",
                    "text/x-java-source"
                ],
                "isCompressionEnabled": true,
                "optimizationType": "GeneralWebDelivery"
            }
        }
    ],
    "outputs": {
        "cdnUrl": {
            "type": "string",
            "value": "[concat('https://', variables('endpoint').name, '.azureedge.net')]"
        }
    }
}
```

## <a name="create-the-resources"></a>De resources maken

Implementeer de sjabloon met behulp van Azure CLI. U wordt gevraagd om 2 invoer:

**cdnProfileSku** : de CDN-provider die u wilt gebruiken. De opties zijn:

* Standard_Microsoft
* Standard_Akamai
* Standard_Verizon
* Premium_Verizon.

**endpointOriginHostName** : het eind punt dat wordt geleverd via het CDN, bijvoorbeeld cdndemo.azurewebsites.net.

```bash
az group deployment create --resource-group cdn --template-file arm-cdn.json
```

![Resource Manager-sjabloon implementeren](./media/create-profile-resource-manager-template/cdn-deploy-resource-manager.png)

## <a name="view-the-cdn-profile"></a>Het CDN-profiel weer geven

```bash
az cdn profile list --resource-group cdn -o table
```

![CDN-profiel weer geven](./media/create-profile-resource-manager-template/cdn-view-profile.png)

## <a name="view-the-cdn-endpoint-for-the-profile-standard-microsoft"></a>Het CDN-eind punt voor de profiel standaard weer geven-micro soft

```bash
az cdn endpoint list --profile-name standard-microsoft --resource-group cdn -o table
```

![CDN-eind punt weer geven](./media/create-profile-resource-manager-template/cdn-view-endpoint.png)

Gebruik de hostnaam om de inhoud weer te geven. U kunt bijvoorbeeld toegang https://cdndemo-azurewebsites-net.azureedge.net met uw browser.

## <a name="clean-up"></a>Opruimen

Als u de resource groep verwijdert, worden alle resources die erin zijn geïmplementeerd, automatisch verwijderd.

```bash
az group delete --name cdn
```

![Resource groep verwijderen](./media/create-profile-resource-manager-template/cdn-delete-resource-group.png)

## <a name="references"></a>Verwijzingen

* CDN-profiel- [referentie voor Azure Resource Manager sjabloon](https://docs.microsoft.com/azure/templates/microsoft.cdn/2017-10-12/profiles)
* [Referentie documentatie voor Azure Resource Manager sjabloon](https://docs.microsoft.com/azure/templates/microsoft.cdn/2017-10-12/profiles/endpoints) voor CDN-eind punten

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over het toevoegen van een aangepast domein aan uw CDN-eindpunt, de volgende zelfstudie:

> [!div class="nextstepaction"]
> [Zelfstudie: Een aangepast domein toevoegen aan uw Azure CDN-eindpunt](cdn-map-content-to-custom-domain.md)
