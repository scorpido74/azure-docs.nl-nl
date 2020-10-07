---
title: 'Azure-hand leiding: een Azure-sleutel kluis en een beleid voor kluis toegang maken met behulp van Azure Resource Manager sjabloon | Microsoft Docs'
description: Laat zien hoe u Azure-sleutel kluizen en kluis toegangs beleid maakt met behulp van Azure Resource Manager sjabloon.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 10/5/2020
ms.author: mbaldwin
ms.openlocfilehash: cf19561005fe2e98b7b5cf6812ff9224fd9474dc
ms.sourcegitcommit: 23aa0cf152b8f04a294c3fca56f7ae3ba562d272
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/07/2020
ms.locfileid: "91804338"
---
# <a name="how-to-create-azure-key-vault-and-vault-access-policy-using-a-resource-manager-template"></a>Azure Key Vault-en kluis toegangs beleid maken met behulp van een resource manager-sjabloon

[Azure Key Vault](../general/overview.md) is een cloudservice die een veilig archief biedt voor geheimen, zoals sleutels, wachtwoorden, certificaten en andere geheimen. Deze hand leiding is gericht op het proces van het implementeren van een Azure Resource Manager sjabloon (ARM-sjabloon) om een sleutel kluis te maken.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van dit artikel, is het volgende vereist:

* Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.


## <a name="create-key-vault-resource-manager-template"></a>Key Vault Resource Manager-sjabloon maken

In de volgende sjabloon ziet u een eenvoudige manier om een sleutel kluis te maken. Sommige waarden worden opgegeven in de sjabloon.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "keyVaultName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the key vault."
      }
    },
    "skuName": {
      "type": "string",
      "defaultValue": "Standard",
      "allowedValues": [
        "Standard",
        "Premium"
      ],
      "metadata": {
        "description": "Specifies whether the key vault is a standard vault or a premium vault."
      }
    }
   },
  "resources": [
    {
      "type": "Microsoft.KeyVault/vaults",
      "apiVersion": "2019-09-01",
      "name": "[parameters('keyVaultName')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "enabledForDeployment": "false",
        "enabledForDiskEncryption": "false",
        "enabledForTemplateDeployment": "false",
        "tenantId": "[subscription().tenantId]",
        "accessPolicies": [],
        "sku": {
          "name": "[parameters('skuName')]",
          "family": "A"
        },
        "networkAcls": {
          "defaultAction": "Allow",
          "bypass": "AzureServices"
        }
      }
    }
  ]
}

