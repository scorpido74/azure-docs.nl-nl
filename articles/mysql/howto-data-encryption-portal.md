---
title: Gegevens versleuteling-Azure Portal-Azure Database for MySQL
description: Meer informatie over het instellen en beheren van gegevens versleuteling voor uw Azure Database for MySQL met behulp van de Azure Portal.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 78a290b1e2984719645fb4d4ff253ab021a0826e
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79299036"
---
# <a name="data-encryption-for-azure-database-for-mysql-by-using-the-azure-portal"></a>Gegevens versleuteling voor Azure Database for MySQL met behulp van de Azure Portal

Meer informatie over het gebruik van de Azure Portal voor het instellen en beheren van gegevens versleuteling voor uw Azure Database for MySQL.

## <a name="prerequisites-for-azure-cli"></a>Vereisten voor Azure CLI

* U moet een Azure-abonnement hebben en een beheerder van dat abonnement zijn.
* In Azure Key Vault maakt u een sleutel kluis en een sleutel die u kunt gebruiken voor een door de klant beheerde sleutel.
* De sleutel kluis moet de volgende eigenschappen hebben voor gebruik als een door de klant beheerde sleutel:
  * [Voorlopig verwijderen](../key-vault/key-vault-ovw-soft-delete.md)

    ```azurecli-interactive
    az resource update --id $(az keyvault show --name \ <key_vault_name> -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
    ```

  * [Beveiligd leegmaken](../key-vault/key-vault-ovw-soft-delete.md#purge-protection)

    ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --enable-purge-protection true
    ```

* De sleutel moet de volgende kenmerken hebben om te kunnen worden gebruikt als een door de klant beheerde sleutel:
  * Geen verval datum
  * Niet uitgeschakeld
  * Kan de Get-, terugloop-, sleutel bewerking uitpakken

## <a name="set-the-right-permissions-for-key-operations"></a>De juiste machtigingen voor sleutel bewerkingen instellen

1. Selecteer in Key Vault **toegangs beleid** > **beleid voor toegang toevoegen**.

   ![Scherm opname van Key Vault, met beleids regels voor toegang en toegangs beleid toevoegen gemarkeerd](media/concepts-data-access-and-security-data-encryption/show-access-policy-overview.png)

2. Selecteer **sleutel machtigingen**en selecteer **ophalen**, **teruglopen**, **uitpakken**en de **Principal**. Dit is de naam van de mysql-server. Als uw server-Principal niet kan worden gevonden in de lijst met bestaande principals, moet u deze registreren. U wordt gevraagd om de serverprincipal te registreren wanneer u de gegevens versleuteling voor de eerste keer probeert in te stellen. dit mislukt.

   ![Overzicht van toegangs beleid](media/concepts-data-access-and-security-data-encryption/access-policy-wrap-unwrap.png)

3. Selecteer **Opslaan**.

## <a name="set-data-encryption-for-azure-database-for-mysql"></a>Gegevens versleuteling instellen voor Azure Database for MySQL

1. Selecteer in Azure Database for MySQL **gegevens versleuteling** om de door de klant beheerde sleutel in te stellen.

   ![Scherm opname van Azure Database for MySQL, met de gegevens versleuteling gemarkeerd](media/concepts-data-access-and-security-data-encryption/data-encryption-overview.png)

2. U kunt een sleutel kluis en sleutel paar selecteren of een sleutel-id invoeren.

   ![Scherm opname van Azure Database for MySQL, met opties voor gegevens versleuteling gemarkeerd](media/concepts-data-access-and-security-data-encryption/setting-data-encryption.png)

3. Selecteer **Opslaan**.

4. Start de server opnieuw op om ervoor te zorgen dat alle bestanden (inclusief tijdelijke bestanden) volledig zijn versleuteld.

## <a name="restore-or-create-a-replica-of-the-server"></a>Herstel of maak een replica van de server

Nadat Azure Database for MySQL is versleuteld met een door de klant beheerde sleutel die is opgeslagen in Key Vault, wordt een nieuw gemaakt exemplaar van de server ook versleuteld. U kunt deze nieuwe kopie maken via een lokale of geo-herstel bewerking, of via een replica (lokale/Kruis regio). Voor een versleutelde MySQL-server kunt u dus de volgende stappen gebruiken om een versleutelde herstelde server te maken.

1. Selecteer **overzicht** > **herstellen**op uw server.

   ![Scherm opname van Azure Database for MySQL, met overzicht en herstellen gemarkeerd](media/concepts-data-access-and-security-data-encryption/show-restore.png)

   Of voor een server waarvoor replicatie is ingeschakeld, selecteert u in de kop **instellingen** de optie **replicatie**.

   ![Scherm opname van Azure Database for MySQL, waarbij de replicatie is gemarkeerd](media/concepts-data-access-and-security-data-encryption/mysql-replica.png)

2. Nadat de herstel bewerking is voltooid, wordt de nieuwe gemaakte server versleuteld met de sleutel van de primaire server. De functies en opties op de server zijn echter uitgeschakeld en de server is niet toegankelijk. Dit voor komt dat gegevens worden gemanipuleerd, omdat de identiteit van de nieuwe server nog niet is gemachtigd voor toegang tot de sleutel kluis.

   ![Scherm opname van Azure Database for MySQL, met een niet-toegankelijke status gemarkeerd](media/concepts-data-access-and-security-data-encryption/show-restore-data-encryption.png)

3. Valideer de sleutel op de herstelde server om de server toegankelijk te maken. Selecteer **gegevens versleuteling** > **sleutel opnieuw valideren**.

   > [!NOTE]
   > De eerste poging om opnieuw te valideren, mislukt, omdat de service-principal van de nieuwe server toegang moet krijgen tot de sleutel kluis. Als u de Service-Principal wilt genereren, selecteert u **sleutel opnieuw valideren**. er wordt dan een fout weer gegeven, maar de service-principal wordt gegenereerd. Ga daarna naar [deze stappen](#set-the-right-permissions-for-key-operations) eerder in dit artikel.

   ![Scherm opname van Azure Database for MySQL, waarbij de stap voor hervalidatie is gemarkeerd](media/concepts-data-access-and-security-data-encryption/show-revalidate-data-encryption.png)

   U moet de sleutel kluis toegang verlenen tot de nieuwe server.

4. Nadat u de Service-Principal hebt geregistreerd, moet u de sleutel opnieuw valideren en wordt de normale functionaliteit van de server hervat.

   ![Scherm opname van Azure Database for MySQL, met de herstelde functionaliteit](media/concepts-data-access-and-security-data-encryption/restore-successful.png)


## <a name="using-an-azure-resource-manager-template-to-enable-data-encryption"></a>Een Azure Resource Manager sjabloon gebruiken om gegevens versleuteling in te scha kelen

Naast de Azure Portal, kunt u ook gegevens versleuteling op uw Azure Database for MySQL-server inschakelen met behulp van Azure Resource Manager-sjablonen voor nieuwe en bestaande servers.

### <a name="for-a-new-server"></a>Voor een nieuwe server

Gebruik een van de vooraf gemaakte Azure Resource Manager sjablonen om de server in te richten met gegevens versleuteling ingeschakeld: [voor beeld met gegevens versleuteling](https://github.com/Azure/azure-mysql/tree/master/arm-templates/ExampleWithDataEncryption)

Met deze Azure Resource Manager sjabloon maakt u een Azure Database for MySQL-server en gebruikt u de sleutel **kluis** en **code** die zijn door gegeven als para meters om gegevens versleuteling op de server in te scha kelen.

### <a name="for-an-existing-server"></a>Voor een bestaande server
Daarnaast kunt u Azure Resource Manager sjablonen gebruiken om gegevens versleuteling in te scha kelen op uw bestaande Azure Database for MySQL-servers.

* Geef de URI van de Azure Key Vault sleutel die u eerder hebt gekopieerd, op onder de `keyVaultKeyUri` eigenschap in het object Properties.

* Gebruik *2020-01-01-preview* als de API-versie.

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
      "type": "Microsoft.DBforMySQL/servers",
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
        "[resourceId('Microsoft.DBforMySQL/servers', parameters('serverName'))]"
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
                    "objectId": "[reference(resourceId('Microsoft.DBforMySQL/servers/', parameters('serverName')), '2017-12-01', 'Full').identity.principalId]",
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
      "type": "Microsoft.DBforMySQL/servers/keys",
      "apiVersion": "2020-01-01-preview",
      "dependsOn": [
        "addAccessPolicy",
        "[resourceId('Microsoft.DBforMySQL/servers', parameters('serverName'))]"
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

 Zie [Azure database for MySQL Data Encryption with door de klant beheerde sleutel](concepts-data-encryption-mysql.md)voor meer informatie over gegevens versleuteling.
