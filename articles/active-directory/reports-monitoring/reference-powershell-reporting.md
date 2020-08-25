---
title: Azure AD Power shell-cmdlets voor rapportage | Microsoft Docs
description: Verwijzing van de Azure AD Power shell-cmdlets voor rapportage.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 08/07/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6a198a63d633573ad683a3f8e8215b2975721bc9
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/25/2020
ms.locfileid: "88794928"
---
# <a name="azure-ad-powershell-cmdlets-for-reporting"></a>Azure AD PowerShell-cmdlets voor rapportage

> [!NOTE] 
> Deze Power shell-cmdlets werken momenteel alleen met de [Azure ad-preview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#directory_auditing) -module. Houd er rekening mee dat de preview-module niet wordt voorgesteld voor productie gebruik. 

Gebruik het volgende om de open bare preview-versie te installeren. 

```powershell
Install-module AzureADPreview
```

Raadpleeg het artikel [Azure AD Power shell voor Graph](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0)voor meer informatie over het maken van een verbinding met Azure AD met behulp van Power shell.  

Met Azure Active Directory-rapporten (Azure AD) kunt u Details opvragen over activiteiten rond alle schrijf bewerkingen in uw richting (audit Logboeken) en verificatie gegevens (aanmeld Logboeken). Hoewel de informatie beschikbaar is via de MS Graph API, kunt u nu dezelfde gegevens ophalen met behulp van de Azure AD Power shell-cmdlets voor rapportage.

In dit artikel vindt u een overzicht van de Power shell-cmdlets die moeten worden gebruikt voor audit logboeken en aanmeldings Logboeken.

## <a name="audit-logs"></a>Auditlogboeken

[Audit logboeken](concept-audit-logs.md) bieden traceer baarheid via Logboeken voor alle wijzigingen die worden uitgevoerd door diverse functies in azure AD. Voor beelden van audit logboeken zijn wijzigingen die zijn aangebracht in resources binnen Azure AD, zoals het toevoegen of verwijderen van gebruikers, apps, groepen, rollen en beleid.

U krijgt toegang tot de audit logboeken met behulp van de cmdlet Get-AzureADAuditDirectoryLogs.


| Scenario                      | PowerShell-opdracht |
| :--                           | :--                |
| Weergave naam van toepassing      | Get-AzureADAuditDirectoryLogs: filter "initiatedBy/app/displayName EQ" Azure AD Cloud Sync "" |
| Categorie                      | Get-AzureADAuditDirectoryLogs-filter "Category EQ" ApplicationManagement "" |
| Datum en tijd van activiteit            | Get-AzureADAuditDirectoryLogs-filter "activityDateTime gt 2019-04-18" |
| Alle hierboven genoemde antwoorden              | Get-AzureADAuditDirectoryLogs: filter "initiatedBy/app/displayName EQ" Azure AD Cloud Sync "en Category EQ" ApplicationManagement "en activityDateTime gt 2019-04-18"|


In de volgende afbeelding ziet u een voor beeld van deze opdracht. 

![De knop gegevens overzicht](./media/reference-powershell-reporting/get-azureadauditdirectorylogs.png)



## <a name="sign-in-logs"></a>Aanmeldingslogboeken

De [aanmeldings](concept-sign-ins.md) logboeken bevatten informatie over het gebruik van beheerde toepassingen en aanmeldings activiteiten voor gebruikers.

U krijgt toegang tot de aanmeld logboeken met behulp van de cmdlet Get-AzureADAuditSignInLogs.


| Scenario                      | PowerShell-opdracht |
| :--                           | :--                |
| Weergave naam van gebruiker             | Get-AzureADAuditSignInLogs-filter "userDisplayName EQ" Timothy Perkins ' " |
| Datum en tijd maken              | Get-AzureADAuditSignInLogs-filter "createdDateTime gt 2019-04-18T17:30:00.0 Z" (alles sinds 5:30 uur op 4/18) |
| Status                        | Get-AzureADAuditSignInLogs-filter "status/error code EQ 50105" |
| Weergave naam van toepassing      | Get-AzureADAuditSignInLogs-filter "appDisplayName EQ" StoreFrontStudio [wsfed ingeschakeld] "" |
| Alle hierboven genoemde antwoorden              | Get-AzureADAuditSignInLogs-filter "userDisplayName EQ ' Timothy Perkins ' en status/error code ne 0 en appDisplayName EQ ' StoreFrontStudio [wsfed enabled] '" |


In de volgende afbeelding ziet u een voor beeld van deze opdracht. 

![De knop gegevens overzicht](./media/reference-powershell-reporting/get-azureadauditsigninlogs.png)



## <a name="next-steps"></a>Volgende stappen

- [Overzicht van Azure AD-rapporten](overview-reports.md).
- [Rapport controle logboeken](concept-audit-logs.md). 
- [Programmatische toegang tot Azure AD-rapporten](concept-reporting-api.md)
