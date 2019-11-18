---
title: Geheim Key Vault met sjabloon
description: Laat zien hoe u tijdens de implementatie een geheim kunt door geven van een sleutel kluis als een para meter.
ms.topic: conceptual
ms.date: 05/09/2019
ms.openlocfilehash: 37d21e295eca2b40e91f92d65d6e927ee6857d0e
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2019
ms.locfileid: "74149488"
---
# <a name="use-azure-key-vault-to-pass-secure-parameter-value-during-deployment"></a>Azure Key Vault gebruiken om een beveiligde parameter waarde door te geven tijdens de implementatie

In plaats van een beveiligde waarde (zoals een wacht woord) rechtstreeks in uw sjabloon of parameter bestand te plaatsen, kunt u tijdens een implementatie de waarde van een [Azure Key Vault](../key-vault/key-vault-overview.md) ophalen. U haalt de waarde op door te verwijzen naar de sleutel kluis en het geheim in het parameter bestand. De waarde zelf wordt nooit getoond, omdat u alleen verwijst naar de sleutelkluis-id. De sleutel kluis kan zich in een ander abonnement bevinden dan de resource groep die u implementeert.

## <a name="deploy-key-vaults-and-secrets"></a>Sleutel kluizen en geheimen implementeren

Als u toegang wilt krijgen tot een sleutel kluis tijdens het implementeren van de sjabloon, stelt u `enabledForTemplateDeployment` op de sleutel kluis in op `true`.

De volgende Azure CLI-en Azure PowerShell-voor beelden laten zien hoe u de sleutel kluis maakt en een geheim toevoegt.

```azurecli
az group create --name $resourceGroupName --location $location
az keyvault create \
  --name $keyVaultName \
  --resource-group $resourceGroupName \
  --location $location \
  --enabled-for-template-deployment true
az keyvault secret set --vault-name $keyVaultName --name "ExamplePassword" --value "hVFkk965BuUv"
```

```azurepowershell
New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzKeyVault `
  -VaultName $keyVaultName `
  -resourceGroupName $resourceGroupName `
  -Location $location `
  -EnabledForTemplateDeployment
$secretvalue = ConvertTo-SecureString 'hVFkk965BuUv' -AsPlainText -Force
$secret = Set-AzKeyVaultSecret -VaultName $keyVaultName -Name 'ExamplePassword' -SecretValue $secretvalue
```

Als eigenaar van de sleutel kluis hebt u automatisch toegang tot het maken van geheimen. Als de gebruiker werkt met geheimen niet de eigenaar van de sleutel kluis, verleent u toegang met:

```azurecli
az keyvault set-policy \
  --upn $userPrincipalName \
  --name $keyVaultName \
  --secret-permissions set delete get list
```

```azurepowershell
$userPrincipalName = "<Email Address of the deployment operator>"

Set-AzKeyVaultAccessPolicy `
  -VaultName $keyVaultName `
  -UserPrincipalName $userPrincipalName `
  -PermissionsToSecrets set,delete,get,list
