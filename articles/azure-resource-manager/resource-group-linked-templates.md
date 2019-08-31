---
title: Koppeling van sjablonen voor Azure-implementatie | Microsoft Docs
description: Beschrijft hoe u gekoppelde sjablonen gebruiken in een Azure Resource Manager-sjabloon om een sjabloonoplossing modulaire te maken. Laat zien hoe u de parameterwaarden doorgeven, geeft u een parameterbestand en de dynamisch gemaakte URL's.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: tomfitz
ms.openlocfilehash: b48988c04f6b387a8124a812a836e2b92a9d3ada
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70194379"
---
# <a name="using-linked-and-nested-templates-when-deploying-azure-resources"></a>Met behulp van gekoppelde en geneste sjablonen bij het implementeren van Azure-resources

Voor het implementeren van uw oplossing, kunt u één enkele sjabloon of een belangrijkste sjabloon kunt gebruiken met veel gerelateerde sjablonen. De gerelateerde sjabloon is een afzonderlijk bestand dat is gekoppeld aan de basis van de belangrijkste sjabloon of een sjabloon die is ingesloten in de belangrijkste sjabloon.

Voor kleine tot middelgrote oplossingen is één enkele sjabloon gemakkelijker te begrijpen en onderhouden. U kunt alle resources en -waarden in een enkel bestand zien. Gekoppelde sjablonen kunnen u de oplossing in de betreffende onderdelen opdelen en sjablonen gebruiken voor geavanceerde scenario's.

Wanneer u gekoppelde sjablonen gebruikt, maakt u een belangrijkste sjabloon waarmee de parameterwaarden die zijn ontvangen tijdens de implementatie. De belangrijkste sjabloon bevat alle gekoppelde sjablonen en geeft waarden aan deze sjablonen indien nodig.

Zie voor een zelfstudie [zelfstudie: gekoppelde Azure Resource Manager-sjablonen maken](./resource-manager-tutorial-create-linked-templates.md).

> [!NOTE]
> Voor gekoppelde of geneste sjablonen kunt u alleen [incrementele](deployment-modes.md) implementatie modus gebruiken.
>

## <a name="link-or-nest-a-template"></a>Een koppeling of nesten van een sjabloon

Als u wilt koppelen aan een andere sjabloon, Voeg een **implementaties** resource die u wilt uw belangrijkste sjabloon.

```json
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2018-05-01",
    "name": "linkedTemplate",
    "properties": {
        "mode": "Incremental",
        <nested-template-or-external-template>
    }
  }
]
```

De eigenschappen die u voor de implementatie-resource opgeeft, afhankelijk van of u bent koppelen aan een externe-sjabloon of een inline-sjabloon in de belangrijkste sjabloon nesten.

### <a name="nested-template"></a>Geneste sjabloon

Als u wilt de sjabloon in de belangrijkste sjabloon nesten, gebruikt u de **sjabloon** eigenschap en geeft u de sjabloonsyntaxis van de.

```json
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2018-05-01",
    "name": "nestedTemplate",
    "properties": {
      "mode": "Incremental",
      "template": {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "resources": [
          {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-04-01",
            "name": "[variables('storageName')]",
            "location": "West US",
            "kind": "StorageV2",
            "sku": {
                "name": "Standard_LRS"
            }
          }
        ]
      }
    }
  }
]
```

> [!NOTE]
> Voor geneste sjablonen, kunt u parameters en variabelen die zijn gedefinieerd in de geneste sjabloon niet gebruiken. U kunt parameters en variabelen van de belangrijkste sjabloon gebruiken. In het voorgaande voorbeeld `[variables('storageName')]` haalt een waarde op basis van de belangrijkste sjabloon, niet de geneste sjabloon. Deze beperking geldt niet voor externe sjablonen.
>
> Voor twee resources die zijn gedefinieerd binnen een geneste sjabloon en waarvan één resource afhankelijk is, is de waarde van de afhankelijkheid gewoon de naam van de afhankelijke resource:
> ```json
> "dependsOn": [
>   "[variables('storageAccountName')]"
> ],
> ```
>
> U kunt de `reference` functie niet gebruiken in het gedeelte outputs van een geneste sjabloon voor een resource die u in de geneste sjabloon hebt geïmplementeerd. Als u wilt de waarden voor een geïmplementeerde resource in een geneste sjabloon, uw geneste sjabloon te converteren naar een gekoppelde sjabloon.

