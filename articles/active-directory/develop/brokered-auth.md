---
title: Gebrokereerde verificatie in Android | Azure
titlesuffix: Microsoft identity platform
description: Een overzicht van gebrokereerde verificatie & autorisatie voor Android in het Microsoft-identiteitsplatform
services: active-directory
author: shoatman
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/14/2019
ms.author: shoatman
ms.custom: aaddev
ms.reviewer: shoatman, hahamil, brianmel
ms.openlocfilehash: a734589178438fd65d9a2d156fd91fc82807f578
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76697894"
---
# <a name="brokered-authentication-in-android"></a>Gebrokereerde verificatie in Android

U moet een van de verificatiemakelaars van Microsoft gebruiken om deel te nemen aan apparaatbrede Single Sign-On (SSO) en om te voldoen aan het beleid voor voorwaardelijke toegang voor organisaties. Integratie met een makelaar biedt de volgende voordelen:

- Apparaatenkele aanmelding
- Voorwaardelijke toegang voor:
  - Intune-app-beveiliging
  - Apparaatregistratie (Workplace Join)
  - Beheer van mobiele apparaten
- Accountbeheer voor het hele apparaat
  -  via Android AccountManager & Accountinstellingen
  - 'Werkaccount' - aangepast accounttype

Op Android is de Microsoft Authentication Broker een onderdeel dat is opgenomen in [Microsoft Authenticator App](https://play.google.com/store/apps/details?id=com.azure.authenticator) en [Intune Company Portal](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal)

> [!TIP]
> Slechts één toepassing die de makelaar host, is tegelijkertijd actief als de makelaar. Welke toepassing actief is als makelaar wordt bepaald door de installatievolgorde op het apparaat. De eerste die wordt geïnstalleerd, of het laatste geschenk op het apparaat, wordt de actieve makelaar.

In het volgende diagram wordt de relatie tussen uw app, de Microsoft Authentication Library (MSAL) en de verificatiemakelaars van Microsoft geïllustreerd.

![Implementatiediagram voor broker](./media/brokered-auth/brokered-deployment-diagram.png)

## <a name="installing-apps-that-host-a-broker"></a>Apps installeren die een broker hosten

Broker-hosting apps kunnen op elk gewenst moment door de eigenaar van het apparaat worden geïnstalleerd in de app store (meestal Google Play Store). Sommige API's (resources) worden echter beschermd door beleid voor voorwaardelijke toegang, waarvoor apparaten moeten worden:

- Geregistreerd (werkplek samengevoegd) en/of
- Ingeschreven voor apparaatbeheer of
- Ingeschreven in Intune-appbeveiliging

Als een apparaat nog geen broker-app heeft geïnstalleerd, instrueert MSAL de gebruiker er een te installeren zodra de app probeert een token interactief te krijgen. De app moet de gebruiker dan door de stappen leiden om het apparaat aan het vereiste beleid te laten voldoen.

## <a name="effects-of-installing-and-uninstalling-a-broker"></a>Effecten van het installeren en verwijderen van een makelaar

### <a name="when-a-broker-is-installed"></a>Wanneer een makelaar is geïnstalleerd

Wanneer een broker op een apparaat is geïnstalleerd, `acquireToken()`worden alle daaropvolgende interactieve tokenaanvragen (oproepen naar) behandeld door de makelaar in plaats van lokaal door MSAL. Elke SSO-status die voorheen beschikbaar was voor MSAL is niet beschikbaar voor de makelaar. Als gevolg hiervan moet de gebruiker opnieuw verifiëren of een account selecteren in de bestaande lijst met accounts die bekend zijn met het apparaat.

Het installeren van een makelaar vereist niet dat de gebruiker zich opnieuw aanmeldt. Alleen wanneer de gebruiker `MsalUiRequiredException` een oplossing moet oplossen, gaat het volgende verzoek naar de makelaar. `MsalUiRequiredException`wordt gegooid om een aantal redenen, en moet interactief worden opgelost. Dit zijn enkele veel voorkomende redenen:

- De gebruiker heeft het wachtwoord gewijzigd dat aan zijn/haar account is gekoppeld.
- Het account van de gebruiker voldoet niet meer aan een beleid voor voorwaardelijke toegang.
- De gebruiker heeft zijn toestemming ingetrokken om de app aan zijn account te laten gekoppeld.

### <a name="when-a-broker-is-uninstalled"></a>Wanneer een broker is verwijderd

Als er slechts één broker hosting app is geïnstalleerd en deze wordt verwijderd, moet de gebruiker zich opnieuw aanmelden. Als u de actieve broker verwijdert, verwijdert u het account en de bijbehorende tokens van het apparaat.

Als Intune Company Portal is geïnstalleerd en werkt als de actieve broker, en Microsoft Authenticator is ook geïnstalleerd, dan als de Intune Company Portal (actieve broker) is verwijderd, moet de gebruiker zich opnieuw aanmelden. Zodra ze zich opnieuw aanmelden, wordt de Microsoft Authenticator-app de actieve broker.

## <a name="integrating-with-a-broker"></a>Integreren met een makelaar

### <a name="generating-a-redirect-uri-for-a-broker"></a>Het genereren van een omleiding URI voor een makelaar

U moet een omleiding URI die compatibel is met de makelaar registreren. De omleiding URI voor de makelaar moet de pakketnaam van uw app bevatten, evenals de base64 gecodeerde weergave van de handtekening van uw app.

Het formaat van de omleiding URI is:`msauth://<yourpackagename>/<base64urlencodedsignature>`

Genereer uw Base64 url gecodeerde handtekening met behulp van de ondertekeningssleutels van uw app. Hier volgen enkele voorbeeldopdrachten die uw handtekeningsleutels voor foutopsporingssleutels gebruiken:

#### <a name="macos"></a>macOS

```bash
keytool -exportcert -alias androiddebugkey -keystore ~/.android/debug.keystore | openssl sha1 -binary | openssl base64
```

#### <a name="windows"></a>Windows

```powershell
keytool -exportcert -alias androiddebugkey -keystore %HOMEPATH%\.android\debug.keystore | openssl sha1 -binary | openssl base64
```

Zie [Uw app ondertekenen](https://developer.android.com/studio/publish/app-signing) voor informatie over het ondertekenen van uw app.

> [!IMPORTANT]
> Gebruik uw tekensleutel voor de productieproductieversie van uw app.

### <a name="configure-msal-to-use-a-broker"></a>MSAL configureren om een broker te gebruiken

Als u een broker in uw app wilt gebruiken, moet u bevestigen dat u uw broker-omleiding hebt geconfigureerd. Neem bijvoorbeeld zowel uw broker ingeschakeld omleiden URI - en geven aan dat u het hebt geregistreerd - door het volgende op te nemen in uw MSAL-configuratiebestand:

```javascript
"redirect_uri" : "<yourbrokerredirecturi>",
"broker_redirect_uri_registered": true
```

> [!TIP]
> Met de nieuwe gebruikersinterface voor de registratie van azure-portal-apps u uri doordeleiden. Als u uw app hebt geregistreerd met behulp van de oudere ervaring of dit hebt gedaan met behulp van de microsoft-appregistratieportal, moet u mogelijk de omleidings-URI genereren en de lijst met omleidings-URI's in de portal handmatig bijwerken.

### <a name="broker-related-exceptions"></a>Uitzonderingen op basis van broker

MSAL communiceert op twee manieren met de makelaar:

- Broker gebonden service
- Android-accountmanager

MSAL maakt eerst gebruik van de broker gebonden service omdat het aanroepen van deze service geen Android-machtigingen vereist. Als binding aan de gebonden service mislukt, gebruikt MSAL de Android AccountManager API. MSAL doet dit alleen als uw `"READ_CONTACTS"` app al toestemming heeft gekregen.

Als u `MsalClientException` een met `"BROKER_BIND_FAILURE"`foutcode krijgt, dan zijn er twee opties:

- Vraag de gebruiker om energieoptimalisatie uit te schakelen voor de Microsoft Authenticator-app en de Intune Company Portal.
- De gebruiker vragen `"READ_CONTACTS"` om de toestemming te verlenen