```

Zie voor meer informatie over het maken van sleutel kluizen en het toevoegen van geheimen:

- [Een geheim instellen en ophalen met behulp van CLI](../key-vault/quick-create-cli.md)
- [Een geheim instellen en ophalen met behulp van Power shell](../key-vault/quick-create-powershell.md)
- [Een geheim instellen en ophalen met behulp van de portal](../key-vault/quick-create-portal.md)
- [Een geheim instellen en ophalen met behulp van .NET](../key-vault/quick-create-net.md)
- [Een geheim instellen en ophalen met behulp van node. js](../key-vault/quick-create-node.md)

## <a name="grant-access-to-the-secrets"></a>Toegang verlenen tot de geheimen

De gebruiker die de sjabloon implementeert, moet over de machtiging `Microsoft.KeyVault/vaults/deploy/action` beschikken voor het bereik van de resource groep en de sleutel kluis. De rollen [eigenaar](../role-based-access-control/built-in-roles.md#owner) en [Inzender](../role-based-access-control/built-in-roles.md#contributor) verlenen deze toegang. Als u de sleutel kluis hebt gemaakt, bent u de eigenaar, zodat u over de juiste machtigingen beschikt.

De volgende procedure laat zien hoe u een rol met de minimale machtiging maakt en hoe u de gebruiker toewijst

1. Een JSON-bestand met een aangepaste roldefinitie maken:

    ```json
    {
      "Name": "Key Vault resource manager template deployment operator",
      "IsCustom": true,
      "Description": "Lets you deploy a resource manager template with the access to the secrets in the Key Vault.",
      "Actions": [
        "Microsoft.KeyVault/vaults/deploy/action"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/00000000-0000-0000-0000-000000000000"
      ]
    }
    ```
    Vervang "00000000-0000-0000-0000-000000000000" door de abonnements-ID.

2. Maak de nieuwe rol met behulp van het JSON-bestand:

    ```azurecli
    az role definition create --role-definition "<PathToRoleFile>"
    az role assignment create \
      --role "Key Vault resource manager template deployment operator" \
      --assignee $userPrincipalName \
      --resource-group $resourceGroupName
    ```

    ```azurepowershell
    New-AzRoleDefinition -InputFile "<PathToRoleFile>" 
    New-AzRoleAssignment `
      -ResourceGroupName $resourceGroupName `
      -RoleDefinitionName "Key Vault resource manager template deployment operator" `
      -SignInName $userPrincipalName
    ```

    De voor beelden wijzen de aangepaste rol toe aan de gebruiker op het niveau van de resource groep.  

Wanneer u een Key Vault gebruikt met de sjabloon voor een [beheerde toepassing](../managed-applications/overview.md), moet u toegang verlenen tot de service-principal van het **toestel bron provider** . Zie [toegang Key Vault geheim bij het implementeren van Azure Managed Applications](../managed-applications/key-vault-access.md)voor meer informatie.

## <a name="reference-secrets-with-static-id"></a>Referentie geheimen met een statische ID

Met deze methode verwijst u naar de sleutel kluis in het parameter bestand, niet de sjabloon. De volgende afbeelding laat zien hoe het parameter bestand verwijst naar het geheim en dat deze waarde wordt door gegeven aan de sjabloon.

![Diagram van de statische ID van de Resource Manager-sleutel kluis integratie](./media/resource-manager-keyvault-parameter/statickeyvault.png)

[Zelf studie: Azure Key Vault integreren in Resource Manager Sjabloonimlementatie](./resource-manager-tutorial-use-key-vault.md) gebruikt deze methode.

Met de volgende sjabloon wordt een SQL-Server geïmplementeerd met een beheerders wachtwoord. De wachtwoord parameter is ingesteld op een veilige teken reeks. Maar de sjabloon geeft niet op waar de waarde vandaan komt.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "adminLogin": {
      "type": "string"
    },
    "adminPassword": {
      "type": "securestring"
    },
    "sqlServerName": {
      "type": "string"
    }
  },
  "resources": [
    {
      "name": "[parameters('sqlServerName')]",
      "type": "Microsoft.Sql/servers",
      "apiVersion": "2015-05-01-preview",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
        "administratorLogin": "[parameters('adminLogin')]",
        "administratorLoginPassword": "[parameters('adminPassword')]",
        "version": "12.0"
      }
    }
  ],
  "outputs": {
  }
}
```

Maak nu een parameter bestand voor de voor gaande sjabloon. Geef in het parameter bestand een para meter op die overeenkomt met de naam van de para meter in de sjabloon. Voor de parameter waarde verwijst u naar het geheim van de sleutel kluis. U verwijst naar het geheim door de resource-id van de sleutel kluis en de naam van het geheim door te geven:

In het volgende parameter bestand moet het sleutel kluis geheim al bestaan en kunt u een statische waarde voor de resource-ID opgeven.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "adminLogin": {
            "value": "exampleadmin"
        },
        "adminPassword": {
            "reference": {
              "keyVault": {
                "id": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.KeyVault/vaults/<vault-name>"
              },
              "secretName": "ExamplePassword"
            }
        },
        "sqlServerName": {
            "value": "<your-server-name>"
        }
    }
}
```

Als u een andere versie van het geheim wilt gebruiken dan de huidige versie, gebruikt u de eigenschap `secretVersion`.

