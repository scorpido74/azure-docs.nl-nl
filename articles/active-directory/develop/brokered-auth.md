---
title: Brokered-verificatie in Android | Azure
titlesuffix: Microsoft identity platform
description: Een overzicht van brokered verificatie & autorisatie voor Android in het micro soft Identity-platform
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: f5204ad71efa2587341600d2c5c1e5195d15445e
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74843713"
---
# <a name="brokered-authentication-in-android"></a>Brokered-verificatie in Android

U moet een van de verificatie-Brokers van micro soft gebruiken om deel te nemen aan SSO (single sign-on) voor het hele apparaat en om te voldoen aan beleid voor voorwaardelijke toegang voor organisaties. Integreren met een Broker biedt de volgende voor delen:

- Eenmalige aanmelding voor het apparaat
- Voorwaardelijke toegang voor:
  - Intune-app-beveiliging
  - Apparaatregistratie (Workplace Join)
  - Beheer van mobiele apparaten
- Account beheer op het hele apparaat
  -  via Android AccountManager &-account instellingen
  - "Werk account"-aangepast account type

Op Android is de micro soft Authentication Broker een onderdeel dat is opgenomen in [Microsoft Authenticator app](https://play.google.com/store/apps/details?id=com.azure.authenticator) en [intune-bedrijfsportal](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal)

> [!TIP]
> Er is slechts één toepassing die als host fungeert voor de Broker als de Broker tegelijk actief is. Welke toepassing is actief als Broker, wordt bepaald door de installatie volgorde op het apparaat. Het eerste dat moet worden geïnstalleerd, of de laatste huidige op het apparaat, wordt de actieve Broker.

In het volgende diagram ziet u de relatie tussen uw app, de micro soft Authentication Library (MSAL) en de verificatie-Brokers van micro soft.

![Broker-implementatie diagram](./media/brokered-auth/brokered-deployment-diagram.png)

## <a name="installing-apps-that-host-a-broker"></a>Apps installeren die een Broker hosten

Apps met Broker-hosting kunnen op elk gewenst moment worden geïnstalleerd door de eigenaar van het apparaat vanuit hun App Store (meestal Google Play Store). Sommige Api's (bronnen) worden echter beschermd door beleids regels voor voorwaardelijke toegang waarvoor apparaten zijn vereist:

- Geregistreerd (toegevoegd aan werk plek) en/of
- Geregistreerd in Apparaatbeheer of
- Inge schreven bij Intune-app-beveiliging

Als op een apparaat nog geen Broker-app is geïnstalleerd, geeft MSAL de gebruiker de opdracht om er een te installeren zodra de app interactief een token probeert op te halen. De app moet de gebruiker vervolgens door geven aan de hand van de stappen om het apparaat compatibel te maken met het vereiste beleid.

## <a name="effects-of-installing-and-uninstalling-a-broker"></a>Gevolgen van het installeren en verwijderen van een Broker

### <a name="when-a-broker-is-installed"></a>Wanneer een Broker is geïnstalleerd

Wanneer een Broker is geïnstalleerd op een apparaat, worden alle volgende interactieve token aanvragen (aanroepen naar `acquireToken()`) verwerkt door de broker in plaats van lokaal door MSAL. Elke SSO-status die eerder beschikbaar is voor MSAL, is niet beschikbaar voor de Broker. Als gevolg hiervan moet de gebruiker zich opnieuw verifiëren of een account selecteren uit de bestaande lijst met accounts die bekend zijn bij het apparaat.

Als u een Broker installeert, hoeft de gebruiker zich niet opnieuw aan te melden. Alleen als de gebruiker een `MsalUiRequiredException` moet omzetten, gaat de volgende aanvraag naar de Broker. `MsalUiRequiredException` wordt om een aantal redenen gegenereerd en moet interactief worden opgelost. Dit zijn enkele veelvoorkomende redenen:

- De gebruiker heeft het wacht woord gewijzigd dat is gekoppeld aan het account.
- Het account van de gebruiker voldoet niet meer aan het beleid voor voorwaardelijke toegang.
- De gebruiker heeft de toestemming ingetrokken om de app te koppelen aan hun account.

### <a name="when-a-broker-is-uninstalled"></a>Wanneer een Broker wordt verwijderd

Als er slechts één app voor het hosten van Broker is geïnstalleerd en deze is verwijderd, moet de gebruiker zich opnieuw aanmelden. Als u de actieve Broker verwijdert, worden het account en de bijbehorende tokens van het apparaat verwijderd.

Als Intune-bedrijfsportal is geïnstalleerd en als de actieve Broker fungeert, en Microsoft Authenticator ook is geïnstalleerd, moet de gebruiker zich opnieuw aanmelden als de Intune-bedrijfsportal (actieve Broker) is verwijderd. Zodra de gebruiker zich opnieuw aanmeldt, wordt de app Microsoft Authenticator de actieve Broker.

## <a name="integrating-with-a-broker"></a>Integreren met een Broker

### <a name="generating-a-redirect-uri-for-a-broker"></a>Een omleidings-URI voor een Broker genereren

U moet een omleidings-URI registreren die compatibel is met de Broker. De omleidings-URI voor de Broker moet de pakket naam van uw app bevatten, evenals de door base64 gecodeerde representatie van de hand tekening van uw app.

De indeling van de omleidings-URI is: `msauth://<yourpackagename>/<base64urlencodedsignature>`

Genereer uw base64 URL-gecodeerde hand tekening met behulp van de handtekening sleutels van uw app. Hier volgen enkele voor beelden van opdrachten die gebruikmaken van uw handtekening sleutels voor fout opsporing:

#### <a name="macos"></a>macOS

```bash
keytool -exportcert -alias androiddebugkey -keystore ~/.android/debug.keystore | openssl sha1 -binary | openssl base64
```

#### <a name="windows"></a>Windows

```powershell
keytool -exportcert -alias androiddebugkey -keystore %HOMEPATH%\.android\debug.keystore | openssl sha1 -binary | openssl base64
```

Zie [uw app ondertekenen](https://developer.android.com/studio/publish/app-signing) voor informatie over het ondertekenen van uw app.

> [!IMPORTANT]
> Gebruik de handtekening sleutel van uw productie voor de productie versie van uw app.

### <a name="configure-msal-to-use-a-broker"></a>MSAL configureren voor het gebruik van een Broker

Als u een Broker in uw app wilt gebruiken, moet u bevestigen dat u de omleiding van de Broker hebt geconfigureerd. Neem bijvoorbeeld zowel de door Broker ingeschakelde omleidings-URI op, en geef aan dat u deze hebt geregistreerd, door het volgende op te nemen in uw MSAL-configuratie bestand:

```javascript
"redirect_uri" : "<yourbrokerredirecturi>",
"broker_redirect_uri_registered": true
```

> [!TIP]
> De nieuwe Azure Portal app-registratie GEBRUIKERSINTERFACE helpt u bij het genereren van de Broker omleidings-URI. Als u uw app hebt geregistreerd met behulp van de oudere ervaring of als u de micro soft app-registratie Portal hebt gebruikt, moet u mogelijk de omleidings-URI genereren en de lijst met omleidings-Uri's in de portal hand matig bijwerken.

### <a name="broker-related-exceptions"></a>Uitzonde ringen die betrekking hebben op Broker

MSAL communiceert met de Broker op twee manieren:

- Broker gebonden service
- Android-AccountManager

MSAL maakt eerst gebruik van de Broker gebonden service omdat het aanroepen van deze service geen Android-machtigingen vereist. Als binding met de gebonden service mislukt, gebruikt MSAL de Android AccountManager-API. MSAL doet dit alleen als uw app de `"READ_CONTACTS"` machtiging al heeft gekregen.

Als u een `MsalClientException` met fout code `"BROKER_BIND_FAILURE"`krijgt, zijn er twee opties:

- Vraag de gebruiker om energie optimalisatie uit te scha kelen voor de Microsoft Authenticator-app en de Intune-bedrijfsportal.
- De gebruiker vragen om de `"READ_CONTACTS"` machtiging te verlenen
