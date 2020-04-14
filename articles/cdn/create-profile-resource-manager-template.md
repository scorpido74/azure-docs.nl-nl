---
title: Snelstart - Een profiel en eindpunt maken met resourcebeheersjablonen
titleSuffix: Azure Content Delivery Network
description: Meer informatie over het maken van een Azure Content Deliver Network-profiel en eindpunt met resourcebeheersjablonen
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
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 7c7186b6ac5d5dcf4db75ccba9e08f8e0484932f
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81253421"
---
# <a name="quickstart-create-an-azure-cdn-profile-and-endpoint-using-resource-manager-template"></a>Snelstart: een Azure CDN-profiel en eindpunt maken met de sjabloon Resourcebeheer

In deze quickstart implementeert u een Azure Resource Manager-sjabloon met CLI. De sjabloon die u maakt, implementeert een CDN-profiel en CDN-eindpunt voor uw webtoepassing.
Het duurt ongeveer tien minuten om deze stappen te voltooien.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prequisites"></a>Prequisites

Voor deze quickstart moet je een webtoepassing hebben om als Origin te gebruiken. Het voorbeeld webtoepassing dat in deze quickstart wordt gebruikt, is geïmplementeerdhttps://cdndemo.azurewebsites.net

Zie [Een statische HTML-webapp maken in Azure](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-html)voor meer informatie.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Alle resources moeten in dezelfde resourcegroep worden geïmplementeerd.

Maak de resourcegroep op de locatie die u selecteert. In dit voorbeeld wordt de creatie weergegeven van een resourcegroep met de naam cdn op de locatie Oost-VS.

```azurecli-interactive
az group create --name cdn --location eastus
```

![Nieuwe resourcegroep](./media/create-profile-resource-manager-template/cdn-create-resource-group.png)

## <a name="create-the-resource-manager-template"></a>Het Resource Manager-sjabloon maken

In deze stap maakt u een sjabloonbestand waarmee de resources worden geïmplementeerd.

Terwijl dit voorbeeld door een algemeen websiteversnellingsscenario loopt, zijn er veel andere instellingen die kunnen worden geconfigureerd. Deze instellingen zijn beschikbaar in de sjabloonverwijzing azure resource manager. Zie referenties voor [CDN Profile](https://docs.microsoft.com/azure/templates/microsoft.cdn/2017-10-12/profiles) en [CDN Profile Endpoint](https://docs.microsoft.com/azure/templates/microsoft.cdn/2017-10-12/profiles/endpoints).

Houd er rekening mee dat Microsoft CDN geen ondersteuning biedt voor het wijzigen van de lijst met inhoudstypen.

Sla de sjabloon op als **resource-manager-cdn.json**.

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

Implementeer de sjabloon met Azure CLI. U wordt gevraagd om 2 ingangen:

**cdnProfileSku** - de CDN-provider die u wilt gebruiken. De opties zijn:

* Standard_Microsoft
* Standard_Akamai
* Standard_Verizon
* Premium_Verizon.

**eindpuntOriginHostName** - het eindpunt dat wordt geserveerd via het CDN, bijvoorbeeld cdndemo.azurewebsites.net.

```azurecli-interactive
az group deployment create --resource-group cdn --template-file arm-cdn.json
```

![Sjabloon Resourcemanager implementeren](./media/create-profile-resource-manager-template/cdn-deploy-resource-manager.png)

## <a name="view-the-cdn-profile"></a>Het CDN-profiel weergeven

```azurecli-interactive
az cdn profile list --resource-group cdn -o table
```

![CDN-profiel weergeven](./media/create-profile-resource-manager-template/cdn-view-profile.png)

## <a name="view-the-cdn-endpoint-for-the-profile-standard-microsoft"></a>Bekijk het CDN-eindpunt voor de profielstandaard-microsoft

```azurecli-interactive
az cdn endpoint list --profile-name standard-microsoft --resource-group cdn -o table
```

![CDN-eindpunt weergeven](./media/create-profile-resource-manager-template/cdn-view-endpoint.png)

Gebruik de HostName om de inhoud te bekijken. Bijvoorbeeld toegang https://cdndemo-azurewebsites-net.azureedge.net via uw browser.

## <a name="clean-up"></a>Opruimen

Als u de resourcegroep verwijdert, worden automatisch alle resources verwijderd die erin zijn geïmplementeerd.

```azurecli-interactive
az group delete --name cdn
```

![Brongroep verwijderen](./media/create-profile-resource-manager-template/cdn-delete-resource-group.png)

## <a name="references"></a>Verwijzingen

* CDN-profiel - [Naslaginformatie over azure resource manager-sjabloon](https://docs.microsoft.com/azure/templates/microsoft.cdn/2017-10-12/profiles)
* CDN-eindpunt - [Naslagdocumentatie van Azure Resource Manager-sjabloon](https://docs.microsoft.com/azure/templates/microsoft.cdn/2017-10-12/profiles/endpoints)

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over het toevoegen van een aangepast domein aan uw CDN-eindpunt, de volgende zelfstudie:

> [!div class="nextstepaction"]
> [Zelfstudie: Een aangepast domein toevoegen aan uw Azure CDN-eindpunt](cdn-map-content-to-custom-domain.md)
