---
title: Cross-app SSO inschakelen op iOS met ADAL | Microsoft Documenten
description: De functies van de ADAL SDK gebruiken om Single Sign On in al uw toepassingen in te schakelen.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: brandwe
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 082cbb931c9dae60b39f9ee5323337bf051fb56d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154777"
---
# <a name="how-to-enable-cross-app-sso-on-ios-using-adal"></a>How to: Cross-app SSO inschakelen op iOS met ADAL

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Met eenmalige aanmelding (SSO) kunnen gebruikers hun referenties slechts één keer invoeren en deze referenties automatisch laten werken op verschillende toepassingen en op verschillende platforms die andere toepassingen kunnen gebruiken (zoals Microsoft-accounts of een werkaccount van Microsoft 365) geen zaak de uitgever.

Het identiteitsplatform van Microsoft, samen met de SDK's, maakt het eenvoudig om SSO in te schakelen binnen uw eigen suite van apps, of met de broker-mogelijkheden en Authenticator-toepassingen, op het hele apparaat.

In deze how-to leert u hoe u de SDK binnen uw toepassing configureert om SSO aan uw klanten te leveren.

Deze how-to is van toepassing op:

* Azure Active Directory (Azure Active Directory)
* Azure Active Directory B2C
* Azure Active Directory B2B
* Voorwaardelijke toegang van Azure Active Directory

## <a name="prerequisites"></a>Vereisten

Deze how-to gaat ervan uit dat je weet hoe je:

