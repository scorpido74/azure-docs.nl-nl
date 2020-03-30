---
title: Key Vault gebruiken bij het implementeren van beheerde app
description: Laat zien hoe u toegangsgeheimen gebruiken in Azure Key Vault bij het implementeren van beheerde toepassingen
author: tfitzmac
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: tomfitz
ms.openlocfilehash: f434ad6e19c89f248fec948c0a049fabb0f7c476
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248435"
---
# <a name="access-key-vault-secret-when-deploying-azure-managed-applications"></a>Access Key Vault geheim bij het implementeren van Azure Managed Applications

Wanneer u tijdens de implementatie een veilige waarde (zoals een wachtwoord) als parameter moet doorgeven, u de waarde ophalen uit een [Azure Key Vault.](../../key-vault/key-vault-overview.md) Als u toegang wilt krijgen tot de Key Vault bij het implementeren van beheerde toepassingen, moet u toegang verlenen tot de serviceprincipal **van de Toestelbronprovider.** De service Beheerde toepassingen gebruikt deze identiteit om bewerkingen uit te voeren. Als u tijdens de implementatie een waarde uit een sleutelkluis wilt ophalen, moet de serviceprincipal toegang hebben tot de Key Vault.

In dit artikel wordt beschreven hoe u de Sleutelkluis configureert om te werken met beheerde toepassingen.

## <a name="enable-template-deployment"></a>Sjabloonimplementatie inschakelen

1. Selecteer in de portal uw Key Vault.

1. Selecteer **Toegangsbeleid**.   

   ![Toegangsbeleid selecteren](./media/key-vault-access/select-access-policies.png)

1. Selecteer **Klik hierop om geavanceerde beleidsregels weer te geven**.

   ![Geavanceerd toegangsbeleid weergeven](./media/key-vault-access/advanced.png)

1. Selecteer **Toegang tot Azure Resource Manager inschakelen voor sjabloonimplementatie**. Selecteer vervolgens **Opslaan**.

   ![Sjabloonimplementatie inschakelen](./media/key-vault-access/enable-template.png)

## <a name="add-service-as-contributor"></a>Service toevoegen als bijdrager

1. Selecteer **Toegangsbesturingselement (IAM)**.

   ![Toegangsbeheer selecteren](./media/key-vault-access/access-control.png)

1. Selecteer **Roltoewijzing toevoegen**.

   ![Toevoegen selecteren](./media/key-vault-access/add-access-control.png)

1. Selecteer **Bijdrager** voor de rol. Zoek naar **toestelresourceprovider** en selecteer deze in de beschikbare opties.

   ![Zoeken naar provider](./media/key-vault-access/search-provider.png)

1. Selecteer **Opslaan**.

## <a name="reference-key-vault-secret"></a>Referentiesleutelkluisgeheim

Als u een geheim van een sleutelkluis wilt doorgeven aan een sjabloon in uw beheerde toepassing, moet u een [gekoppelde of geneste sjabloon](../templates/linked-templates.md) gebruiken en verwijzen naar de Sleutelkluis in de parameters voor de gekoppelde of geneste sjabloon. Geef de bron-ID van de Key Vault en de naam van het geheim.

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

U hebt uw Key Vault zo geconfigureerd dat deze toegankelijk is tijdens de implementatie van een beheerde toepassing.

* Zie Azure Key Vault gebruiken om de waarde [van de beveiligde parameter tijdens de implementatie door te geven](../templates/key-vault-parameter.md)voor informatie over het doorgeven van een waarde uit een sleutelkluis als sjabloonparameter.
* Zie [Voorbeeldprojecten voor beheerde toepassingen voor](sample-projects.md)beheerde toepassingen voor beheerde toepassingen .
* Zie [Aan de slag met CreateUiDefinition](create-uidefinition-overview.md) voor meer informatie over het maken van een UI-definitiebestand voor een beheerde toepassing.