```

Zie voor meer informatie over Key Vault-sjabloon instellingen [Key Vault arm-sjabloon verwijzing](https://docs.microsoft.com/azure/templates/microsoft.keyvault/vaults).

> [!IMPORTANT]
> Als de sjabloon opnieuw wordt geïmplementeerd, wordt het bestaande toegangs beleid in de sleutel kluis overschreven. Het is raadzaam om `accessPolicies` eigenschap in te vullen met een bestaand toegangs beleid om verbroken toegang tot de sleutel kluis te voor komen. 

## <a name="add-access-policy-to-key-vault-resource-manager-template"></a>Toegangs beleid toevoegen aan Key Vault Resource Manager-sjabloon

U kunt toegangs beleid implementeren voor een bestaande sleutel kluis zonder de volledige sleutel kluis sjabloon opnieuw te implementeren. In de volgende sjabloon ziet u een eenvoudige manier om toegangs beleid te maken.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "keyVaultName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the key vault."
      }
    },
    "objectId": {
      "type": "string",
      "metadata": {
        "description": "Specifies the object ID of a user, service principal or security group in the Azure Active Directory tenant for the vault. The object ID must be unique for the list of access policies. Get it by using Get-AzADUser or Get-AzADServicePrincipal cmdlets."
      }
    },
    "keysPermissions": {
      "type": "array",
      "defaultValue": [
        "list"
      ],
      "metadata": {
        "description": "Specifies the permissions to keys in the vault. Valid values are: all, encrypt, decrypt, wrapKey, unwrapKey, sign, verify, get, list, create, update, import, delete, backup, restore, recover, and purge."
      }
    },
    "secretsPermissions": {
      "type": "array",
      "defaultValue": [
        "list"
      ],
      "metadata": {
        "description": "Specifies the permissions to secrets in the vault. Valid values are: all, get, list, set, delete, backup, restore, recover, and purge."
      }
    },
    "certificatePermissions": {
      "type": "array",
      "defaultValue": [
        "list"
      ],
      "metadata": {
        "description": "Specifies the permissions to certificates in the vault. Valid values are: all,  create, delete, update, deleteissuers, get, getissuers, import, list, listissuers, managecontacts, manageissuers,  recover, backup, restore, setissuers, and purge."
      }
    },
  "resources": [
     {
      "type": "Microsoft.KeyVault/vaults/accessPolicies",
      "name": "[concat(parameters('keyVaultName'), '/add')]",
      "apiVersion": "2019-09-01",
      "properties": {
        "accessPolicies": [
          {
            "tenantId": "[subscription().tenantId]",
            "objectId": "[parameters('objectId')]",
            "permissions": {
              "keys": "[parameters('keysPermissions')]",
              "secrets": "[parameters('secretsPermissions')]",
              "certificates": [parameters('certificatesPermissions')]
            }
          }
        ]
      }
    }
  ]
}

```
Zie voor meer informatie over Key Vault-sjabloon instellingen [Key Vault arm-sjabloon verwijzing](https://docs.microsoft.com/azure/templates/microsoft.keyvault/vaults/accesspolicies).

## <a name="other-available-key-vault-resource-manager-templates"></a>Andere beschik bare Key Vault Resource Manager-sjablonen

Er zijn andere Resource Manager-sjablonen beschikbaar voor Key Vault objecten:

| Geheimen | Sleutels | Certificaten |
|--|--|--|
|[Snelstartgids](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-template)<br>[Verwijzing](https://docs.microsoft.com/azure/templates/microsoft.keyvault/vaults/secrets)|N.v.t.|N.v.t.|

Meer Key Vault sjablonen die u hier kunt vinden: [Naslag informatie over Key Vault Resource Manager](https://docs.microsoft.com/azure/templates/microsoft.keyvault/allversions)

## <a name="deploy-the-templates"></a>De sjablonen implementeren

U kunt de Azure Portal gebruiken om bovenstaande sjablonen te implementeren met behulp van de optie uw eigen sjabloon in de editor bouwen in de onderstaande gids: [resources implementeren vanuit een aangepaste sjabloon](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-portal#deploy-resources-from-custom-template)

U kunt bovenstaande sjablonen ook opslaan in bestanden en de volgende opdrachten gebruiken:  [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) en [AZ Group Deployment Create](/cli/azure/group/deployment#az-group-deployment-create):

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile key-vault-template.json
```

```azurecli
az group deployment create --resource-group ExampleGroup --template-file key-vault-template.json
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u van plan bent om door te gaan en verder te werken met de volgende Quick starts en zelf studies, kunt u deze resources blijven gebruiken. Wanneer de resources niet meer nodig zijn, verwijdert u de resource groep, waarmee de sleutel kluis en gerelateerde resources worden verwijderd. Gebruik de volgende stappen om de resource groep te verwijderen met behulp van Azure CLI of Azure PowerShell.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="resources"></a>Resources

- Lees een [Overzicht van Azure Key Vault](../general/overview.md)
- Meer informatie over [Azure Resource Manager](../../azure-resource-manager/management/overview.md)
- Bekijk de [best practices voor Azure Key Vault](../general/best-practices.md)

## <a name="next-steps"></a>Volgende stappen

- [Veilige toegang tot een sleutel kluis](secure-your-key-vault.md)
- [Verifiëren bij een sleutel kluis](authentication.md)
- [Ontwikkelaars handleiding Azure Key Vault](developers-guide.md)