* Uw app inrichten met de legacy-portal voor Azure AD. Zie [Een app registreren](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) voor meer informatie
* Integreer uw toepassing met de [Azure AD iOS SDK.](https://github.com/AzureAD/azure-activedirectory-library-for-objc)

## <a name="single-sign-on-concepts"></a>Concepten met één aanmelding

### <a name="identity-brokers"></a>Identiteitsmakelaars

Microsoft biedt toepassingen voor elk mobiel platform waarmee referenties kunnen worden overbrugd tussen toepassingen van verschillende leveranciers en voor verbeterde functies die één veilige plaats vereisen van waaruit referenties kunnen worden gevalideerd. Dit worden **makelaars**genoemd.

Op iOS en Android worden brokers geleverd via downloadbare applicaties die klanten onafhankelijk installeren of naar het apparaat worden geduwd door een bedrijf dat sommige of alle apparaten voor hun werknemers beheert. Makelaars ondersteunen het beheer van de beveiliging alleen voor sommige toepassingen of het hele apparaat op basis van de configuratie van it-beheerders. In Windows wordt deze functionaliteit geleverd door een accountkiezer die is ingebouwd in het besturingssysteem, technisch bekend als de Web Authentication Broker.

### <a name="patterns-for-logging-in-on-mobile-devices"></a>Patronen voor inloggen op mobiele apparaten

Toegang tot referenties op apparaten volgt twee basispatronen:

* Niet-broker assisted logins
* Broker assisted logins

#### <a name="non-broker-assisted-logins"></a>Niet-broker assisted logins

Niet-broker assisted logins zijn login ervaringen die gebeuren in lijn met de toepassing en gebruik maken van de lokale opslag op het apparaat voor die toepassing. Deze opslag kan worden gedeeld tussen toepassingen, maar de referenties zijn nauw gebonden aan de app of suite van apps met behulp van die referentie. U hebt dit waarschijnlijk in veel mobiele toepassingen ervaren wanneer u een gebruikersnaam en wachtwoord invoert in de toepassing zelf.

Deze aanmeldingen hebben de volgende voordelen:

* Gebruikerservaring bestaat volledig binnen de toepassing.
* Referenties kunnen worden gedeeld voor toepassingen die zijn ondertekend door hetzelfde certificaat, waardoor een enkele aanmeldingservaring wordt geboden aan uw reeks toepassingen.
* Controle over de ervaring van het inloggen wordt verstrekt aan de toepassing voor en na het aanmelden.

Deze aanmeldingen hebben de volgende nadelen:

* Gebruikers kunnen geen single-sign-aan ervaren in alle apps die een Microsoft-identiteit gebruiken, alleen voor de Microsoft-identiteiten die uw toepassing heeft geconfigureerd.
* Uw toepassing kan niet worden gebruikt met meer geavanceerde zakelijke functies, zoals voorwaardelijke toegang of het gebruik van de Intune-suite van producten.
* Uw toepassing kan verificatie op basis van certificaten voor zakelijke gebruikers niet ondersteunen.

Hier vindt u een weergave van hoe de SDK's werken met de gedeelde opslag van uw toepassingen om SSO in te schakelen:

```
+------------+ +------------+  +-------------+
|            | |            |  |             |
|   App 1    | |   App 2    |  |   App 3     |
|            | |            |  |             |
|            | |            |  |             |
+------------+ +------------+  +-------------+
| ADAL SDK  |  |  ADAL SDK  |  |  ADAL SDK   |
+------------+-+------------+--+-------------+
|                                            |
|            App Shared Storage              |
+--------------------------------------------+
```

#### <a name="broker-assisted-logins"></a>Broker assisted logins

Broker-ondersteunde logins zijn login-ervaringen die zich voordoen binnen de broker applicatie en gebruik maken van de opslag en beveiliging van de makelaar om referenties te delen over alle toepassingen op het apparaat dat het identiteitsplatform toe te passen. Dit betekent dat uw toepassingen vertrouwen op de makelaar om gebruikers aan te melden. Op iOS en Android worden deze brokers geleverd via downloadbare applicaties die klanten onafhankelijk installeren of naar het apparaat worden geduwd door een bedrijf dat het apparaat voor hun gebruiker beheert. Een voorbeeld van dit type toepassing is de Microsoft Authenticator-toepassing op iOS. In Windows wordt deze functionaliteit geleverd door een accountkiezer die is ingebouwd in het besturingssysteem, technisch bekend als de Web Authentication Broker.

De ervaring verschilt per platform en kan soms storend zijn voor gebruikers als ze niet correct worden beheerd. Je bent waarschijnlijk het meest bekend met dit patroon als je de Facebook-applicatie hebt geïnstalleerd en Facebook Connect gebruikt vanuit een andere toepassing. Het identiteitsplatform gebruikt hetzelfde patroon.

Voor iOS leidt dit tot een "overgangsanimatie" waarbij uw toepassing naar de achtergrond wordt verzonden terwijl de Microsoft Authenticator-toepassingen op de voorgrond komen voor de gebruiker om te selecteren bij welk account hij zich wilt aanmelden.

Voor Android en Windows wordt de accountkiezer boven op uw toepassing weergegeven, wat minder storend is voor de gebruiker.

#### <a name="how-the-broker-gets-invoked"></a>Hoe de makelaar wordt aangeroepen

Als een compatibele broker op het apparaat is geïnstalleerd, zoals de Microsoft Authenticator-toepassing, zullen de SDK's automatisch het werk doen om de makelaar voor u in te roepen wanneer een gebruiker aangeeft dat hij zich wil aanmelden met een account van het identiteitsplatform. Dit account kan een persoonlijk Microsoft-account, een werk- of schoolaccount zijn of een account dat u in Azure verstrekt en host met onze B2C- en B2B-producten.

#### <a name="how-we-ensure-the-application-is-valid"></a>Hoe wij ervoor zorgen dat de aanvraag geldig is

De noodzaak om de identiteit van een applicatie die de makelaar roept is cruciaal voor de veiligheid die wij bieden in broker assisted logins te waarborgen. Noch iOS, noch Android dwingt unieke id's af die alleen geldig zijn voor een bepaalde toepassing, dus kwaadaardige toepassingen kunnen de id van een legitieme toepassing "spoofen" en de tokens ontvangen die bedoeld zijn voor de legitieme toepassing. Om ervoor te zorgen dat we altijd communiceren met de juiste toepassing tijdens runtime, vragen we de ontwikkelaar om een aangepaste redirectURI te bieden bij het registreren van hun toepassing bij Microsoft. Hoe ontwikkelaars moeten ambachtelijke deze redirect URI wordt besproken in detail hieronder. Deze aangepaste redirectURI bevat de bundel-ID van de applicatie en is verzekerd van uniek zijn voor de toepassing door de Apple App Store. Wanneer een toepassing de makelaar aanroept, vraagt de makelaar het iOS-besturingssysteem om het te voorzien van de bundel-id die de makelaar heeft gebeld. De makelaar verstrekt deze bundel-id aan Microsoft in de oproep naar ons identiteitssysteem. Als de bundel-id van de toepassing niet overeenkomt met de bundel-id die de ontwikkelaar ons tijdens de registratie heeft verstrekt, weigeren we de toegang tot de tokens voor de bron die de toepassing aanvraagt. Deze controle zorgt ervoor dat alleen de applicatie die door de ontwikkelaar is geregistreerd tokens ontvangt.

**De ontwikkelaar heeft de keuze of de SDK de makelaar belt of gebruik maakt van de niet-broker assisted flow.** Maar als de ontwikkelaar ervoor kiest om de broker-ondersteunde stroom niet te gebruiken, verliezen ze het voordeel van het gebruik van SSO-referenties die de gebruiker mogelijk al op het apparaat heeft toegevoegd en voorkomt dat de toepassing ervan wordt gebruikt met zakelijke functies Microsoft biedt zijn klanten zoals Voorwaardelijke toegang, Intune-beheermogelijkheden en verificatie op basis van certificaten.

Deze aanmeldingen hebben de volgende voordelen:

* Gebruikers ervaringen SSO in al hun toepassingen, ongeacht de leverancier.
* Uw toepassing kan geavanceerdere zakelijke functies gebruiken, zoals Voorwaardelijke toegang of het Intune-productpakket gebruiken.
* Uw toepassing kan verificatie op basis van certificaten ondersteunen voor zakelijke gebruikers.
* Veel veiliger aanmeldingservaring als de identiteit van de toepassing en de gebruiker worden geverifieerd door de broker-toepassing met extra beveiligingsalgoritmen en versleuteling.

Deze aanmeldingen hebben de volgende nadelen:

* In iOS wordt de gebruiker overgestapt van de ervaring van uw toepassing terwijl referenties worden gekozen.
* Verlies van de mogelijkheid om de inlogervaring voor uw klanten binnen uw applicatie te beheren.

Hier is een weergave van hoe de SDKs werken met de makelaar toepassingen om SSO in te schakelen:

```
+------------+ +------------+   +-------------+
|            | |            |   |             |
|   App 1    | |   App 2    |   |   Someone   |
|            | |            |   |    Else's   |
|            | |            |   |     App     |
+------------+ +------------+   +-------------+
| Azure SDK  | | Azure SDK  |   | Azure SDK   |
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

## <a name="enabling-cross-app-sso-using-adal"></a>Cross-app SSO inschakelen met ADAL

Hier gebruiken we de ADAL iOS SDK om:

* SSO met niet-broker-ondersteuning inschakelen voor uw reeks apps
* Ondersteuning inschakelen voor sso met een assistentie bij een broker

### <a name="turning-on-sso-for-non-broker-assisted-sso"></a>Het inschakelen van SSO voor niet-broker assisted SSO

Voor niet-broker assisted SSO in verschillende toepassingen beheren de SDK's een groot deel van de complexiteit van SSO voor u. Dit omvat het vinden van de juiste gebruiker in de cache en het bijhouden van een lijst met ingelogde gebruikers die u opvragen.

Om SSO in te schakelen voor toepassingen die u bezit, moet u het volgende doen:

1. Zorg ervoor dat al uw toepassingen dezelfde client-id of toepassings-id gebruiken.
2. Zorg ervoor dat al uw toepassingen hetzelfde ondertekeningscertificaat van Apple delen, zodat u sleutelhangers delen.
3. Vraag voor elk van uw toepassingen hetzelfde sleutelketenrecht aan.
4. Vertel de SDKs over de gedeelde sleutelhanger die u wilt dat we gebruiken.

#### <a name="using-the-same-client-id--application-id-for-all-the-applications-in-your-suite-of-apps"></a>Dezelfde client-id / toepassings-id gebruiken voor alle toepassingen in uw reeks apps

Om ervoor te zorgen dat het identiteitsplatform weet dat het toegestaan is om tokens te delen in uw toepassingen, moet elk van uw toepassingen dezelfde client-id of toepassings-id delen. Dit is de unieke id die u werd verstrekt toen u uw eerste toepassing in de portal registreerde.

Met URI's u verschillende apps identificeren naar de Microsoft-identiteitsservice als deze dezelfde toepassings-id gebruikt. Elke toepassing kan meerdere Omleidings-URI's hebben die zijn geregistreerd in de onboarding-portal. Elke app in uw suite heeft een andere omleiding URI. Een voorbeeld van hoe dit eruit ziet is hieronder:

App1 Redirect URI:`x-msauth-mytestiosapp://com.myapp.mytestapp`

App2 Redirect URI:`x-msauth-mytestiosapp://com.myapp.mytestapp2`

App3 Redirect URI:`x-msauth-mytestiosapp://com.myapp.mytestapp3`

....

Deze zijn genest onder dezelfde client-ID / applicatie-ID en opgezocht op basis van de omleiding URI u terug naar ons in uw SDK-configuratie.

```
+-------------------+
|                   |
|  Client ID        |
+---------+---------+
          |
          |           +-----------------------------------+
          |           |  App 1 Redirect URI               |
          +----------^+                                   |
          |           +-----------------------------------+
          |
          |           +-----------------------------------+
          +----------^+  App 2 Redirect URI               |
          |           |                                   |
          |           +-----------------------------------+
          |
          +----------^+-----------------------------------+
                      |  App 3 Redirect URI               |
                      |                                   |
                      +-----------------------------------+

```

Het formaat van deze omleiding URI's wordt hieronder uitgelegd. U een Redirect URI gebruiken, tenzij u de makelaar wilt ondersteunen, in welk geval ze er ongeveer hetzelfde uit moeten zien als het bovenstaande*

#### <a name="create-keychain-sharing-between-applications"></a>Sleutelhangerdelen tussen toepassingen maken

Het inschakelen van het delen van sleutelhangers valt buiten het bereik van dit document en wordt door Apple behandeld in hun mogelijkheden [voor het toevoegen van documenten](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html). Wat belangrijk is, is dat u beslist hoe u uw sleutelhanger wilt noemen en die mogelijkheid voor al uw toepassingen toevoegt.

Wanneer u rechten correct hebt ingesteld, ziet u een bestand `entitlements.plist` in uw projectmap met de titel dat iets bevat dat er als volgt uitziet:

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "https://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>keychain-access-groups</key>
    <array>
        <string>$(AppIdentifierPrefix)com.myapp.mytestapp</string>
        <string>$(AppIdentifierPrefix)com.myapp.mycache</string>
    </array>
</dict>
</plist>
```

Zodra u het keychain-recht hebt ingeschakeld in elk van uw toepassingen en u klaar bent om SSO te `ADAuthenticationSettings` gebruiken, vertelt u de identiteit SDK over uw sleutelhanger door de volgende instelling in uw met de volgende instelling te gebruiken:

```
defaultKeychainSharingGroup=@"com.myapp.mycache";
```

> [!WARNING]
> Wanneer u een sleutelhanger deelt in uw toepassingen, kan elke toepassing gebruikers verwijderen of slechter alle tokens in uw toepassing verwijderen. Dit is vooral rampzalig als u toepassingen hebt die afhankelijk zijn van de tokens om achtergrondwerk te doen. Het delen van een sleutelhanger betekent dat u zeer voorzichtig moet zijn in alle bewerkingen verwijderen via de identiteit SDKs.

Dat is alles. De SDK deelt nu referenties voor al uw toepassingen. De gebruikerslijst wordt ook gedeeld tussen toepassingsinstanties.

### <a name="turning-on-sso-for-broker-assisted-sso"></a>Het inschakelen van SSO voor broker assisted SSO

De mogelijkheid voor een toepassing om een broker te gebruiken die op het apparaat is geïnstalleerd, is **standaard uitgeschakeld.** Om uw toepassing te gebruiken bij de makelaar moet u wat extra configuratie doen en wat code toevoegen aan uw toepassing.

De volgende stappen zijn:

1. Schakel de broker-modus in in de oproep van uw toepassingscode naar de MS SDK.
2. Stel een nieuwe omleiding URI en bieden dat aan zowel de app en uw app registratie.
3. Een URL-schema registreren.
4. Voeg een machtiging toe aan uw info.plist-bestand.

#### <a name="step-1-enable-broker-mode-in-your-application"></a>Stap 1: Broker-modus inschakelen in uw toepassing

De mogelijkheid voor uw toepassing om de broker te gebruiken is ingeschakeld wanneer u de 'context' of de eerste instelling van uw verificatieobject maakt. U doet dit door het type referenties in uw code in te stellen:

```
/*! See the ADCredentialsType enumeration definition for details */
@propertyADCredentialsType credentialsType;
```
De `AD_CREDENTIALS_AUTO` instelling zal de SDK in staat stellen `AD_CREDENTIALS_EMBEDDED` om te proberen te roepen naar de makelaar, zal voorkomen dat de SDK van het bellen naar de makelaar.

#### <a name="step-2-registering-a-url-scheme"></a>Stap 2: Een URL-schema registreren

Het identiteitsplatform gebruikt URL's om de broker aan te roepen en vervolgens het controlebeheer terug te sturen naar uw toepassing. Om die rondreis af te maken heb je een URL-schema nodig dat is geregistreerd voor je applicatie waarvan het identiteitsplatform op de hoogte is. Dit kan in aanvulling op alle andere app regelingen die u eerder hebt geregistreerd bij uw aanvraag.

> [!WARNING]
> We raden u aan het URL-schema vrij uniek te maken om de kans te minimaliseren dat een andere app hetzelfde URL-schema gebruikt. Apple handhaaft niet de uniciteit van URL-schema's die zijn geregistreerd in de app store.

Hieronder vindt u een voorbeeld van hoe dit wordt weergegeven in uw projectconfiguratie. U dit ook doen in XCode:

```
<key>CFBundleURLTypes</key>
<array>
    <dict>
        <key>CFBundleTypeRole</key>
        <string>Editor</string>
        <key>CFBundleURLName</key>
        <string>com.myapp.mytestapp</string>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>x-msauth-mytestiosapp</string>
        </array>
    </dict>
</array>
```

#### <a name="step-3-establish-a-new-redirect-uri-with-your-url-scheme"></a>Stap 3: Een nieuwe omleiding uri instellen met uw URL-schema

Om ervoor te zorgen dat we de referentietokens altijd terugsturen naar de juiste toepassing, moeten we ervoor zorgen dat we terugbellen naar uw toepassing op een manier die het iOS-besturingssysteem kan verifiëren. Het iOS-besturingssysteem rapporteert aan de Microsoft broker-toepassingen de bundel-id van de toepassing die het aanroept. Dit kan niet worden vervalst door een malafide toepassing. Daarom maken we gebruik van dit samen met de URI van onze broker-applicatie om ervoor te zorgen dat de tokens worden teruggestuurd naar de juiste toepassing. We vereisen dat u deze unieke omleiding URI zowel in uw toepassing en ingesteld als een Redirect URI in onze ontwikkelaar portal vast te stellen.

Uw omleiding URI moet in de juiste vorm van:

`<app-scheme>://<your.bundle.id>`

ex: *x-msauth-mytestiosapp://com.myapp.mytestapp*

Deze omleiding URI moet worden opgegeven in uw app-registratie met behulp van de [Azure-portal.](https://portal.azure.com/) Zie Integratie met Azure Active [Directory](../develop/active-directory-how-to-integrate.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)voor meer informatie over de registratie van Azure AD-apps.

##### <a name="step-3a-add-a-redirect-uri-in-your-app-and-dev-portal-to-support-certificate-based-authentication"></a>Stap 3a: Een omleidingsURI toevoegen in uw app en dev-portal om verificatie op basis van certificaten te ondersteunen

Om verificatie op basis van certificaten te ondersteunen, moet een tweede "msauth" worden geregistreerd in uw toepassing en de [Azure-portal](https://portal.azure.com/) om certificaatverificatie te verwerken als u die ondersteuning in uw toepassing wilt toevoegen.

`msauth://code/<broker-redirect-uri-in-url-encoded-form>`

ex: *msauth://code/x-msauth-mytestiosapp%3A%2F%2Fcom.myapp.mytestapp*

#### <a name="step-4-add-a-configuration-parameter-to-your-app"></a>Stap 4: Een configuratieparameter toevoegen aan uw app

ADAL gebruikt -canOpenURL: om te controleren of de broker op het apparaat is geïnstalleerd. In iOS 9 aan heeft Apple vergrendeld welke schema's een toepassing kan bevragen. U moet "msauth" toevoegen aan de sectie LSApplicationQueriesSchemes van uw `info.plist file`.

```
    <key>LSApplicationQueriesSchemes</key>
    <array>
        <string>msauth</string>
    </array>

```

### <a name="youve-configured-sso"></a>Je hebt SSO geconfigureerd!

Nu zal de identiteit SDK automatisch zowel referenties delen in uw toepassingen en beroep doen op de makelaar als het aanwezig is op hun apparaat.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [saml-protocol voor eenmalig aanmelden](../develop/single-sign-on-saml-protocol.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)
