---
title: Gebruik Azure PowerShell voor het beheren van Azure AD-toegangs rechten voor Blob-en wachtrij gegevens met RBAC-Azure Storage
description: Gebruik Azure PowerShell om toegang toe te wijzen aan containers en wacht rijen met op rollen gebaseerd toegangs beheer (RBAC). Azure Storage ondersteunt ingebouwde en aangepaste RBAC-rollen voor verificatie via Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 967e1754ec4be504669e176a5643186d08efb9d4
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/29/2019
ms.locfileid: "71673182"
---
# <a name="grant-access-to-azure-blob-and-queue-data-with-rbac-using-powershell"></a>Toegang verlenen tot Azure Blob-en wachtrij gegevens met RBAC met behulp van Power shell

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

Gebruik de opdracht [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) om een RBAC-rol toe te wijzen aan een beveiligingsprincipal. De indeling van de opdracht kan verschillen op basis van het bereik van de toewijzing. In de volgende voor beelden ziet u hoe u een rol toewijst aan een gebruiker in verschillende bereiken, maar u kunt dezelfde opdracht gebruiken om een rol toe te wijzen aan een beveiligings-principal.

### <a name="container-scope"></a>Container bereik

Als u een rollen bereik wilt toewijzen aan een container, geeft u een teken reeks met het bereik van de container voor de para meter `--scope` op. Het bereik voor een container bevindt zich in de vorm:

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

Als u een rollen bereik wilt toewijzen aan een wachtrij, geeft u een teken reeks met het bereik van de wachtrij voor de para meter `--scope` op. Het bereik voor een wachtrij bevindt zich in de vorm:

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

Geef het bereik van de bron van het opslag account voor de para meter `--scope` op om een rollen bereik toe te wijzen aan het opslag account. Het bereik van een opslag account bevindt zich in de vorm:

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

Als u een rollen bereik wilt toewijzen aan de resource groep, geeft u de naam van de resource groep of ID voor de para meter `--resource-group` op. In het volgende voor beeld wordt de rol van **gegevens lezer van de opslag wachtrij** toegewezen aan een gebruiker op het niveau van de resource groep. Vervang de voorbeeld waarden en de waarden van de tijdelijke aanduiding tussen vier Kante haken door uw eigen waarden: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Queue Data Reader" `
    -ResourceGroupName "sample-resource-group"
```

### <a name="subscription-scope"></a>Abonnements bereik

Als u een rollen bereik wilt toewijzen aan het abonnement, geeft u het bereik voor het abonnement voor de para meter `--scope` op. Het bereik voor een abonnement bevindt zich in de vorm:

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
