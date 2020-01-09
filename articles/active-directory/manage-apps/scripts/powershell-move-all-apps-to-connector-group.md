---
title: 'Power shell-voor beeld: toepassings proxy-apps verplaatsen naar een andere groep'
description: Azure Active Directory (Azure AD) Power shell-voor beeld van Application proxy gebruikt om alle toepassingen te verplaatsen die momenteel aan een connector groep zijn toegewezen, naar een andere connector groep.
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
ms.openlocfilehash: f6dccdaa96dadb061b168bbdf6148ed4d759776c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75481982"
---
# <a name="move-all-apps-assigned-to-a-connector-group-to-another-connector-group"></a>Alle apps die zijn toegewezen aan een connector groep verplaatsen naar een andere connector groep

In dit voor beeld van een Power shell-script worden alle Azure Active Directory-toepassings proxy toepassingen (Azure AD) die momenteel aan een connector groep zijn toegewezen, verplaatst naar een andere connector groep.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Voor dit voor beeld is de [AzureAD v2 Power shell voor Graph module](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) (AzureAD) of de [Preview-versie van de AzureAD v2 Power shell voor Graph-module](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) (AzureADPreview) vereist.

## <a name="sample-script"></a>Voorbeeldscript

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/move-all-apps-to-a-connector-group.ps1 "Move all apps assigned to a connector group to another connector group")]

## <a name="script-explanation"></a>Uitleg van het script

| Opdracht | Opmerkingen |
|---|---|
|[Get-Azureadserviceprincipal namelijk niet](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0) | Hiermee wordt een Service-Principal opgehaald. |
|[Get-AzureADApplication](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplication?view=azureadps-2.0) | Hiermee haalt u een Azure AD-toepassing op. |
| [Get-AzureADApplicationProxyConnectorGroup](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplicationproxyconnectorgroup?view=azureadps-2.0) | Hiermee wordt een lijst opgehaald met alle connector groepen, of indien opgegeven, Details van de opgegeven connector groep. |
| [Set-AzureADApplicationProxyConnectorGroup](https://docs.microsoft.com/powershell/module/azuread/set-azureadapplicationproxyapplicationconnectorgroup?view=azureadps-2.0) | Hiermee wijst u de gegeven connector groep toe aan een opgegeven toepassing.|

## <a name="next-steps"></a>Volgende stappen

Zie [overzicht van Azure AD Power shell-module](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0)voor meer informatie over de Azure AD Power shell-module.

Raadpleeg voor andere Power shell-voor beelden voor toepassings proxy [Azure AD Power shell-voor beelden voor azure AD-toepassingsproxy](../application-proxy-powershell-samples.md).