De geneste sjabloon moet de [dezelfde eigenschappen](resource-group-authoring-templates.md) als een standaardsjabloon.

### <a name="external-template-and-external-parameters"></a>Externe sjabloon en externe parameters

Gebruiken om een koppeling naar een externe sjabloon en het parameterbestand, **templateLink** en **parametersLink**. Wanneer u een koppeling naar een sjabloon, is de Resource Manager-service moet toegang hebben tot deze. U kunt een lokaal bestand of een bestand dat is alleen beschikbaar op uw lokale netwerk opgeven. U kunt alleen opgeven met een URI-waarde met een **http** of **https**. Een optie is de gekoppelde sjabloon in een storage-account en gebruik van de URI voor dit item.

```json
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2018-05-01",
    "name": "linkedTemplate",
    "properties": {
    "mode": "Incremental",
    "templateLink": {
        "uri":"https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.json",
        "contentVersion":"1.0.0.0"
    },
    "parametersLink": {
        "uri":"https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.parameters.json",
        "contentVersion":"1.0.0.0"
    }
    }
  }
]
```

U hoeft te bieden de `contentVersion` eigenschap voor de sjabloon of de parameters. Als u een waarde voor de inhoud versie niet opgeeft, wordt de huidige versie van de sjabloon wordt geïmplementeerd. Als u een waarde voor de inhoudsversie opgeeft, moet deze overeenkomen met de versie in de gekoppelde sjabloon. anders mislukt de implementatie met een fout.

### <a name="external-template-and-inline-parameters"></a>Externe sjabloon en inline-parameters

Of u kunt de parameter-inline opgeeft tussen. U kunt zowel inline-parameters en een koppeling niet gebruiken voor een parameterbestand. De implementatie is mislukt met foutmelding wanneer beide `parametersLink` en `parameters` zijn opgegeven.

Gebruik een waarde van de belangrijkste sjabloon doorgeven aan de gekoppelde sjabloon, **parameters**.

```json
"resources": [
  {
     "type": "Microsoft.Resources/deployments",
     "apiVersion": "2018-05-01",
     "name": "linkedTemplate",
     "properties": {
       "mode": "Incremental",
       "templateLink": {
          "uri":"https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.json",
          "contentVersion":"1.0.0.0"
       },
       "parameters": {
          "StorageAccountName":{"value": "[parameters('StorageAccountName')]"}
        }
     }
  }
]
```

## <a name="using-copy"></a>Kopiëren gebruiken

Als u meerdere exemplaren van een resource met een geneste sjabloon wilt maken, voegt u het element Copy toe op het niveau van de resource **micro soft. resources/Deployments** .

In de volgende voorbeeld sjabloon ziet u hoe u een kopie gebruikt met een geneste sjabloon.

```json
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2018-05-01",
    "name": "[concat('nestedTemplate', copyIndex())]",
    // yes, copy works here
    "copy":{
      "name": "storagecopy",
      "count": 2
    },
    "properties": {
      "mode": "Incremental",
      "template": {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "resources": [
          {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-04-01",
            "name": "[concat(variables('storageName'), copyIndex())]",
            "location": "West US",
            "kind": "StorageV2",
            "sku": {
              "name": "Standard_LRS"
            }
            // no, copy doesn't work here
            //"copy":{
            //  "name": "storagecopy",
            //  "count": 2
            //}
          }
        ]
      }
    }
  }
]
```

## <a name="using-variables-to-link-templates"></a>Variabelen gebruiken om te koppelen van sjablonen

De voorgaande voorbeelden toonden vastgelegde URL waarden voor de sjabloon-koppelingen. Deze benadering werkt mogelijk voor een eenvoudige sjabloon, maar werkt niet goed als u werkt met een groot aantal modulaire sjablonen. U kunt in plaats daarvan een statische variabele waarin een basis-URL voor de belangrijkste sjabloon maken en vervolgens de URL's voor de gekoppelde sjablonen vanuit die basis-URL dynamisch maken. Het voordeel van deze benadering is kunt u eenvoudig verplaatsen of de sjabloon splitsen omdat u alleen hoeft te wijzigen van de statische variabele in de belangrijkste sjabloon. De belangrijkste sjabloon wordt de juiste URI's in de sjabloon opgesplitste doorgegeven.

