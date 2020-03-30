---
title: Cross-app SSO inschakelen op Android met ADAL | Microsoft Documenten
description: De functies van de ADAL SDK gebruiken om eenmalig aanmelden voor uw toepassingen mogelijk te maken.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.tgt_pltfrm: android
ms.devlang: java
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: brandwe, jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 0b87a9cd0ae29281faad4209f4449d547921835d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154811"
---
# <a name="how-to-enable-cross-app-sso-on-android-using-adal"></a>How to: Cross-app SSO inschakelen op Android met ADAL

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Met eenmalige aanmelding (SSO) kunnen gebruikers hun referenties slechts één keer invoeren en deze referenties automatisch laten werken op verschillende toepassingen en op verschillende platforms die andere toepassingen kunnen gebruiken (zoals Microsoft-accounts of een werkaccount van Microsoft 365) geen zaak de uitgever.

Het identiteitsplatform van Microsoft, samen met de SDK's, maakt het eenvoudig om SSO in te schakelen binnen uw eigen suite van apps, of met de broker-mogelijkheden en Authenticator-toepassingen, op het hele apparaat.

In deze how-to leert u hoe u de SDK binnen uw toepassing configureert om SSO aan uw klanten te leveren.

## <a name="prerequisites"></a>Vereisten

Deze how-to gaat ervan uit dat je weet hoe je:

