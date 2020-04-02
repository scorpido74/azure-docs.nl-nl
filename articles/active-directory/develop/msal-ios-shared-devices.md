---
title: Modus voor gedeeld apparaat voor iOS-apparaten
titleSuffix: Microsoft identity platform | Azure
description: Meer informatie over het inschakelen van de modus gedeeld apparaat zodat Firstline Workers een iOS-apparaat kan delen
services: active-directory
author: brandwe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/31/2020
ms.author: brandwe
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 7cecbc48eb362c2c0f1741352e6f7f5f6ad40c9e
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80550251"
---
# <a name="shared-device-mode-for-ios-devices"></a>Modus voor gedeeld apparaat voor iOS-apparaten

> [!NOTE]
> Deze functie is beschikbaar voor openbare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie.

Firstline Workers zoals retailmedewerkers, cockpitmedewerkers en velddienstmedewerkers gebruiken vaak een gedeeld mobiel apparaat om hun werk uit te voeren. Deze gedeelde apparaten kunnen beveiligingsrisico's met zich meebrengen als uw gebruikers hun wachtwoorden of pincodes opzettelijk of niet delen om toegang te krijgen tot klant- en bedrijfsgegevens op het gedeelde apparaat.

Met de modus Gedeeld apparaat u een iOS 13 of hoger apparaat configureren om gemakkelijker en veiliger door werknemers te worden gedeeld. Medewerkers kunnen zich aanmelden en snel toegang krijgen tot klantgegevens. Wanneer ze klaar zijn met hun shift of taak, kunnen ze zich afmelden bij het apparaat en is het onmiddellijk klaar voor gebruik door de volgende werknemer.

De modus Voor gedeeld apparaat biedt ook het beheer van het apparaat met identiteitsbeheer door Microsoft.Shared device mode also provides Microsoft identity-backed management of the device.

Deze functie maakt gebruik van de [Microsoft Authenticator-app](../user-help/user-help-auth-app-overview.md) om de gebruikers op het apparaat te beheren en de [Microsoft Enterprise SSO-plug-in voor Apple-apparaten](apple-sso-plugin.md)te distribueren.

## <a name="create-a-shared-device-mode-app"></a>Een app voor gedeelde apparaatmodus maken

Ontwikkelaars en cloudapparaatbeheerders werken samen om een app voor gedeelde apparaatmodus te maken:

1. **Toepassingsontwikkelaars** schrijven een app met één account (apps met meerdere accountworden niet ondersteund in de modus gedeeld apparaat) en schrijven code om zaken als afmelding van gedeelde apparaten af te handelen.

1. **Apparaatbeheerders** bereiden het apparaat voor om te worden gedeeld met behulp van een MDM-provider (Mobile Device Management), zoals Microsoft Intune), om de apparaten in hun organisatie te beheren. De MDM duwt de Microsoft Authenticator-app naar de apparaten en schakelt 'Gedeelde modus' voor elk apparaat in via een profielupdate naar het apparaat. Deze instelling voor gedeelde modus is wat het gedrag van de ondersteunde apps op het apparaat verandert. Deze configuratie van de MDM-provider stelt de gedeelde apparaatmodus voor het apparaat in en maakt de [Microsoft Enterprise SSO-plug-in voor Apple-apparaten](apple-sso-plugin.md) mogelijk die vereist zijn voor de modus voor gedeeld apparaat.

