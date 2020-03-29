---
title: PowerShell gebruiken om een Azure AD-app te maken om toegang te krijgen tot de Azure Media Services API | Microsoft Documenten
description: Meer informatie over het gebruik van PowerShell om een Azure Active Directory-app (Azure AD) te maken en deze in te stellen voor toegang tot de Azure Media Services API.
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
ms.openlocfilehash: ff7f8bc27d358c667b10c0bd3383e78b20494303
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "64680120"
---
# <a name="use-powershell-to-create-an-azure-ad-app-to-use-with-the-azure-media-services-api"></a>PowerShell gebruiken om een Azure AD-app te maken die u gebruiken met de Azure Media Services-API

> [!NOTE]
> Er worden geen nieuwe functies of functionaliteit meer aan Media Services v2. toegevoegd. <br/>Bekijk de nieuwste versie, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Zie ook [migratierichtlijnen van v2 naar v3](../latest/migrate-from-v2-to-v3.md)

Meer informatie over het gebruik van een PowerShell-script om een Azure Active Directory-toepassing en serviceprincipal te maken voor toegang tot Azure Media Services-bronnen.  

## <a name="prerequisites"></a>Vereisten

- Een Azure-account. Als u geen account hebt, begint u met een [gratis proefversie van Azure.](https://azure.microsoft.com/pricing/free-trial/) 
- Een Media Services-account. Zie [Een Azure Media Services-account maken in de Azure-portal](media-services-portal-create-account.md)voor meer informatie.

- Azure PowerShell. Zie [Azure PowerShell gebruiken voor](https://docs.microsoft.com/powershell/azure/overview)meer informatie.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="create-an-azure-ad-app-by-using-powershell"></a>Een Azure AD-app maken met PowerShell  

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
- [Toegangsbeheer op basis van rollen beheren met Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)
- [Daemon-apps handmatig configureren met behulp van certificaten](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential/blob/master/Manual-Configuration-Steps.md#add-the-certificate-as-a-key-for-the-todolistdaemonwithcert-application-in-azure-ad)

## <a name="next-steps"></a>Volgende stappen

Ga aan de slag met [het uploaden van bestanden naar je account.](media-services-portal-upload-files.md)
