---
title: Brokered-verificatie in Android | Azure
titleSuffix: Microsoft identity platform
description: Een overzicht van brokered verificatie & autorisatie voor Android in het micro soft Identity-platform
services: active-directory
author: shoatman
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/17/2020
ms.author: shoatman
ms.custom: aaddev
ms.reviewer: shoatman, hahamil, brianmel
ms.openlocfilehash: 2bb48971e86c2b61742735020469865fa969bee3
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91258404"
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

Op Android is de micro soft Authentication Broker een onderdeel dat is opgenomen in [Microsoft Authenticator app](https://play.google.com/store/apps/details?id=com.azure.authenticator) en [intune-bedrijfsportal](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal).

In het volgende diagram ziet u de relatie tussen uw app, de micro soft Authentication Library (MSAL) en de verificatie-Brokers van micro soft.

![Diagram waarin wordt getoond hoe een toepassing is gekoppeld aan MSAL, Broker-apps en de Android-account beheerder.](./media/brokered-auth/brokered-deployment-diagram.png)

## <a name="installing-apps-that-host-a-broker"></a>Apps installeren die een Broker hosten

Apps met Broker-hosting kunnen op elk gewenst moment worden geïnstalleerd door de eigenaar van het apparaat vanuit hun App Store (meestal Google Play Store). Sommige Api's (bronnen) worden echter beschermd door beleids regels voor voorwaardelijke toegang waarvoor apparaten zijn vereist:

- Geregistreerd (toegevoegd aan werk plek) en/of
- Geregistreerd in Apparaatbeheer of
- Inge schreven bij Intune-app-beveiliging

Als op een apparaat nog geen Broker-app is geïnstalleerd, geeft MSAL de gebruiker de opdracht om er een te installeren zodra de app interactief een token probeert op te halen. De app moet de gebruiker vervolgens door geven aan de hand van de stappen om het apparaat compatibel te maken met het vereiste beleid.

## <a name="effects-of-installing-and-uninstalling-a-broker"></a>Gevolgen van het installeren en verwijderen van een Broker

### <a name="when-a-broker-is-installed"></a>Wanneer een Broker is geïnstalleerd

Wanneer een Broker op een apparaat is geïnstalleerd, worden alle volgende interactieve token aanvragen (aanroepen naar `acquireToken()` ) afgehandeld door de broker in plaats van lokaal door MSAL. Elke SSO-status die eerder beschikbaar is voor MSAL, is niet beschikbaar voor de Broker. Als gevolg hiervan moet de gebruiker zich opnieuw verifiëren of een account selecteren uit de bestaande lijst met accounts die bekend zijn bij het apparaat.

Als u een Broker installeert, hoeft de gebruiker zich niet opnieuw aan te melden. Alleen als de gebruiker een moet omzetten `MsalUiRequiredException` , gaat de volgende aanvraag naar de Broker. `MsalUiRequiredException` kan om verschillende redenen worden gegenereerd en moet interactief worden opgelost. Bijvoorbeeld:

- De gebruiker heeft het wacht woord gewijzigd dat is gekoppeld aan het account.
- Het account van de gebruiker voldoet niet meer aan het beleid voor voorwaardelijke toegang.
- De gebruiker heeft de toestemming ingetrokken om de app te koppelen aan hun account.

#### <a name="multiple-brokers"></a>Meerdere Brokers

Als er meerdere makelaars op een apparaat zijn geïnstalleerd, is de eerst geïnstalleerde Broker altijd de actieve Broker. Er kan slechts één Broker actief zijn op een apparaat.

### <a name="when-a-broker-is-uninstalled"></a>Wanneer een Broker wordt verwijderd

Als er slechts één app voor het hosten van Broker is geïnstalleerd en deze is verwijderd, moet de gebruiker zich opnieuw aanmelden. Als u de actieve Broker verwijdert, worden het account en de bijbehorende tokens van het apparaat verwijderd.

Als Intune-bedrijfsportal is geïnstalleerd en als de actieve Broker fungeert, en Microsoft Authenticator ook is geïnstalleerd, moet de gebruiker zich opnieuw aanmelden als de Intune-bedrijfsportal (actieve Broker) is verwijderd. Zodra de gebruiker zich opnieuw aanmeldt, wordt de app Microsoft Authenticator de actieve Broker.

## <a name="integrating-with-a-broker"></a>Integreren met een Broker

### <a name="generating-a-redirect-uri-for-a-broker"></a>Een omleidings-URI voor een Broker genereren

U moet een omleidings-URI registreren die compatibel is met de Broker. De omleidings-URI voor de Broker moet de pakket naam van uw app en de door base64 gecodeerde weer gave van de hand tekening van uw app bevatten.

De indeling van de omleidings-URI is: `msauth://<yourpackagename>/<base64urlencodedsignature>`

U kunt met het [hulp programma](https://manpages.debian.org/buster/openjdk-11-jre-headless/keytool.1.en.html) Keys een met base64 gecodeerde hand tekening-hash genereren met behulp van de handtekening sleutels van uw app en vervolgens de Azure Portal gebruiken om de omleidings-URI te genereren met behulp van die hash.

Linux en macOS:

```bash
keytool -exportcert -alias androiddebugkey -keystore ~/.android/debug.keystore | openssl sha1 -binary | openssl base64
```

Windows:

```powershell
keytool -exportcert -alias androiddebugkey -keystore %HOMEPATH%\.android\debug.keystore | openssl sha1 -binary | openssl base64
```

Nadat u een hand tekening-hash hebt gegenereerd met het *hulp programma*, gebruikt u de Azure Portal om de omleidings-URI te genereren:

1. Meld u aan bij de [Azure Portal](https://protal.azure.com) en selecteer uw Android-app in **app-registraties**.
1. Selecteer **verificatie**  >  **een platform**  >  **Android**toevoegen.
1. In het deel venster **uw Android-app configureren** dat wordt geopend, voert u de **hand tekening-hash** in die u eerder hebt gegenereerd en een **pakket naam**.
1. Selecteer de knop **configureren** .

De Azure Portal genereert de omleidings-URI voor u en wordt weer gegeven in het veld **omleidings-URI** van het configuratie deel venster van **Android** .

Zie [uw app ondertekenen](https://developer.android.com/studio/publish/app-signing) in de gebruikers handleiding van Android Studio voor meer informatie over het ondertekenen van uw app.

> [!IMPORTANT]
> Gebruik de handtekening sleutel van uw productie voor de productie versie van uw app.

### <a name="configure-msal-to-use-a-broker"></a>MSAL configureren voor het gebruik van een Broker

Als u een Broker in uw app wilt gebruiken, moet u bevestigen dat u de omleiding van de Broker hebt geconfigureerd. Neem bijvoorbeeld zowel de door Broker ingeschakelde omleidings-URI op, en geef aan dat u deze hebt geregistreerd, door de volgende instellingen op te nemen in uw MSAL-configuratie bestand:

```json
"redirect_uri" : "<yourbrokerredirecturi>",
"broker_redirect_uri_registered": true
```

### <a name="broker-related-exceptions"></a>Uitzonde ringen die betrekking hebben op Broker

MSAL communiceert met de Broker op twee manieren:

- Broker gebonden service
- Android-AccountManager

MSAL maakt eerst gebruik van de Broker-gebonden service omdat het aanroepen van deze service geen Android-machtigingen vereist. Als binding met de gebonden service mislukt, gebruikt MSAL de Android AccountManager-API. MSAL doet dit alleen als de machtiging al is toegekend aan uw app `"READ_CONTACTS"` .

Als er een met de fout code wordt weer geven `MsalClientException` `"BROKER_BIND_FAILURE"` , zijn er twee opties:

- Vraag de gebruiker om energie optimalisatie uit te scha kelen voor de Microsoft Authenticator-app en de Intune-bedrijfsportal.
- De gebruiker vragen om de machtiging te verlenen `"READ_CONTACTS"`

## <a name="verifying-broker-integration"></a>De integratie van de Broker controleren

Het is mogelijk niet onmiddellijk duidelijk dat de integratie van de Broker werkt, maar u kunt de volgende stappen gebruiken om te controleren:

1. Voltooi op uw Android-apparaat een aanvraag met behulp van de Broker.
1. Zoek in de instellingen op uw Android-apparaat naar een nieuw gemaakt account dat overeenkomt met het account waarmee u bent geverifieerd. Het account moet van het type *work-account*zijn.

U kunt het account uit instellingen verwijderen als u de test wilt herhalen.

## <a name="next-steps"></a>Volgende stappen

Met de [modus gedeeld apparaat voor Android-apparaten](msal-android-shared-devices.md) kunt u een Android-apparaat configureren zodat het eenvoudig kan worden gedeeld door meerdere werk nemers.
