---
title: Gegevensversleuteling - Azure CLI - voor Azure Database voor PostgreSQL - Enkele server
description: Meer informatie over het instellen en beheren van gegevensversleuteling voor uw Azure Database voor PostgreSQL Single-server met behulp van de Azure CLI.
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/30/2020
ms.openlocfilehash: fcdd7c13c9e0a5f9e858309bea50bb0264b7b301
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81460678"
---
# <a name="data-encryption-for-azure-database-for-postgresql-single-server-by-using-the-azure-cli"></a>Gegevensversleuteling voor Azure Database voor PostgreSQL Single-server met behulp van de Azure CLI

Meer informatie over het gebruik van de Azure CLI voor het instellen en beheren van gegevensversleuteling voor uw Azure Database voor PostgreSQL Single server.

## <a name="prerequisites-for-azure-cli"></a>Voorwaarden voor Azure CLI

* U moet een Azure-abonnement hebben en een beheerder zijn bij dat abonnement.
* Maak een sleutelkluis en een sleutel om te gebruiken voor een door de klant beheerde sleutel. Ook maken zuivering bescherming en zachte verwijderen op de sleutel kluis.

    ```azurecli-interactive
    az keyvault create -g <resource_group> -n <vault_name> --enable-soft-delete true --enable-purge-protection true
    ```

* Maak in de gemaakte Azure Key Vault de sleutel die wordt gebruikt voor de gegevensversleuteling van de Azure Database voor PostgreSQL Single-server.

    ```azurecli-interactive
    az keyvault key create --name <key_name> -p software --vault-name <vault_name>
    ```

