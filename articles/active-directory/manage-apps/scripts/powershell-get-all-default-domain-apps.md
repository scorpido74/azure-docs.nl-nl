---
title: Power shell-voor beeld-toepassings proxy-apps met standaard domein
description: Power shell-voor beeld met een lijst met alle Azure Active Directory (Azure AD) toepassings proxy toepassingen die gebruikmaken van standaard domeinen (. msappproxy.net).
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
ms.openlocfilehash: 40a9f7ce86981d6f03b25237a7a73869fd5fd4ba
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75483373"
---
# <a name="get-all-application-proxy-apps-using-default-domains-msappproxynet"></a>Alle toepassings proxy-apps ophalen met behulp van standaard domeinen (. msappproxy.net)

In dit Power shell-voorbeeld script wordt een lijst weer gegeven met alle Azure Active Directory-toepassings proxy toepassingen (Azure AD) die gebruikmaken van standaard domeinen (. msappproxy.net).

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Voor dit voor beeld is de [AzureAD v2 Power shell voor Graph module](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) (AzureAD) of de [Preview-versie van de AzureAD v2 Power shell voor Graph-module](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) (AzureADPreview) vereist.

## <a name="sample-script"></a>Voorbeeldscript

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-default-domain-apps.ps1 "Get all Application Proxy apps using default domains (.msappproxy.net)")]

## <a name="script-explanation"></a>Uitleg van het script

| Opdracht | Opmerkingen |
|---|---|
|[Get-AzureADServicePrincipal](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0) | Hiermee wordt een Service-Principal opgehaald. |
|[Get-AzureADApplication](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplication?view=azureadps-2.0) | Hiermee haalt u een Azure AD-toepassing op. |
|[Get-AzureADApplicationProxyApplication](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplicationproxyapplication?view=azureadps-2.0) | Hiermee haalt u een toepassing op die is geconfigureerd voor toepassings proxy in azure AD. |

## <a name="next-steps"></a>Volgende stappen

Zie [overzicht van Azure AD Power shell-module](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0)voor meer informatie over de Azure AD Power shell-module.

Raadpleeg voor andere Power shell-voor beelden voor toepassings proxy [Azure AD Power shell-voor beelden voor azure AD-toepassingsproxy](../application-proxy-powershell-samples.md).