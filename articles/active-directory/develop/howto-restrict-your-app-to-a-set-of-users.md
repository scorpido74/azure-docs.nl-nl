---
title: Uw door Azure Active Directory geregistreerde app beperken tot een aantal gebruikers
titleSuffix: Microsoft identity platform
description: Meer informatie over het beperken van de toegang tot uw apps die zijn geregistreerd in azure AD voor een geselecteerde groep gebruikers.
services: active-directory
documentationcenter: ''
author: kalyankrishna1
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: kkrishna
ms.reviewer: ''
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: c768730a00d424333e735bb4f6a385d294f38513
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72803961"
---
# <a name="how-to-restrict-your-app-to-a-set-of-users"></a>Procedure: uw app beperken tot een aantal gebruikers

Toepassingen die zijn geregistreerd in een Azure Active Directory-Tenant (Azure AD), zijn standaard beschikbaar voor alle gebruikers van de Tenant die zijn geverifieerd.

In het geval van een [multi tenant](howto-convert-app-to-be-multi-tenant.md) -app kunnen alle gebruikers in de Azure AD-Tenant waar deze app is ingericht, toegang krijgen tot deze toepassing zodra ze zijn geverifieerd in hun respectieve Tenant.

Tenant beheerders en ontwikkel aars hebben vaak vereisten waarbij een app moet worden beperkt tot een bepaalde groep gebruikers. Ontwikkel aars kunnen hetzelfde doen met behulp van populaire verificatie patronen als op rollen gebaseerd Access Control (RBAC), maar deze benadering vereist een aanzienlijke hoeveelheid werk voor een deel van de ontwikkelaar.

Met Azure AD kunnen Tenant beheerders en ontwikkel aars een app beperken tot een specifieke set gebruikers of beveiligings groepen in de Tenant.

## <a name="supported-app-configurations"></a>Ondersteunde app-configuraties

De optie om een app te beperken tot een specifieke set gebruikers of beveiligings groepen in een Tenant werkt met de volgende typen toepassingen:

- Toepassingen die zijn geconfigureerd voor federatieve eenmalige aanmelding met op SAML gebaseerde verificatie
- Toepassings proxy toepassingen die gebruikmaken van pre-authenticatie van Azure AD
- Toepassingen die rechtstreeks zijn gebouwd op het Azure AD-toepassings platform die gebruikmaken van OAuth 2.0/OpenID Connect Connect-verificatie nadat een gebruiker of beheerder heeft ingestemd met die toepassing.

     > [!NOTE]
     > Deze functie is alleen beschikbaar voor web-app/Web-API en bedrijfs toepassingen. Apps die zijn geregistreerd als [systeem eigen](quickstart-v1-integrate-apps-with-azure-ad.md) , kunnen niet worden beperkt tot een set gebruikers of beveiligings groepen in de Tenant.

## <a name="update-the-app-to-enable-user-assignment"></a>De app bijwerken om de gebruikers toewijzing in te scha kelen

Er zijn twee manieren om een toepassing met ingeschakelde gebruikers toewijzing te maken. Voor één is de rol **globale beheerder** vereist, de tweede niet.

### <a name="enterprise-applications-requires-the-global-adminstrator-role"></a>Bedrijfs toepassingen (vereist de rol van globale beheerder)

1. Ga naar de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder**.
1. Selecteer op de bovenste balk het aangemelde account. 
1. Onder **Directory**selecteert u de Azure AD-Tenant waar de app wordt geregistreerd.
1. Selecteer in de navigatie aan de linkerkant **Azure Active Directory**. Als Azure Active Directory niet beschikbaar is in het navigatie venster, voert u de volgende stappen uit:

    1. Selecteer **alle services** boven aan het hoofd navigatie menu aan de linkerkant.
    1. Typ **Azure Active Directory** in het vak Zoek opdracht filteren en selecteer vervolgens het **Azure Active Directory** item in het resultaat.

1. Selecteer in het deel venster Azure Active Directory **bedrijfs toepassingen** in het navigatie menu **Azure Active Directory** links.
1. Selecteer **alle toepassingen** om een lijst met al uw toepassingen weer te geven.

     Als u de toepassing die u wilt weer geven hier niet ziet, gebruikt u de diverse filters boven aan de lijst **alle toepassingen** om de lijst te beperken of omlaag te schuiven in de lijst om uw toepassing te vinden.

1. Selecteer in de lijst de toepassing waaraan u een gebruiker of beveiligings groep wilt toewijzen.
1. Op de **overzichts** pagina van de toepassing selecteert u **Eigenschappen** in het navigatie menu van de toepassing aan de linkerkant.
1. Zoek de instelling **gebruikers toewijzing vereist?** en stel deze in op **Ja**. Als deze optie is ingesteld op **Ja**, moeten gebruikers eerst worden toegewezen aan deze toepassing voordat ze er toegang toe hebben.
1. Selecteer **Opslaan** om deze configuratie wijziging op te slaan.

### <a name="app-registration"></a>App-registratie

1. Ga naar de [**Azure Portal**](https://portal.azure.com/).
1. Selecteer op de bovenste balk het aangemelde account. 
1. Onder **Directory**selecteert u de Azure AD-Tenant waar de app wordt geregistreerd.
1. Selecteer in de navigatie aan de linkerkant **Azure Active Directory**.
1. Selecteer **app-registraties** in het deel venster **Azure Active Directory** van het navigatie menu **Azure Active Directory** links.
1. Maak of selecteer de app die u wilt beheren. U moet **eigenaar** zijn van deze app-registratie.
1. Op de **overzichts** pagina van de toepassing volgt u de koppeling **beheerde toepassing in lokale map** onder de essentiële elementen boven aan de pagina. Hiermee gaat u naar de _beheerde bedrijfs toepassing_ van de app-registratie.
1. Selecteer **Eigenschappen**in de Blade navigatie aan de linkerkant.
1. Zoek de instelling **gebruikers toewijzing vereist?** en stel deze in op **Ja**. Als deze optie is ingesteld op **Ja**, moeten gebruikers eerst worden toegewezen aan deze toepassing voordat ze er toegang toe hebben.
1. Selecteer **Opslaan** om deze configuratie wijziging op te slaan.

## <a name="assign-users-and-groups-to-the-app"></a>Gebruikers en groepen toewijzen aan de app

Zodra u uw app hebt geconfigureerd om gebruikers toewijzing in te scha kelen, kunt u gebruikers en groepen toewijzen aan de app.

1. Selecteer het deel venster **gebruikers en groepen** in het navigatie menu aan de linkerkant van de toepassing.
1. Klik boven aan de lijst **gebruikers en groepen** op de knop **gebruiker toevoegen** om het deel venster **toewijzing toevoegen** te openen.
1. Selecteer de optie **gebruikers** selecteren in het deel venster **toewijzing toevoegen** . 

     Er wordt een lijst met gebruikers en beveiligings groepen weer gegeven samen met een tekstvak om te zoeken en een bepaalde gebruiker of groep te zoeken. In dit scherm kunt u meerdere gebruikers en groepen tegelijk selecteren.

1. Wanneer u klaar bent met het selecteren van de gebruikers en groepen, klikt u op de knop **selecteren** aan de onderkant om naar het volgende deel te gaan.
1. Druk op de knop **toewijzen** aan de onderkant om de toewijzingen van gebruikers en groepen aan de app te volt ooien. 
1. Controleer of de gebruikers en groepen die u hebt toegevoegd, worden weer gegeven in de lijst met bijgewerkte **gebruikers en groepen** .