* Om een bestaande sleutelkluis te kunnen gebruiken, moet deze de volgende eigenschappen hebben om te gebruiken als een door de klant beheerde sleutel:
  * [Zachte verwijderen](../key-vault/general/overview-soft-delete.md)

    ```azurecli-interactive
    az resource update --id $(az keyvault show --name \ <key_vault_name> -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
    ```

  * [Gezuiverd beschermd](../key-vault/general/overview-soft-delete.md#purge-protection)

    ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --enable-purge-protection true
    ```

* De sleutel moet de volgende kenmerken hebben om te gebruiken als een door de klant beheerde sleutel:
  * Geen vervaldatum
  * Niet uitgeschakeld
  * Bewerkingen **voor het uitvoeren van get,** **wrap** en **uitpakken**

## <a name="set-the-right-permissions-for-key-operations"></a>De juiste machtigingen instellen voor belangrijke bewerkingen

1. Er zijn twee manieren om de beheerde identiteit voor uw Azure Database voor PostgreSQL Single-server te verkrijgen.

    ### <a name="create-an-new-azure-database-for-mysql-server-with-a-managed-identity"></a>Maak een nieuwe Azure Database voor MySQL-server met een beheerde identiteit.

    ```azurecli-interactive
    az postgres server create --name -g <resource_group> --location <locations> --storage-size <size>  -u <user>-p <pwd> --backup-retention <7> --sku-name <sku name> --geo-redundant-backup <Enabled/Disabled>  --assign-identity
    ```

    ### <a name="update-an-existing-the-azure-database-for-mysql-server-to-get-a-managed-identity"></a>Een bestaande Azure-database voor MySQL-server bijwerken om een beheerde identiteit te krijgen.

    ```azurecli-interactive
    az postgres server update –name <server name>  -g <resoure_group> --assign-identity
    ```

2. Stel de **sleutelmachtigingen** in (**Get**, **Wrap**, **Uitpakken**) voor de **principal**, de naam van de PostgreSQL Single-serverserver.

    ```azurecli-interactive
    az keyvault set-policy --name -g <resource_group> --key-permissions get unwrapKey wrapKey --object-id <principal id of the server>
    ```

## <a name="set-data-encryption-for-azure-database-for-postgresql-single-server"></a>Gegevensversleuteling instellen voor Azure Database voor PostgreSQL Single-server

1. Gegevensversleuteling inschakelen voor de Azure Database voor PostgreSQL Single-server met behulp van de sleutel die is gemaakt in de Azure Key Vault.

    ```azurecli-interactive
    az postgres server key create –name  <server name>  -g <resource_group> --kid <key url>
    ```

    Belangrijkste url:https://YourVaultName.vault.azure.net/keys/YourKeyName/01234567890123456789012345678901>

## <a name="using-data-encryption-for-restore-or-replica-servers"></a>Gegevensversleuteling gebruiken voor herstel- of replicaservers

Nadat Azure Database voor PostgreSQL Single-server is versleuteld met de beheerde sleutel van een klant die is opgeslagen in Key Vault, wordt ook elke nieuw gemaakte kopie van de server versleuteld. U deze nieuwe kopie maken via een lokale of geoherstelbewerking of via een bewerking voor replica's (lokaal/regio). Voor een versleutelde PostgreSQL Single-serverserver u dus de volgende stappen gebruiken om een versleutelde herstelde server te maken.

### <a name="creating-a-restoredreplica-server"></a>Een herstelde/replicaserver maken

  *  [Een herstelserver maken](howto-restore-server-cli.md) 
  *  [Een gelezen replicaserver maken](howto-read-replicas-cli.md) 

### <a name="once-the-server-is-restored-revalidate-data-encryption-the-restored-server"></a>Zodra de server is hersteld, moet de herstelde server de herstelde server opnieuw valideren

    ```azurecli-interactive
    az postgres server key create –name  <server name> -g <resource_group> --kid <key url>
    ```

## <a name="additional-capability-for-the-key-being-used-for-the-azure-database-for-postgresql-single-server"></a>Extra mogelijkheden voor de sleutel die wordt gebruikt voor de Azure Database voor PostgreSQL Single-server

### <a name="get-the-key-used"></a>Gebruik de sleutel

    ```azurecli-interactive
    az mysql server key show --name  <server name>  -g <resource_group> --kid <key url>
    ```

    Key url:  https://YourVaultName.vault.azure.net/keys/YourKeyName/01234567890123456789012345678901>

### <a name="list-the-key-used"></a>De gebruikte sleutel weergeven

    ```azurecli-interactive
    az postgres server key list --name  <server name>  -g <resource_group>
    ```

### <a name="drop-the-key-being-used"></a>Laat de sleutel vallen die wordt gebruikt

    ```azurecli-interactive
    az postgres server key delete -g <resource_group> --kid <key url> 
    ```
## <a name="using-an-azure-resource-manager-template-to-enable-data-encryption"></a>Een Azure Resource Manager-sjabloon gebruiken om gegevensversleuteling in te schakelen

Naast azure-portal u ook gegevensversleuteling inschakelen op uw Azure Database voor PostgreSQL-enkele server met Azure Resource Manager-sjablonen voor nieuwe en bestaande server.

### <a name="for-a-new-server"></a>Voor een nieuwe server

Gebruik een van de vooraf gemaakte Azure Resource Manager-sjablonen om de server te voorzien van gegevensversleuteling ingeschakeld: [Voorbeeld met gegevensversleuteling](https://github.com/Azure/azure-postgresql/tree/master/arm-templates/ExampleWithDataEncryption)

Met deze sjabloon Azure Resource Manager wordt een Azure Database gemaakt voor PostgreSQL Single server en wordt de **KeyVault** en **Key** gebruikt als parameters om gegevensversleuteling op de server in te schakelen.

### <a name="for-an-existing-server"></a>Voor een bestaande server
Bovendien u Azure Resource Manager-sjablonen gebruiken om gegevensversleuteling in te schakelen op uw bestaande Azure Database voor PostgreSQL Single-servers.

* Geef de resource-id door van de Azure Key `Uri` Vault-sleutel die u eerder hebt gekopieerd onder de eigenschap in het eigenschappenobject.

* Gebruik *2020-01-01-preview* als API-versie.

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string"
    },
    "serverName": {
      "type": "string"
    },
    "keyVaultName": {
      "type": "string",
      "metadata": {
        "description": "Key vault name where the key to use is stored"
      }
    },
    "keyVaultResourceGroupName": {
      "type": "string",
      "metadata": {
        "description": "Key vault resource group name where it is stored"
      }
    },
    "keyName": {
      "type": "string",
      "metadata": {
        "description": "Key name in the key vault to use as encryption protector"
      }
    },
    "keyVersion": {
      "type": "string",
      "metadata": {
        "description": "Version of the key in the key vault to use as encryption protector"
      }
    }
  },
  "variables": {
    "serverKeyName": "[concat(parameters('keyVaultName'), '_', parameters('keyName'), '_', parameters('keyVersion'))]"
  },
  "resources": [
    {
      "type": "Microsoft.DBforPostgreSQL/servers",
      "apiVersion": "2017-12-01",
      "kind": "",
      "location": "[parameters('location')]",
      "identity": {
        "type": "SystemAssigned"
      },
      "name": "[parameters('serverName')]",
      "properties": {
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2019-05-01",
      "name": "addAccessPolicy",
      "resourceGroup": "[parameters('keyVaultResourceGroupName')]",
      "dependsOn": [
        "[resourceId('Microsoft.DBforPostgreSQL/servers', parameters('serverName'))]"
      ],
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": [
            {
              "type": "Microsoft.KeyVault/vaults/accessPolicies",
              "name": "[concat(parameters('keyVaultName'), '/add')]",
              "apiVersion": "2018-02-14-preview",
              "properties": {
                "accessPolicies": [
                  {
                    "tenantId": "[subscription().tenantId]",
                    "objectId": "[reference(resourceId('Microsoft.DBforPostgreSQL/servers/', parameters('serverName')), '2017-12-01', 'Full').identity.principalId]",
                    "permissions": {
                      "keys": [
                        "get",
                        "wrapKey",
                        "unwrapKey"
                      ]
                    }
                  }
                ]
              }
            }
          ]
        }
      }
    },
    {
      "name": "[concat(parameters('serverName'), '/', variables('serverKeyName'))]",
      "type": "Microsoft.DBforPostgreSQL/servers/keys",
      "apiVersion": "2020-01-01-preview",
      "dependsOn": [
        "addAccessPolicy",
        "[resourceId('Microsoft.DBforPostgreSQL/servers', parameters('serverName'))]"
      ],
      "properties": {
        "serverKeyType": "AzureKeyVault",
        "uri": "[concat(reference(resourceId(parameters('keyVaultResourceGroupName'), 'Microsoft.KeyVault/vaults/', parameters('keyVaultName')), '2018-02-14-preview', 'Full').properties.vaultUri, 'keys/', parameters('keyName'), '/', parameters('keyVersion'))]"
      }
    }
  ]
}
```

## <a name="next-steps"></a>Volgende stappen

 Zie Azure Database voor [PostgreSQL Single server-gegevensversleuteling met door de klant beheerde sleutel voor](concepts-data-encryption-postgresql.md)meer informatie over gegevensversleuteling.
