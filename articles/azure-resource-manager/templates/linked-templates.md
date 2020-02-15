---
title: Koppelings sjablonen voor implementatie
description: Beschrijft hoe u gekoppelde sjablonen gebruiken in een Azure Resource Manager-sjabloon om een sjabloonoplossing modulaire te maken. Laat zien hoe u de parameterwaarden doorgeven, geeft u een parameterbestand en de dynamisch gemaakte URL's.
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: c6a5dced0f8607e760422bebd114a08ca0a238e3
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2020
ms.locfileid: "77207601"
---
# <a name="using-linked-and-nested-templates-when-deploying-azure-resources"></a>Met behulp van gekoppelde en geneste sjablonen bij het implementeren van Azure-resources

Als u complexe oplossingen wilt implementeren, kunt u uw sjabloon opdelen in veel gerelateerde sjablonen en deze vervolgens samen implementeren via een hoofd sjabloon. De gerelateerde sjablonen kunnen afzonderlijke bestanden of sjabloon syntaxis zijn die is inge sloten in de hoofd sjabloon. In dit artikel wordt gebruikgemaakt van de term **gekoppelde sjabloon** om te verwijzen naar een afzonderlijk sjabloon bestand dat is gekoppeld aan uit de hoofd sjabloon. De term **geneste sjabloon** wordt gebruikt om te verwijzen naar de Inge sloten sjabloon syntaxis binnen de hoofd sjabloon.

Voor kleine tot middelgrote oplossingen is één enkele sjabloon gemakkelijker te begrijpen en onderhouden. U kunt alle resources en -waarden in een enkel bestand zien. Voor geavanceerde scenario's kunt u met gekoppelde sjablonen de oplossing opsplitsen in doel onderdelen. U kunt deze sjablonen eenvoudig opnieuw gebruiken voor andere scenario's.

Zie [zelf studie: gekoppelde Azure Resource Manager sjablonen maken](template-tutorial-create-linked-templates.md)voor een zelf studie.

> [!NOTE]
> Voor gekoppelde of geneste sjablonen kunt u alleen [incrementele](deployment-modes.md) implementatie modus gebruiken.
>

## <a name="nested-template"></a>Geneste sjabloon

Als u een sjabloon wilt nesten, moet u een implementatie van een [resource](/azure/templates/microsoft.resources/deployments) toevoegen aan uw hoofd sjabloon. Geef in de **sjabloon** eigenschap de syntaxis van de sjabloon op.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [
    {
      "name": "nestedTemplate1",
      "apiVersion": "2017-05-10",
      "type": "Microsoft.Resources/deployments",
      "properties": {
        "mode": "Incremental",
        "template": {
          <nested-template-syntax>
        }
      }
    }
  ],
  "outputs": {
  }
}
```

In het volgende voor beeld wordt een opslag account met een geneste sjabloon geïmplementeerd.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountName": {
      "type": "string"
    }
  },
  "resources": [
    {
      "name": "nestedTemplate1",
      "apiVersion": "2017-05-10",
      "type": "Microsoft.Resources/deployments",
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": [
            {
              "type": "Microsoft.Storage/storageAccounts",
              "apiVersion": "2019-04-01",
              "name": "[parameters('storageAccountName')]",
              "location": "West US",
              "sku": {
                "name": "Standard_LRS"
              },
              "kind": "StorageV2"
            }
          ]
        }
      }
    }
  ],
  "outputs": {
  }
}
```

### <a name="scope-for-expressions-in-nested-templates"></a>Bereik voor expressies in geneste sjablonen

