---
title: PowerShell-voorbeeld - Alle toepassingsproxy-connectorgroepen weergeven
description: PowerShell-voorbeeld met alle Azure Active Directory-connectorgroepen en -connectoren voor azure active directory (Azure AD)
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
ms.openlocfilehash: 53af7f3f9049edc23ed4226f43e7fe67398c29f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75482112"
---
# <a name="get-all-connector-groups-and-connectors-in-the-directory"></a>Alle verbindingsgroepen en connectoren in de map downloaden

In dit voorbeeld van PowerShell-script worden alle Azure Active Directory-connectorgroepen en -connectoren voor azure active directory (Azure AD) application proxy en connectoren in uw map weergegeven.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Voor dit voorbeeld is de [AzureAD V2 PowerShell for Graph-module](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) (AzureAD) of de [AzureAD V2 PowerShell voor graph-modulepreviewversie](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) (AzureADPreview) vereist.

## <a name="sample-script"></a>Voorbeeldscript

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-connectors.ps1 "Get all connector groups and connectors in the directory")]

## <a name="script-explanation"></a>Uitleg van het script

| Opdracht | Opmerkingen |
|---|---|
| [Get-AzureADApplicationProxyConnectorGroup](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplicationproxyconnectorgroup?view=azureadps-2.0) | Hiermee haalt u een lijst op met alle verbindingsgroepen of, indien opgegeven, details van de opgegeven verbindingsgroep. |
| [Get-AzureADApplicationProxyConnectorGroupGroupMembers](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplicationproxyconnectorgroupmembers?view=azureadps-2.0) | Hiermee worden alle toepassingsproxyconnectors gekoppeld aan elke connectorgroep.|

## <a name="next-steps"></a>Volgende stappen

Zie azure [AD PowerShell-moduleoverzicht](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0)voor meer informatie over de Azure AD PowerShell-module.

Zie [Azure AD PowerShell-voorbeelden voor](../application-proxy-powershell-samples.md)andere PowerShell-voorbeelden voor andere PowerShell-toepassingen.