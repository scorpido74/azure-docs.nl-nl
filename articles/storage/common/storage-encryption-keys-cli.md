---
title: Azure CLI gebruiken om door de klant beheerde sleutels te configureren
titleSuffix: Azure Storage
description: Meer informatie over het gebruik van Azure CLI om door klanten beheerde sleutels te configureren met Azure Key Vault voor Azure Storage-versleuteling.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: ea944d4cfa3006c33f1dee3dd8e6ee6088681aa7
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618652"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-azure-cli"></a>Door de klant beheerde sleutels configureren met Azure Key Vault met Azure CLI

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

In dit artikel ziet u hoe u een Azure Key Vault configureert met door de klant beheerde sleutels met Azure CLI. Zie [Snelstart: Een geheim instellen en ophalen uit Azure Key Vault met Azure CLI](../../key-vault/quick-create-cli.md)voor meer informatie over het maken van een sleutelkluis met Azure CLI.

## <a name="assign-an-identity-to-the-storage-account"></a>Een identiteit toewijzen aan het opslagaccount

Als u door de klant beheerde sleutels voor uw opslagaccount wilt inschakelen, wijst u eerst een door het systeem toegewezen beheerde identiteit toe aan het opslagaccount. U gebruikt deze beheerde identiteit om de opslagaccountmachtigingen te verlenen voor toegang tot de sleutelkluis.