```json
"secretName": "ExamplePassword",
"secretVersion": "cd91b2b7e10e492ebb870a6ee0591b68"
```

Implementeer de sjabloon en geef deze door in het parameter bestand:

Gebruik voor Azure CLI:

```azurecli
az group create --name $resourceGroupName --location $location
az group deployment create \
    --resource-group $resourceGroupName \
    --template-uri <The Template File URI> \
    --parameters <The Parameter File>
```

Gebruik voor PowerShell:

```powershell
New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
  -ResourceGroupName $resourceGroupName `
  -TemplateUri <The Template File URI> `
  -TemplateParameterFile <The Parameter File>
```

## <a name="reference-secrets-with-dynamic-id"></a>Referentie geheimen met een dynamische ID

In de vorige sectie is geleerd hoe u een statische Resource-ID voor het sleutel kluis geheim kunt door geven uit de para meter. In sommige scenario's moet u echter verwijzen naar een sleutel kluis geheim dat afhankelijk is van de huidige implementatie. Of u wilt parameter waarden door geven aan de sjabloon in plaats van een verwijzings parameter in het parameter bestand te maken. In beide gevallen kunt u de resource-ID voor een sleutel kluis geheim dynamisch genereren met behulp van een gekoppelde sjabloon.

U kunt de resource-ID in het parameter bestand niet dynamisch genereren omdat sjabloon expressies niet zijn toegestaan in het parameter bestand. 

In de bovenliggende sjabloon voegt u de gekoppelde sjabloon toe en geeft u een door gegeven in een para meter die de dynamisch gegenereerde Resource-ID bevat. In de volgende afbeelding ziet u hoe een para meter in de gekoppelde sjabloon verwijst naar het geheim.

![Dynamische ID](./media/resource-manager-keyvault-parameter/dynamickeyvault.png)

Met de [volgende sjabloon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-key-vault-use-dynamic-id) wordt de sleutel kluis-id dynamisch gemaakt en door gegeven als para meter.

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
        },
        "_artifactsLocation": {
            "type": "string",
            "metadata": {
                "description": "The base URI where artifacts required by this template are located. When the template is deployed using the accompanying scripts, a private location in the subscription will be used and this value will be automatically generated."
            },
            "defaultValue": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-key-vault-use-dynamic-id/"
        },
        "_artifactsLocationSasToken": {
            "type": "securestring",
            "metadata": {
                "description": "The sasToken required to access _artifactsLocation.  When the template is deployed using the accompanying scripts, a sasToken will be automatically generated."
            },
            "defaultValue": ""
        }
    },
    "resources": [
        {
            "apiVersion": "2018-05-01",
            "name": "dynamicSecret",
            "type": "Microsoft.Resources/deployments",
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                    "contentVersion": "1.0.0.0",
                    "uri": "[uri(parameters('_artifactsLocation'), concat('./nested/sqlserver.json', parameters('_artifactsLocationSasToken')))]"
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
        "sqlFQDN": {
            "type": "string",
            "value": "[reference('dynamicSecret').outputs.sqlFQDN.value]"
        }
    }
}
```

Implementeer de voor gaande sjabloon en geef waarden op voor de para meters. U kunt de voorbeeld sjabloon van GitHub gebruiken, maar u moet parameter waarden opgeven voor uw omgeving.

Gebruik voor Azure CLI:

```azurecli
az group create --name $resourceGroupName --location $location
az group deployment create \
    --resource-group $resourceGroupName \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-key-vault-use-dynamic-id/azuredeploy.json \
    --parameters vaultName=$keyVaultName vaultResourceGroupName=examplegroup secretName=examplesecret
```

Gebruik voor PowerShell:

```powershell
New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
  -ResourceGroupName $resourceGroupName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-key-vault-use-dynamic-id/azuredeploy.json `
  -vaultName $keyVaultName -vaultResourceGroupName $keyVaultResourceGroupName -secretName $secretName
```

## <a name="next-steps"></a>Volgende stappen

- Zie [Wat is Azure Key Vault?](../key-vault/key-vault-overview.md)voor algemene informatie over sleutel kluizen.
- Zie [Key Vault-voor beelden](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples)voor volledige voor beelden van het verwijzen naar sleutel geheimen.