1. [**Alleen vereist tijdens Openbare Preview**] Een gebruiker met de rol [Cloud Device Administrator](../users-groups-roles/directory-assign-admin-roles.md#cloud-device-administrator) moet vervolgens de Microsoft [Authenticator-app](../user-help/user-help-auth-app-overview.md) starten en zijn apparaat aansluiten bij de organisatie.

    Het lidmaatschap van uw organisatierollen configureren in de Azure-portal: **Azure Active Directory** > **Roles and Administrators** > **Cloud Device Administrator**

Met de volgende secties u uw toepassing bijwerken om de modus gedeeld apparaat te ondersteunen.

## <a name="use-intune-to-enable-shared-device-mode--sso-extension"></a>Intune gebruiken om de modus voor gedeelde apparaten in te schakelen & SSO-extensie

> [!NOTE]
> De volgende stap is alleen vereist tijdens openbare preview.

Uw apparaat moet zijn geconfigureerd om de modus gedeeld apparaat te ondersteunen. Het moet iOS 13+ geïnstalleerd hebben en MDM-ingeschreven zijn. MDM-configuratie moet ook [Microsoft Enterprise SSO-plug-in inschakelen voor Apple-apparaten.](apple-sso-plugin.md) Zie de [Apple-video](https://developer.apple.com/videos/play/tech-talks/301/)voor meer informatie over SSO-extensies.

1. Vertel het apparaat in de Intune Configuration Portal dat het de [Microsoft Enterprise SSO-plug-in voor Apple-apparaten](apple-sso-plugin.md) met de volgende configuratie moet inschakelen:

    - **Type**: Omleiden
    - **Extensie-id**: com.microsoft.azureauthenticator.ssoextension
    - **Team ID**: SGGM6D27TK
    - **URL's**:https://login.microsoftonline.com
    - Aanvullende gegevens om te configureren:
      - Sleutel: sharedDeviceMode
      - Type: Boolean
      - Waarde: True

    Zie de [configuratiedocumentatie van Intune](https://docs.microsoft.com/intune/configuration/ios-device-features-settings)voor meer informatie over configureren met Intune.

1. Configureer vervolgens uw MDM om de Microsoft Authenticator-app via een MDM-profiel naar uw apparaat te pushen.

    Stel de volgende configuratieopties in om de modus Gedeeld apparaat in te schakelen:

    - Configuratie 1:
      - Sleutel: sharedDeviceMode
      - Type: Boolean
      - Waarde: True

## <a name="modify-your-ios-application-to-support-shared-device-mode"></a>Uw iOS-toepassing wijzigen om de modus gedeeld apparaat te ondersteunen

Uw gebruikers zijn afhankelijk van u om ervoor te zorgen dat hun gegevens niet worden gelekt naar een andere gebruiker. De volgende secties geven nuttige signalen om uw toepassing aan te geven dat er een wijziging heeft plaatsgevonden en moet worden verwerkt.

U bent verantwoordelijk voor het controleren van de status van de gebruiker op het apparaat elke keer dat uw app wordt gebruikt en vervolgens de gegevens van de vorige gebruiker wissen. Dit geldt ook als het wordt herladen vanaf de achtergrond in multi-tasking.

Bij een gebruikerswijziging moet u ervoor zorgen dat zowel de gegevens van de vorige gebruiker worden gewist als dat alle gegevens in de cache die in uw toepassing worden weergegeven, worden verwijderd. We raden u en uw bedrijf ten zeerste aan een beveiligingscontroleproces uit te voeren nadat u uw app hebt bijgewerkt om de modus voor gedeeld apparaat te ondersteunen.

### <a name="detect-shared-device-mode"></a>Gedeelde apparaatmodus detecteren

Het detecteren van gedeelde apparaatmodus is belangrijk voor uw toepassing. Veel toepassingen vereisen een wijziging in hun gebruikerservaring (UX) wanneer de toepassing wordt gebruikt op een gedeeld apparaat. Uw toepassing kan bijvoorbeeld een 'Aanmeldingsfunctie' hebben, wat niet geschikt is voor een Firstline Worker omdat ze waarschijnlijk al een account hebben. U ook extra beveiliging toevoegen aan de verwerking van gegevens door uw toepassing als deze zich in de modus voor gedeelde apparaten bevindt.

Gebruik `getDeviceInformationWithParameters:completionBlock:` de API `MSALPublicClientApplication` in de modus om te bepalen of een app wordt uitgevoerd op een apparaat in de modus Gedeeld apparaat.

In de volgende codefragmenten worden `getDeviceInformationWithParameters:completionBlock:` voorbeelden weergegeven van het gebruik van de API.

#### <a name="swift"></a>Swift

```swift
application.getDeviceInformation(with: nil, completionBlock: { (deviceInformation, error) in

    guard let deviceInfo = deviceInformation else {
        return
    }

    let isSharedDevice = deviceInfo.deviceMode == .shared
    // Change your app UX if needed
})
```

#### <a name="objective-c"></a>Objective-C

```objective-c
[application getDeviceInformationWithParameters:nil
                                completionBlock:^(MSALDeviceInformation * _Nullable deviceInformation, NSError * _Nullable error)
{
    if (!deviceInformation)
    {
        return;
    }

    BOOL isSharedDevice = deviceInformation.deviceMode == MSALDeviceModeShared;
    // Change your app UX if needed
}];
```

### <a name="get-the-signed-in-user-and-determine-if-a-user-has-changed-on-the-device"></a>De aangemelde gebruiker inde wereld krijgen en bepalen of een gebruiker is gewijzigd op het apparaat

Een ander belangrijk onderdeel van het ondersteunen van de modus gedeeld apparaat is het bepalen van de status van de gebruiker op het apparaat en het wissen van toepassingsgegevens als een gebruiker is gewijzigd of als er helemaal geen gebruiker op het apparaat is. U bent verantwoordelijk voor het ervoor zorgen dat gegevens niet worden gelekt naar een andere gebruiker.

U kunt `getCurrentAccountWithParameters:completionBlock:` API gebruiken om het momenteel aangemelde account op het apparaat op te vragen.

#### <a name="swift"></a>Swift

```swift
let msalParameters = MSALParameters()
msalParameters.completionBlockQueue = DispatchQueue.main

application.getCurrentAccount(with: msalParameters, completionBlock: { (currentAccount, previousAccount, error) in

    // currentAccount is the currently signed in account
    // previousAccount is the previously signed in account if any
})
```

#### <a name="objective-c"></a>Objective-C

```objective-c
MSALParameters *parameters = [MSALParameters new];
parameters.completionBlockQueue = dispatch_get_main_queue();

[application getCurrentAccountWithParameters:parameters
                             completionBlock:^(MSALAccount * _Nullable account, MSALAccount * _Nullable previousAccount, NSError * _Nullable error)
{
    // currentAccount is the currently signed in account
    // previousAccount is the previously signed in account if any
}];
```

### <a name="globally-sign-in-a-user"></a>Meld je wereldwijd aan bij een gebruiker

Wanneer een apparaat is geconfigureerd als een gedeeld `acquireTokenWithParameters:completionBlock:` apparaat, kan uw toepassing de API aanroepen om zich bij het account aan te melden. Het account is wereldwijd beschikbaar voor alle in aanmerking komende apps op het apparaat nadat de eerste app-borden in het account zijn geïnstalleerd.

#### <a name="objective-c"></a>Objective-C

```objective-c
MSALInteractiveTokenParameters *parameters = [[MSALInteractiveTokenParameters alloc] initWithScopes:@[@"api://myapi/scope"] webviewParameters:[self msalTestWebViewParameters]];

parameters.loginHint = self.loginHintTextField.text;

[application acquireTokenWithParameters:parameters completionBlock:completionBlock];
```

### <a name="globally-sign-out-a-user"></a>Een gebruiker wereldwijd uitloggen

Met de volgende code wordt het aangemelde account verwijderd en worden tokens in de cache gewist van niet alleen de app, maar ook van het apparaat dat zich in de modus gedeeld apparaat bevindt. Het is echter niet duidelijk de *gegevens* van uw toepassing. U moet de gegevens uit uw toepassing wissen en alle gegevens in de cache wissen die uw toepassing aan de gebruiker kan weergeven.

#### <a name="clear-browser-state"></a>Browserstatus wissen

> [!NOTE]
> De volgende stap is alleen vereist tijdens openbare preview.

In deze openbare preview-versie wordt de [Microsoft Enterprise SSO-plug-in voor Apple-apparaten](apple-sso-plugin.md) gewist, waardoor de status alleen voor toepassingen wordt gewist. Het is niet duidelijk staat op de Safari browser. We raden u aan de browsersessie handmatig te wissen om ervoor te zorgen dat er geen sporen van gebruikersstatus achterblijven. U de `signoutFromBrowser` optionele eigenschap hieronder gebruiken om cookies te wissen. Dit zal ertoe leiden dat de browser kort te starten op het apparaat.

#### <a name="swift"></a>Swift

```swift
let account = .... /* account retrieved above */

let signoutParameters = MSALSignoutParameters(webviewParameters: self.webViewParamaters!)
signoutParameters.signoutFromBrowser = true // Only needed for Public Preview.

application.signout(with: account, signoutParameters: signoutParameters, completionBlock: {(success, error) in

    if let error = error {
        // Signout failed
        return
    }

    // Sign out completed successfully
})
```

#### <a name="objective-c"></a>Objective-C

```objective-c
MSALAccount *account = ... /* account retrieved above */;

MSALSignoutParameters *signoutParameters = [[MSALSignoutParameters alloc] initWithWebviewParameters:webViewParameters];
signoutParameters.signoutFromBrowser = YES; // Only needed for Public Preview.

[application signoutWithAccount:account signoutParameters:signoutParameters completionBlock:^(BOOL success, NSError * _Nullable error)
{
    if (!success)
    {
        // Signout failed
        return;
    }

    // Sign out completed successfully
}];
```

## <a name="next-steps"></a>Volgende stappen

Als u de modus gedeeld apparaat in actie wilt weergeven, bevat het volgende codevoorbeeld op GitHub een voorbeeld van het uitvoeren van een Firstline Worker-app op een iOS-apparaat in de modus gedeeld apparaat:

[MSAL iOS Swift Microsoft Graph API-voorbeeld](https://github.com/Azure-Samples/ms-identity-mobile-apple-swift-objc)