Het volgende voorbeeld laat zien hoe u een basis-URL om te maken van twee URL's voor de gekoppelde sjablonen (**sharedTemplateUrl** en **vmTemplate**).

```json
"variables": {
    "templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/postgresql-on-ubuntu/",
    "sharedTemplateUrl": "[concat(variables('templateBaseUrl'), 'shared-resources.json')]",
    "vmTemplateUrl": "[concat(variables('templateBaseUrl'), 'database-2disk-resources.json')]"
}
```

U kunt ook [deployment()](resource-group-template-functions-deployment.md#deployment) aan de basis-URL ophalen voor de huidige sjabloon, en gebruik die om de URL voor andere sjablonen op dezelfde locatie. Deze methode is handig als uw sjabloon locatie verandert of als u wilt voorkomen dat URL's in het sjabloonbestand hard coderen. De eigenschap templateLink wordt alleen geretourneerd wanneer u een koppeling naar een externe sjabloon met een URL. Als u een lokale sjabloon, is deze eigenschap niet beschikbaar.

```json
"variables": {
    "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"
}
```

## <a name="get-values-from-linked-template"></a>Waarden ophalen uit de gekoppelde sjabloon

Als u een uitvoerwaarde op basis van een gekoppelde sjabloon, halen de waarde van de eigenschap met de syntaxis, zoals: `"[reference('deploymentName').outputs.propertyName.value]"`.

Bij het ophalen van een eigenschap van een uitvoer van een gekoppelde sjabloon, kan niet de eigenschapsnaam van de een streepje bestaan.

De volgende voorbeelden laten zien hoe u verwijzen naar een gekoppelde sjabloon en een uitvoerwaarde op te halen. De gekoppelde sjabloon retourneert een eenvoudige bericht.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [],
    "outputs": {
        "greetingMessage": {
            "value": "Hello World",
            "type" : "string"
        }
    }
}
```

De belangrijkste sjabloon implementeert de gekoppelde sjabloon en de geretourneerde waarde opgehaald. U ziet dat deze verwijst naar de implementatie-resource met de naam, en maakt gebruik van de naam van de eigenschap die wordt geretourneerd door de gekoppelde sjabloon.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2018-05-01",
            "name": "linkedTemplate",
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                    "uri": "[uri(deployment().properties.templateLink.uri, 'helloworld.json')]",
                    "contentVersion": "1.0.0.0"
                }
            }
        }
    ],
    "outputs": {
        "messageFromLinkedTemplate": {
            "type": "string",
            "value": "[reference('linkedTemplate').outputs.greetingMessage.value]"
        }
    }
}
```

Net als andere resourcetypen, kunt u de afhankelijkheden tussen de gekoppelde sjabloon en andere resources instellen. Als voor andere resources een waarde voor de uitvoer van de gekoppelde sjabloon moet, zorg ervoor dat de gekoppelde sjabloon voordat deze is geïmplementeerd. Of, als de gekoppelde sjabloon, is afhankelijk van andere bronnen, zorg ervoor dat andere resources worden geïmplementeerd voordat u de gekoppelde sjabloon.

Het volgende voorbeeld ziet u een sjabloon die wordt geïmplementeerd een openbaar IP-adres en retourneert de resource-ID:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "publicIPAddresses_name": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2018-11-01",
            "name": "[parameters('publicIPAddresses_name')]",
            "location": "eastus",
            "properties": {
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4
            },
            "dependsOn": []
        }
    ],
    "outputs": {
        "resourceID": {
            "type": "string",
            "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
        }
    }
}
```

Koppeling naar de sjabloon voor het gebruik van het openbare IP-adres van de voorgaande sjabloon bij het implementeren van een load balancer, en een afhankelijkheid op de implementatie-resource toevoegen. Het openbare IP-adres op de load balancer is ingesteld op de uitvoerwaarde van de gekoppelde sjabloon.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "loadBalancers_name": {
            "defaultValue": "mylb",
            "type": "string"
        },
        "publicIPAddresses_name": {
            "defaultValue": "myip",
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Network/loadBalancers",
            "apiVersion": "2018-11-01",
            "name": "[parameters('loadBalancers_name')]",
            "location": "eastus",
            "properties": {
                "frontendIPConfigurations": [
                    {
                        "name": "LoadBalancerFrontEnd",
                        "properties": {
                            "privateIPAllocationMethod": "Dynamic",
                            "publicIPAddress": {
                                "id": "[reference('linkedTemplate').outputs.resourceID.value]"
                            }
                        }
                    }
                ],
                "backendAddressPools": [],
                "loadBalancingRules": [],
                "probes": [],
                "inboundNatRules": [],
                "outboundNatRules": [],
                "inboundNatPools": []
            },
            "dependsOn": [
                "linkedTemplate"
            ]
        },
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2018-05-01",
            "name": "linkedTemplate",
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                    "uri": "[uri(deployment().properties.templateLink.uri, 'publicip.json')]",
                    "contentVersion": "1.0.0.0"
                },
                "parameters":{
                    "publicIPAddresses_name":{"value": "[parameters('publicIPAddresses_name')]"}
                }
            }
        }
    ]
}
```

