---
title: Een toepassing verbergen voor de gebruikerservaring in Azure AD
description: Een toepassing verbergen voor de gebruikerservaring in Azure Active Directory-toegangspanelen of Office 365-launchers.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295051"
---
# <a name="hide-applications-from-end-users-in-azure-active-directory"></a>Toepassingen verbergen voor eindgebruikers in Azure Active Directory

Instructies voor het verbergen van toepassingen in het Deelvenster MyApps van eindgebruikers of het Startprogramma voor Office 365. Wanneer een toepassing is verborgen, hebben gebruikers nog steeds machtigingen voor de toepassing. 

## <a name="prerequisites"></a>Vereisten

Toepassingsbeheerdersmoeten zijn vereist om een toepassing te verbergen voor het deelvenster MyApps en het Startprogramma voor Office 365.

Beheerdersbevoegdheden voor globale beheerders zijn vereist om alle Office 365-toepassingen te verbergen.


## <a name="hide-an-application-from-the-end-user"></a>Een toepassing verbergen voor de eindgebruiker
Gebruik de volgende stappen om een toepassing te verbergen in het deelvenster MyApps en het startprogramma voor Office 365-toepassingen.

1.  Meld u aan bij de [Azure-portal](https://portal.azure.com) als de globale beheerder van uw directory.
2.  Selecteer **Azure Active Directory**.
3.  Selecteer **Enterprise-toepassingen**. De **Enterprise-toepassingen - Alle toepassingen** blade opent.
4.  Selecteer **Enterprise-toepassingen**onder **Toepassingstype**als deze nog niet is geselecteerd.
5.  Zoek naar de toepassing die u wilt verbergen en klik op de toepassing.  Het overzicht van de toepassing wordt geopend.
6.  Klik op **Eigenschappen**. 
7.  Voor de **vraag Zichtbaar voor gebruikers?** klik op **Nee.**
8.  Klik op **Opslaan**.

## <a name="use-azure-ad-powershell-to-hide-an-application"></a>Azure AD PowerShell gebruiken om een toepassing te verbergen

Als u een toepassing wilt verbergen in het deelvenster MyApps, u de HideApp-tag handmatig toevoegen aan de serviceprincipal voor de toepassing. Voer de volgende [AzureAD PowerShell-opdrachten](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#service_principals) uit om de eigenschap Zichtbaar van de toepassing in te stellen **op De eigenschap** Van Toepassing op **Nr.** 

```PowerShell
Connect-AzureAD

$objectId = "<objectId>"
$servicePrincipal = Get-AzureADServicePrincipal -ObjectId $objectId
$tags = $servicePrincipal.tags
$tags += "HideApp"
Set-AzureADServicePrincipal -ObjectId $objectId -Tags $tags
```

## <a name="hide-office-365-applications-from-the-myapps-panel"></a>Office 365-toepassingen verbergen in het deelvenster MyApps

Gebruik de volgende stappen om alle Office 365-toepassingen te verbergen in het deelvenster MyApps. De toepassingen zijn nog steeds zichtbaar in de Office 365-portal.

1.  Meld u aan bij de [Azure-portal](https://portal.azure.com) als globale beheerder voor uw directory.
2.  Selecteer **Azure Active Directory**.
3.  Selecteer **Gebruikersinstellingen**.
4.  Klik **onder Enterprise-toepassingen**op **Beheren hoe eindgebruikers hun toepassingen starten en bekijken.**
5.  Voor **gebruikers kunnen alleen Office 365-apps worden zien in de Office 365-portal,** klik op **Ja**.
6.  Klik op **Opslaan**.

## <a name="next-steps"></a>Volgende stappen
* [Bekijk al mijn groepen](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Een gebruiker of groep toewijzen aan een bedrijfsapp](assign-user-or-group-access-portal.md)
* [Een gebruiker of groepstoewijzing verwijderen uit een bedrijfsapp](remove-user-or-group-access-portal.md)
* [De naam of het logo van een bedrijfsapp wijzigen](change-name-or-logo-portal.md)

