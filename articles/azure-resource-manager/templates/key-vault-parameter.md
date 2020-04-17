---
title: Key Vault-geheim met sjabloon
description: Laat zien hoe u een geheim van een sleutelkluis als parameter doorgeeft tijdens de implementatie.
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: d21a7d727091b427fee59e22db6a77a495a4eab7
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458263"
---
# <a name="use-azure-key-vault-to-pass-secure-parameter-value-during-deployment"></a>Azure Key Vault gebruiken om veilige parameterwaarde door te geven tijdens de implementatie

In plaats van een veilige waarde (zoals een wachtwoord) rechtstreeks in uw sjabloon of parameterbestand te plaatsen, u de waarde ophalen uit een [Azure Key Vault](../../key-vault/general/overview.md) tijdens een implementatie. U haalt de waarde op door te verwijzen naar de sleutelkluis en het geheim in uw parameterbestand. De waarde zelf wordt nooit getoond, omdat u alleen verwijst naar de sleutelkluis-id. De sleutelkluis kan bestaan in een ander abonnement dan de resourcegroep waarnaar u implementeert.

Dit artikel richt zich op het scenario van het doorgeven van een gevoelige waarde in als sjabloonparameter. Het heeft geen betrekking op het scenario van het instellen van een eigenschap van een virtuele machine op de URL van een certificaat in een sleutelkluis. Zie Een certificaat installeren vanuit [Azure Key Vault op een virtuele machine voor](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-winrm-keyvault-windows)een snelstartsjabloon van dat scenario.

## <a name="deploy-key-vaults-and-secrets"></a>Belangrijke kluizen en geheimen implementeren

Als u toegang wilt krijgen `enabledForTemplateDeployment` tot een sleutelkluis tijdens de implementatie van een sjabloon, stelt u de sleutelkluis in op `true`.

Als u al een Key Vault hebt, moet u ervoor zorgen dat er sjabloonimplementaties worden toegestaan.

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli-interactive
az keyvault update  --name ExampleVault --enabled-for-template-deployment true
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy -VaultName ExampleVault -EnabledForTemplateDeployment
```

---

Gebruik het volgende om een nieuwe Key Vault te maken en een geheim toe te voegen:

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli-interactive
az group create --name ExampleGroup --location centralus
az keyvault create \
  --name ExampleVault \
  --resource-group ExampleGroup \
  --location centralus \
  --enabled-for-template-deployment true
az keyvault secret set --vault-name ExampleVault --name "ExamplePassword" --value "hVFkk965BuUv"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name ExampleGroup -Location centralus
New-AzKeyVault `
  -VaultName ExampleVault `
  -resourceGroupName ExampleGroup `
  -Location centralus `
  -EnabledForTemplateDeployment
$secretvalue = ConvertTo-SecureString 'hVFkk965BuUv' -AsPlainText -Force
$secret = Set-AzKeyVaultSecret -VaultName ExampleVault -Name 'ExamplePassword' -SecretValue $secretvalue
```

---

Als eigenaar van de sleutelkluis heb je automatisch toegang tot het maken van geheimen. Als de gebruiker die met geheimen werkt niet de eigenaar van de sleutelkluis is, verleent u toegang met:

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli-interactive
az keyvault set-policy \
  --upn <user-principal-name> \
  --name ExampleVault \
  --secret-permissions set delete get list
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$userPrincipalName = "<Email Address of the deployment operator>"

Set-AzKeyVaultAccessPolicy `
  -VaultName ExampleVault `
  -UserPrincipalName <user-principal-name> `
  -PermissionsToSecrets set,delete,get,list
```

---

Zie voor meer informatie over het maken van sleutelkluizen en het toevoegen van geheimen:

- [Een geheim instellen en ophalen met CLI](../../key-vault/secrets/quick-create-cli.md)
- [Een geheim instellen en ophalen met Powershell](../../key-vault/secrets/quick-create-powershell.md)
- [Een geheim instellen en ophalen met behulp van de portal](../../key-vault/secrets/quick-create-portal.md)
- [Een geheim instellen en ophalen met .NET](../../key-vault/secrets/quick-create-net.md)
- [Een geheim instellen en ophalen met Node.js](../../key-vault/secrets/quick-create-node.md)

## <a name="grant-access-to-the-secrets"></a>Geef toegang tot de geheimen