Als u een beheerde identiteit wilt toewijzen met Azure CLI, roept u [az-opslagaccountupdate aan](/cli/azure/storage/account#az-storage-account-update). Vergeet niet om de plaatsaanduidingswaarden tussen haakjes te vervangen door uw eigen waarden.

```azurecli-interactive
az account set --subscription <subscription-id>

az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity
```

Zie [Beheerde identiteiten configureren voor Azure-resources op een Azure VM met Azure CLI voor](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)meer informatie over het configureren van beheerde identiteiten met Azure CLI.

## <a name="create-a-new-key-vault"></a>Een nieuwe sleutelkluis maken

De sleutelkluis die u gebruikt om door de klant beheerde sleutels voor Azure Storage-versleuteling op te slaan, moet twee belangrijke beveiligingsinstellingen hebben ingeschakeld, **Soft Delete** en Do **Not Purge.** Als u een nieuwe sleutelkluis wilt maken met PowerShell of Azure CLI met deze instellingen ingeschakeld, voert u de volgende opdrachten uit. Vergeet niet om de plaatsaanduidingswaarden tussen haakjes te vervangen door uw eigen waarden.

Als u een nieuwe sleutelkluis wilt maken met Azure CLI, belt u [az keyvault maken](/cli/azure/keyvault#az-keyvault-create). Vergeet niet om de plaatsaanduidingswaarden tussen haakjes te vervangen door uw eigen waarden.

```azurecli-interactive
az keyvault create \
    --name <key-vault> \
    --resource-group <resource_group> \
    --location <region> \
    --enable-soft-delete \
    --enable-purge-protection
```

Zie de secties getiteld **Soft-delete** en **Inschakelen van verwijdering** [in](../../key-vault/key-vault-soft-delete-cli.md)een bestaande sleutelkluis met Azure CLI voor meer informatie over het inschakelen van **Soft Delete** en **Niet verwijderen** op een bestaande sleutelkluis met Azure CLI.

## <a name="configure-the-key-vault-access-policy"></a>Het toegangsbeleid voor belangrijke kluizen configureren

Configureer vervolgens het toegangsbeleid voor de sleutelkluis, zodat het opslagaccount machtigingen heeft om toegang te krijgen. In deze stap gebruikt u de beheerde identiteit die u eerder aan het opslagaccount hebt toegewezen.

Als u het toegangsbeleid voor de sleutelkluis wilt instellen, belt u [het az keyvault-setbeleid](/cli/azure/keyvault#az-keyvault-set-policy). Vergeet niet om de plaatsaanduidingswaarden tussen haakjes te vervangen door uw eigen waarden.

```azurecli-interactive
storage_account_principal=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query identity.principalId \
    --output tsv)
az keyvault set-policy \
    --name <key-vault> \
    --resource-group <resource_group>
    --object-id $storage_account_principal \
    --key-permissions get unwrapKey wrapKey
```

## <a name="create-a-new-key"></a>Een nieuwe sleutel maken

Maak vervolgens een sleutel in de sleutelkluis. Als u een sleutel wilt maken, u [de keyvault-sleutel van AZ aanroepen.](/cli/azure/keyvault/key#az-keyvault-key-create) Vergeet niet om de plaatsaanduidingswaarden tussen haakjes te vervangen door uw eigen waarden.

```azurecli-interactive
az keyvault key create
    --name <key> \
    --vault-name <key-vault>
```

Alleen 2048-bits RSA- en RSA-HSM-sleutels worden ondersteund met Azure Storage-versleuteling. Zie **Key Vault-sleutels** in [Over Azure Key Vault-sleutels, -geheimen en -certificaten](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys)voor meer informatie over sleutels.

## <a name="configure-encryption-with-customer-managed-keys"></a>Versleuteling configureren met door de klant beheerde sleutels

Azure Storage-versleuteling maakt standaard gebruik van door Microsoft beheerde sleutels. Configureer uw Azure Storage-account voor door de klant beheerde sleutels en geef de sleutel op die moet worden gekoppeld aan het opslagaccount.

Als u de versleutelingsinstellingen van het opslagaccount wilt bijwerken, roept u [az-opslagaccountupdate](/cli/azure/storage/account#az-storage-account-update)aan, zoals in het volgende voorbeeld wordt weergegeven. Voeg `--encryption-key-source` de parameter toe `Microsoft.Keyvault` en stel deze in om door de klant beheerde sleutels voor het opslagaccount in te schakelen. In het voorbeeld worden ook query's opgevraagd voor de sleutelkluis URI en de nieuwste sleutelversie, die beide waarden nodig hebben om de sleutel te koppelen aan het opslagaccount. Vergeet niet om de plaatsaanduidingswaarden tussen haakjes te vervangen door uw eigen waarden.

```azurecli-interactive
key_vault_uri=$(az keyvault show \
    --name <key-vault> \
    --resource-group <resource_group> \
    --query properties.vaultUri \
    --output tsv)
key_version=$(az keyvault key list-versions \
    --name <key> \
    --vault-name <key-vault> \
    --query [-1].kid \
    --output tsv | cut -d '/' -f 6)
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-version $key_version \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $key_vault_uri
```

## <a name="update-the-key-version"></a>De belangrijkste versie bijwerken

Wanneer u een nieuwe versie van een sleutel maakt, moet u het opslagaccount bijwerken om de nieuwe versie te gebruiken. Eerst query voor de key vault URI door te bellen [az keyvault show](/cli/azure/keyvault#az-keyvault-show), en voor de belangrijkste versie door te bellen [az keyvault keyvault lijst-versies](/cli/azure/keyvault/key#az-keyvault-key-list-versions). Bel vervolgens [de update van het AZ-opslagaccount](/cli/azure/storage/account#az-storage-account-update) om de versleutelingsinstellingen van het opslagaccount bij te werken om de nieuwe versie van de sleutel te gebruiken, zoals in de vorige sectie wordt weergegeven.

## <a name="use-a-different-key"></a>Een andere sleutel gebruiken

Als u de sleutel wilt wijzigen die wordt gebruikt voor Azure Storage-versleuteling, roept u [az-opslagaccountupdate](/cli/azure/storage/account#az-storage-account-update) aan zoals weergegeven in [Versleuteling configureren met door de klant beheerde sleutels](#configure-encryption-with-customer-managed-keys) en geeft u de nieuwe sleutelnaam en -versie op. Als de nieuwe sleutel zich in een andere sleutelkluis bevindt, werkt u ook de sleutelkluis URI bij.

## <a name="revoke-customer-managed-keys"></a>Door de klant beheerde sleutels intrekken

Als u van mening bent dat een sleutel mogelijk is gecompromitteerd, u door de klant beheerde sleutels intrekken door het toegangsbeleid voor belangrijke kluizen te verwijderen. Als u een door de klant beheerde sleutel wilt intrekken, belt u de opdracht [voor het verwijderen van az-keyvault,](/cli/azure/keyvault#az-keyvault-delete-policy) zoals in het volgende voorbeeld wordt weergegeven. Vergeet niet om de plaatsaanduidingswaarden tussen haakjes te vervangen door uw eigen waarden en de variabelen te gebruiken die in de vorige voorbeelden zijn gedefinieerd.

```azurecli-interactive
az keyvault delete-policy \
    --name <key-vault> \
    --object-id $storage_account_principal
```

## <a name="disable-customer-managed-keys"></a>Door de klant beheerde sleutels uitschakelen

Wanneer u door klanten beheerde sleutels uitschakelt, wordt uw opslagaccount opnieuw versleuteld met door Microsoft beheerde sleutels. Als u door klanten beheerde sleutels wilt `--encryption-key-source parameter` uitschakelen, belt u [az-opslagaccountupdate](/cli/azure/storage/account#az-storage-account-update) en stelt u de in, `Microsoft.Storage`zoals in het volgende voorbeeld. Vergeet niet om de plaatsaanduidingswaarden tussen haakjes te vervangen door uw eigen waarden en de variabelen te gebruiken die in de vorige voorbeelden zijn gedefinieerd.

```azurecli-interactive
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-source Microsoft.Storage
```

## <a name="next-steps"></a>Volgende stappen

- [Azure Storage-versleuteling voor gegevens in rust](storage-service-encryption.md) 
- [Wat is Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