## <a name="linked-and-nested-templates-in-deployment-history"></a>Gekoppelde en geneste sjablonen in de implementatiegeschiedenis

Elke sjabloon verwerkt Resource Manager als een afzonderlijke implementatie in de geschiedenis van de implementatie. Daarom een belangrijke sjabloon met drie gekoppelde of geneste sjablonen wordt weergegeven in de geschiedenis van de implementatie als:

![Implementatiegeschiedenis](./media/resource-group-linked-templates/deployment-history.png)

U kunt deze afzonderlijke items in de geschiedenis voor het ophalen van uitvoerwaarden na de implementatie. De volgende sjabloon wordt een openbaar IP-adres en het IP-adres weergeeft:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "publicIPAddresses_name": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2018-11-01",
            "name": "[parameters('publicIPAddresses_name')]",
            "location": "southcentralus",
            "properties": {
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Static",
                "idleTimeoutInMinutes": 4,
                "dnsSettings": {
                    "domainNameLabel": "[concat(parameters('publicIPAddresses_name'), uniqueString(resourceGroup().id))]"
                }
            },
            "dependsOn": []
        }
    ],
    "outputs": {
        "returnedIPAddress": {
            "type": "string",
            "value": "[reference(parameters('publicIPAddresses_name')).ipAddress]"
        }
    }
}
```

De volgende sjabloon een koppeling naar de voorgaande sjabloon. Hiermee maakt u drie openbare IP-adressen.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2018-05-01",
            "name": "[concat('linkedTemplate', copyIndex())]",
            "copy": {
                "count": 3,
                "name": "ip-loop"
            },
            "properties": {
              "mode": "Incremental",
              "templateLink": {
                "uri": "[uri(deployment().properties.templateLink.uri, 'static-public-ip.json')]",
                "contentVersion": "1.0.0.0"
              },
              "parameters":{
                  "publicIPAddresses_name":{"value": "[concat('myip-', copyIndex())]"}
              }
            }
        }
    ]
}
```

Na de implementatie, kunt u de uitvoerwaarden met de volgende PowerShell-script ophalen:

```azurepowershell-interactive
$loopCount = 3
for ($i = 0; $i -lt $loopCount; $i++)
{
    $name = 'linkedTemplate' + $i;
    $deployment = Get-AzResourceGroupDeployment -ResourceGroupName examplegroup -Name $name
    Write-Output "deployment $($deployment.DeploymentName) returned $($deployment.Outputs.returnedIPAddress.value)"
}
```

Of Azure CLI-script in een Bash-shell:

```azurecli-interactive
#!/bin/bash

for i in 0 1 2;
do
    name="linkedTemplate$i";
    deployment=$(az group deployment show -g examplegroup -n $name);
    ip=$(echo $deployment | jq .properties.outputs.returnedIPAddress.value);
    echo "deployment $name returned $ip";
done
```

## <a name="securing-an-external-template"></a>Beveiligen van een externe-sjabloon

Hoewel de gekoppelde sjabloon extern beschikbaar zijn moet, hoeft niet te worden in het algemeen beschikbaar is voor het publiek. U kunt uw sjabloon toevoegen aan een persoonlijke storage-account die toegankelijk is voor alleen de eigenaar van het opslagaccount. Vervolgens maakt u een token shared access signature (SAS) waarmee toegang tijdens de implementatie. U toevoegen deze SAS-token aan de URI voor de gekoppelde sjabloon. Hoewel het token is doorgegeven als een beveiligde tekenreeks, wordt de URI van de gekoppelde sjabloon, met inbegrip van de SAS-token in de implementatiebewerkingen vastgelegd. Als u wilt beperken van blootstelling, instellen dat een verlopen voor het token.