De gebruiker die de sjabloon `Microsoft.KeyVault/vaults/deploy/action` implementeert, moet toestemming hebben voor het bereik van de resourcegroep en sleutelkluis. De rollen [Eigenaar](../../role-based-access-control/built-in-roles.md#owner) en [Medewerker](../../role-based-access-control/built-in-roles.md#contributor) verlenen beide deze toegang. Als u de sleutelkluis hebt gemaakt, bent u de eigenaar, zodat u de toestemming hebt.

In de volgende procedure ziet u hoe u een rol maakt met de minimale machtiging en hoe u de gebruiker toewijst

1. Een JSON-bestand voor aangepaste roldefinitie maken:

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
    Vervang "00000000-0000-0000-0000-000000000000000000" door de abonnements-ID.

2. Maak de nieuwe rol met het JSON-bestand:

    # <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

    ```azurecli-interactive
    az role definition create --role-definition "<path-to-role-file>"
    az role assignment create \
      --role "Key Vault resource manager template deployment operator" \
      --assignee <user-principal-name> \
      --resource-group ExampleGroup
    ```

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

    ```azurepowershell-interactive
    New-AzRoleDefinition -InputFile "<path-to-role-file>"
    New-AzRoleAssignment `
      -ResourceGroupName ExampleGroup `
      -RoleDefinitionName "Key Vault resource manager template deployment operator" `
      -SignInName <user-principal-name>
    ```

    ---

    De voorbeelden wijzen de aangepaste rol toe aan de gebruiker op het niveau van de resourcegroep.

Wanneer u een sleutelkluis gebruikt met de sjabloon voor een [beheerde toepassing,](../managed-applications/overview.md)moet u toegang verlenen tot de serviceprincipal **van de Toestelbronprovider.** Zie Access Key Vault geheim voor meer informatie [bij het implementeren van Azure Managed Applications.](../managed-applications/key-vault-access.md)

## <a name="reference-secrets-with-static-id"></a>Referentiegeheimen met statische ID

Met deze benadering verwijst u naar de sleutelkluis in het parameterbestand, niet naar de sjabloon. In de volgende afbeelding ziet u hoe het parameterbestand naar het geheim verwijst en deze waarde doorgeeft aan de sjabloon.

![Statische ID-diagram voor resourcebeheer-sleutelkluis](./media/key-vault-parameter/statickeyvault.png)

[Zelfstudie: Integreer Azure Key Vault in Resource Manager Template deployment](./template-tutorial-use-key-vault.md) uses this method.

In de volgende sjabloon wordt een SQL-server geïmplementeerd die een beheerderswachtwoord bevat. De parameter wachtwoord is ingesteld op een beveiligde tekenreeks. De sjabloon geeft echter niet aan waar die waarde vandaan komt.

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
      "type": "Microsoft.Sql/servers",
      "apiVersion": "2015-05-01-preview",
      "name": "[parameters('sqlServerName')]",
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

Maak nu een parameterbestand voor de voorgaande sjabloon. Geef in het parameterbestand een parameter op die overeenkomt met de naam van de parameter in de sjabloon. Raadpleeg voor de parameterwaarde het geheim van de sleutelkluis. U verwijst naar het geheim door de bron-id van de sleutelkluis en de naam van het geheim door te geven:

In het volgende parameterbestand moet het sleutelkluisgeheim al bestaan en geeft u een statische waarde voor de bron-ID.

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

Als u een andere versie van het geheim dan `secretVersion` de huidige versie moet gebruiken, gebruikt u de eigenschap.

```json
"secretName": "ExamplePassword",
"secretVersion": "cd91b2b7e10e492ebb870a6ee0591b68"
```

Implementeer de sjabloon en geef de pas in het parameterbestand:

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli-interactive
az group create --name SqlGroup --location westus2
az deployment group create \
  --resource-group SqlGroup \
  --template-uri <template-file-URI> \
  --parameters <parameter-file>
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
  -ResourceGroupName $resourceGroupName `
  -TemplateUri <template-file-URI> `
  -TemplateParameterFile <parameter-file>
```

---

## <a name="reference-secrets-with-dynamic-id"></a>Referentiegeheimen met dynamische ID

In het vorige gedeelte werd laten zien hoe u een statische resource-ID voor het sleutelkluisgeheim van de parameter doorgeven. In sommige scenario's moet u echter verwijzen naar een sleutelkluisgeheim dat varieert op basis van de huidige implementatie. U ook parameterwaarden doorgeven aan de sjabloon in plaats van een referentieparameter in het parameterbestand te maken. In beide gevallen u de bron-ID voor een sleutelkluisgeheim dynamisch genereren met behulp van een gekoppelde sjabloon.

U de bron-id in het parametersbestand niet dynamisch genereren omdat sjabloonexpressies niet zijn toegestaan in het parametersbestand.

In de bovenliggende sjabloon voegt u de geneste sjabloon toe en geeft u een parameter door die de dynamisch gegenereerde bron-ID bevat. In de volgende afbeelding ziet u hoe een parameter in de gekoppelde sjabloon naar het geheim verwijst.

![Dynamische id](./media/key-vault-parameter/dynamickeyvault.png)

Met de volgende sjabloon wordt de sleutelkluis-ID dynamisch gemaakt en wordt deze als parameter doorgegeven.

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

## <a name="next-steps"></a>Volgende stappen

- Zie [Wat is Azure Key Vault](../../key-vault/general/overview.md)voor algemene informatie over sleutelkluizen?
- Zie Voorbeelden van [sleutelkluizen](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples)voor volledige voorbeelden van het verwijzen naar belangrijke geheimen.