Wanneer u een geneste sjabloon gebruikt, kunt u opgeven of sjabloon expressies worden geëvalueerd binnen het bereik van de bovenliggende sjabloon of de geneste sjabloon. Het bereik bepaalt hoe para meters, variabelen en functies, zoals [resourceGroup](template-functions-resource.md#resourcegroup) en [abonnementen](template-functions-resource.md#subscription) , worden opgelost.

U stelt het bereik in via de eigenschap `expressionEvaluationOptions`. De eigenschap `expressionEvaluationOptions` is standaard ingesteld op `outer`, wat inhoudt dat het bereik van de bovenliggende sjabloon wordt gebruikt. Stel de waarde in op `inner` Scope-expressies naar de geneste sjabloon.

```json
{
  "type": "Microsoft.Resources/deployments",
  "apiVersion": "2017-05-10",
  "name": "nestedTemplate1",
  "properties": {
  "expressionEvaluationOptions": {
    "scope": "inner"
  },
  ...
```

In de volgende sjabloon ziet u hoe sjabloon expressies worden omgezet volgens het bereik. Het bevat een variabele met de naam `exampleVar` die is gedefinieerd in zowel de bovenliggende sjabloon als de geneste sjabloon. Hiermee wordt de waarde van de variabele geretourneerd.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
  },
  "variables": {
    "exampleVar": "from parent template"
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2017-05-10",
      "name": "nestedTemplate1",
      "properties": {
        "expressionEvaluationOptions": {
          "scope": "inner"
        },
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "variables": {
            "exampleVar": "from nested template"
          },
          "resources": [
          ],
          "outputs": {
            "testVar": {
              "type": "string",
              "value": "[variables('exampleVar')]"
            }
          }
        }
      }
    }
  ],
  "outputs": {
    "messageFromLinkedTemplate": {
      "type": "string",
      "value": "[reference('nestedTemplate1').outputs.testVar.value]"
    }
  }
}
```

De waarde van de variabele wordt gewijzigd op basis van het bereik. In de volgende tabel ziet u de resultaten voor beide bereiken.

| Bereik | Uitvoer |
| ----- | ------ |
| wend | van geneste sjabloon |
| Outer (of standaard) | van bovenliggende sjabloon |

In het volgende voor beeld wordt een SQL Server geïmplementeerd en wordt een sleutel kluis geheim opgehaald dat moet worden gebruikt voor het wacht woord. Het bereik is ingesteld op `inner`, omdat de sleutel kluis-ID dynamisch wordt gemaakt en door gegeven als een para meter voor de geneste sjabloon.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "The location where the resources will be deployed."
      }
    },
    "vaultName": {
      "type": "string",
      "metadata": {
        "description": "The name of the keyvault that contains the secret."
      }
    },
    "secretName": {
      "type": "string",
      "metadata": {
        "description": "The name of the secret."
      }
    },
    "vaultResourceGroupName": {
      "type": "string",
      "metadata": {
        "description": "The name of the resource group that contains the keyvault."
      }
    },
    "vaultSubscription": {
      "type": "string",
      "defaultValue": "[subscription().subscriptionId]",
      "metadata": {
        "description": "The name of the subscription that contains the keyvault."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2018-05-01",
      "name": "dynamicSecret",
      "properties": {
        "mode": "Incremental",
        "expressionEvaluationOptions": {
          "scope": "inner"
        },
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminLogin": {
              "type": "string"
            },
            "adminPassword": {
              "type": "securestring"
            },
            "location": {
              "type": "string"
            }
          },
          "variables": {
            "sqlServerName": "[concat('sql-', uniqueString(resourceGroup().id, 'sql'))]"
          },
          "resources": [
            {
              "type": "Microsoft.Sql/servers",
              "apiVersion": "2018-06-01-preview",
              "name": "[variables('sqlServerName')]",
              "location": "[parameters('location')]",
              "properties": {
                "administratorLogin": "[parameters('adminLogin')]",
                "administratorLoginPassword": "[parameters('adminPassword')]"
              }
            }
          ],
          "outputs": {
            "sqlFQDN": {
              "type": "string",
              "value": "[reference(variables('sqlServerName')).fullyQualifiedDomainName]"
            }
          }
        },
        "parameters": {
          "location": {
            "value": "[parameters('location')]"
          },
          "adminLogin": {
            "value": "ghuser"
          },
          "adminPassword": {
            "reference": {
              "keyVault": {
                "id": "[resourceId(parameters('vaultSubscription'), parameters('vaultResourceGroupName'), 'Microsoft.KeyVault/vaults', parameters('vaultName'))]"
              },
              "secretName": "[parameters('secretName')]"
            }
          }
        }
      }
    }
  ],
  "outputs": {
  }
}
```

