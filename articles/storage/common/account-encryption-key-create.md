---
title: Een account maken dat door klanten beheerde sleutels voor tabellen en wachtrijen ondersteunt
titleSuffix: Azure Storage
description: Meer informatie over het maken van een opslagaccount dat het configureren van door de klant beheerde sleutels voor tabellen en wachtrijen ondersteunt. Gebruik de Azure CLI- of Azure Resource Manager-sjabloon om een opslagaccount te maken dat is gebaseerd op de accountversleutelingssleutel voor Azure Storage-versleuteling. U vervolgens door de klant beheerde sleutels voor het account configureren.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/05/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 09558a8d1e4e2dc68cefd2c870f54e008d10b97b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77083570"
---
# <a name="create-an-account-that-supports-customer-managed-keys-for-tables-and-queues"></a>Een account maken dat door klanten beheerde sleutels voor tabellen en wachtrijen ondersteunt

Azure Storage versleutelt alle gegevens in een opslagaccount in rust. Standaard gebruiken wachtrijopslag en tabelopslag een sleutel die is ingesteld op de service en wordt beheerd door Microsoft. U er ook voor kiezen om door de klant beheerde sleutels te gebruiken om wachtrij- of tabelgegevens te versleutelen. Als u door de klant beheerde sleutels wilt gebruiken met wachtrijen en tabellen, moet u eerst een opslagaccount maken dat een versleutelingssleutel gebruikt die is afgestemd op het account, in plaats van op de service. Nadat u een account hebt gemaakt dat de accountversleutelingssleutel voor wachtrij- en tabelgegevens gebruikt, u door de klant beheerde sleutels configureren met Azure Key Vault voor dat opslagaccount.

In dit artikel wordt beschreven hoe u een opslagaccount maakt dat is gebaseerd op een sleutel die is beperkt tot het account. Wanneer het account voor het eerst wordt gemaakt, gebruikt Microsoft de accountsleutel om de gegevens in het account te versleutelen en beheert Microsoft de sleutel. U vervolgens door de klant beheerde sleutels voor het account configureren om te profiteren van deze voordelen, waaronder de mogelijkheid om uw eigen sleutels te verstrekken, de sleutelversie bij te werken, de sleutels te roteren en toegangsbesturingselementen in te trekken.

## <a name="about-the-feature"></a>Over de functie

Als u een opslagaccount wilt maken dat is gebaseerd op de accountversleutelingssleutel voor wachtrij- en tabelopslag, moet u zich eerst registreren om deze functie met Azure te gebruiken. Vanwege de beperkte capaciteit kan het enkele maanden duren voordat verzoeken om toegang zijn goedgekeurd.

U een opslagaccount maken dat is gebaseerd op de accountversleutelingssleutel voor wachtrij- en tabelopslag in de volgende regio's:

- VS - oost
- VS - zuid-centraal
- VS - west 2  

### <a name="register-to-use-the-account-encryption-key"></a>Registreren om de accountversleutelingssleutel te gebruiken

Als u zich wilt registreren om de accountversleutelingssleutel te gebruiken met wachtrij- of tabelopslag, gebruikt u PowerShell of Azure CLI.

# <a name="powershell"></a>[Powershell](#tab/powershell)

Als u zich wilt registreren bij PowerShell, belt u de opdracht [Get-AzProviderFeature.](/powershell/module/az.resources/get-azproviderfeature)

```powershell
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowAccountEncryptionKeyForQueues
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowAccountEncryptionKeyForTables
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u zich wilt registreren bij Azure CLI, belt u de opdracht [AZ-functieregister.](/cli/azure/feature#az-feature-register)

```azurecli
az feature register --namespace Microsoft.Storage \
    --name AllowAccountEncryptionKeyForQueues
az feature register --namespace Microsoft.Storage \
    --name AllowAccountEncryptionKeyForTables
```

# <a name="template"></a>[Sjabloon](#tab/template)

N.v.t.

---

### <a name="check-the-status-of-your-registration"></a>Controleer de status van uw inschrijving

Als u de status van uw registratie wilt controleren voor wachtrij- of tabelopslag, gebruikt u PowerShell of Azure CLI.

# <a name="powershell"></a>[Powershell](#tab/powershell)

Als u de status van uw registratie bij PowerShell wilt controleren, belt u de opdracht [Get-AzProviderFeature.](/powershell/module/az.resources/get-azproviderfeature)

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowAccountEncryptionKeyForQueues
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowAccountEncryptionKeyForTables
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u de status van uw registratie bij Azure CLI wilt controleren, roept u de opdracht [AZ-functie](/cli/azure/feature#az-feature-show) aan.

```azurecli
az feature show --namespace Microsoft.Storage \
    --name AllowAccountEncryptionKeyForQueues
