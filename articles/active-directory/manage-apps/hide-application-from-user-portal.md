---
title: Een toepassing verbergen voor gebruikers ervaring in azure AD
description: Hoe u een toepassing kunt verbergen voor gebruikers ervaring in Azure Active Directory toegangs Vensters of Office 365-starters.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: mimart
ms.reviewer: kasimpso
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5718adf4fd76e2fbd0ff793dd2fa33ee08f7c0fe
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80295051"
---
# <a name="hide-applications-from-end-users-in-azure-active-directory"></a>Toepassingen verbergen voor eind gebruikers in Azure Active Directory

Instructies voor het verbergen van toepassingen van het deel venster MyApps of Office 365 Launcher van eind gebruikers. Wanneer een toepassing wordt verborgen, hebben gebruikers nog steeds machtigingen voor de toepassing. 

## <a name="prerequisites"></a>Vereisten

De bevoegdheden van de toepassings beheerder zijn vereist voor het verbergen van een toepassing uit het deel venster MyApps en Office 365 Launcher.

Globale beheerders bevoegdheden zijn vereist om alle Office 365-toepassingen te verbergen.


## <a name="hide-an-application-from-the-end-user"></a>Een toepassing verbergen voor de eind gebruiker
Voer de volgende stappen uit om een toepassing te verbergen in het MyApps-deel venster en het start programma voor Office 365 Application.

1.  Meld u aan bij de [Azure Portal](https://portal.azure.com) als globale beheerder voor uw Directory.
2.  Selecteer **Azure Active Directory**.
3.  Selecteer **bedrijfs toepassingen**. De Blade **bedrijfs toepassingen-alle toepassingen** wordt geopend.
4.  Onder **toepassings type**selecteert u **bedrijfs toepassingen**, als deze nog niet is geselecteerd.
5.  Zoek de toepassing die u wilt verbergen en klik op de toepassing.  Het overzicht van de toepassing wordt geopend.
6.  Klik op **Eigenschappen**. 
7.  Voor de **zicht bare gebruiker?** vraag, klikt u op **Nee**.
8.  Klik op **Opslaan**.

## <a name="use-azure-ad-powershell-to-hide-an-application"></a>Azure AD Power shell gebruiken om een toepassing te verbergen

Als u een toepassing wilt verbergen in het deel venster MyApps, kunt u de HideApp-tag hand matig toevoegen aan de service-principal voor de toepassing. Voer de volgende [AzureAD Power shell](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#service_principals) -opdrachten uit om de eigenschap **zichtbaar voor gebruikers** in te stellen op **Nee**. 

```PowerShell
Connect-AzureAD

$objectId = "<objectId>"
$servicePrincipal = Get-AzureADServicePrincipal -ObjectId $objectId
$tags = $servicePrincipal.tags
$tags += "HideApp"
Set-AzureADServicePrincipal -ObjectId $objectId -Tags $tags
```

## <a name="hide-office-365-applications-from-the-myapps-panel"></a>Office 365-toepassingen verbergen in het deel venster MyApps

Gebruik de volgende stappen om alle Office 365-toepassingen te verbergen in het deel venster MyApps. De toepassingen zijn nog steeds zichtbaar in de Office 365-Portal.

1.  Meld u aan bij de [Azure Portal](https://portal.azure.com) als globale beheerder voor uw Directory.
2.  Selecteer **Azure Active Directory**.
3.  Selecteer **Gebruikersinstellingen**.
4.  Onder **bedrijfs toepassingen**klikt **u op beheren hoe eind gebruikers hun toepassingen starten en weer geven.**
5.  Voor **gebruikers kunnen alleen office 365-apps in de office 365-Portal zien**, klikt u op **Ja**.
6.  Klik op **Opslaan**.

## <a name="next-steps"></a>Volgende stappen
* [Alle groepen weer geven](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Een gebruiker of groep toewijzen aan een bedrijfs-app](assign-user-or-group-access-portal.md)
* [Een gebruiker of groeps toewijzing verwijderen uit een bedrijfs-app](remove-user-or-group-access-portal.md)
* [De naam of het logo van een zakelijke app wijzigen](change-name-or-logo-portal.md)

