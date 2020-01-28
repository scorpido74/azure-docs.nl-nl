---
title: Een account maken dat door de klant beheerde sleutels voor tabellen en wacht rijen ondersteunt
titleSuffix: Azure Storage
description: Meer informatie over het maken van een opslag account dat ondersteuning biedt voor het configureren van door de klant beheerde sleutels voor tabellen en wacht rijen. Gebruik de Azure-CLI of een Azure Resource Manager sjabloon voor het maken van een opslag account dat afhankelijk is van de versleutelings sleutel van het account voor Azure Storage versleuteling. U kunt vervolgens door de klant beheerde sleutels configureren voor het account.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 01/10/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 8cf1f8ecb68e31f93c19d93d6ebc4f8ef37724e7
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "76028451"
---
# <a name="create-an-account-that-supports-customer-managed-keys-for-tables-and-queues"></a>Een account maken dat door de klant beheerde sleutels voor tabellen en wacht rijen ondersteunt

Azure Storage versleutelt alle gegevens in een opslag account in rust. Wachtrij opslag en tabel opslag gebruiken standaard een sleutel die is toegewezen aan de service en die wordt beheerd door micro soft. U kunt er ook voor kiezen om door de klant beheerde sleutels te gebruiken om wachtrij-of tabel gegevens te versleutelen. Als u door de klant beheerde sleutels met wacht rijen en tabellen wilt gebruiken, moet u eerst een opslag account maken dat gebruikmaakt van een versleutelings sleutel die is afgestemd op het account, in plaats van naar de service. Nadat u een account hebt gemaakt dat gebruikmaakt van de account versleutelings sleutel voor wachtrij-en tabel gegevens, kunt u door de klant beheerde sleutels configureren met Azure Key Vault voor dat opslag account.

In dit artikel wordt beschreven hoe u een opslag account maakt dat afhankelijk is van een sleutel die is afgestemd op het account. Wanneer het account voor het eerst wordt gemaakt, gebruikt micro soft de account sleutel voor het versleutelen van de gegevens in het account en wordt de sleutel door micro soft beheerd. U kunt vervolgens door de klant beheerde sleutels voor het account configureren om gebruik te maken van deze voor delen, waaronder de mogelijkheid om uw eigen sleutels op te geven, de sleutel versie bij te werken, de sleutels te draaien en toegangs beheer in te trekken.

## <a name="about-the-feature"></a>Over de functie

Als u een opslag account wilt maken dat afhankelijk is van de account versleutelings sleutel voor wachtrij-en tabel opslag, moet u zich eerst registreren voor het gebruik van deze functie met Azure. Vanwege beperkte capaciteit moet u er rekening mee houden dat het enkele maanden kan duren voordat aanvragen voor toegang zijn goedgekeurd.

U kunt een opslag account maken dat afhankelijk is van de account versleutelings sleutel voor wachtrij-en tabel opslag in de volgende regio's:

- VS - oost
- US - zuid-centraal
- US - west 2  

### <a name="register-to-use-the-account-encryption-key"></a>Registreren voor het gebruik van de account versleutelings sleutel

