---
title: Power shell gebruiken om een RBAC-rol toe te wijzen voor gegevens toegang
titleSuffix: Azure Storage
description: Meer informatie over het gebruik van Power shell om machtigingen toe te wijzen aan een Azure Active Directory beveiligingsprincipal met op rollen gebaseerd toegangs beheer (RBAC). Azure Storage ondersteunt ingebouwde en aangepaste RBAC-rollen voor verificatie via Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: e95d25e4f26abc48f2a734cf8456739e6ef55b84
ms.sourcegitcommit: ad66392df535c370ba22d36a71e1bbc8b0eedbe3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/16/2020
ms.locfileid: "84807698"
---
# <a name="use-powershell-to-assign-an-rbac-role-for-access-to-blob-and-queue-data"></a>Power shell gebruiken om een RBAC-rol toe te wijzen voor toegang tot Blob-en wachtrij gegevens

Met Azure Active Directory (Azure AD) worden de toegangs rechten voor beveiligde bronnen geautoriseerd via [op rollen gebaseerd toegangs beheer (RBAC)](../../role-based-access-control/overview.md). Azure Storage definieert een set ingebouwde RBAC-rollen die algemene sets machtigingen omvatten die worden gebruikt voor toegang tot containers of wacht rijen.

Wanneer een RBAC-rol is toegewezen aan een Azure AD-beveiligings-principal, verleent Azure toegang tot de resources voor die beveiligings-principal. De toegang kan worden beperkt tot het niveau van het abonnement, de resource groep, het opslag account of een afzonderlijke container of wachtrij. Een beveiligings-principal voor Azure AD kan een gebruiker, een groep, een service-principal van de toepassing of een [beheerde identiteit voor Azure-resources](../../active-directory/managed-identities-azure-resources/overview.md)zijn.

In dit artikel wordt beschreven hoe u Azure PowerShell kunt gebruiken om ingebouwde RBAC-rollen weer te geven en toe te wijzen aan gebruikers. Zie [overzicht van Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)voor meer informatie over het gebruik van Azure PowerShell.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="rbac-roles-for-blobs-and-queues"></a>RBAC-rollen voor blobs en wacht rijen

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>Resource bereik bepalen

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="list-available-rbac-roles"></a>Beschik bare RBAC-rollen weer geven

Als u beschik bare ingebouwde RBAC-rollen met Azure PowerShell wilt weer geven, gebruikt u de opdracht [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) :

```powershell
Get-AzRoleDefinition | FT Name, Description
```

De ingebouwde Azure Storage gegevens rollen worden weer gegeven, samen met andere ingebouwde rollen voor Azure:

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

Gebruik de opdracht [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) om een RBAC-rol toe te wijzen aan een beveiligingsprincipal. De indeling van de opdracht kan verschillen op basis van het bereik van de toewijzing. Als u de opdracht wilt uitvoeren, moet u de rol eigenaar of Inzender hebben toegewezen aan het overeenkomstige bereik. In de volgende voor beelden ziet u hoe u een rol toewijst aan een gebruiker in verschillende bereiken, maar u kunt dezelfde opdracht gebruiken om een rol toe te wijzen aan een beveiligings-principal.

### <a name="container-scope"></a>Container bereik

Als u een Role bereik wilt toewijzen aan een container, geeft u een teken reeks op met het bereik van de container voor de `--scope` para meter. Het bereik voor een container bevindt zich in de vorm:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container-name>
```

In het volgende voor beeld wordt de rol **Storage BLOB data Inzender** toegewezen aan een gebruiker, met een container met de naam *sample-container*. Vervang de voorbeeld waarden en de waarden van de tijdelijke aanduiding tussen vier Kante haken door uw eigen waarden: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/sample-container"
```

### <a name="queue-scope"></a>Wachtrij bereik

Als u een Role bereik aan een wachtrij wilt toewijzen, geeft u een teken reeks met het bereik van de wachtrij voor de `--scope` para meter op. Het bereik voor een wachtrij bevindt zich in de vorm:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/<queue-name>
```

In het volgende voor beeld wordt de rol Inzender voor gegevens van de **opslag wachtrij** toegewezen aan een gebruiker, met het bereik van een wachtrij met de naam *voorbeeld wachtrij*. Vervang de voorbeeld waarden en de waarden van de tijdelijke aanduiding tussen vier Kante haken door uw eigen waarden: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Queue Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/sample-queue"
```

### <a name="storage-account-scope"></a>Bereik van opslag account

Als u een rollen bereik wilt toewijzen aan het opslag account, geeft u het bereik van de bron van het opslag account op voor de `--scope` para meter. Het bereik van een opslag account bevindt zich in de vorm:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

In het volgende voor beeld ziet u hoe u de rol van **Blob Storage-gegevens lezer** kunt bereiken voor een gebruiker op het niveau van het opslag account. Zorg ervoor dat u de voorbeeld waarden vervangt door uw eigen waarden: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Reader" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

### <a name="resource-group-scope"></a>Bereik van de resource groep

Als u een rollen bereik wilt toewijzen aan de resource groep, geeft u de naam van de resource groep of ID voor de `--resource-group` para meter op. In het volgende voor beeld wordt de rol van **gegevens lezer van de opslag wachtrij** toegewezen aan een gebruiker op het niveau van de resource groep. Vervang de voorbeeld waarden en de waarden van de tijdelijke aanduiding tussen vier Kante haken door uw eigen waarden: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Queue Data Reader" `
    -ResourceGroupName "sample-resource-group"
```

### <a name="subscription-scope"></a>Abonnements bereik

Als u een rollen bereik wilt toewijzen aan het abonnement, geeft u het bereik op voor het abonnement voor de `--scope` para meter. Het bereik voor een abonnement bevindt zich in de vorm:

```
/subscriptions/<subscription>
```

In het volgende voor beeld ziet u hoe u de rol **Storage BLOB data Reader** toewijst aan een gebruiker op het niveau van het opslag account. Zorg ervoor dat u de voorbeeld waarden vervangt door uw eigen waarden: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Reader" `
    -Scope  "/subscriptions/<subscription>"
```

## <a name="next-steps"></a>Volgende stappen

- [Toegang tot Azure-resources beheren met behulp van RBAC en Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)
- [Toegang verlenen tot Azure blob en wachtrijgegevens met RBAC met behulp van Azure CLI](storage-auth-aad-rbac-cli.md)
- [Toegang verlenen tot Azure blob en wachtrijgegevens met RBAC in de Azure-portal](storage-auth-aad-rbac-portal.md)
