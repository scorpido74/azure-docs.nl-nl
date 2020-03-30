---
title: Koppelingssjablonen voor implementatie
description: Beschrijft hoe u gekoppelde sjablonen in een Azure Resource Manager-sjabloon gebruiken om een modulaire sjabloonoplossing te maken. Toont hoe u parameterswaarden doorgeeft, een parameterbestand opgeeft en dynamisch gemaakte URL's.
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: 322797383ee865ceb66c44793387da827aeb8879
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131916"
---
# <a name="using-linked-and-nested-templates-when-deploying-azure-resources"></a>Gekoppelde en geneste sjablonen gebruiken bij het implementeren van Azure-resources

Als u complexe oplossingen wilt implementeren, u uw sjabloon opsplitsen in veel gerelateerde sjablonen en deze vervolgens samen implementeren via een hoofdsjabloon. De bijbehorende sjablonen kunnen afzonderlijke bestanden of sjabloonsyntaxis zijn die zijn ingesloten in de hoofdsjabloon. In dit artikel wordt de term **gekoppelde sjabloon** gebruikt om te verwijzen naar een afzonderlijk sjabloonbestand waarnaar wordt verwezen via een koppeling van de hoofdsjabloon. Het gebruikt de term **geneste sjabloon** om te verwijzen naar ingesloten sjabloonsyntaxis in de hoofdsjabloon.

Voor kleine tot middelgrote oplossingen is één sjabloon gemakkelijker te begrijpen en te onderhouden. U alle bronnen en waarden in één bestand bekijken. Voor geavanceerde scenario's u met gekoppelde sjablonen de oplossing opsplitsen in gerichte componenten. U deze sjablonen eenvoudig opnieuw gebruiken voor andere scenario's.

Zie [Zelfstudie: gekoppelde Azure Resource Manager-sjablonen maken](template-tutorial-create-linked-templates.md)voor een zelfstudie.

> [!NOTE]
> Voor gekoppelde of geneste sjablonen u alleen [incrementele](deployment-modes.md) implementatiemodus gebruiken.
>

## <a name="nested-template"></a>Geneste sjabloon

Als u een sjabloon wilt nesten, voegt u een [implementatiebron](/azure/templates/microsoft.resources/deployments) toe aan uw hoofdsjabloon. Geef in de eigenschap **sjabloon** de syntaxis van de sjabloon op.

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

In het volgende voorbeeld wordt een opslagaccount geïmplementeerd via een geneste sjabloon.

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

### <a name="expression-evaluation-scope-in-nested-templates"></a>Expressiebeoordelingsbereik in geneste sjablonen

