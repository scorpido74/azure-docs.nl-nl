---
title: 'Power shell-voor beeld: gebruikers & groepen voor de toepassings proxy-app weer geven'
description: Power shell-voor beeld met een lijst met alle gebruikers en groepen die zijn toegewezen aan een specifieke Azure Active Directory-toepassings proxy toepassing (Azure AD).
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
ms.openlocfilehash: aab5ac5d4712dd9e72fc0b9665d740859b5a6775
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "75483386"
---
# <a name="display-users-and-groups-assigned-to-an-application-proxy-application"></a>Gebruikers en groepen weer geven die zijn toegewezen aan een toepassings proxy toepassing

In dit voor beeld van een Power shell-script vindt u de gebruikers en groepen die zijn toegewezen aan een specifieke Azure Active Directory-toepassings proxy toepassing (Azure AD).

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Voor dit voor beeld is de [AzureAD v2 Power shell voor Graph module](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) (AzureAD) of de [Preview-versie van de AzureAD v2 Power shell voor Graph-module](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) (AzureADPreview) vereist.

## <a name="sample-script"></a>Voorbeeldscript

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/display-users-group-of-an-app.ps1 "Display users and groups assigned to an Application Proxy application")]

## <a name="script-explanation"></a>Uitleg van het script

| Opdracht | Opmerkingen |
|---|---|
| [Get-AzureADUser](https://docs.microsoft.com/powershell/module/AzureAD/get-azureaduser?view=azureadps-2.0)| Hiermee wordt een gebruiker opgehaald. |
| [Get-AzureADGroup](https://docs.microsoft.com/powershell/module/AzureAD/get-azureadgroup?view=azureadps-2.0)| Hiermee wordt een groep opgehaald. |
| [Get-AzureADServicePrincipal](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0) | Hiermee wordt een Service-Principal opgehaald. |
| [Get-AzureADUserAppRoleAssignment](https://docs.microsoft.com/powershell/module/AzureAD/get-azureaduserapproleassignment?view=azureadps-2.0) | Een roltoewijzing voor een gebruikers toepassing ophalen. |
| [Get-AzureADGroupAppRoleAssignment](https://docs.microsoft.com/powershell/module/AzureAD/get-azureadgroupapproleassignment?view=azureadps-2.0) | Een roltoewijzing voor een groeps toepassing ophalen. |

## <a name="next-steps"></a>Volgende stappen

Zie [overzicht van Azure AD Power shell-module](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0)voor meer informatie over de Azure AD Power shell-module.

Raadpleeg voor andere Power shell-voor beelden voor toepassings proxy [Azure AD Power shell-voor beelden voor azure AD-toepassingsproxy](../application-proxy-powershell-samples.md).