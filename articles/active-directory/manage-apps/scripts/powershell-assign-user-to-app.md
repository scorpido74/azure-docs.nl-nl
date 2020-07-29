---
title: 'Power shell-voor beeld: gebruiker toewijzen aan een app voor toepassings proxy'
description: Power shell-voor beeld dat een gebruiker toewijst aan een toepassing voor toepassings proxy van Azure Active Directory (Azure AD).
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
ms.openlocfilehash: 42667ebdfc37b679d56421e3a82d4a6aaaed8fdf
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "75483334"
---
# <a name="assign-a-user-to-a-specific-azure-ad-application-proxy-application"></a>Een gebruiker toewijzen aan een specifieke Azure AD-toepassingsproxy-toepassing

In dit voor beeld van een Power shell-script kunt u een gebruiker toewijzen aan een specifieke Azure AD-toepassingsproxy-toepassing.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Voor dit voor beeld is de [AzureAD v2 Power shell voor Graph module](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) (AzureAD) of de [Preview-versie van de AzureAD v2 Power shell voor Graph-module](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) (AzureADPreview) vereist.

## <a name="sample-script"></a>Voorbeeldscript

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/assign-user-to-app.ps1 "Assign a user to an application")]

## <a name="script-explanation"></a>Uitleg van het script

| Opdracht | Opmerkingen |
|---|---|
| [New-AzureADUserAppRoleAssignment](https://docs.microsoft.com/powershell/module/AzureAD/new-azureaduserapproleassignment?view=azureadps-2.0) | Hiermee wijst u een gebruiker toe aan een toepassingsrol. |

## <a name="next-steps"></a>Volgende stappen

Zie [overzicht van Azure AD Power shell-module](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0)voor meer informatie over de Azure AD Power shell-module.

Raadpleeg voor andere Power shell-voor beelden voor toepassings proxy [Azure AD Power shell-voor beelden voor azure AD-toepassingsproxy](../application-proxy-powershell-samples.md).