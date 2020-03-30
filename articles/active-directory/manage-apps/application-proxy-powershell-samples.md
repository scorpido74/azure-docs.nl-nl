---
title: PowerShell-voorbeelden voor Azure AD-toepassingsproxy
description: Gebruik deze PowerShell-voorbeelden voor Azure AD Application Proxy om informatie te krijgen over toepassingsproxy-apps en -connectoren in uw map, gebruikers en groepen toe te wijzen aan apps en certificaatgegevens op te vragen.
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
ms.openlocfilehash: 476bdfdd2c0b81d86900cee18024cc261ee80a07
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481259"
---
# <a name="azure-ad-powershell-examples-for-azure-ad-application-proxy"></a>Azure AD PowerShell-voorbeelden voor Azure AD-toepassingsproxy

In de volgende tabel vindt u koppelingen naar PowerShell-scriptvoorbeelden voor Azure AD-toepassingsproxy. Deze voorbeelden vereisen de [AzureAD V2 PowerShell voor graph-module](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) of de [AzureAD V2 PowerShell voor graph-modulepreviewversie,](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview)tenzij anders vermeld.


Zie [Application Proxy Application Management](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_application_management) en Application Proxy Connector [Management](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_connector_management)voor meer informatie over de cmdlets die in deze voorbeelden worden gebruikt.

| | |
|---|---|
|**Toepassingsproxy-apps**||
| [Basisinformatie voor alle toepassingsproxy-apps weergeven](scripts/powershell-get-all-app-proxy-apps-basic.md) | Bevat basisinformatie (AppId, DisplayName, ObjId) over alle toepassingsproxy-apps in uw map. |
| [Uitgebreide informatie voor alle toepassingsproxy-apps weergeven](scripts/powershell-get-all-app-proxy-apps-extended.md) | Bevat uitgebreide informatie (AppId, DisplayName, ExternalUrl, InternalUrl, ExternalAuthenticationType) over alle toepassingsproxy-apps in uw directory.  |
| [Alle toepassingsproxy-apps op connectorgroep weergeven](scripts/powershell-get-all-app-proxy-apps-by-connector-group.md) | Bevat informatie over alle toepassingsproxy-apps in uw map en aan welke verbindingsgroepen de apps zijn toegewezen. |
| [Alle Application Proxy-apps downloaden met een tokenlevenslangbeleid](scripts/powershell-get-all-app-proxy-apps-with-policy.md) | Hiermee worden alle toepassingsproxy-apps in uw map weergegeven met een beleid voor het levenslang van een token en de details ervan. Voor dit voorbeeld is de [previewversie van AzureAD V2 PowerShell voor graph-module vereist.](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) |
|**Connectorgroepen**||
| [Alle verbindingsgroepen en connectoren in de map downloaden](scripts/powershell-get-all-connectors.md) | Hier worden alle verbindingsgroepen en connectoren in uw map weergegeven. |
| [Alle apps die zijn toegewezen aan een verbindingsgroep naar een andere verbindingsgroep verplaatsen](scripts/powershell-move-all-apps-to-connector-group.md) | Hiermee verplaatst u alle toepassingen die momenteel zijn toegewezen aan een verbindingsgroep naar een andere verbindingsgroep. |
|**Gebruikers en toegewezen groep**||
| [Gebruikers en groepen weergeven die zijn toegewezen aan een toepassingsproxytoepassing](scripts/powershell-display-users-group-of-app.md) | Hiermee worden de gebruikers en groepen weergegeven die zijn toegewezen aan een specifieke toepassing voor toepassingsproxy. |
| [Een gebruiker toewijzen aan een toepassing](scripts/powershell-assign-user-to-app.md) | Wijst een specifieke gebruiker toe aan een toepassing. |
| [Een groep toewijzen aan een toepassing](scripts/powershell-assign-group-to-app.md) | Wijst een specifieke groep toe aan een toepassing. |
|**Externe URL-configuratie**||
| [Alle toepassingsproxy-apps downloaden met standaarddomeinen (.msappproxy.net)](scripts/powershell-get-all-default-domain-apps.md)  | Hiermee worden alle toepassingsproxytoepassingen weergegeven met standaarddomeinen (.msappproxy.net). |
| [Alle Application Proxy-apps downloaden met behulp van het publiceren van jokertekens](scripts/powershell-get-all-wildcard-apps.md) | Hiermee worden alle toepassingsproxy-apps weergegeven met behulp van het publiceren van jokertekens. |
|**Aangepaste domeinconfiguratie**||
| [Alle Toepassingsproxy-apps downloaden met aangepaste domeinen en certificaatgegevens](scripts/powershell-get-all-custom-domains-and-certs.md) | Hiermee worden alle toepassingsproxy-apps weergegeven die aangepaste domeinen gebruiken en de certificaatgegevens die zijn gekoppeld aan de aangepaste domeinen. |
| [Alle Azure AD Proxy-toepassingsapps gepubliceerd zonder certificaat te uploaden](scripts/powershell-get-all-custom-domain-no-cert.md) | Hiermee worden alle toepassingsproxy-apps weergegeven die aangepaste domeinen gebruiken, maar geen geldig TLS/SSL-certificaat hebben geüpload. |
| [Alle Azure AD Proxy-toepassingsapps publiceren met het identieke certificaat](scripts/powershell-get-custom-domain-identical-cert.md) | Hier worden alle Azure AD Proxy-toepassingsapps weergegeven die met het identieke certificaat zijn gepubliceerd. |
| [Alle Azure AD Proxy-toepassingsapps publiceren met het identieke certificaat en deze vervangen](scripts/powershell-get-custom-domain-replace-cert.md) | Met Azure AD Proxy-toepassingsapps die met een identiek certificaat zijn gepubliceerd, u het certificaat in bulk vervangen. |
