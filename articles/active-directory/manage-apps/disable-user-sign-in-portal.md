---
title: Gebruikers aanmeldingen uitschakelen voor een bedrijfs-app in azure AD
description: Een bedrijfs toepassing uitschakelen zodat gebruikers zich niet kunnen aanmelden in Azure Active Directory
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 04/12/2019
ms.author: kenwith
ms.reviewer: asteen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4c6fc9a4716f1427257b3bbf18e5fa653567e141
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84763377"
---
# <a name="disable-user-sign-ins-for-an-enterprise-app-in-azure-active-directory"></a>Aanmeldingen van gebruikers voor een bedrijfs-app in Azure Active Directory uitschakelen

Het is eenvoudig om een bedrijfs toepassing uit te scha kelen zodat gebruikers zich niet kunnen aanmelden in Azure Active Directory (Azure AD). U hebt de juiste machtigingen nodig om de Enter prise-app te beheren. En u moet globale beheerder zijn voor de Directory.

## <a name="how-do-i-disable-user-sign-ins"></a>Gebruikers aanmeldingen Hoe kan ik uitschakelen?

1. Meld u aan bij [Azure Portal](https://portal.azure.com) met een account van een globale beheerder voor de directory.
1. Selecteer **alle services**, Voer **Azure Active Directory** in het tekstvak in en selecteer vervolgens **Enter**.
1. Selecteer in **Azure Active Directory**het  -   deel venster Azure Active Directory***directorynaam*** (dat wil zeggen, het deel venster Azure AD voor de directory die u beheert) **bedrijfs toepassingen**.
1. In het deel venster **bedrijfs toepassingen-alle toepassingen** ziet u een lijst met de apps die u kunt beheren. Selecteer een app.
1. In het deel venster ***AppName*** (dat wil zeggen, het deel venster met de naam van de geselecteerde app in de titel), selecteert u **Eigenschappen**.
1. Selecteer in ***appname***  -  het deel venster appName-**Eigenschappen** de optie **Nee** voor **ingeschakeld voor gebruikers om zich aan te melden**.
1. Selecteer de opdracht **Opslaan** .

## <a name="use-azure-ad-powershell-to-disable-an-unlisted-app"></a>Azure AD Power shell gebruiken om een niet-gevermelde app uit te scha kelen

Als u de AppId kent van een app die niet wordt weer gegeven in de lijst met zakelijke apps (bijvoorbeeld omdat u de app hebt verwijderd of als de Service-Principal nog niet is gemaakt door de app die door micro soft wordt geautoriseerd), kunt u de Service-Principal hand matig voor de app maken en deze vervolgens uitschakelen met behulp van de [AzureAD Power shell-cmdlet](https://docs.microsoft.com/powershell/module/azuread/New-AzureADServicePrincipal?view=azureadps-2.0).

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

* [Alle groepen weer geven](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Een gebruiker of groep toewijzen aan een bedrijfs-app](assign-user-or-group-access-portal.md)
* [Een gebruiker of groeps toewijzing verwijderen uit een bedrijfs-app](remove-user-or-group-access-portal.md)
* [De naam of het logo van een zakelijke app wijzigen](change-name-or-logo-portal.md)
