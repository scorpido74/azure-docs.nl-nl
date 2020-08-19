---
title: 'PowerShell-voorbeeld: certificaat vervangen in toepassingsproxy-apps'
description: PowerShell-voorbeeld waarin bulksgewijs een certificaat wordt vervangen in Azure Active Directory (Azure AD)-toepassingsproxytoepassingen.
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 12/05/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 8ef083a079ff4bc42959c7734595e41793cd04bc
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/18/2020
ms.locfileid: "88511109"
---
# <a name="get-all-application-proxy-applications-published-with-the-identical-certificate-and-replace-it"></a>Alle toepassingsproxytoepassingen ophalen die zijn gepubliceerd met het identieke certificaat en dit vervangen

In dit PowerShell-voorbeeldscript kunt u het certificaat bulksgewijs vervangen voor alle Azure Active Directory (Azure AD)-toepassingsproxytoepassingen die met het identieke certificaat worden gepubliceerd.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Voor dit voorbeeld is de [AzureAD V2 PowerShell voor Graph-module](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) (AzureAD) of de [AzureAD V2 PowerShell voor Graph-module (preview)](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) (AzureADPreview) vereist.

## <a name="sample-script"></a>Voorbeeldscript

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-custom-domain-replace-cert.ps1 "Get all Application Proxy applications published with the identical certificate and replace it")]

## <a name="script-explanation"></a>Uitleg van het script

| Opdracht | Opmerkingen |
|---|---|
|[Get-AzureADServicePrincipal](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0) | Hiermee wordt een service-principal opgehaald. |
|[Get-AzureADApplication](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplication?view=azureadps-2.0) | Hiermee wordt een Azure AD-toepassing opgehaald. |
|[Get-AzureADApplicationProxyApplication](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplicationproxyapplication?view=azureadps-2.0) | Hiermee wordt een toepassing opgehaald die is geconfigureerd voor een toepassingsproxy in Azure AD. |
|[Set-AzureADApplicationProxyApplicationCustomDomainCertificate](https://docs.microsoft.com/powershell/module/azuread/set-azureadapplicationproxyapplicationcustomdomaincertificate?view=azureadps-2.0) | Hiermee wordt een certificaat toegewezen aan een toepassing die is geconfigureerd voor een toepassingsproxy in Azure AD. Met deze opdracht wordt het certificaat geüpload en kan de toepassing gebruikmaken van aangepaste domeinen. |

## <a name="next-steps"></a>Volgende stappen

Zie het [overzicht van de Azure PowerShell-module](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0) voor meer informatie over de Azure AD PowerShell-module.

Zie [Azure AD PowerShell-voorbeelden voor de Azure AD-toepassingsproxy](../application-proxy-powershell-samples.md) voor andere PowerShell-voorbeelden voor de toepassingsproxy.