---
title: Power shell gebruiken om een Azure AD-app te maken voor toegang tot de Azure Media Services-API | Microsoft Docs
description: Meer informatie over het gebruik van Power shell om een Azure Active Directory-app (Azure AD) te maken en deze in te stellen voor toegang tot de Azure Media Services-API.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 2f9568d175d564547e58bc3584174cb0f239dd49
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/22/2020
ms.locfileid: "92425213"
---
# <a name="use-powershell-to-create-an-azure-ad-app-to-use-with-the-azure-media-services-api"></a>Power shell gebruiken om een Azure AD-app te maken voor gebruik met de Azure Media Services-API

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Er worden geen nieuwe functies of functionaliteit meer aan Media Services v2. toegevoegd. <br/>Bekijk de nieuwste versie [Media Services v3](../latest/index.yml). Zie ook [migratie richtlijnen van v2 naar v3](../latest/migrate-from-v2-to-v3.md)

Meer informatie over het gebruik van een Power shell-script om een Azure Active Directory (Azure AD)-toepassing en Service-Principal te maken voor toegang tot Azure Media Services resources.  

## <a name="prerequisites"></a>Vereisten

- Een Azure-account. Als u geen account hebt, kunt u beginnen met een [gratis proef versie van Azure](https://azure.microsoft.com/pricing/free-trial/). 
- Een Media Services-account. Zie [een Azure Media Services-account maken in de Azure Portal](media-services-portal-create-account.md)voor meer informatie.

- Azure PowerShell. Zie [Azure PowerShell gebruiken](/powershell/azure/)voor meer informatie.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="create-an-azure-ad-app-by-using-powershell"></a>Een Azure AD-app maken met behulp van Power shell  

```powershell
Connect-AzAccount
Import-Module Az.Resources
Set-AzContext -SubscriptionId $SubscriptionId
$ServicePrincipal = New-AzADServicePrincipal -DisplayName $ApplicationDisplayName -Password $Password

Get-AzADServicePrincipal -ObjectId $ServicePrincipal.Id 
$NewRole = $null
$Scope = "/subscriptions/your subscription id/resourceGroups/userresourcegroup/providers/microsoft.media/mediaservices/your media account"

$Retries = 0;While ($NewRole -eq $null -and $Retries -le 6)
{
    # Sleep here for a few seconds to allow the service principal application to become active (usually, it will take only a couple of seconds)
    Sleep 15
    New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $ServicePrincipal.ApplicationId -Scope $Scope | Write-Verbose -ErrorAction SilentlyContinue
    $NewRole = Get-AzRoleAssignment -ServicePrincipalName $ServicePrincipal.ApplicationId -ErrorAction SilentlyContinue
    $Retries++;
}
```

Raadpleeg voor meer informatie de volgende artikelen:

- [Azure PowerShell gebruiken om een service-principal te maken voor toegang tot resources](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)
- [Azure-roltoewijzingen toevoegen of verwijderen met behulp van Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)
- [Daemon-apps hand matig configureren met behulp van certificaten](https://github.com/azure-samples/active-directory-dotnetcore-daemon-v2)

## <a name="next-steps"></a>Volgende stappen

Ga aan de slag met [het uploaden van bestanden naar uw account](media-services-portal-upload-files.md).
