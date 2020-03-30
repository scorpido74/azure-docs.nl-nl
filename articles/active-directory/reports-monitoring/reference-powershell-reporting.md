---
title: Azure AD PowerShell-cmdlets voor rapportage | Microsoft Documenten
description: Referentie van de Azure AD PowerShell-cmdlets voor rapportage.
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
ms.date: 07/12/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2192c472e00d123780ec6bc5574e7b9fe326258b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75495305"
---
# <a name="azure-ad-powershell-cmdlets-for-reporting"></a>Azure AD PowerShell-cmdlets voor rapportage

> [!NOTE] 
> Deze Powershell-cmdlets werken momenteel alleen met de [Azure AD Preview-module.](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#directory_auditing) Houd er rekening mee dat de preview-module niet wordt voorgesteld voor productiegebruik. 

Als u de openbare preview-release wilt installeren, gebruikt u het volgende. 

```powershell
Install-module AzureADPreview
```
Zie het artikel [Azure AD Powershell for Graph voor](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0)meer informatie over hoe u verbinding maken met Azure AD met Powershell.  

Met Azure Active Directory-rapporten (Azure AD) u informatie krijgen over activiteiten rond alle schrijfbewerkingen in uw richting (controlelogboeken) en verificatiegegevens (aanmeldingslogboeken). Hoewel de informatie beschikbaar is met behulp van de MS Graph API, u nu dezelfde gegevens ophalen met behulp van de Azure AD PowerShell-cmdlets voor rapportage.

Dit artikel geeft u een overzicht van de PowerShell-cmdlets die u gebruiken voor controlelogboeken en aanmeldingslogboeken.

## <a name="audit-logs"></a>Auditlogboeken

[Controlelogboeken](concept-audit-logs.md) bieden traceerbaarheid via logboeken voor alle wijzigingen die worden uitgevoerd door verschillende functies binnen Azure AD. Voorbeelden van controlelogboeken zijn wijzigingen die zijn aangebracht in resources binnen Azure AD, zoals het toevoegen of verwijderen van gebruikers, apps, groepen, rollen en beleidsregels.

U krijgt toegang tot de controlelogboeken met de cmdlet 'Get-AzureADAuditDirectoryLogs'.


| Scenario                      | PowerShell-opdracht |
| :--                           | :--                |
| Naam van toepassingsweergave      | Get-AzureADAuditDirectoryLogs -Filter "geïnitieerdDoor/app/displayName eq 'Azure AD Cloud Sync'" |
| Categorie                      | Get-AzureADAuditDirectoryLogs -Filter "categorie eq 'Application Management'" |
| Datum activiteitsdatum            | Get-AzureADAuditDirectoryLogs -Filter "activityDateTime gt 2019-04-18" |
| Alle bovenstaande              | Get-AzureADAuditDirectoryLogs -Filter "geïnitieerdDoor/app/displayName eq 'Azure AD Cloud Sync' en categorie eq 'Application Management' en activityDateTime gt 2019-04-18"|


In de volgende afbeelding ziet u een voorbeeld voor deze opdracht. 

![De knop 'Gegevensoverzicht'](./media/reference-powershell-reporting/get-azureadauditdirectorylogs.png)



## <a name="sign-in-logs"></a>Aanmeldingslogboeken

De [aanmeldingslogboeken](concept-sign-ins.md) geven informatie over het gebruik van beheerde toepassingen en aanmeldingsactiviteiten voor gebruikers.

U krijgt toegang tot de aanmeldingslogboeken met de cmdlet 'Get-AzureADAuditSignInLogs.You get access to the sign-in logs using the 'Get-AzureADAuditSignInLogs cmdlet.


| Scenario                      | PowerShell-opdracht |
| :--                           | :--                |
| Gebruikersnaam             | Get-AzureADAuditSignInLogs -Filter "userDisplayName eq 'Timothy Perkins'" |
| Datumtijd maken              | Get-AzureADAuditSignInLogs -Filter "createdDateTime gt 2019-04-18T17:30:00.0Z" (Alles sinds 17:30 uur op 4/18) |
| Status                        | Get-AzureADAuditSignInLogs -Filter "status/foutCode eq 50105" |
| Naam van toepassingsweergave      | Get-AzureADAuditSignInLogs -Filter "appDisplayName eq 'StoreFrontStudio [wsfed enabled]'" |
| Alle bovenstaande              | Get-AzureADAuditSignInLogs -Filter "userDisplayName eq 'Timothy Perkins' and status/errorCode ne 0 and appDisplayName eq 'StoreFrontStudio [wsfed enabled]'" |


In de volgende afbeelding ziet u een voorbeeld voor deze opdracht. 

![De knop 'Gegevensoverzicht'](./media/reference-powershell-reporting/get-azureadauditsigninlogs.png)



## <a name="next-steps"></a>Volgende stappen

- [Overzicht van Azure AD-rapporten](overview-reports.md).
- [Rapport controlelogboeken](concept-audit-logs.md). 
- [Programmatische toegang tot Azure AD-rapporten](concept-reporting-api.md)
