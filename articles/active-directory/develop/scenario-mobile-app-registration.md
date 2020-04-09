---
title: Mobiele apps registreren die web-API's oproepen | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het bouwen van een mobiele app die web-API's aanroept (de codeconfiguratie van de app)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: brandwe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: a5b65bbdc790a27a06298a77aac2721e071adec8
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80882706"
---
# <a name="register-mobile-apps-that-call-web-apis"></a>Mobiele apps registreren die web-API's oproepen

Dit artikel bevat instructies om u te helpen een mobiele toepassing te registreren die u maakt.

## <a name="supported-account-types"></a>Ondersteunde accounttypen

De accounttypen die uw mobiele toepassingen ondersteunen, zijn afhankelijk van de ervaring die u wilt inschakelen en de stromen die u wilt gebruiken.

### <a name="audience-for-interactive-token-acquisition"></a>Publiek voor interactieve tokenacquisitie

De meeste mobiele toepassingen maken gebruik van interactieve authenticatie. Als uw app deze vorm van verificatie gebruikt, u gebruikers van elk [accounttype](quickstart-register-app.md#register-a-new-application-using-the-azure-portal)aanmelden.

### <a name="audience-for-integrated-windows-authentication-username-password-and-b2c"></a>Doelgroep voor geïntegreerde Windows-verificatie, gebruikersnaam-wachtwoord en B2C

Als u een UWP-app (Universal Windows Platform) hebt, u geïntegreerde Windows-verificatie gebruiken om gebruikers aan te melden. Als u geïntegreerde Windows-verificatie of verificatie van gebruikerswachtwoord wilt gebruiken, moet uw toepassing zich aanmelden voor gebruikers in uw eigen lob-ontwikkelaartenant (Line-of-Business). In een isv-scenario (independent software vendor) kan uw toepassing gebruikers aanmelden in Azure Active Directory-organisaties. Deze verificatiestromen worden niet ondersteund voor persoonlijke Microsoft-accounts.

U zich ook aanmelden voor gebruikers door gebruik te maken van sociale identiteiten die een B2C-autoriteit en -beleid doorstaan. Als u deze methode wilt gebruiken, u alleen interactieve verificatie en verificatie van het gebruikersnaam-wachtwoord gebruiken. Verificatie met gebruikersnaam-wachtwoord wordt momenteel alleen ondersteund op Xamarin.iOS, Xamarin.Android en UWP.

Zie [Scenario's en ondersteunde verificatiestromen](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows) en [scenario's en ondersteunde platforms en talen voor](authentication-flows-app-scenarios.md#scenarios-and-supported-platforms-and-languages)meer informatie.

## <a name="platform-configuration-and-redirect-uris"></a>Platformconfiguratie en omleiding van URI's  

### <a name="interactive-authentication"></a>Interactieve verificatie

Wanneer u een mobiele app bouwt die interactieve verificatie gebruikt, is de meest kritieke registratiestap de omleidinguri. U interactieve verificatie instellen via de [platformconfiguratie op het **verificatieblad.** ](https://aka.ms/MobileAppReg)

Met deze ervaring kan uw app één aanmelding (SSO) krijgen via Microsoft Authenticator (en Intune Company Portal op Android). Het ondersteunt ook het beleid voor apparaatbeheer.

De app-registratieportal biedt een voorbeeldervaring om u te helpen het bemiddelde antwoord URI voor iOS- en Android-toepassingen te berekenen:

1. Selecteer **verificatie** > in de app-registratieportal**En probeer de nieuwe ervaring uit.**

   ![Het verificatieblad, waar u een nieuwe ervaring kiest](https://user-images.githubusercontent.com/13203188/60799285-2d031b00-a173-11e9-9d28-ac07a7ae894a.png)

2. Selecteer **Een platform toevoegen**.

   ![Een platform toevoegen](https://user-images.githubusercontent.com/13203188/60799366-4c01ad00-a173-11e9-934f-f02e26c9429e.png)

3. Wanneer de lijst met platforms wordt ondersteund, selecteert u **iOS**.

   ![Kies een mobiele applicatie](https://user-images.githubusercontent.com/13203188/60799411-60de4080-a173-11e9-9dcc-d39a45826d42.png)

4. Voer uw bundel-id in en selecteer **Register**.

   ![Voer uw bundel-id in](https://user-images.githubusercontent.com/13203188/60799477-7eaba580-a173-11e9-9f8b-431f5b09344e.png)

Wanneer u de stappen voltooit, wordt de omleidings-URI voor u berekend, zoals in de volgende afbeelding.

![De resulterende omleiding URI](https://user-images.githubusercontent.com/13203188/60799538-9e42ce00-a173-11e9-860a-015a1840fd19.png)

Als u de omleidinguri liever handmatig configureert, u dit doen via het toepassingsmanifest. Hier is het aanbevolen formaat voor het manifest:

- **iOS**:`msauth.<BUNDLE_ID>://auth` 
  - Voer bijvoorbeeld`msauth.com.yourcompany.appName://auth`
- **Android**:`msauth://<PACKAGE_NAME>/<SIGNATURE_HASH>`
  - U de Android-handtekeninghash genereren met behulp van de releasesleutel of debugtoets via de opdracht KeyTool.

### <a name="username-password-authentication"></a>Verificatie voor gebruikersnaam-wachtwoord

Als uw app alleen verificatie van gebruikersnaam-wachtwoord gebruikt, hoeft u geen omleidings-URI voor uw toepassing te registreren. Deze stroom doet een rondreis naar het Microsoft-identiteitsplatform versie 2.0 eindpunt. Uw aanvraag wordt niet teruggebeld op een specifieke URI. 

U moet uw toepassing echter identificeren als een openbare clienttoepassing. Ga hiervoor aan de slag in het gedeelte **Verificatie** van uw toepassing. Selecteer in de onderafdeling **Geavanceerde instellingen** in de alinea **Standaardclienttype** voor de toepassing **Vraag Behandelen als openbare client**de optie **Ja**.

## <a name="api-permissions"></a>API-machtigingen

Mobiele toepassingen bellen API's namens de aangemelde gebruiker. Uw app moet gedelegeerde machtigingen aanvragen. Deze machtigingen worden ook scopes genoemd. Afhankelijk van de gewenste ervaring u gedegedelegeerde machtigingen statisch aanvragen via de Azure-portal. Of u ze dynamisch aanvragen tijdens runtime. 

Door machtigingen statisch te registreren, u beheerders toestaan uw app eenvoudig goed te keuren. Statische registratie wordt aanbevolen.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Codeconfiguratie](scenario-mobile-app-configuration.md)