Wanneer u een geneste sjabloon gebruikt, u opgeven of sjabloonexpressies worden geëvalueerd binnen het bereik van de bovenliggende sjabloon of de geneste sjabloon. Het bereik bepaalt hoe parameters, variabelen en functies zoals [resourceGroup](template-functions-resource.md#resourcegroup) en [abonnement](template-functions-resource.md#subscription) worden opgelost.

U stelt de `expressionEvaluationOptions` scope in via het pand. Standaard is `expressionEvaluationOptions` de eigenschap `outer`ingesteld op , wat betekent dat het de bovenliggende sjabloonbereik wordt gebruikt. Stel de `inner` waarde in om ervoor te zorgen dat expressies worden geëvalueerd binnen het bereik van de geneste sjabloon.

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

In de volgende sjabloon wordt uitgelegd hoe sjabloonexpressies worden opgelost op basis van het bereik. Het bevat een `exampleVar` variabele met de naam die is gedefinieerd in zowel de bovenliggende sjabloon als de geneste sjabloon. Hiermee wordt de waarde van de variabele geretourneerd.

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

De waarde `exampleVar` van veranderingen afhankelijk van `scope` de `expressionEvaluationOptions`waarde van de eigenschap in . In de volgende tabel worden de resultaten voor beide scopes weergegeven.

| `expressionEvaluationOptions` `scope` | Uitvoer |
| ----- | ------ |
| Innerlijke | van geneste sjabloon |
| buitenste (of standaard) | van bovenliggende sjabloon |

In het volgende voorbeeld wordt een SQL-server geïmplementeerd en wordt een sleutelkluisgeheim opgehaald dat moet worden gebruikt voor het wachtwoord. Het bereik is `inner` ingesteld op omdat het dynamisch `adminPassword.reference.keyVault` de sleutelkluis-ID maakt (zie in de buitenste sjablonen) `parameters`en deze als parameter doorgeeft aan de geneste sjabloon.

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
> Wanneer bereik is `outer`ingesteld op , `reference` kunt u de functie niet gebruiken in het gedeelte uitvoer van een geneste sjabloon voor een resource die u hebt geïmplementeerd in de geneste sjabloon. Als u de waarden voor een geïmplementeerde resource `inner` wilt retourneren in een geneste sjabloon, gebruikt u bereik of converteert u uw geneste sjabloon naar een gekoppelde sjabloon.

## <a name="linked-template"></a>Gekoppelde sjabloon

Als u een sjabloon wilt koppelen, voegt u een [implementatiebron](/azure/templates/microsoft.resources/deployments) toe aan uw hoofdsjabloon. Geef in de eigenschap **templateLink** de URI op van de sjabloon die moet worden opgenomen. In het volgende voorbeeld wordt koppelingen gemaakt naar een sjabloon waarmee een nieuw opslagaccount wordt geïmplementeerd.

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

Wanneer u naar een gekoppelde `uri` sjabloon verwijst, mag de waarde van geen lokaal bestand of een bestand zijn dat alleen beschikbaar is op uw lokale netwerk. U moet een URI-waarde opgeven die kan worden gedownload als **http** of **https.** 

> [!NOTE]
>
> U naar sjablonen verwijzen met behulp van parameters die uiteindelijk worden `_artifactsLocation` opgelost tot iets dat **http** of **https**gebruikt, bijvoorbeeld met behulp van de parameter zoals:`"uri": "[concat(parameters('_artifactsLocation'), '/shared/os-disk-parts-md.json', parameters('_artifactsLocationSasToken'))]",`



Resourcemanager moet toegang hebben tot de sjabloon. Een optie is om uw gekoppelde sjabloon in een opslagaccount te plaatsen en de URI voor dat item te gebruiken.

### <a name="parameters-for-linked-template"></a>Parameters voor gekoppelde sjabloon

U de parameters voor uw gekoppelde sjabloon opgeven in een extern bestand of inline. Wanneer u een extern parameterbestand opgeeft, gebruikt u de eigenschap **parametersLink:**

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

Als u parameterwaarden inline wilt doorgeven, gebruikt u de eigenschap **parameters.**

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

U niet zowel inlineparameters als een koppeling naar een parameterbestand gebruiken. De implementatie mislukt met `parametersLink` een `parameters` fout wanneer beide en zijn opgegeven.

## `contentVersion`

U hoeft de `contentVersion` accommodatie niet te `templateLink` `parametersLink` voorzien voor de woning. Als u geen `contentVersion`nieuwe versie van de sjabloon verstrekt, wordt de huidige versie van de sjabloon geïmplementeerd. Als u een waarde voor inhoudsversie opgeeft, moet deze overeenkomen met de versie in de gekoppelde sjabloon. anders mislukt de implementatie met een fout.

## <a name="using-variables-to-link-templates"></a>Variabelen gebruiken om sjablonen te koppelen

De vorige voorbeelden toonden hard-gecodeerde URL-waarden voor de sjabloonkoppelingen. Deze aanpak werkt mogelijk voor een eenvoudige sjabloon, maar werkt niet goed voor een grote set modulaire sjablonen. In plaats daarvan u een statische variabele maken die een basis-URL voor de hoofdsjabloon opslaat en vervolgens dynamisch URL's maakt voor de gekoppelde sjablonen van die basis-URL. Het voordeel van deze aanpak is dat u de sjabloon gemakkelijk verplaatsen of vorken omdat u alleen de statische variabele in de hoofdsjabloon moet wijzigen. De hoofdsjabloon geeft de juiste URI's door de ontbonden sjabloon.

In het volgende voorbeeld ziet u hoe u een basis-URL gebruiken om twee URL's te maken voor gekoppelde sjablonen **(sharedTemplateUrl** en **vmTemplate).**

```json
"variables": {
  "templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/postgresql-on-ubuntu/",
  "sharedTemplateUrl": "[uri(variables('templateBaseUrl'), 'shared-resources.json')]",
  "vmTemplateUrl": "[uri(variables('templateBaseUrl'), 'database-2disk-resources.json')]"
}
```

U [ook deployment()](template-functions-deployment.md#deployment) gebruiken om de basis-URL voor de huidige sjabloon op te halen en deze te gebruiken om de URL voor andere sjablonen op dezelfde locatie te krijgen. Deze benadering is handig als de locatie van uw sjabloon verandert of als u harde coderings-URL's in het sjabloonbestand wilt vermijden. De eigenschap templateLink wordt alleen geretourneerd wanneer u een koppeling maakt naar een externe sjabloon met een URL. Als u een lokale sjabloon gebruikt, is die eigenschap niet beschikbaar.

```json
"variables": {
  "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"
}
```

Uiteindelijk zou u gebruik maken `uri` van de `templateLink` variabele in het pand van een woning.

```json
"templateLink": {
 "uri": "[variables('sharedTemplateUrl')]",
 "contentVersion":"1.0.0.0"
}
```

## <a name="using-copy"></a>Kopiëren gebruiken

Als u meerdere exemplaren van een resource met een geneste sjabloon wilt maken, voegt u het kopieerelement toe op het niveau van de bron **Microsoft.Resources/Deployments.** Of als het bereik binnen is, u de kopie toevoegen in de geneste sjabloon.

In de volgende voorbeeldsjabloon ziet u hoe u kopiëren gebruikt met een geneste sjabloon.

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

## <a name="get-values-from-linked-template"></a>Waarden ophalen uit de gekoppelde sjabloon

Als u een uitvoerwaarde wilt ophalen uit een gekoppelde sjabloon, haalt u de eigenschapswaarde op met syntaxis als: `"[reference('deploymentName').outputs.propertyName.value]"`.

Wanneer u een uitvoereigenschap van een gekoppelde sjabloon krijgt, mag de eigenschapsnaam geen streepje bevatten.

In de volgende voorbeelden wordt uitgelegd hoe u naar een gekoppelde sjabloon verwijzen en een uitvoerwaarde ophalen. De gekoppelde sjabloon retourneert een eenvoudig bericht.  Ten eerste de gekoppelde sjabloon:

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

De hoofdsjabloon implementeert de gekoppelde sjabloon en krijgt de geretourneerde waarde. Deze verwijst naar de implementatiebron op naam en gebruikt de naam van de eigenschap die wordt geretourneerd door de gekoppelde sjabloon.

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

Net als bij andere resourcetypen u afhankelijkheden instellen tussen de gekoppelde sjabloon en andere bronnen. Wanneer andere resources een uitvoerwaarde van de gekoppelde sjabloon vereisen, controleert u of de gekoppelde sjabloon vóór deze gegevens is geïmplementeerd. Of, wanneer de gekoppelde sjabloon afhankelijk is van andere bronnen, moet u ervoor zorgen dat andere bronnen worden geïmplementeerd vóór de gekoppelde sjabloon.

In het volgende voorbeeld wordt een sjabloon weergegeven die een openbaar IP-adres implementeert en de bron-id van de Azure-bron voor dat openbare IP retourneert:

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

Als u het openbare IP-adres van de voorgaande sjabloon wilt gebruiken bij het implementeren `Microsoft.Resources/deployments` van een load balancer, koppelt u een koppeling aan de sjabloon en verklaart u een afhankelijkheid van de bron. Het openbare IP-adres op de load balancer is ingesteld op de uitvoerwaarde van de gekoppelde sjabloon.

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
                // this is where the output value from linkedTemplate is used
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
      // This is where the dependency is declared
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

Resource manager verwerkt elke sjabloon als een afzonderlijke implementatie in de implementatiegeschiedenis. Een hoofdsjabloon met drie gekoppelde of geneste sjablonen wordt in de implementatiegeschiedenis weergegeven als:

![Implementatiegeschiedenis](./media/linked-templates/deployment-history.png)

U deze afzonderlijke vermeldingen in de geschiedenis gebruiken om uitvoerwaarden op te halen na de implementatie. Met de volgende sjabloon wordt een openbaar IP-adres gemaakt en wordt het IP-adres uitgevoerd:

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

De volgende sjabloon wordt koppelingen naar de voorgaande sjabloon. Het creëert drie openbare IP-adressen.

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

Na de implementatie u de uitvoerwaarden ophalen met het volgende PowerShell-script:

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
  deployment=$(az deployment group show -g examplegroup -n $name);
  ip=$(echo $deployment | jq .properties.outputs.returnedIPAddress.value);
  echo "deployment $name returned $ip";
done
```

## <a name="securing-an-external-template"></a>Een externe sjabloon beveiligen

Hoewel de gekoppelde sjabloon extern beschikbaar moet zijn, hoeft deze niet algemeen beschikbaar te zijn voor het publiek. U uw sjabloon toevoegen aan een privéopslagaccount dat alleen toegankelijk is voor de eigenaar van het opslagaccount. Vervolgens maakt u een SAS-token (Shared Access Signature) om toegang tijdens de implementatie in te schakelen. U voegt dat SAS-token toe aan de URI voor de gekoppelde sjabloon. Hoewel het token wordt doorgegeven als een beveiligde tekenreeks, wordt de URI van de gekoppelde sjabloon, inclusief het SAS-token, aangemeld bij de implementatiebewerkingen. Als u de blootstelling wilt beperken, stelt u een vervaldatum in voor het token.

Het parameterbestand kan ook worden beperkt tot toegang via een SAS-token.

Momenteel u geen koppeling maken naar een sjabloon in een opslagaccount dat zich achter een [Azure Storage-firewall bevindt.](../../storage/common/storage-network-security.md)

In het volgende voorbeeld ziet u hoe u een SAS-token doorgeeft bij het koppelen aan een sjabloon:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
  "containerSasToken": { "type": "securestring" }
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

In PowerShell krijgt u een token voor de container en implementeert u de sjablonen met de volgende opdrachten. De parameter **containerSasToken** wordt gedefinieerd in de sjabloon. Het is geen parameter in de opdracht **Nieuw-AzResourceGroupDeployment.**

```azurepowershell-interactive
Set-AzCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates
$token = New-AzStorageContainerSASToken -Name templates -Permission r -ExpiryTime (Get-Date).AddMinutes(30.0)
$url = (Get-AzStorageBlob -Container templates -Blob parent.json).ICloudBlob.uri.AbsoluteUri
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateUri ($url + $token) -containerSasToken $token
```

Voor Azure CLI in een Bash-shell krijgt u een token voor de container en implementeert u de sjablonen met de volgende code:

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
az deployment group create --resource-group ExampleGroup --template-uri $url?$token --parameters $parameter
```

## <a name="example-templates"></a>Voorbeeldsjablonen

In de volgende voorbeelden worden veelvoorkomende toepassingen van gekoppelde sjablonen weergegeven.

|Hoofdsjabloon  |Gekoppelde sjabloon |Beschrijving  |
|---------|---------| ---------|
|[Hallo mensen](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/helloworldparent.json) |[gekoppelde sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/helloworld.json) | Retourneert tekenreeks van gekoppelde sjabloon. |
|[Load Balancer met openbaar IP-adres](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) |[gekoppelde sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) |Retourneert het openbare IP-adres van gekoppelde sjabloon en stelt die waarde in de load balancer in. |
|[Meerdere IP-adressen](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/static-public-ip-parent.json) | [gekoppelde sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/static-public-ip.json) |Hiermee maakt u verschillende openbare IP-adressen in gekoppelde sjabloon.  |

## <a name="next-steps"></a>Volgende stappen

* Zie [Zelfstudie: gekoppelde Azure Resource Manager-sjablonen maken](template-tutorial-create-linked-templates.md)om een zelfstudie te doorlopen.
* Zie [Afhankelijkheden definiëren in Azure Resource Manager-sjablonen](define-resource-dependency.md)voor meer informatie over het definiëren van de implementatievolgorde voor uw resources.
* Zie Meerdere exemplaren van resources maken [in Azure Resource Manager](copy-resources.md)voor meer informatie over het definiëren van één resource, maar er veel exemplaren van maken.
* Zie Resources implementeren [met Resource Manager-sjablonen en Azure PowerShell-](deploy-powershell.md) of [Deploy-resources met Resource Manager-sjablonen voor](deploy-cli.md)stappen bij het instellen van een sjabloon in een opslagaccount en het genereren van een SAS-token.
