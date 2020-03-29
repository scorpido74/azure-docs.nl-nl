---
title: Azure AD-app beperken tot een set gebruikers | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over hoe u de toegang tot uw apps die zijn geregistreerd in Azure AD beperken tot een geselecteerde set gebruikers.
services: active-directory
author: kalyankrishna1
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: kkrishna
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: cccd2df334828c0b8103e4da2ffcd8549673b69c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76696993"
---
# <a name="how-to-restrict-your-azure-ad-app-to-a-set-of-users"></a>How to: Uw Azure AD-app beperken tot een set gebruikers

Toepassingen die zijn geregistreerd in een Azure Active Directory-tenant (Azure AD) zijn standaard beschikbaar voor alle gebruikers van de tenant die met succes zijn geverifieerd.

In het geval van een [multi-tenant-app](howto-convert-app-to-be-multi-tenant.md) kunnen alle gebruikers in de Azure AD-tenant waarin deze app is ingericht, toegang krijgen tot deze toepassing zodra ze met succes zijn geverifieerd in hun respectievelijke tenant.

Tenantbeheerders en ontwikkelaars hebben vaak vereisten waarbij een app moet worden beperkt tot een bepaalde set gebruikers. Ontwikkelaars kunnen hetzelfde bereiken door populaire autorisatiepatronen zoals Role Based Access Control (RBAC) te gebruiken, maar deze aanpak vereist een aanzienlijke hoeveelheid werk van een deel van de ontwikkelaar.

Azure AD stelt tenantbeheerders en ontwikkelaars in staat om een app te beperken tot een specifieke set gebruikers of beveiligingsgroepen in de tenant.

## <a name="supported-app-configurations"></a>Ondersteunde app-configuraties

De optie om een app te beperken tot een specifieke set gebruikers of beveiligingsgroepen in een tenant werkt met de volgende typen toepassingen:

- Toepassingen die zijn geconfigureerd voor federatieve enkele aanmelding met SAML-gebaseerde verificatie
- Toepassingsproxytoepassingen die Azure AD-pre-authenticatie gebruiken
- Toepassingen die rechtstreeks zijn gebouwd op het Azure AD-toepassingsplatform en die OAuth 2.0/OpenID Connect-verificatie gebruiken nadat een gebruiker of beheerder toestemming heeft gegeven voor die toepassing.

     > [!NOTE]
     > Deze functie is alleen beschikbaar voor web-app/web-API- en bedrijfstoepassingen. Apps die als native zijn [geregistreerd,](quickstart-v1-integrate-apps-with-azure-ad.md) kunnen niet worden beperkt tot een set gebruikers of beveiligingsgroepen in de tenant.

## <a name="update-the-app-to-enable-user-assignment"></a>De app bijwerken om gebruikerstoewijzing in te schakelen

Er zijn twee manieren om een toepassing te maken met ingeschakelde gebruikerstoewijzing. Een vereist de **rol globale beheerder,** de tweede niet.

### <a name="enterprise-applications-requires-the-global-administrator-role"></a>Bedrijfstoepassingen (vereist de rol Globale beheerder)

1. Ga naar de [**Azure-portal**](https://portal.azure.com/) en meld u aan als **globale beheerder.**
1. Selecteer op de bovenste balk het aangemelde account. 
1. Selecteer **onder Directory**de Azure AD-tenant waar de app wordt geregistreerd.
1. Selecteer **Azure Active Directory**in de navigatie aan de linkerkant . Als Azure Active Directory niet beschikbaar is in het navigatiedeelvenster, voert u de volgende stappen uit:

    1. Selecteer **Alle services** boven aan het linkernavigatiemenu.
    1. Typ **Azure Active Directory** in het zoekvak van het filter en selecteer vervolgens het **Azure Active Directory-item** uit het resultaat.

1. Selecteer In het deelvenster **Azure Active Directory** de optie **Ondernemingstoepassingen** in het navigatiemenu **azure Active Directory** met de linker.
1. Selecteer **Alle toepassingen** om een lijst met al uw toepassingen weer te geven.

     Als u de gewenste toepassing niet ziet, gebruikt u de verschillende filters boven aan de lijst **Alle toepassingen** om de lijst te beperken of omlaag te scrollen om uw toepassing te vinden.

1. Selecteer de toepassing waaraan u een gebruiker of beveiligingsgroep wilt toewijzen in de lijst.
1. Selecteer **eigenschappen** op de pagina **Overzicht** van de toepassing in het linkernavigatiemenu van de toepassing.
1. Zoek de instelling **Gebruikerstoewijzing vereist?** **Yes** Wanneer deze optie is ingesteld op **Ja,** moeten gebruikers eerst aan deze toepassing worden toegewezen voordat ze er toegang toe hebben.
1. Selecteer **Opslaan** om deze configuratiewijziging op te slaan.

### <a name="app-registration"></a>App-registratie

1. Ga naar de [**Azure-portal.**](https://portal.azure.com/)
1. Selecteer op de bovenste balk het aangemelde account. 
1. Selecteer **onder Directory**de Azure AD-tenant waar de app wordt geregistreerd.
1. Selecteer **Azure Active Directory**in de navigatie aan de linkerkant .
1. Selecteer **app-registraties** in het linkernavigatiemenu van Azure Active Directory in het deelvenster **Azure Active** **Directory.**
1. Maak of selecteer de app die u wilt beheren. Je moet **eigenaar** zijn van deze app registratie.
1. Volg op de pagina **Overzicht** van de toepassing de **beheerde toepassing in de** link Met lokale directory onder de essentiële gegevens boven aan de pagina. Dit brengt u naar de _beheerde Enterprise-toepassing_ van uw app-registratie.
1. Selecteer **Eigenschappen**in het navigatieblad aan de linkerkant .
1. Zoek de instelling **Gebruikerstoewijzing vereist?** **Yes** Wanneer deze optie is ingesteld op **Ja,** moeten gebruikers eerst aan deze toepassing worden toegewezen voordat ze er toegang toe hebben.
1. Selecteer **Opslaan** om deze configuratiewijziging op te slaan.

## <a name="assign-users-and-groups-to-the-app"></a>Gebruikers en groepen toewijzen aan de app

Zodra u uw app hebt geconfigureerd om gebruikerstoewijzing in te schakelen, u gebruikers en groepen aan de app toewijzen.

1. Selecteer het deelvenster **Gebruikers en groepen** in het linkernavigatiemenu van de toepassing.
1. Selecteer boven aan de lijst **Gebruikers en groepen** de knop Gebruiker **toevoegen** om het deelvenster **Toewijzing toevoegen** te openen.
1. Selecteer de kiezer **voor gebruikers** in het deelvenster **Toewijzing toevoegen.** 

     Een lijst met gebruikers en beveiligingsgroepen wordt weergegeven samen met een tekstvak om een bepaalde gebruiker of groep te zoeken en te lokaliseren. Met dit scherm u meerdere gebruikers en groepen in één keer selecteren.

1. Zodra u klaar bent met het selecteren van de gebruikers en groepen, drukt u op de knop **Selecteren** onderaan om naar het volgende deel te gaan.
1. Druk op de knop **Toewijzen** onderaan om de toewijzingen van gebruikers en groepen aan de app te voltooien. 
1. Controleer of de gebruikers en groepen die u hebt toegevoegd, worden weergegeven in de lijst met bijgewerkte **gebruikers en groepen.**

