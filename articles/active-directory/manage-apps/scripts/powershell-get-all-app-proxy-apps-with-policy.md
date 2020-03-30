---
title: PowerShell-voorbeeld - Alle toepassingsproxy-apps met een beleid weergeven
description: PowerShell-voorbeeld met alle Azure Active Directory-toepassingsproxytoepassingen (Azure Active Directory) in uw map met een levenslang tokenbeleid.
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
ms.openlocfilehash: c2ad7b769dc890917e5364fe57582acdfe16acac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75482125"
---
# <a name="get-all-application-proxy-apps-with-a-token-lifetime-policy"></a>Alle Application Proxy-apps downloaden met een tokenlevenslangbeleid

In dit voorbeeld van PowerShell-script worden alle Azure Active Directory-toepassingsproxytoepassingen (Azure AD) in uw map weergegeven met een tokenlevenslangbeleid en worden details over het beleid weergegeven.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Voor dit voorbeeld is de [azuread V2 PowerShell voor previewversie van de Graph-module](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) (AzureADPreview) vereist.

## <a name="sample-script"></a>Voorbeeldscript

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-appproxy-apps-with-policy.ps1 "Get all Application Proxy apps with a token lifetime policy")]

## <a name="script-explanation"></a>Uitleg van het script

| Opdracht | Opmerkingen |
|---|---|
|[Get-AzureADServicePrincipal](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0) | Krijgt een service principal. |
|[Get-AzureADApplication](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplication?view=azureadps-2.0) | Hier krijgt u een Azure AD-toepassing. |
|[Get-AzureADPolicy](https://docs.microsoft.com/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview) | Krijgt een beleid in Azure AD. |
|[Get-AzureADServicePrincipalPolicy](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipalpolicy?view=azureadps-2.0-preview) | Krijgt het beleid van een serviceprincipal in Azure AD. |


## <a name="next-steps"></a>Volgende stappen

Zie azure [AD PowerShell-moduleoverzicht](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0)voor meer informatie over de Azure AD PowerShell-module.

Zie [Azure AD PowerShell-voorbeelden voor](../application-proxy-powershell-samples.md)andere PowerShell-voorbeelden voor andere PowerShell-toepassingen.