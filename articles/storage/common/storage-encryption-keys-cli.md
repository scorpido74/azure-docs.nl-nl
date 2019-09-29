---
title: Door de klant beheerde sleutels configureren voor Azure Storage versleuteling van Azure CLI
description: Meer informatie over het gebruik van Azure CLI voor het configureren van door de klant beheerde sleutels voor Azure Storage versleuteling. Door de klant beheerde sleutels bieden u de mogelijkheid om toegangs beheer te maken, te draaien, uit te scha kelen en in te trekken.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 09/17/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 036322e73470ad4aa25de03e95c506e9f04496d8
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/29/2019
ms.locfileid: "71670987"
---
# <a name="configure-customer-managed-keys-for-azure-storage-encryption-from-azure-cli"></a>Door de klant beheerde sleutels configureren voor Azure Storage versleuteling van Azure CLI

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

In dit artikel wordt beschreven hoe u een sleutel kluis kunt configureren met door de klant beheerde sleutels met behulp van Azure CLI.

> [!IMPORTANT]
> Het gebruik van door de klant beheerde sleutels met Azure Storage versleuteling vereist dat er voor de sleutel kluis twee vereiste eigenschappen zijn geconfigureerd, worden **verwijderd** en kunnen **niet worden**verwijderd. Deze eigenschappen zijn standaard ingeschakeld wanneer u een nieuwe sleutel kluis maakt in de Azure Portal. Als u deze eigenschappen echter wilt inschakelen voor een bestaande sleutel kluis, moet u Power shell of Azure CLI gebruiken.
> Alleen RSA-sleutels en sleutel grootte 2048 worden ondersteund.

## <a name="assign-an-identity-to-the-storage-account"></a>Een identiteit toewijzen aan het opslag account

Als u door de klant beheerde sleutels voor uw opslag account wilt inschakelen, moet u eerst een door het systeem toegewezen beheerde identiteit toewijzen aan het opslag account. U gebruikt deze beheerde identiteit om de machtigingen voor het opslag account te verlenen voor toegang tot de sleutel kluis.

Als u een beheerde identiteit wilt toewijzen met behulp van Azure CLI, roept u de [Update AZ Storage account](/cli/azure/storage/account#az-storage-account-update)aan. Vergeet niet om de waarden van de tijdelijke aanduidingen tussen vier Kante haken te vervangen door uw eigen waarden.

```azurecli-interactive
az account set --subscription <subscription-id>

az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity
```

Voor meer informatie over het configureren van door het systeem toegewezen beheerde identiteiten met Azure CLI raadpleegt u [Managed Identities voor Azure resources configureren op een virtuele Azure-machine met behulp van Azure cli](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md).

## <a name="create-a-new-key-vault"></a>Een nieuwe sleutel kluis maken

De sleutel kluis die u gebruikt voor het opslaan van door de klant beheerde sleutels voor Azure Storage versleuteling moet twee sleutel beveiligings instellingen hebben ingeschakeld, **verwijderen** en **niet wissen**. Als u een nieuwe sleutel kluis met Power shell of Azure CLI wilt maken en u deze instellingen hebt ingeschakeld, voert u de volgende opdrachten uit. Vergeet niet om de waarden van de tijdelijke aanduidingen tussen vier Kante haken te vervangen door uw eigen waarden. 

Als u een nieuwe sleutel kluis wilt maken met behulp van Azure CLI, roept u de opdracht [AZ Key kluis Create](/cli/azure/keyvault#az-keyvault-create)aan. Vergeet niet om de waarden van de tijdelijke aanduidingen tussen vier Kante haken te vervangen door uw eigen waarden.

```azurecli-interactive
az keyvault create \
    --name <key-vault> \
    --resource-group <resource_group> \
    --location <region> \
    --enable-soft-delete \
    --enable-purge-protection
```

## <a name="configure-the-key-vault-access-policy"></a>Het toegangs beleid voor de sleutel kluis configureren

Configureer vervolgens het toegangs beleid voor de sleutel kluis zodat het opslag account toegang heeft tot het. In deze stap gebruikt u de beheerde identiteit die u eerder aan het opslag account hebt toegewezen.

Om het toegangs beleid voor de sleutel kluis in te stellen, roept u het [beleid AZ set-Policy](/cli/azure/keyvault#az-keyvault-set-policy). Vergeet niet om de waarden van de tijdelijke aanduidingen tussen vier Kante haken te vervangen door uw eigen waarden.

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
    --key-permissions get recover unwrapKey wrapKey
```

## <a name="create-a-new-key"></a>Een nieuwe sleutel maken

Maak vervolgens een sleutel in de sleutel kluis. Als u een sleutel wilt maken, roept u de [sleutel AZ Key kluis Create](/cli/azure/keyvault/key#az-keyvault-key-create)op. Vergeet niet om de waarden van de tijdelijke aanduidingen tussen vier Kante haken te vervangen door uw eigen waarden.

```azurecli-interactive
az keyvault key create
    --name <key> \
    --vault-name <key-vault>
```

## <a name="configure-encryption-with-customer-managed-keys"></a>Versleuteling configureren met door de klant beheerde sleutels

Azure Storage versleuteling maakt standaard gebruik van door micro soft beheerde sleutels. Configureer uw Azure Storage-account voor door de klant beheerde sleutels en geef de sleutel op die u wilt koppelen aan het opslag account.

Roep [AZ Storage account update](/cli/azure/storage/account#az-storage-account-update)aan om de versleutelings instellingen van het opslag account bij te werken. In dit voor beeld worden ook query's uitgevoerd voor de sleutel kluis-URI en de sleutel versie. beide waarden zijn nodig om de sleutel te koppelen aan het opslag account. Vergeet niet om de waarden van de tijdelijke aanduidingen tussen vier Kante haken te vervangen door uw eigen waarden.

```azurecli-interactive
key_vault_uri=$(az keyvault show \
    --name <key-vault> \
    --resource-group <resource_group> \
    --query properties.vaultUri \
    --output tsv)
key_version=$(az keyvault key list-versions \
    --name <key> \
    --vault-name <key-vault> \
    --query [].kid \
    --output tsv | cut -d '/' -f 6)
az storage account update 
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-version $key_version \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $key_vault_uri
```

## <a name="update-the-key-version"></a>De sleutel versie bijwerken

Wanneer u een nieuwe versie van een sleutel maakt, moet u het opslag account bijwerken voor gebruik van de nieuwe versie. Eerst moet u een query uitvoeren voor de sleutel kluis-URI door het aanroepen van [AZ Key kluis show](/cli/azure/keyvault#az-keyvault-show), en voor de sleutel versie door het aanroepen van [AZ sleutel kluis Key List-versies](/cli/azure/keyvault/key#az-keyvault-key-list-versions). Roep vervolgens [AZ Storage account update](/cli/azure/storage/account#az-storage-account-update) aan om de versleutelings instellingen van het opslag account bij te werken om de nieuwe versie van de sleutel te gebruiken, zoals wordt weer gegeven in de vorige sectie.

## <a name="next-steps"></a>Volgende stappen

- [Azure Storage versleuteling voor Data-at-rest](storage-service-encryption.md) 
- [Wat is Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)?
