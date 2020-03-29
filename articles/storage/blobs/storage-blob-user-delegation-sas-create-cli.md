---
title: Azure CLI gebruiken om een SAS voor gebruikersdelegatie sas te maken voor een container of blob
titleSuffix: Azure Storage
description: Meer informatie over het maken van een SAS voor gebruikersoverdrachten met Azure Active Directory-referenties met Azure CLI.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: blobs
ms.openlocfilehash: e1a81b25042501a166cee122279d21e3702cd419
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75371986"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-the-azure-cli"></a>Een SAS voor gebruikersdelegatie maken voor een container of blob met de Azure CLI

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

In dit artikel ziet u hoe u Azure Active Directory-referenties (Azure AD) gebruikt om een SAS voor gebruikersdelegatie sas te maken voor een container of blob met de Azure CLI.

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="install-the-latest-version-of-the-azure-cli"></a>De nieuwste versie van de Azure CLI installeren

Als u de Azure CLI wilt gebruiken om een SAS met Azure AD-referenties te beveiligen, moet u eerst ervoor zorgen dat u de nieuwste versie van Azure CLI hebt geïnstalleerd. Zie [Azure CLI installeren](/cli/azure/install-azure-cli)voor meer informatie over het installeren van de Azure CLI.

Als u een SAS voor gebruikersdelegatie wilt maken met de Azure CLI, controleert u of u versie 2.0.78 of hoger hebt geïnstalleerd. Als u de geïnstalleerde `az --version` versie wilt controleren, gebruikt u de opdracht.

## <a name="sign-in-with-azure-ad-credentials"></a>Aanmelden met Azure AD-referenties

Meld u aan bij de Azure CLI met uw Azure AD-referenties. Zie [Aanmelden met de Azure CLI](/cli/azure/authenticate-azure-cli) voor meer informatie.

## <a name="assign-permissions-with-rbac"></a>Machtigingen toewijzen met RBAC

Als u een SAS voor gebruikersoverdracht wilt maken vanuit Azure PowerShell, moet het Azure AD-account dat wordt gebruikt om u aan te melden bij Azure CLI, een rol worden toegewezen die de actie **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey** bevat. Met deze machtiging kan dat Azure AD-account de *sleutel voor gebruikersoverdracht*aanvragen. De gebruikersdelegatiesleutel wordt gebruikt om de gebruikersdelegatie SAS te ondertekenen. De rol die de actie **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey** biedt, moet worden toegewezen op het niveau van het opslagaccount, de brongroep of het abonnement.

Als u niet over voldoende machtigingen beschikt om RBAC-rollen toe te wijzen aan een Azure AD-beveiligingsprincipal, moet u mogelijk de eigenaar of beheerder van het account vragen om de benodigde machtigingen toe te wijzen.

In het volgende voorbeeld wordt de rol **opslagblobgegevensbijdrager toetekent,** waaronder de actie **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey.** De rol wordt verdeeld op het niveau van het opslagaccount.

Vergeet niet om plaatsaanduidingswaarden in hoekhaakjes te vervangen door uw eigen waarden:

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

Zie [Ingebouwde rollen voor Azure-resources voor](../../role-based-access-control/built-in-roles.md)meer informatie over de ingebouwde rollen die de actie **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey** bevatten.

## <a name="use-azure-ad-credentials-to-secure-a-sas"></a>Azure AD-referenties gebruiken om een SAS te beveiligen

Wanneer u een SAS voor gebruikersdelegatie maakt met de Azure CLI, wordt de gebruikersdelegatiesleutel die wordt gebruikt om de SAS te ondertekenen impliciet voor u gemaakt. De begin- en vervaldatum die u voor de SAS opgeeft, worden ook gebruikt als begin- en vervaldatum voor de gebruikersdelegatiesleutel.

Omdat het maximale interval waarop de gebruikersdelegatiesleutel geldig is, 7 dagen na de begindatum is, moet u een vervaldatum opgeven voor de SAS die binnen 7 dagen na de begintijd is. De SAS is ongeldig nadat de sleutel voor de delegatie van de gebruiker is verlopen, dus een SAS met een vervaldatum van meer dan 7 dagen is nog steeds slechts 7 dagen geldig.

Bij het maken van een `--auth-mode login` `--as-user parameters` gebruiker delegatie SAS, de en zijn vereist. Geef *aanmelding* op voor de `--auth-mode` parameter, zodat aanvragen voor Azure Storage zijn geautoriseerd met uw Azure AD-referenties. Geef `--as-user` de parameter op om aan te geven dat de geretourneerde SAS een SAS voor gebruikersdelegatie moet zijn.

### <a name="create-a-user-delegation-sas-for-a-container"></a>Een gebruikersdelegatie SAS voor een container maken