De parameter-bestand kan ook worden beperkt tot toegang tot en met een SAS-token.

Op dit moment kunt u geen koppeling maken met een sjabloon in een opslag account dat zich achter een [Azure Storage firewall](../storage/common/storage-network-security.md)bevindt.

Het volgende voorbeeld laat zien hoe om door te geven van een SAS-token bij het koppelen aan een sjabloon:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "containerSasToken": { "type": "string" }
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2018-05-01",
      "name": "linkedTemplate",
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "uri": "[concat(uri(deployment().properties.templateLink.uri, 'helloworld.json'), parameters('containerSasToken'))]",
          "contentVersion": "1.0.0.0"
        }
      }
    }
  ],
  "outputs": {
  }
}
```

In PowerShell kunt u een token verkrijgen voor de container en de sjablonen implementeren met de volgende opdrachten. U ziet dat de **containerSasToken** parameter is gedefinieerd in de sjabloon. Het is geen para meter in de opdracht **New-AzResourceGroupDeployment** .

```azurepowershell-interactive
Set-AzCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates
$token = New-AzStorageContainerSASToken -Name templates -Permission r -ExpiryTime (Get-Date).AddMinutes(30.0)
$url = (Get-AzStorageBlob -Container templates -Blob parent.json).ICloudBlob.uri.AbsoluteUri
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateUri ($url + $token) -containerSasToken $token
```

Voor Azure CLI in een Bash-shell, moet u een token verkrijgen voor de container en de sjablonen implementeren met de volgende code:

```azurecli-interactive
#!/bin/bash

expiretime=$(date -u -d '30 minutes' +%Y-%m-%dT%H:%MZ)
connection=$(az storage account show-connection-string \
    --resource-group ManageGroup \
    --name storagecontosotemplates \
    --query connectionString)
token=$(az storage container generate-sas \
    --name templates \
    --expiry $expiretime \
    --permissions r \
    --output tsv \
    --connection-string $connection)
url=$(az storage blob url \
    --container-name templates \
    --name parent.json \
    --output tsv \
    --connection-string $connection)
parameter='{"containerSasToken":{"value":"?'$token'"}}'
az group deployment create --resource-group ExampleGroup --template-uri $url?$token --parameters $parameter
```

## <a name="example-templates"></a>Voorbeeldsjablonen

De volgende voorbeelden ziet veelvoorkomende toepassingen van gekoppelde sjablonen.

|Belangrijkste sjabloon  |Gekoppelde sjabloon |Description  |
|---------|---------| ---------|
|[Hello World](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/helloworldparent.json) |[Gekoppelde sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/helloworld.json) | Retourneert de tekenreeks van gekoppelde sjabloon. |
|[Load Balancer met openbare IP-adres](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) |[Gekoppelde sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) |Openbare IP-adres van de gekoppelde sjabloon retourneert en stelt u die waarde in de load balancer. |
|[Meerdere IP-adressen](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/static-public-ip-parent.json) | [Gekoppelde sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/static-public-ip.json) |Hiermee maakt u meerdere openbare IP-adressen in gekoppelde sjabloon.  |

## <a name="next-steps"></a>Volgende stappen

* Zie voor een zelfstudie doorloopt, [zelfstudie: gekoppelde Azure Resource Manager-sjablonen maken](./resource-manager-tutorial-create-linked-templates.md).
* Zie voor meer informatie over het definiëren van de implementatievolgorde voor uw resources, [afhankelijkheden definiëren in Azure Resource Manager-sjablonen](resource-group-define-dependencies.md).
* Zie voor informatie over het definiëren van een resource, maar veel exemplaren van het maken, [meerdere exemplaren van resources maken in Azure Resource Manager](resource-group-create-multiple.md).
* Zie voor stappen voor het instellen van een sjabloon in een storage-account en het genereren van een SAS-token, [resources implementeren met Resource Manager-sjablonen en Azure PowerShell](resource-group-template-deploy.md) of [resources implementeren met Resource Manager-sjablonen en Azure CLI](resource-group-template-deploy-cli.md).
