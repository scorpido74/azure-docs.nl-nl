---
title: 'Power shell-voor beeld: alle toepassings proxy-apps met een beleid weer geven'
description: Power shell-voor beeld met een lijst met alle Azure Active Directory (Azure AD) toepassings proxy toepassingen in uw directory die een levens duur token beleid hebben.
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
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75482125"
---
# <a name="get-all-application-proxy-apps-with-a-token-lifetime-policy"></a>Alle toepassings proxy-apps met een levens duur beleid van een Token ophalen

In dit voor beeld van een Power shell-script wordt een lijst weer gegeven met alle Azure Active Directory-toepassings proxy toepassingen (Azure AD) in uw directory met een levens duur van tokens en worden details over het beleid weer gegeven.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Voor dit voor beeld is de [Preview-versie van de AzureAD v2 Power shell for Graph-module](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) (AzureADPreview) vereist.

## <a name="sample-script"></a>Voorbeeldscript

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-appproxy-apps-with-policy.ps1 "Get all Application Proxy apps with a token lifetime policy")]

## <a name="script-explanation"></a>Uitleg van het script

| Opdracht | Opmerkingen |
|---|---|
|[Get-Azureadserviceprincipal namelijk niet](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0) | Hiermee wordt een Service-Principal opgehaald. |
|[Get-AzureADApplication](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplication?view=azureadps-2.0) | Hiermee haalt u een Azure AD-toepassing op. |
|[Get-AzureADPolicy](https://docs.microsoft.com/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview) | Hiermee wordt een beleid opgehaald in azure AD. |
|[Get-AzureADServicePrincipalPolicy](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipalpolicy?view=azureadps-2.0-preview) | Hiermee wordt het beleid van een Service-Principal in azure AD opgehaald. |


## <a name="next-steps"></a>Volgende stappen

Zie [overzicht van Azure AD Power shell-module](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0)voor meer informatie over de Azure AD Power shell-module.

Raadpleeg voor andere Power shell-voor beelden voor toepassings proxy [Azure AD Power shell-voor beelden voor azure AD-toepassingsproxy](../application-proxy-powershell-samples.md).