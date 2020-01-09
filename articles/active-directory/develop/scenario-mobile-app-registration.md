---
title: Mobiele apps registreren die web-Api's aanroepen | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het bouwen van een mobiele app die web-Api's aanroept (de code configuratie van de app)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0684b8002dad8e0cb29bb1b7afb6ac5a2bd4d685
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75423780"
---
# <a name="mobile-app-that-calls-web-apis---app-registration"></a>Mobiele app die web-Api's aanroept-app-registratie

Dit artikel bevat de instructies voor het registreren van apps voor het maken van een mobiele toepassing.

## <a name="supported-accounts-types"></a>Ondersteunde account typen

De account typen die in mobiele toepassingen worden ondersteund, zijn afhankelijk van de ervaring die u wilt inschakelen en de stromen die u wilt gebruiken.

### <a name="audience-for-interactive-token-acquisition"></a>Doel groep voor het verkrijgen van interactief tokens

De meeste mobiele toepassingen gebruiken interactieve verificatie. Als dat het geval is, kunt u gebruikers aanmelden vanuit elk [account type](quickstart-register-app.md#register-a-new-application-using-the-azure-portal)

### <a name="audience-for-integrated-authentication-usernamepassword-and-b2c"></a>Doel groep voor geïntegreerde verificatie, gebruikers naam/wacht woord en B2C

- Als u van plan bent geïntegreerde Windows-authenticatie (mogelijk in UWP-apps) of gebruikers naam/wacht woord te gebruiken, moet uw toepassing zich aanmelden bij uw eigen Tenant (LOB-ontwikkelaar) of in azure Active Directory-organisaties (ISV-scenario). Deze verificatie stromen worden niet ondersteund voor persoonlijke micro soft-accounts
- Als u gebruikers aanmeldt met sociale identiteiten die een B2C-instantie en-beleid door geven, kunt u alleen interactieve en gebruikers naam-wachtwoord verificatie gebruiken. Username: het wacht woord wordt momenteel alleen ondersteund op Xamarin. iOS, Xamarin. Android en UWP.

Zie [scenario's en ondersteunde verificatie stromen](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows) en- [scenario's en ondersteunde platforms en talen](authentication-flows-app-scenarios.md#scenarios-and-supported-platforms-and-languages) voor de grote afbeelding.

## <a name="platform-configuration-and-redirect-uris"></a>Platform configuratie en omleidings-Uri's  

### <a name="interactive-authentication"></a>Interactieve verificatie

Wanneer u een mobiele app bouwt met behulp van interactieve verificatie, is de meest kritieke registratie stap de omleidings-URI. Dit kan worden ingesteld via de [platform configuratie op de Blade authenticatie](https://aka.ms/MobileAppReg).

Met deze ervaring kan uw app gebruikmaken van eenmalige aanmelding (SSO) via de Microsoft Authenticator (en Intune-bedrijfsportal op Android) en wordt het beheer beleid voor apparaten ondersteund.

Houd er rekening mee dat er een preview-ervaring is in de portal voor app-registratie om u te helpen de brokered antwoord-URI te berekenen voor iOS-en Android-toepassingen:

1. Kies in de app-registratie **verificatie** en selectie **try-out voor de nieuwe ervaring**
   ![installatie kopie](https://user-images.githubusercontent.com/13203188/60799285-2d031b00-a173-11e9-9d28-ac07a7ae894a.png)

2. Selecteer **platform
   toevoegen** ![installatie kopie](https://user-images.githubusercontent.com/13203188/60799366-4c01ad00-a173-11e9-934f-f02e26c9429e.png)

3. Wanneer de lijst met platformen wordt ondersteund, selecteert u **IOS** -
   ![installatie kopie](https://user-images.githubusercontent.com/13203188/60799411-60de4080-a173-11e9-9dcc-d39a45826d42.png)

4. Voer de gewenste bundel-ID in en druk vervolgens op **REGI ster**
   ![installatie kopie](https://user-images.githubusercontent.com/13203188/60799477-7eaba580-a173-11e9-9f8b-431f5b09344e.png)

5. De omleidings-URI wordt voor u berekend.
   ![image](https://user-images.githubusercontent.com/13203188/60799538-9e42ce00-a173-11e9-860a-015a1840fd19.png)

Als u de omleidings-URI liever hand matig wilt configureren, kunt u dit doen via het manifest van de toepassing. De aanbevolen indeling is als volgt:

- ***IOS***: `msauth.<BUNDLE_ID>://auth` (bijvoorbeeld ' msauth. com. uwbedrijf. appName://auth ')
- ***Android***: `msauth://<PACKAGE_NAME>/<SIGNATURE_HASH>`
  - De hash van de Android-hand tekening kan worden gegenereerd met de release-of fout opsporings sleutels via de opdracht van het hulp programma.

### <a name="username-password"></a>Gebruikers naam wacht woord

Als uw app alleen gebruikmaakt van de gebruikers naam en het wacht woord, hoeft u geen omleidings-URI voor uw toepassing te registreren. Deze stroom doet inderdaad een afronding op het micro soft Identity platform v 2.0-eind punt en uw toepassing wordt niet teruggebeld op een specifieke URI. U moet echter wel uitdrukken dat uw toepassing een open bare client toepassing is. Deze configuratie wordt bereikt door naar de sectie **verificatie** voor uw toepassing te gaan en kies **Ja**in de Subsectie **Geavanceerde instellingen** om de **toepassing te behandelen als een open bare client** (in de standaard alinea van het **type client** )

## <a name="api-permissions"></a>API-machtigingen

Mobiele toepassingen bellen Api's namens de aangemelde gebruiker. Uw app moet gedelegeerde machtigingen aanvragen, ook wel bereiken genoemd. Afhankelijk van de gewenste ervaring kan dit statisch worden gedaan via de Azure Portal of dynamisch tijdens runtime. Met statische registratie machtigingen kunnen beheerders uw app eenvoudig goed keuren en wordt aanbevolen.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Code configuratie](scenario-mobile-app-configuration.md)