Als u een SAS voor gebruikersdelegatie wilt maken voor een container met de Azure CLI, roept u de opdracht [az-opslagcontainer generate-sas](/cli/azure/storage/container#az-storage-container-generate-sas) aan.

Ondersteunde machtigingen voor een gebruikersdelegatie SAS op een container zijn toevoegen, maken, verwijderen, Aanbieden, Lezen en Schrijven. Machtigingen kunnen afzonderlijk worden opgegeven of gecombineerd. Zie [SAS voor gebruikersdelegatie maken voor](/rest/api/storageservices/create-user-delegation-sas)meer informatie over deze machtigingen.

In het volgende voorbeeld wordt een SAS-token voor gebruikersdelegatie voor een container geretourneerd. Vergeet niet om de plaatsaanduidingswaarden tussen haakjes te vervangen door uw eigen waarden:

```azurecli-interactive
az storage container generate-sas \
    --account-name <storage-account> \
    --name <container> \
    --permissions acdlrw \
    --expiry <date-time> \
    --auth-mode login \
    --as-user
```

Het geretourneerde SAS-token voor gebruikersdelegeren is vergelijkbaar met:

```
se=2019-07-27&sp=r&sv=2018-11-09&sr=c&skoid=<skoid>&sktid=<sktid>&skt=2019-07-26T18%3A01%3A22Z&ske=2019-07-27T00%3A00%3A00Z&sks=b&skv=2018-11-09&sig=<signature>
```

### <a name="create-a-user-delegation-sas-for-a-blob"></a>Een SAS voor gebruikersdelegatie maken voor een blob

Als u een SAS voor gebruikersdelegatie wilt maken voor een blob met de Azure CLI, roept u de opdracht [az-opslagblob generate-sas](/cli/azure/storage/blob#az-storage-blob-generate-sas) aan.

Ondersteunde machtigingen voor een gebruikersdelegatie SAS op een blob zijn Toevoegen, Maken, Verwijderen, Lezen en Schrijven. Machtigingen kunnen afzonderlijk worden opgegeven of gecombineerd. Zie [SAS voor gebruikersdelegatie maken voor](/rest/api/storageservices/create-user-delegation-sas)meer informatie over deze machtigingen.

Met de volgende syntaxis retourneert een gebruikersdelegatie SAS voor een blob. In het `--full-uri` voorbeeld wordt de parameter opgegeven, waardoor de blob URI wordt geretourneerd met het SAS-token toegevoegd. Vergeet niet om de plaatsaanduidingswaarden tussen haakjes te vervangen door uw eigen waarden:

```azurecli-interactive
az storage blob generate-sas \
    --account-name <storage-account> \
    --container-name <container> \
    --name <blob> \
    --permissions acdrw \
    --expiry <date-time> \
    --auth-mode login \
    --as-user
    --full-uri
```

De teruggekeerde gebruikersdelegatie SAS URI is vergelijkbaar met:

```
https://storagesamples.blob.core.windows.net/sample-container/blob1.txt?se=2019-08-03&sp=rw&sv=2018-11-09&sr=b&skoid=<skoid>&sktid=<sktid>&skt=2019-08-02T2
2%3A32%3A01Z&ske=2019-08-03T00%3A00%3A00Z&sks=b&skv=2018-11-09&sig=<signature>
```

> [!NOTE]
> Een gebruikersdelegatie SAS ondersteunt geen definiërende machtigingen met een opgeslagen toegangsbeleid.

## <a name="revoke-a-user-delegation-sas"></a>Een gebruikersdelegatie SAS intrekken

Als u een sas voor gebruikersdeleger wilt intrekken uit de Azure CLI, roept u de opdracht [az-opslagaccount intrekken-delegatiesleutels](/cli/azure/storage/account#az-storage-account-revoke-delegation-keys) in. Met deze opdracht worden alle gebruikersdelegatiesleutels ingetrokken die zijn gekoppeld aan het opgegeven opslagaccount. Alle gedeelde toegangshandtekeningen die aan deze sleutels zijn gekoppeld, worden ongeldig verklaard.

Vergeet niet om plaatsaanduidingswaarden in hoekhaakjes te vervangen door uw eigen waarden:

```azurecli-interactive
az storage account revoke-delegation-keys \
    --name <storage-account> \
    --resource-group <resource-group>
```

> [!IMPORTANT]
> Zowel de gebruikersdelegatiesleutel als de RBAC-roltoewijzingen worden in de cache opgeslagen door Azure Storage, dus er kan een vertraging optreden tussen het moment waarop u het intrekkingsproces initieert en wanneer een bestaande gebruikersdelegatie SAS ongeldig wordt.

## <a name="next-steps"></a>Volgende stappen

- [Een SAS voor gebruikersdelegatie (REST API) maken](/rest/api/storageservices/create-user-delegation-sas)
- [De sleutelbewerking voor gebruikersdelegatie uitvoeren](/rest/api/storageservices/get-user-delegation-key)