- Uw app inrichten met de verouderde portal voor Azure Active Directory (Azure AD). Zie [Een app registreren](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) voor meer informatie
- Integreer uw toepassing met de [Azure AD Android SDK](https://github.com/AzureAD/azure-activedirectory-library-for-android).

## <a name="single-sign-on-concepts"></a>Concepten met één aanmelding

### <a name="identity-brokers"></a>Identiteitsmakelaars

Microsoft biedt toepassingen voor elk mobiel platform waarmee referenties kunnen worden overbrugd tussen toepassingen van verschillende leveranciers en voor verbeterde functies die één veilige plaats vereisen van waaruit referenties kunnen worden gevalideerd. Dit worden **makelaars**genoemd.

Op iOS en Android worden brokers geleverd via downloadbare applicaties die klanten onafhankelijk installeren of naar het apparaat worden geduwd door een bedrijf dat sommige of alle apparaten voor hun werknemers beheert. Makelaars ondersteunen het beheer van de beveiliging alleen voor sommige toepassingen of het hele apparaat op basis van de configuratie van it-beheerders. In Windows wordt deze functionaliteit geleverd door een accountkiezer die is ingebouwd in het besturingssysteem, technisch bekend als de Web Authentication Broker.

#### <a name="broker-assisted-login"></a>Broker assisted login

Broker-ondersteunde logins zijn login-ervaringen die zich voordoen binnen de broker applicatie en gebruik maken van de opslag en beveiliging van de makelaar om referenties te delen over alle toepassingen op het apparaat dat het identiteitsplatform toe te passen. De implicatie is uw toepassingen zal vertrouwen op de makelaar om gebruikers aan te melden. Op iOS en Android worden deze brokers geleverd via downloadbare applicaties die klanten onafhankelijk installeren of naar het apparaat kunnen worden geduwd door een bedrijf dat het apparaat voor hun gebruiker beheert. Een voorbeeld van dit type toepassing is de Microsoft Authenticator-toepassing op iOS. In Windows wordt deze functionaliteit geleverd door een accountkiezer die is ingebouwd in het besturingssysteem, technisch bekend als de Web Authentication Broker.
De ervaring verschilt per platform en kan soms storend zijn voor gebruikers als ze niet correct worden beheerd. Je bent waarschijnlijk het meest bekend met dit patroon als je de Facebook-applicatie hebt geïnstalleerd en Facebook Connect gebruikt vanuit een andere toepassing. Het identiteitsplatform gebruikt hetzelfde patroon.

Op Android wordt de accountkiezer boven op uw toepassing weergegeven, wat minder storend is voor de gebruiker.

#### <a name="how-the-broker-gets-invoked"></a>Hoe de makelaar wordt aangeroepen

Als een compatibele broker op het apparaat is geïnstalleerd, zoals de Microsoft Authenticator-toepassing, zullen de identiteitSDKs automatisch het werk doen om de makelaar voor u aan te roepen wanneer een gebruiker aangeeft dat hij zich wil aanmelden met een account van het identiteitsplatform.

#### <a name="how-microsoft-ensures-the-application-is-valid"></a>Hoe Microsoft ervoor zorgt dat de toepassing geldig is

De noodzaak om de identiteit van een applicatie bellen naar de makelaar is cruciaal voor de veiligheid die in broker assisted logins. iOS en Android dwingen geen unieke id's af die alleen geldig zijn voor een bepaalde toepassing, dus kwaadwillende toepassingen kunnen de id van een legitieme toepassing "spoofen" en de tokens ontvangen die bedoeld zijn voor de legitieme toepassing. Om ervoor te zorgen dat Microsoft altijd communiceert met de juiste toepassing tijdens runtime, wordt de ontwikkelaar gevraagd om een aangepaste redirectURI te verstrekken bij het registreren van hun toepassing bij Microsoft. **Hoe ontwikkelaars moeten ambachtelijke deze redirect URI wordt besproken in detail hieronder.** Deze aangepaste redirectURI bevat de certificaatduimafdruk van de applicatie en is verzekerd van uniek voor de toepassing door de Google Play Store. Wanneer een applicatie de makelaar belt, de makelaar vraagt het Android-besturingssysteem om het te voorzien van het certificaat duimafdruk dat de makelaar genoemd. De broker verstrekt dit certificaat duimafdruk aan Microsoft in de oproep naar het identiteitssysteem. Als de certificaatduimafdruk van de toepassing niet overeenkomt met het certificaat duimafdruk die de ontwikkelaar ons tijdens de registratie heeft verstrekt, wordt de toegang tot de tokens voor de bron die de toepassing aanvraagt geweigerd. Deze controle zorgt ervoor dat alleen de applicatie die door de ontwikkelaar is geregistreerd tokens ontvangt.

Brokered-SSO logins hebben de volgende voordelen:

* Gebruikers ervaringen SSO in al hun toepassingen, ongeacht de leverancier.
* Uw toepassing kan geavanceerdere zakelijke functies gebruiken, zoals voorwaardelijke toegang en ondersteunings-Intune-scenario's.
* Uw toepassing kan verificatie op basis van certificaten ondersteunen voor zakelijke gebruikers.
* Veiligere aanmeldingservaring omdat de identiteit van de toepassing en de gebruiker worden geverifieerd door de broker-toepassing met extra beveiligingsalgoritmen en versleuteling.

Hier is een weergave van hoe de SDKs werken met de makelaar toepassingen om SSO in te schakelen:

```
+------------+ +------------+   +-------------+
|            | |            |   |             |
|   App 1    | |   App 2    |   |   Someone   |
|            | |            |   |    else's   |
|            | |            |   |     App     |
+------------+ +------------+   +-------------+
|  ADAL SDK  | |  ADAL SDK  |   |  ADAL SDK   |
+-----+------+-+-----+------+-  +-------+-----+
      |              |                  |
      |       +------v------+           |
      |       |             |           |
      |       | Microsoft   |           |
      +-------> Broker      |^----------+
              | Application
              |             |
              +-------------+
              |             |
              |   Broker    |
              |   Storage   |
              |             |
              +-------------+

```

### <a name="turning-on-sso-for-broker-assisted-sso"></a>Het inschakelen van SSO voor broker assisted SSO

De mogelijkheid voor een toepassing om een broker te gebruiken die op het apparaat is geïnstalleerd, is standaard uitgeschakeld. Om uw toepassing bij de makelaar te gebruiken, moet u wat extra configuratie doen en wat code toevoegen aan uw toepassing.

De volgende stappen zijn:

1. Schakel de broker-modus in uw toepassingscode in bij de MS SDK
2. Stel een nieuwe omleiding URI en bieden dat aan zowel de app en uw app registratie
3. De juiste machtigingen instellen in het Android-manifest

#### <a name="step-1-enable-broker-mode-in-your-application"></a>Stap 1: Broker-modus inschakelen in uw toepassing

De mogelijkheid voor uw toepassing om de broker te gebruiken is ingeschakeld wanneer u de 'instellingen' of de eerste instelling van uw verificatie-exemplaar maakt. Ga als volgt te werk om dit in uw app te doen:

```
AuthenticationSettings.Instance.setUseBroker(true);
```

#### <a name="step-2-establish-a-new-redirect-uri-with-your-url-scheme"></a>Stap 2: Een nieuwe omleiding uri instellen met uw URL-schema

Om ervoor te zorgen dat de juiste toepassing de geretourneerde de referentietokens ontvangt, is het nodig om ervoor te zorgen dat de oproep terugnaar uw toepassing wordt teruggebeld op een manier die het Android-besturingssysteem kan verifiëren. Het Android-besturingssysteem gebruikt de hash van het certificaat in de Google Play Store. Deze hash van het certificaat kan niet worden vervalst door een malafide toepassing. Samen met de URI van de broker-toepassing zorgt Microsoft ervoor dat de tokens worden teruggestuurd naar de juiste toepassing. Een unieke omleiding URI is vereist om te worden geregistreerd op de aanvraag.

Uw omleiding URI moet in de juiste vorm van:

`msauth://packagename/Base64UrlencodedSignature`

ex: *msauth://com.example.userapp/IcB5PxIyvbLkbFVtBI%2FitkW%2Fejk%3D*

U deze omleidinguri registreren in uw app-registratie via de [Azure-portal.](https://portal.azure.com/) Zie Integratie met Azure Active [Directory](../develop/active-directory-how-to-integrate.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)voor meer informatie over de registratie van Azure AD-apps.

#### <a name="step-3-set-up-the-correct-permissions-in-your-application"></a>Stap 3: De juiste machtigingen instellen in uw toepassing

De broker-toepassing in Android gebruikt de functie Accounts Manager van het Android-besturingssysteem om referenties voor verschillende toepassingen te beheren. Om de broker in Android te kunnen gebruiken, moet uw app-manifest machtigingen hebben om AccountManager-accounts te gebruiken. Deze machtigingen worden hier in detail besproken in de [Google-documentatie voor Accountmanager](https://developer.android.com/reference/android/accounts/AccountManager.html)

Deze machtigingen zijn met name:

```
GET_ACCOUNTS
USE_CREDENTIALS
MANAGE_ACCOUNTS
```

### <a name="youve-configured-sso"></a>Je hebt SSO geconfigureerd!

Nu zal de identiteit SDK automatisch zowel referenties delen in uw toepassingen en beroep doen op de makelaar als het aanwezig is op hun apparaat.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [saml-protocol voor eenmalig aanmelden](../develop/single-sign-on-saml-protocol.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)
