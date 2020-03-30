---
title: Aanmeldingen van gebruikers voor een bedrijfsapp in Azure AD uitschakelen
description: Een bedrijfstoepassing uitschakelen zodat gebruikers zich niet kunnen aanmelden in Azure Active Directory
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: mimart
ms.reviewer: asteen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 10553898376c4b9236ee62718fffccd45b12d70b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74274094"
---
# <a name="disable-user-sign-ins-for-an-enterprise-app-in-azure-active-directory"></a>Aanmeldingen van gebruikers voor een bedrijfsapp uitschakelen in Azure Active Directory

Het is eenvoudig om een bedrijfstoepassing uit te schakelen, zodat gebruikers zich niet kunnen aanmelden in Azure Active Directory (Azure AD). U hebt de juiste machtigingen nodig om de bedrijfsapp te beheren. En u moet globale beheerder voor de directory zijn.

## <a name="how-do-i-disable-user-sign-ins"></a>Hoe schakel ik aanmeldingen van gebruikers uit?

1. Meld u aan bij [Azure Portal](https://portal.azure.com) met een account van een globale beheerder voor de directory.
1. Selecteer **Alle services,** voer **Azure Active Directory** in het tekstvak in en selecteer **Enter**.
1. Selecteer **Enterprise-toepassingen** -  in het venster Azure Active Directory***directoryname*** (dat wil **Enterprise applications**zeggen het Azure AD-deelvenster voor de map die u beheert).
1. In het deelvenster **Enterprise-toepassingen - Alle toepassingen** ziet u een lijst met de apps die u beheren. Selecteer een app.
1. Selecteer **Eigenschappen**in het deelvenster ***Appname*** (dat wil zeggen het deelvenster met de naam van de geselecteerde app in de titel).
1. Selecteer in het deelvenster**Eigenschappen van** ***appnaam*** - de optie **Nee** voor gebruikers om zich aan **te melden?**.
1. Selecteer de opdracht **Opslaan.**

## <a name="use-azure-ad-powershell-to-disable-an-unlisted-app"></a>Azure AD PowerShell gebruiken om een niet-vermelde app uit te schakelen

Als u de appid van een app kent die niet wordt weergegeven in de lijst met Enterprise-apps (bijvoorbeeld omdat u de app hebt verwijderd of omdat de serviceprincipal nog niet is gemaakt omdat de app vooraf is geautoriseerd door Microsoft), u handmatig de serviceprincipal voor de app maken en deze vervolgens uitschakelen met [azuread PowerShell-cmdlet](https://docs.microsoft.com/powershell/module/azuread/New-AzureADServicePrincipal?view=azureadps-2.0).

```PowerShell
# The AppId of the app to be disabled
$appId = "{AppId}"

# Check if a service principal already exists for the app
$servicePrincipal = Get-AzureADServicePrincipal -Filter "appId eq '$appId'"
if ($servicePrincipal) {
    # Service principal exists already, disable it
    Set-AzureADServicePrincipal -ObjectId $servicePrincipal.ObjectId -AccountEnabled $false
} else {
    # Service principal does not yet exist, create it and disable it at the same time
    $servicePrincipal = New-AzureADServicePrincipal -AppId $appId -AccountEnabled $false
}
```

## <a name="next-steps"></a>Volgende stappen

* [Bekijk al mijn groepen](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Een gebruiker of groep toewijzen aan een bedrijfsapp](assign-user-or-group-access-portal.md)
* [Een gebruiker of groepstoewijzing verwijderen uit een bedrijfsapp](remove-user-or-group-access-portal.md)
* [De naam of het logo van een bedrijfsapp wijzigen](change-name-or-logo-portal.md)
