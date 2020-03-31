---
title: PowerShell-voorbeeld - Basisgegevens voor toepassingsproxy-apps weergeven
description: PowerShell-voorbeeld met Azure Active Directory-toepassingsproxytoepassingen (Azure Active Directory) samen met de toepassings-id (AppId), naam (DisplayName) en object-id (ObjId).
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 009aabb8b742a752a29d775efbd11b153588c09b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75482216"
---
# <a name="get-all-application-proxy-apps-and-list-basic-information"></a>Alle Application Proxy-apps downloaden en basisgegevens aanbieden

In dit voorbeeld van PowerShell-scriptvindt u informatie over alle Azure Active Directory-toepassingsproxytoepassingen (Azure AD), waaronder de toepassings-id (AppId), naam (DisplayName) en object-id (ObjId).

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Voor dit voorbeeld is de [AzureAD V2 PowerShell for Graph-module](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) (AzureAD) of de [AzureAD V2 PowerShell voor graph-modulepreviewversie](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) (AzureADPreview) vereist.
## <a name="sample-script"></a>Voorbeeldscript

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-appproxy-apps-basic.ps1 "Get all Application Proxy apps")]

## <a name="script-explanation"></a>Uitleg van het script

| Opdracht | Opmerkingen |
|---|---|
|[Get-AzureADServicePrincipal](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0) | Krijgt een service principal. |

## <a name="next-steps"></a>Volgende stappen

Zie azure [AD PowerShell-moduleoverzicht](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0)voor meer informatie over de Azure AD PowerShell-module.

Zie [Azure AD PowerShell-voorbeelden voor](../application-proxy-powershell-samples.md)andere PowerShell-voorbeelden voor andere PowerShell-toepassingen.