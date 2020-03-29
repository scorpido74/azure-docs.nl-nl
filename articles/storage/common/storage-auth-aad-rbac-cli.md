---
title: Azure CLI gebruiken om een RBAC-rol toe te wijzen voor gegevenstoegang
titleSuffix: Azure Storage
description: Meer informatie over het gebruik van Azure CLI om machtigingen toe te wijzen aan een Azure Active Directory-beveiligingsprincipal met rbac (Role-based access control). Azure Storage ondersteunt ingebouwde en aangepaste RBAC-rollen voor verificatie via Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: abe35f3193e2d7ff9a949ca7cd330cb58da2b78c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74891965"
---
# <a name="use-azure-cli-to-assign-an-rbac-role-for-access-to-blob-and-queue-data"></a>Azure CLI gebruiken om een RBAC-rol toe te wijzen voor toegang tot blob- en wachtrijgegevens

Azure Active Directory (Azure AD) geeft toegangsrechten voor beveiligde bronnen door middel [van rbac (role-based access control).](../../role-based-access-control/overview.md) Azure Storage definieert een set ingebouwde RBAC-rollen die veelvoorkomende sets machtigingen omvatten die worden gebruikt om toegang te krijgen tot blob- of wachtrijgegevens.

Wanneer een RBAC-rol is toegewezen aan een Azure AD-beveiligingsprincipal, verleent Azure toegang tot deze bronnen voor die beveiligingsprincipal. Toegang kan worden beperkt tot het niveau van het abonnement, de resourcegroep, het opslagaccount of een afzonderlijke container of wachtrij. Een Azure AD-beveiligingsprincipal kan een gebruiker, een groep, een hoofd van de toepassingsservice of een [beheerde identiteit voor Azure-resources](../../active-directory/managed-identities-azure-resources/overview.md)zijn.

In dit artikel wordt beschreven hoe u Azure CLI gebruiken om ingebouwde RBAC-rollen weer te geven en aan gebruikers toe te wijzen. Zie [Azure Command-Line Interface (CLI) voor](https://docs.microsoft.com/cli/azure)meer informatie over het gebruik van Azure CLI.

## <a name="rbac-roles-for-blobs-and-queues"></a>RBAC-rollen voor blobs en wachtrijen

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>Resourcebereik bepalen

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="list-available-rbac-roles"></a>Beschikbare RBAC-rollen weergeven

Als u beschikbare ingebouwde RBAC-rollen met Azure CLI wilt weergeven, gebruikt u de [opdracht lijst met lijst met az-rollendefinitie:](/cli/azure/role/definition#az-role-definition-list)

```azurecli-interactive
az role definition list --out table
```

U ziet de ingebouwde Azure Storage-gegevensrollen die worden weergegeven, samen met andere ingebouwde rollen voor Azure:

```Example
Storage Blob Data Contributor             Allows for read, write and delete access to Azure Storage blob containers and data
Storage Blob Data Owner                   Allows for full access to Azure Storage blob containers and data, including assigning POSIX access control.
Storage Blob Data Reader                  Allows for read access to Azure Storage blob containers and data
Storage Queue Data Contributor            Allows for read, write, and delete access to Azure Storage queues and queue messages
Storage Queue Data Message Processor      Allows for peek, receive, and delete access to Azure Storage queue messages
Storage Queue Data Message Sender         Allows for sending of Azure Storage queue messages
Storage Queue Data Reader                 Allows for read access to Azure Storage queues and queue messages
```

## <a name="assign-an-rbac-role-to-a-security-principal"></a>Een RBAC-rol toewijzen aan een beveiligingsprincipal

Als u een RBAC-rol wilt toewijzen aan een beveiligingsprincipal, gebruikt u de opdracht [Voor het maken van az-rollen.](/cli/azure/role/assignment#az-role-assignment-create) De indeling van de opdracht kan verschillen op basis van het bereik van de toewijzing. In de volgende voorbeelden ziet u hoe u een rol aan een gebruiker op verschillende scopes toewijst, maar u dezelfde opdracht gebruiken om een rol toe te wijzen aan een beveiligingsprincipal.

### <a name="container-scope"></a>Containerbereik

Als u een rol wilt toewijzen die aan een container is `--scope` toegewezen, geeft u een tekenreeks op die het bereik van de container voor de parameter bevat. De ruimte voor een container is in de vorm:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container>
```

In het volgende voorbeeld wordt de rol **opslagblobgegevensbijdrager** toewijst aan een gebruiker die wordt verdeeld over het niveau van de container. Zorg ervoor dat u de voorbeeldwaarden en de tijdelijke aanduidingswaarden tussen haakjes vervangt door uw eigen waarden:

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container>"
```

### <a name="queue-scope"></a>Wachtrijbereik

Als u een rol wilt toewijzen die aan een wachtrij is `--scope` toegewezen, geeft u een tekenreeks op met de bereik van de wachtrij voor de parameter. De ruimte voor een wachtrij is in het formulier:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/<queue>
```

In het volgende voorbeeld wordt de rol van de **bijdrage voor opslagwachtrijgegevens aan** een gebruiker toegesproken die is geplaatst op het niveau van de wachtrij. Zorg ervoor dat u de voorbeeldwaarden en de tijdelijke aanduidingswaarden tussen haakjes vervangt door uw eigen waarden:

```azurecli-interactive
az role assignment create \
    --role "Storage Queue Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/<queue>"
```

### <a name="storage-account-scope"></a>Bereik van opslagaccount

Als u een rol wilt toewijzen die aan het opslagaccount `--scope` is toegewezen, geeft u het bereik van de bron van het opslagaccount voor de parameter op. De ruimte voor een opslagaccount is in het formulier:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

In het volgende voorbeeld ziet u hoe u de rol **Storage Blob Data Reader** toewijst aan een gebruiker op het niveau van het opslagaccount. Zorg ervoor dat u de voorbeeldwaarden vervangt door uw eigen waarden: \

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Reader" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

### <a name="resource-group-scope"></a>Bereik van resourcegroepen

Als u een rol wilt toewijzen die aan de resourcegroep `--resource-group` is toegewezen, geeft u de naam of id van de resourcegroep voor de parameter op. In het volgende voorbeeld wordt de rol Van de **gegevenslezer van** de opslagwachtrij aan een gebruiker op het niveau van de resourcegroep toegerichtingd. Zorg ervoor dat u de voorbeeldwaarden en tijdelijke aanduidingswaarden tussen haakjes vervangt door uw eigen waarden:

```azurecli-interactive
az role assignment create \
    --role "Storage Queue Data Reader" \
    --assignee <email> \
    --resource-group <resource-group>
```

### <a name="subscription-scope"></a>Abonnementsbereik

Als u een rol wilt toewijzen die aan het `--scope` abonnement is toegewezen, geeft u de ruimte voor het abonnement voor de parameter op. De ruimte voor een abonnement is in het formulier:

```
/subscriptions/<subscription>
```

In het volgende voorbeeld ziet u hoe u de rol **Storage Blob Data Reader** toewijst aan een gebruiker op het niveau van het opslagaccount. Zorg ervoor dat u de voorbeeldwaarden vervangt door uw eigen waarden: 

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Reader" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>"
```

## <a name="next-steps"></a>Volgende stappen

- [Toegang tot Azure-resources beheren met behulp van RBAC en Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)
- [Toegang verlenen tot Azure blob- en wachtrijgegevens met RBAC met Azure PowerShell](storage-auth-aad-rbac-powershell.md)
- [Toegang verlenen tot Azure blob en wachtrijgegevens met RBAC in de Azure-portal](storage-auth-aad-rbac-portal.md)