Als u zich wilt registreren bij Azure CLI, roept u de opdracht [AZ feature REGI ster](/cli/azure/feature#az-feature-register) aan.

Registreren voor het gebruik van de account versleutelings sleutel met wachtrij opslag:

```azurecli
az feature register --namespace Microsoft.Storage --name AllowAccountEncryptionKeyForQueues
```

Registreren voor het gebruik van de account versleutelings sleutel met tabel opslag:

```azurecli
az feature register --namespace Microsoft.Storage --name AllowAccountEncryptionKeyForTables
```

### <a name="check-the-status-of-your-registration"></a>Controleer de status van uw registratie

De status van de registratie voor de wachtrij opslag controleren:

```azurecli
az feature show --namespace Microsoft.Storage --name AllowAccountEncryptionKeyForQueues
```

De status van uw registratie voor tabel opslag controleren:

```azurecli
az feature show --namespace Microsoft.Storage --name AllowAccountEncryptionKeyForTables
```

### <a name="re-register-the-azure-storage-resource-provider"></a>De Azure Storage Resource provider opnieuw registreren

Nadat de registratie is goedgekeurd, moet u de Azure Storage Resource provider opnieuw registreren. Roep de opdracht [AZ provider REGI ster](/cli/azure/provider#az-provider-register) aan:

```azurecli
az provider register --namespace 'Microsoft.Storage'
```

## <a name="create-an-account-that-uses-the-account-encryption-key"></a>Een account maken dat gebruikmaakt van de versleutelings sleutel van het account

U moet een nieuw opslag account configureren voor het gebruik van de account versleutelings sleutel voor wacht rijen en tabellen op het moment dat u het opslag account maakt. Het bereik van de versleutelings sleutel kan niet worden gewijzigd nadat het account is gemaakt.

Het opslag account moet van het type voor algemeen gebruik v2 zijn. U kunt het opslag account maken en configureren om te vertrouwen op de account versleutelings sleutel met behulp van Azure CLI of een Azure Resource Manager sjabloon.

> [!NOTE]
> Alleen wachtrij-en tabel opslag kunnen eventueel worden geconfigureerd voor het versleutelen van gegevens met de versleutelings sleutel van het account wanneer het opslag account wordt gemaakt. Blob-opslag en Azure Files altijd de account versleutelings sleutel gebruiken voor het versleutelen van gegevens.

### <a name="azure-clitabazure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u Azure CLI wilt gebruiken om een opslag account te maken dat afhankelijk is van de versleutelings sleutel van het account, moet u ervoor zorgen dat u Azure CLI-versie 2.0.80 of hoger hebt geïnstalleerd. Zie voor meer informatie, [Azure CLI installeren](/cli/azure/install-azure-cli).

Maak vervolgens een voor algemeen gebruik v2-opslag account door de opdracht [AZ Storage account create](/cli/azure/storage/account#az-storage-account-create) aan te roepen, met de juiste para meters:

- Neem de optie `--encryption-key-type-for-queue` op en stel de waarde ervan in op `Account` om de account versleutelings sleutel te gebruiken voor het versleutelen van gegevens in de wachtrij opslag.
- Neem de optie `--encryption-key-type-for-table` op en stel de waarde ervan in op `Account` om de account versleutelings sleutel te gebruiken voor het versleutelen van gegevens in de tabel opslag.

In het volgende voor beeld ziet u hoe u een v2-opslag account voor algemeen gebruik maakt dat is geconfigureerd voor LRS en dat de versleutelings sleutel van het account gebruikt voor het versleutelen van gegevens voor wachtrij-en tabel opslag. Vergeet niet om de waarden van de tijdelijke aanduidingen tussen vier Kante haken te vervangen door uw eigen waarden:

```azurecli
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --location <location> \
    --sku Standard_LRS \
    --kind StorageV2 \
    --encryption-key-type-for-table Account \
    --encryption-key-type-for-queue Account
```

### <a name="templatetabtemplate"></a>[Sjabloon](#tab/template)

In het volgende JSON-voor beeld wordt een v2-opslag account voor algemeen gebruik gemaakt dat is geconfigureerd voor LRS en die gebruikmaakt van de account versleutelings sleutel voor het versleutelen van gegevens voor wachtrij-en tabel opslag. Vergeet niet om de waarden van de tijdelijke aanduidingen tussen punt haken te vervangen door uw eigen waarden:

```json
"resources": [
    {
        "type": "Microsoft.Storage/storageAccounts",
        "apiVersion": "2019-06-01",
        "name": "[parameters('<storage-account>')]",
        "location": "[parameters('<location>')]",
        "dependsOn": [],
        "tags": {},
        "sku": {
            "name": "[parameters('Standard_LRS')]"
        },
        "kind": "[parameters('StorageV2')]",
        "properties": {
            "accessTier": "[parameters('<accessTier>')]",
            "supportsHttpsTrafficOnly": "[parameters('supportsHttpsTrafficOnly')]",
            "largeFileSharesState": "[parameters('<largeFileSharesState>')]",
            "encryption": {
                "services": {
                    "queue": {
                        "keyType": "Account"
                    },
                    "table": {
                        "keyType": "Account"
                    }
                },
                "keySource": "Microsoft.Storage"
            }
        }
    }
],
```

---

Nadat u een account hebt gemaakt dat afhankelijk is van de versleutelings sleutel van het account, raadpleegt u een van de volgende artikelen voor het configureren van door de klant beheerde sleutels met Azure Key Vault:

- [Door de klant beheerde sleutels met Azure Key Vault configureren met behulp van de Azure Portal](storage-encryption-keys-portal.md)
- [Door de klant beheerde sleutels configureren met Azure Key Vault met behulp van Power shell](storage-encryption-keys-powershell.md)
- [Door de klant beheerde sleutels configureren met Azure Key Vault met behulp van Azure CLI](storage-encryption-keys-cli.md)

## <a name="verify-the-account-encryption-key"></a>De versleutelings sleutel voor het account controleren

Als u wilt controleren of een service in een opslag account gebruikmaakt van de versleutelings sleutel van het account, roept u de opdracht Azure CLI [AZ Storage account](/cli/azure/storage/account#az-storage-account-show) aan. Met deze opdracht wordt een set eigenschappen van het opslag account en de bijbehorende waarden geretourneerd. Zoek naar het veld `keyType` voor elke service binnen de versleutelings eigenschap en controleer of deze is ingesteld op `Account`.

```azurecli
az storage account show /
    --name <storage-account> /
    --resource-group <resource-group>
```

## <a name="next-steps"></a>Volgende stappen

- [Azure Storage versleuteling voor Data-at-rest](storage-service-encryption.md) 
- [Wat is Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)?
