---
title: Microsoft Enterprise SSO-invoegtoepassing voor Apple-apparaten
titleSuffix: Microsoft identity platform | Azure
description: Meer informatie over de Azure Active Directory SSO-invoeg toepassing van micro soft voor iOS-en macOS-apparaten.
services: active-directory
author: brandwe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/15/2020
ms.author: brandwe
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: ec0ab4601e15129ecd8917e0e750a3e1661dc558
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91530694"
---
# <a name="microsoft-enterprise-sso-plug-in-for-apple-devices-preview"></a>Micro soft Enter prise SSO-invoeg toepassing voor Apple-apparaten (preview-versie)

> [!NOTE]
> Deze functie is beschikbaar voor openbare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

De *micro soft Enter PRISE SSO-invoeg toepassing voor Apple-apparaten* biedt eenmalige aanmelding (SSO) voor Azure Active Directory (Azure AD)-accounts voor alle toepassingen die ondersteuning bieden voor de functie voor [eenmalige aanmelding voor ondernemingen](https://developer.apple.com/documentation/authenticationservices) van Apple. Micro soft werkte nauw samen met Apple voor het ontwikkelen van deze invoeg toepassing om de bruikbaarheid van uw toepassingen te verhogen en de beste beveiliging te bieden die door Apple en micro soft kan worden geboden.

In deze open bare preview-versie is de Enter prise SSO-invoeg toepassing alleen beschikbaar voor iOS-apparaten en wordt deze in bepaalde micro soft-toepassingen gedistribueerd.

## <a name="features"></a>Functies

De micro soft Enter prise SSO-invoeg toepassing voor Apple-apparaten biedt de volgende voor delen:

- Voorziet in SSO voor Azure AD-accounts voor alle toepassingen die ondersteuning bieden voor de functie voor eenmalige aanmelding van Apple.
- Wordt automatisch geleverd in de Microsoft Authenticator en kan worden ingeschakeld door elke Mobile Device Management (MDM)-oplossing.

## <a name="requirements"></a>Vereisten

Micro soft Enter prise SSO-invoeg toepassing gebruiken voor Apple-apparaten:

- iOS 13,0 of hoger moet op het apparaat zijn geïnstalleerd.
- Een micro soft-toepassing die de micro soft Enter prise SSO-invoeg toepassing voor Apple-apparaten levert, moet op het apparaat zijn geïnstalleerd. Voor een open bare preview omvatten deze toepassingen de [app Microsoft Authenticator](../user-help/user-help-auth-app-overview.md).
- Apparaat moet zijn Inge schreven bij MDM (bijvoorbeeld met Microsoft Intune).
- De configuratie moet worden gepusht naar het apparaat om de micro soft Enter prise SSO-invoeg toepassing voor Apple-apparaten op het apparaat in te scha kelen. Deze beveiligings beperking wordt vereist door Apple.

## <a name="enable-the-sso-plug-in-with-mobile-device-management-mdm"></a>De SSO-invoeg toepassing inschakelen met Mobile Device Management (MDM)

Als u de micro soft Enter prise SSO-invoeg toepassing voor Apple-apparaten wilt inschakelen, moeten uw apparaten een signaal verzenden via een MDM-service. Omdat micro soft de SSO-invoeg toepassing voor bedrijven in de [Microsoft Authenticator-app](..//user-help/user-help-auth-app-overview.md)bevat, gebruikt u uw MDM om de app te configureren om de micro soft Enter prise SSO-invoeg toepassing in te scha kelen.

Gebruik de volgende para meters voor het configureren van de micro soft Enter prise SSO-invoeg toepassing voor Apple-apparaten:

- **Type**: omleiden
- **Extensie-id**: `com.microsoft.azureauthenticator.ssoextension`
- **Team-ID**: (dit veld is niet nodig voor IOS)
- **Url's**:
  - `https://login.microsoftonline.com`
  - `https://login.microsoft.com`
  - `https://sts.windows.net`
  - `https://login.partner.microsoftonline.cn`
  - `https://login.chinacloudapi.cn`
  - `https://login.microsoftonline.de`
  - `https://login.microsoftonline.us`
  - `https://login.usgovcloudapi.net`
  - `https://login-us.microsoftonline.com`
  
### <a name="additional-configuration-options"></a>Extra configuratieopties
Er kunnen aanvullende configuratie opties worden toegevoegd om SSO-functionaliteit uit te breiden naar extra apps.

#### <a name="enable-sso-for-apps-that-dont-use-msal"></a>Eenmalige aanmelding inschakelen voor apps die geen gebruikmaken van MSAL

De SSO-invoeg toepassing maakt het mogelijk deel te nemen aan eenmalige aanmelding, zelfs als deze niet is ontwikkeld met een micro soft-SDK zoals micro soft Authentication Library (MSAL).

De SSO-invoeg toepassing wordt automatisch geïnstalleerd door apparaten die de Microsoft Authenticator-app hebben gedownload en hun apparaat bij uw organisatie hebben geregistreerd. Uw organisatie gebruikt de verificator-app waarschijnlijk vandaag voor scenario's zoals multi-factor Authentication, wacht woord-less-verificatie en voorwaardelijke toegang. Het kan worden ingeschakeld voor uw toepassingen met een MDM-provider, maar micro soft heeft het eenvoudig geconfigureerd in micro soft Endpoint Manager van intune. Een acceptatie lijst wordt gebruikt om deze toepassingen te configureren voor het gebruik van de SSO-invoeg toepassing die door de verificator-app is geïnstalleerd.

Alleen apps die systeem eigen Apple-netwerk technologieën of webweergave gebruiken, worden ondersteund. Als een toepassing een eigen implementatie van de netwerklaag levert, wordt de micro soft Enter prise SSO-invoeg toepassing niet ondersteund.  

Gebruik de volgende para meters voor het configureren van de micro soft Enter prise SSO-invoeg toepassing voor apps die geen gebruik hoeven te MSAL:

- **Sleutel**: `AppAllowList`
- **Type**: `String`
- **Waarde**: een door komma's gescheiden lijst met toepassings bundel-id's voor de toepassingen die aan de SSO mogen deel nemen
- **Voor beeld**: `com.contoso.workapp, com.contoso.travelapp`

Toegestane [apps](./application-consent-experience.md) die door de MDM-beheerder zijn toegestaan om deel te nemen aan de SSO, kunnen een token ontvangen voor de eind gebruiker. Daarom is het belang rijk om alleen vertrouwde toepassingen toe te voegen aan de acceptatie lijst. 

U hoeft geen toepassingen toe te voegen die gebruikmaken van MSAL of ASWebAuthenticationSession aan deze lijst. Deze toepassingen zijn standaard ingeschakeld. 

#### <a name="allow-creating-sso-session-from-any-application"></a>Het maken van een SSO-sessie vanuit elke toepassing toestaan

Standaard biedt de micro soft Enter prise SSO-invoeg toepassing alleen SSO voor geautoriseerde apps wanneer de SSO-invoeg toepassing al een gedeelde referentie heeft. De invoeg toepassing micro soft Enter prise SSO kan een gedeelde referentie verkrijgen als deze wordt aangeroepen door een andere ADAL-of MSAL-toepassing tijdens het ophalen van tokens. De meeste micro soft-apps gebruiken Microsoft Authenticator of SSO-invoeg toepassing. Dit betekent dat standaard-SSO buiten systeem eigen app-stromen het beste is.  

`browser_sso_interaction_enabled`Als u de vlag inschakelt, kunnen niet-MSAL apps en Safari-browser de eerste Boots trap ping uitvoeren en een gedeelde referentie ophalen. Als de micro soft Enter prise SSO-invoeg toepassing nog geen gedeelde referentie heeft, wordt er een opgehaald wanneer een aanmelding wordt aangevraagd vanuit een Azure AD-URL in de Safari-browser, ASWebAuthenticationSession, SafariViewController of een andere toegestane systeem eigen toepassing.  

- **Sleutel**: `browser_sso_interaction_enabled`
- **Type**: `Integer`
- **Waarde**: 1 of 0

We raden u aan deze vlag in te scha kelen om consistenter te zijn voor alle apps. Deze optie is standaard uitgeschakeld. 

#### <a name="disable-oauth2-application-prompts"></a>OAuth2-toepassings prompts uitschakelen

De micro soft Enter prise SSO-invoeg toepassing biedt SSO door gedeelde referenties toe te voegen aan netwerk aanvragen die afkomstig zijn van toegestane toepassingen. Bij sommige OAuth2-toepassingen wordt de prompt voor eind gebruikers op de protocol-laag mogelijk afgedwongen. Gedeelde referenties worden genegeerd voor deze apps.  

`disable_explicit_app_prompt`Als u deze optie inschakelt, wordt de mogelijkheid van zowel systeem eigen als webtoepassingen beperkt om de prompt van een eind gebruiker op de protocol-laag af te dwingen en SSO over te slaan.

- **Sleutel**: `disable_explicit_app_prompt`
- **Type**: `Integer`
- **Waarde**: 1 of 0

We raden u aan deze vlag in te scha kelen om consistenter te zijn voor alle apps. Deze optie is standaard uitgeschakeld. 

#### <a name="use-intune-for-simplified-configuration"></a>InTune gebruiken voor vereenvoudigde configuratie

U kunt Microsoft Intune als uw MDM-service gebruiken om de configuratie van de micro soft Enter prise SSO-invoeg toepassing te vereenvoudigen. Zie de [intune-configuratie documentatie](/intune/configuration/ios-device-features-settings)voor meer informatie.

## <a name="using-the-sso-plug-in-in-your-application"></a>De SSO-invoeg toepassing in uw toepassing gebruiken

De [micro soft Authentication Library (MSAL) voor Apple-apparaten](https://github.com/AzureAD/microsoft-authentication-library-for-objc) versie 1.1.0 en hoger ondersteunt de micro soft Enter prise SSO-invoeg toepassing voor Apple-apparaten.

Als u een toepassing bouwt voor Frontline-werk scenario's, raadpleegt u de [modus voor gedeelde apparaten voor IOS-apparaten](msal-ios-shared-devices.md) voor meer informatie over het instellen van de functie.

## <a name="how-the-sso-plug-in-works"></a>Hoe de SSO-invoeg toepassing werkt

De micro soft Enter prise SSO-invoeg toepassing is afhankelijk van het [Apple-Framework voor eenmalige aanmelding voor ondernemingen](https://developer.apple.com/documentation/authenticationservices/asauthorizationsinglesignonprovider?language=objc). Id-providers die de onboarding van het Framework mogelijk maken, kunnen netwerk verkeer voor hun domeinen onderscheppen en verbeteren of wijzigen hoe deze aanvragen worden afgehandeld. De SSO-invoeg toepassing kan bijvoorbeeld een extra gebruikers interface weer geven om referenties voor de eind gebruiker veilig te verzamelen, MFA vereist of op de achtergrond tokens voor de toepassing bieden.

Systeem eigen toepassingen kunnen ook aangepaste bewerkingen implementeren en rechtstreeks communiceren met de SSO-invoeg toepassing.
Meer informatie over het Framework voor eenmalige aanmelding vindt u in deze [2019 WWDC-video van Apple](https://developer.apple.com/videos/play/tech-talks/301/)

### <a name="applications-that-use-msal"></a>Toepassingen die gebruikmaken van MSAL

De [micro soft Authentication Library (MSAL) voor Apple-apparaten](https://github.com/AzureAD/microsoft-authentication-library-for-objc) versie 1.1.0 en hoger ondersteunt de micro soft Enter prise SSO-invoeg toepassing voor Apple-apparaten systeem eigen voor werk-en school accounts. 

Er is geen speciale configuratie nodig als u [alle aanbevolen stappen](./quickstart-v2-ios.md) hebt gevolgd en de standaard- [omleidings-URI-indeling](./redirect-uris-ios.md)hebt gebruikt. Wanneer u uitvoert op een apparaat waarop de SSO-invoeg toepassing is geïnstalleerd, wordt deze automatisch door MSAL aangeroepen voor alle interactieve-en Silent-token aanvragen, evenals account inventarisatie en het verwijderen van accounts. Omdat MSAL een systeem eigen SSO-invoeg toepassing implementeert dat afhankelijk is van aangepaste bewerkingen, biedt deze installatie de meest soepele systeem eigen ervaring voor de eind gebruiker. 

Als de SSO-invoeg toepassing niet is ingeschakeld op MDM, maar de Microsoft Authenticator-app aanwezig is op het apparaat, gebruikt MSAL in plaats daarvan de Microsoft Authenticator-app voor alle interactieve token aanvragen. Met de SSO-invoeg toepassing wordt SSO met de Microsoft Authenticator-app gedeeld.

### <a name="applications-that-dont-use-msal"></a>Toepassingen die geen gebruikmaken van MSAL

Toepassingen die geen gebruik maken van MSAL, kunnen nog steeds SSO ophalen als een beheerder deze expliciet toevoegt aan de acceptatie lijst. 

Er zijn geen code wijzigingen nodig in deze apps zolang aan de volgende voor waarden wordt voldaan:

- Toepassing maakt gebruik van Apple frameworks om netwerk aanvragen uit te voeren (bijvoorbeeld [WKWebView](https://developer.apple.com/documentation/webkit/wkwebview), [NSURLSession](https://developer.apple.com/documentation/foundation/nsurlsession)) 
- De toepassing gebruikt standaard protocollen om te communiceren met Azure AD (bijvoorbeeld OAuth2, SAML, WS-Federation)
- De gebruikers naam en het wacht woord van de Lees bare tekst worden niet in de systeem eigen gebruikers interface

In dit geval wordt SSO gegeven wanneer de toepassing een netwerk aanvraag maakt en een webbrowser opent om de gebruiker in te ondertekenen. Wanneer een gebruiker wordt omgeleid naar een aanmeldings-URL voor Azure AD, valideert de SSO-invoeg toepassing de URL en controleert deze of er een SSO-referentie beschikbaar is voor die URL. Als dat het geval is, geeft de SSO-invoeg toepassing de SSO-referentie door aan Azure AD, waarmee de toepassing wordt geautoriseerd om de netwerk aanvraag te volt ooien zonder de gebruiker te vragen hun referenties in te voeren. Als het apparaat wordt herkend door Azure AD, wordt door de SSO-invoeg toepassing ook het certificaat van het apparaat door gegeven om te voldoen aan de op apparaten gebaseerde controle van voorwaardelijke toegang. 

Ter ondersteuning van SSO voor niet-MSAL apps, implementeert de SSO-invoeg toepassing een protocol dat lijkt op de Windows-browser-invoeg toepassing die wordt beschreven in [Wat is een primair vernieuwings token?](../devices/concept-primary-refresh-token.md#browser-sso-using-prt). 

Vergeleken met MSAL-apps, voert de SSO-invoeg toepassing op transparante wijze uit voor niet-MSAL-apps door te integreren met de bestaande browser aanmeldings ervaring die apps bieden. De eind gebruiker ziet hun vertrouwde ervaring, met het voor deel dat er geen aanvullende aanmeldingen hoeven te worden uitgevoerd in elk van de toepassingen. In plaats van de systeem eigen account kiezer weer te geven, voegt de SSO-invoeg toepassing SSO-sessies toe aan de ervaring op webgebaseerde account kiezer. 

## <a name="next-steps"></a>Volgende stappen

Zie [modus voor gedeeld apparaat voor IOS-apparaten](msal-ios-shared-devices.md)voor meer informatie over de modus gedeeld apparaat op Ios.