az feature show --namespace Microsoft.Storage \
    --name AllowAccountEncryptionKeyForTables
```

# <a name="template"></a>[Sjabloon](#tab/template)

N.v.t.

---

### <a name="re-register-the-azure-storage-resource-provider"></a>De Azure Storage-bronprovider opnieuw registreren

Nadat uw registratie is goedgekeurd, moet u de Azure Storage-bronprovider opnieuw registreren. Gebruik PowerShell of Azure CLI om de resourceprovider opnieuw te registreren.

# <a name="powershell"></a>[Powershell](#tab/powershell)

Als u de resourceprovider opnieuw wilt registreren bij PowerShell, belt u de opdracht [Register-AzResourceProvider.](/powershell/module/az.resources/register-azresourceprovider)

```powershell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.Storage'
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u de resourceprovider opnieuw wilt registreren bij Azure CLI, belt u de [opdracht az-providerregister.](/cli/azure/provider#az-provider-register)

```azurecli
az provider register --namespace 'Microsoft.Storage'
```

# <a name="template"></a>[Sjabloon](#tab/template)

N.v.t.

---

## <a name="create-an-account-that-uses-the-account-encryption-key"></a>Een account maken dat de accountversleutelingssleutel gebruikt

U moet een nieuw opslagaccount configureren om de accountversleutelingssleutel te gebruiken voor wachtrijen en tabellen op het moment dat u het opslagaccount maakt. Het bereik van de versleutelingssleutel kan niet worden gewijzigd nadat het account is gemaakt.

De opslagrekening moet van het type algemene v2 zijn. U het opslagaccount maken en configureren om op de accountversleutelingssleutel te vertrouwen met Azure CLI of een Azure Resource Manager-sjabloon.

> [!NOTE]
> Alleen wachtrij- en tabelopslag kan optioneel worden geconfigureerd om gegevens te versleutelen met de accountversleutelingssleutel wanneer het opslagaccount wordt gemaakt. Blob-opslag en Azure Files gebruiken altijd de accountversleutelingssleutel om gegevens te versleutelen.

# <a name="powershell"></a>[Powershell](#tab/powershell)

Als u PowerShell wilt gebruiken om een opslagaccount te maken dat is gebaseerd op de accountversleutelingssleutel, controleert u of u de Azure PowerShell-module, versie 3.4.0 of hoger, hebt geïnstalleerd. Zie [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps)voor meer informatie.

Maak vervolgens een v2-opslagaccount voor algemene doeleinden aan door de opdracht [Nieuw-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) aan te roepen, met de juiste parameters:

- Voeg `-EncryptionKeyTypeForQueue` de optie toe `Account` en stel de waarde in om de accountversleutelingssleutel te gebruiken om gegevens in wachtrijopslag te versleutelen.
- Voeg `-EncryptionKeyTypeForTable` de optie toe `Account` en stel de waarde in om de accountversleutelingssleutel te gebruiken om gegevens in tabelopslag te versleutelen.

In het volgende voorbeeld ziet u hoe u een v2-opslagaccount voor algemene doeleinden maakt dat is geconfigureerd voor georedundante opslag (Read-Access) en dat de accountversleutelingssleutel gebruikt om gegevens te versleutelen voor zowel wachtrij- als tabelopslag. Vergeet niet om de plaatsaanduidingswaarden tussen haakjes te vervangen door uw eigen waarden:

```powershell
New-AzStorageAccount -ResourceGroupName <resource_group> `
    -AccountName <storage-account> `
    -Location <location> `
    -SkuName "Standard_RAGRS" `
    -Kind StorageV2 `
    -EncryptionKeyTypeForTable Account `
    -EncryptionKeyTypeForQueue Account
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u Azure CLI wilt gebruiken om een opslagaccount te maken dat is gebaseerd op de accountversleutelingssleutel, controleert u of u Azure CLI-versie 2.0.80 of hoger hebt geïnstalleerd. Zie [Azure CLI installeren](/cli/azure/install-azure-cli)voor meer informatie.

Maak vervolgens een v2-opslagaccount voor algemene doeleinden door de opdracht [AZ-opslagaccount te bellen,](/cli/azure/storage/account#az-storage-account-create) met de juiste parameters:

- Voeg `--encryption-key-type-for-queue` de optie toe `Account` en stel de waarde in om de accountversleutelingssleutel te gebruiken om gegevens in wachtrijopslag te versleutelen.
- Voeg `--encryption-key-type-for-table` de optie toe `Account` en stel de waarde in om de accountversleutelingssleutel te gebruiken om gegevens in tabelopslag te versleutelen.

In het volgende voorbeeld ziet u hoe u een v2-opslagaccount voor algemene doeleinden maakt dat is geconfigureerd voor georedundante opslag (Read-Access) en dat de accountversleutelingssleutel gebruikt om gegevens te versleutelen voor zowel wachtrij- als tabelopslag. Vergeet niet om de plaatsaanduidingswaarden tussen haakjes te vervangen door uw eigen waarden:

```azurecli
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --location <location> \
    --sku Standard_RAGRS \
    --kind StorageV2 \
    --encryption-key-type-for-table Account \
    --encryption-key-type-for-queue Account
```

# <a name="template"></a>[Sjabloon](#tab/template)

In het volgende JSON-voorbeeld wordt een v2-opslagaccount voor algemene doeleinden gemaakt dat is geconfigureerd voor georedundante opslag (Read-Access) en dat de accountversleutelingssleutel gebruikt om gegevens te versleutelen voor zowel wachtrij- als tabelopslag. Vergeet niet om de plaatsaanduidingswaarden in hoekhaakjes te vervangen door uw eigen waarden:

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
            "name": "[parameters('Standard_RAGRS')]"
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

Nadat u een account hebt gemaakt dat is gebaseerd op de accountversleutelingssleutel, raadpleegt u een van de volgende artikelen om door de klant beheerde sleutels te configureren met Azure Key Vault:

- [Door de klant beheerde sleutels configureren met Azure Key Vault met behulp van de Azure-portal](storage-encryption-keys-portal.md)
- [Door de klant beheerde sleutels configureren met Azure Key Vault met PowerShell](storage-encryption-keys-powershell.md)
- [Door de klant beheerde sleutels configureren met Azure Key Vault met Azure CLI](storage-encryption-keys-cli.md)

## <a name="verify-the-account-encryption-key"></a>De versleutelingssleutel van het account verifiëren

Als u wilt controleren of een service in een opslagaccount de accountversleutelingssleutel gebruikt, belt u de opdracht Azure CLI [az-opslagaccount.](/cli/azure/storage/account#az-storage-account-show) Met deze opdracht retourneert u een set opslagaccounteigenschappen en hun waarden. Zoek naar `keyType` het veld voor elke service binnen de `Account`versleutelingseigenschap en controleer of deze is ingesteld op .

# <a name="powershell"></a>[Powershell](#tab/powershell)

Als u wilt controleren of een service in een opslagaccount de accountversleutelingssleutel gebruikt, belt u de opdracht [Get-AzStorageAccount.](/powershell/module/az.storage/get-azstorageaccount) Met deze opdracht retourneert u een set opslagaccounteigenschappen en hun waarden. Zoek naar `KeyType` het veld voor `Encryption` elke service binnen de `Account`eigenschap en controleer of deze is ingesteld op .

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
$account.Encryption.Services.Queue
$account.Encryption.Services.Table
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u wilt controleren of een service in een opslagaccount de versleutelingssleutel van het account gebruikt, belt u de opdracht [az-opslagaccount.](/cli/azure/storage/account#az-storage-account-show) Met deze opdracht retourneert u een set opslagaccounteigenschappen en hun waarden. Zoek naar `keyType` het veld voor elke service binnen de `Account`versleutelingseigenschap en controleer of deze is ingesteld op .

```azurecli
az storage account show /
    --name <storage-account> /
    --resource-group <resource-group>
```

# <a name="template"></a>[Sjabloon](#tab/template)

N.v.t.

---

## <a name="next-steps"></a>Volgende stappen

- [Azure Storage-versleuteling voor gegevens in rust](storage-service-encryption.md) 
- [Wat is Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