> [!NOTE]
>
> Wanneer bereik is ingesteld op `outer`, kunt u de functie `reference` niet gebruiken in het gedeelte outputs van een geneste sjabloon voor een resource die u in de geneste sjabloon hebt geïmplementeerd. Als u de waarden voor een geïmplementeerde resource in een geneste sjabloon wilt retour neren, gebruikt u binnenste bereik of converteert u uw geneste sjabloon naar een gekoppelde sjabloon.

## <a name="linked-template"></a>Gekoppelde sjabloon

Als u een sjabloon wilt koppelen, moet u een implementatie van een [resource](/azure/templates/microsoft.resources/deployments) toevoegen aan uw hoofd sjabloon. Geef in de eigenschap **templateLink** de URI op van de sjabloon die u wilt toevoegen. Het volgende voor beeld is een koppeling naar een sjabloon waarmee een nieuw opslag account wordt geïmplementeerd.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2017-05-10",
      "name": "linkedTemplate",
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "uri":"https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.json",
          "contentVersion":"1.0.0.0"
        }
      }
    }
  ],
  "outputs": {
  }
}
```

U kunt een lokaal bestand of een bestand dat is alleen beschikbaar op uw lokale netwerk opgeven. U kunt alleen een URI-waarde opgeven die **http** of **https**bevat. Resource Manager moet toegang hebben tot de sjabloon. Een optie is de gekoppelde sjabloon in een storage-account en gebruik van de URI voor dit item.

U hoeft de eigenschap `contentVersion` niet op te geven voor de sjabloon of para meters. Als u een waarde voor de inhoud versie niet opgeeft, wordt de huidige versie van de sjabloon wordt geïmplementeerd. Als u een waarde voor de inhoudsversie opgeeft, moet deze overeenkomen met de versie in de gekoppelde sjabloon. anders mislukt de implementatie met een fout.

### <a name="parameters-for-linked-template"></a>Para meters voor gekoppelde sjabloon

U kunt de para meters voor de gekoppelde sjabloon opgeven in een extern bestand of in inline. Wanneer u een extern parameter bestand opgeeft, gebruikt u de eigenschap **parametersLink** :

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

Gebruik de eigenschap **para meters** om parameter waarden inline door te geven.

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

U kunt zowel inline-parameters en een koppeling niet gebruiken voor een parameterbestand. De implementatie mislukt met een fout wanneer zowel `parametersLink` als `parameters` zijn opgegeven.

## <a name="using-copy"></a>Kopiëren gebruiken

Als u meerdere exemplaren van een resource met een geneste sjabloon wilt maken, voegt u het element Copy toe op het niveau van de resource **micro soft. resources/Deployments** . Of, als de scope binnenste is, kunt u de kopie binnen de geneste sjabloon toevoegen.

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
    "expressionEvaluationOptions": {
    "scope": "inner"
    },
    "template": {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
      {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(variables('storageName'), copyIndex())]",
      "location": "West US",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "StorageV2"
      // Copy works here when scope is inner
      // But, when scope is default or outer, you get an error
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

In het volgende voor beeld ziet u hoe u een basis-URL gebruikt om twee Url's te maken voor gekoppelde sjablonen (**sharedTemplateUrl** en **vmTemplate**).

```json
"variables": {
  "templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/postgresql-on-ubuntu/",
  "sharedTemplateUrl": "[concat(variables('templateBaseUrl'), 'shared-resources.json')]",
  "vmTemplateUrl": "[concat(variables('templateBaseUrl'), 'database-2disk-resources.json')]"
}
```

U kunt ook [Implementatie ()](template-functions-deployment.md#deployment) gebruiken om de basis-URL voor de huidige sjabloon op te halen en gebruiken om de URL voor andere sjablonen op dezelfde locatie op te halen. Deze methode is handig als uw sjabloon locatie verandert of als u wilt voorkomen dat URL's in het sjabloonbestand hard coderen. De eigenschap templateLink wordt alleen geretourneerd wanneer u een koppeling naar een externe sjabloon met een URL. Als u een lokale sjabloon, is deze eigenschap niet beschikbaar.

```json
"variables": {
  "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"
}
```

## <a name="get-values-from-linked-template"></a>Waarden ophalen uit de gekoppelde sjabloon

Als u een uitvoer waarde van een gekoppelde sjabloon wilt ophalen, haalt u de eigenschaps waarde op met de syntaxis als: `"[reference('deploymentName').outputs.propertyName.value]"`.

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

Net als andere resourcetypen, kunt u de afhankelijkheden tussen de gekoppelde sjabloon en andere resources instellen. Als voor andere resources een uitvoer waarde van de gekoppelde sjabloon is vereist, moet u ervoor zorgen dat de gekoppelde sjabloon wordt geïmplementeerd. Of, als de gekoppelde sjabloon, is afhankelijk van andere bronnen, zorg ervoor dat andere resources worden geïmplementeerd voordat u de gekoppelde sjabloon.

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

## <a name="deployment-history"></a>Implementatiegeschiedenis

Elke sjabloon verwerkt Resource Manager als een afzonderlijke implementatie in de geschiedenis van de implementatie. Een hoofd sjabloon met drie gekoppelde of geneste sjablonen wordt weer gegeven in de implementatie geschiedenis als:

![Implementatiegeschiedenis](./media/linked-templates/deployment-history.png)

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

Op dit moment kunt u geen koppeling maken met een sjabloon in een opslag account dat zich achter een [Azure Storage firewall](../../storage/common/storage-network-security.md)bevindt.

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

In PowerShell kunt u een token verkrijgen voor de container en de sjablonen implementeren met de volgende opdrachten. U ziet dat de para meter **containerSasToken** is gedefinieerd in de sjabloon. Het is geen para meter in de opdracht **New-AzResourceGroupDeployment** .

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

|Belangrijkste sjabloon  |Gekoppelde sjabloon |Beschrijving  |
|---------|---------| ---------|
|[Hello World](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/helloworldparent.json) |[gekoppelde sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/helloworld.json) | Retourneert de tekenreeks van gekoppelde sjabloon. |
|[Load Balancer met openbaar IP-adres](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) |[gekoppelde sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) |Openbare IP-adres van de gekoppelde sjabloon retourneert en stelt u die waarde in de load balancer. |
|[Meerdere IP-adressen](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/static-public-ip-parent.json) | [gekoppelde sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/static-public-ip.json) |Hiermee maakt u meerdere openbare IP-adressen in gekoppelde sjabloon.  |

## <a name="next-steps"></a>Volgende stappen

* Zie [zelf studie: gekoppelde Azure Resource Manager sjablonen maken](template-tutorial-create-linked-templates.md)om een zelf studie te door lopen.
* Zie [afhankelijkheden definiëren in azure Resource Manager sjablonen](define-resource-dependency.md)voor meer informatie over het definiëren van de implementatie volgorde voor uw resources.
* Zie [meerdere exemplaren van resources maken in azure Resource Manager](copy-resources.md)voor meer informatie over het definiëren van één resource, maar het maken van een groot aantal exemplaren.
* Zie [resources implementeren met Resource Manager-sjablonen en Azure PowerShell](deploy-powershell.md) of [resources implementeren met Resource Manager-sjablonen en Azure cli](deploy-cli.md)voor stappen voor het instellen van een sjabloon in een opslag account en het genereren van een SAS-token.
