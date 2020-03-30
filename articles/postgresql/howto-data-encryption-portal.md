---
title: Gegevensversleuteling - Azure-portal - voor Azure Database voor PostgreSQL - Enkele server
description: Meer informatie over het instellen en beheren van gegevensversleuteling voor uw Azure Database voor PostgreSQL Single server met behulp van de Azure-portal.
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: fe4c69787b606c601d2dc8b31cadc6dcf57458da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79297064"
---
# <a name="data-encryption-for-azure-database-for-postgresql-single-server-by-using-the-azure-portal"></a>Gegevensversleuteling voor Azure Database voor PostgreSQL Single-server met behulp van de Azure-portal

Meer informatie over het gebruik van de Azure-portal voor het instellen en beheren van gegevensversleuteling voor uw Azure Database voor PostgreSQL Single-server.

## <a name="prerequisites-for-azure-cli"></a>Voorwaarden voor Azure CLI

* U moet een Azure-abonnement hebben en een beheerder zijn bij dat abonnement.
* Maak in Azure Key Vault een sleutelkluis en sleutelsleutel die u gebruiken voor een door de klant beheerde sleutel.
* De sleutelkluis moet de volgende eigenschappen hebben om te gebruiken als een door de klant beheerde sleutel:
  * [Zachte verwijderen](../key-vault/key-vault-ovw-soft-delete.md)

    ```azurecli-interactive
    az resource update --id $(az keyvault show --name \ <key_vault_name> -test -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
    ```

  * [Gezuiverd beschermd](../key-vault/key-vault-ovw-soft-delete.md#purge-protection)

    ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --enable-purge-protection true
    ```

* De sleutel moet de volgende kenmerken hebben om te gebruiken als een door de klant beheerde sleutel:
  * Geen vervaldatum
  * Niet uitgeschakeld
  * In staat om belangrijke bewerkingen te krijgen, verpakken en uitpakken

## <a name="set-the-right-permissions-for-key-operations"></a>De juiste machtigingen instellen voor belangrijke bewerkingen

1. Selecteer **access-beleid** > In Key Vault selecteer**je Toegangsbeleid toevoegen**.

   ![Schermafbeelding van Key Vault, met toegangsbeleid en Toegangsbeleid toevoegen gemarkeerd](media/concepts-data-access-and-security-data-encryption/show-access-policy-overview.png)

2. Selecteer **Belangrijke machtigingen**en selecteer **Get**, **Wrap**, **Uitpakken**en de **principal,** de naam van de PostgreSQL-server. Als uw serverprincipal niet kan worden gevonden in de lijst met bestaande principals, moet u deze registreren. U wordt gevraagd om uw serverprincipal te registreren wanneer u voor de eerste keer gegevensversleuteling probeert in te stellen en dit mislukt.  

   ![Overzicht van toegangsbeleid](media/concepts-data-access-and-security-data-encryption/access-policy-wrap-unwrap.png)

3. Selecteer **Opslaan**.

## <a name="set-data-encryption-for-azure-database-for-postgresql-single-server"></a>Gegevensversleuteling instellen voor Azure Database voor PostgreSQL Single-server

1. Selecteer **gegevensversleuteling in** Azure Database voor PostgreSQL om de door de klant beheerde sleutel in te stellen.

   ![Schermafbeelding van Azure Database voor PostgreSQL, met gemarkeerde gegevensversleuteling](media/concepts-data-access-and-security-data-encryption/data-encryption-overview.png)

2. U een sleutelkluis en sleutelpaar selecteren of een sleutel-id invoeren.

   ![Schermafbeelding van Azure Database voor PostgreSQL, met gemarkeerde opties voor gegevensversleuteling](media/concepts-data-access-and-security-data-encryption/setting-data-encryption.png)

3. Selecteer **Opslaan**.

4. Start de server opnieuw op om ervoor te zorgen dat alle bestanden (inclusief tijdelijke bestanden) volledig zijn versleuteld.

## <a name="restore-or-create-a-replica-of-the-server"></a>Een replica van de server herstellen of maken

Nadat Azure Database voor PostgreSQL Single-server is versleuteld met de beheerde sleutel van een klant die is opgeslagen in Key Vault, wordt ook elke nieuw gemaakte kopie van de server versleuteld. U deze nieuwe kopie maken via een lokale of geoherstelbewerking of via een bewerking voor replica's (lokaal/regio). Voor een versleutelde PostgreSQL-server u dus de volgende stappen gebruiken om een versleutelde herstelde server te maken.

1. Selecteer **Overzicht** > **herstellen**op uw server .

   ![Schermafbeelding van Azure Database voor PostgreSQL, met overzicht en herstel gemarkeerd](media/concepts-data-access-and-security-data-encryption/show-restore.png)

   Of selecteer **Replicatie**voor een server **Settings** met replicatiedie is ingeschakeld.

   ![Schermafbeelding van Azure Database voor PostgreSQL, met replicatie gemarkeerd](media/concepts-data-access-and-security-data-encryption/postgresql-replica.png)

2. Nadat de herstelbewerking is voltooid, wordt de nieuwe server die is gemaakt, versleuteld met de sleutel van de primaire server. De functies en opties op de server zijn echter uitgeschakeld en de server is niet toegankelijk. Dit voorkomt gegevensmanipulatie, omdat de identiteit van de nieuwe server nog geen toestemming heeft gekregen om toegang te krijgen tot de sleutelkluis.

   ![Schermafbeelding van Azure Database voor PostgreSQL, met ontoegankelijke status gemarkeerd](media/concepts-data-access-and-security-data-encryption/show-restore-data-encryption.png)

3. Als u de server toegankelijk wilt maken, moet u de sleutel op de herstelserver opnieuw valideren. Selecteer de sleutel voor **gegevensversleuteling** > **opnieuw valideren**.

   > [!NOTE]
   > De eerste poging om opnieuw te valideren mislukt, omdat de serviceprincipal van de nieuwe server toegang moet krijgen tot de sleutelkluis. Als u de serviceprincipal wilt genereren, selecteert u **De sleutel opnieuw valideren,** waardoor een fout wordt weergegeven, maar de serviceprincipal wordt gegenereerd. Daarna, verwijzen naar [deze stappen](#set-the-right-permissions-for-key-operations) eerder in dit artikel.

   ![Schermafbeelding van Azure Database voor PostgreSQL, waarbij de validatiestap is gemarkeerd](media/concepts-data-access-and-security-data-encryption/show-revalidate-data-encryption.png)

   U moet de sleutelkluis toegang geven tot de nieuwe server.

4. Nadat u de serviceprincipal hebt geregistreerd, wordt de sleutel opnieuw gevalideerd en wordt de normale functionaliteit van de server hervat.

   ![Schermafbeelding van Azure Database voor PostgreSQL, met herstelde functionaliteit](media/concepts-data-access-and-security-data-encryption/restore-successful.png)

## <a name="using-an-azure-resource-manager-template-to-enable-data-encryption"></a>Een Azure Resource Manager-sjabloon gebruiken om gegevensversleuteling in te schakelen

Naast azure-portal u ook gegevensversleuteling inschakelen op uw Azure Database voor PostgreSQL-enkele server met Azure Resource Manager-sjablonen voor nieuwe en bestaande server.

### <a name="for-a-new-server"></a>Voor een nieuwe server

Gebruik een van de vooraf gemaakte Azure Resource Manager-sjablonen om de server te voorzien van gegevensversleuteling ingeschakeld: [Voorbeeld met gegevensversleuteling](https://github.com/Azure/azure-postgresql/tree/master/arm-templates/ExampleWithDataEncryption)

Deze Azure Resource Manager-sjabloon maakt een Azure Database voor PostgreSQL Single-server en gebruikt de **KeyVault** en **Key** die als parameters zijn doorgegeven om gegevensversleuteling op de server in te schakelen.

### <a name="for-an-existing-server"></a>Voor een bestaande server
Bovendien u Azure Resource Manager-sjablonen gebruiken om gegevensversleuteling in te schakelen op uw bestaande Azure Database voor PostgreSQL Single-servers.

* Geef de URI door van de Azure Key Vault-sleutel die u eerder hebt gekopieerd onder de `keyVaultKeyUri` eigenschap in het object Eigenschappen.

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
