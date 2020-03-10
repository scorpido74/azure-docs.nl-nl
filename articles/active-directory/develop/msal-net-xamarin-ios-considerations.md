---
title: Xamarin iOS-overwegingen (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over overwegingen voor het gebruik van Xamarin iOS met micro soft Authentication Library voor .NET (MSAL.NET).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 76e614b605cd07cd5dc454824dd204447f806907
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78377448"
---
# <a name="considerations-for-using-xamarin-ios-with-msalnet"></a>Overwegingen voor het gebruik van Xamarin iOS met MSAL.NET
Wanneer u micro soft Authentication Library voor .NET (MSAL.NET) op Xamarin iOS gebruikt, moet u het volgende doen: 

- De functie `OpenUrl` in `AppDelegate`overschrijven en implementeren.
- Sleutel hanger groepen inschakelen.
- Het delen van token cache inschakelen.
- Toegang tot sleutel hanger inschakelen.
- Bekende problemen met iOS 12 en verificatie begrijpen.

## <a name="implement-openurl"></a>OpenUrl implementeren

Overschrijf de `OpenUrl` methode van de `FormsApplicationDelegate` afgeleide klasse en aanroep `AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs`. Hier volgt een voorbeeld:

```csharp
public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
{
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url);
    return true;
}
```

Voer ook de volgende taken uit: 
* Definieer een URL-schema.
* Machtigingen vereisen voor uw app voor het aanroepen van een andere app.
* Een specifiek formulier hebben voor de omleidings-URL.
* Registreer de omleidings-URL in de [Azure Portal](https://portal.azure.com).

### <a name="enable-keychain-access"></a>Sleutel hanger toegang inschakelen

Als u de toegang tot sleutel keten wilt inschakelen, moet u ervoor zorgen dat uw toepassing een toegangs groep voor de sleutel hanger heeft. U kunt de toegangs groep voor de sleutel hanger instellen wanneer u uw toepassing maakt met behulp van de `WithIosKeychainSecurityGroup()`-API.

Stel de toegangs groep voor de sleutel hanger in voor al uw toepassingen om te profiteren van de cache en eenmalige aanmelding (SSO).

In dit voor beeld van de installatie wordt gebruikgemaakt van MSAL 4. x:
```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
     .WithIosKeychainSecurityGroup("com.microsoft.adalcache")
     .Build();
```

Schakel ook de toegang tot sleutel hanger in het `Entitlements.plist`-bestand in. Gebruik de volgende toegangs groep of uw eigen toegangs groep.

```xml
<dict>
  <key>keychain-access-groups</key>
  <array>
    <string>$(AppIdentifierPrefix)com.microsoft.adalcache</string>
  </array>
</dict>
```

Wanneer u de `WithIosKeychainSecurityGroup()`-API gebruikt, voegt MSAL automatisch uw beveiligings groep toe aan het einde van de *Team-ID* van de toepassing (`AppIdentifierPrefix`). MSAL voegt uw beveiligings groep toe omdat wanneer u uw toepassing in Xcode bouwt, dit hetzelfde doet. Daarom moeten de rechten in het `Entitlements.plist` bestand `$(AppIdentifierPrefix)` vóór de toegangs groep voor de sleutel hanger bevatten.

Zie de documentatie voor IOS- [rechten](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps)voor meer informatie. 

### <a name="enable-token-cache-sharing-across-ios-applications"></a>Het delen van tokens in de cache inschakelen voor iOS-toepassingen

Vanaf MSAL 2. x kunt u een toegangs groep voor de sleutel hanger opgeven om de token cache in meerdere toepassingen te behouden. Met deze instelling kunt u de token cache delen tussen verschillende toepassingen die dezelfde toegangs groep voor de sleutel hanger hebben. U kunt de token kas delen tussen [ADAL.net](https://aka.ms/adal-net) -toepassingen, MSAL.net Xamarin. IOS-toepassingen en systeem eigen IOS-toepassingen die zijn ontwikkeld in [ADAL. objc](https://github.com/AzureAD/azure-activedirectory-library-for-objc) of [MSAL. objc](https://github.com/AzureAD/microsoft-authentication-library-for-objc).

Door de token cache te delen, kunt u eenmalige aanmelding (SSO) toestaan voor alle toepassingen die gebruikmaken van de toegangs groep voor de sleutel hanger.

Als u deze cache wilt delen, gebruikt u de methode `WithIosKeychainSecurityGroup()` om de toegangs groep voor de sleutel hanger in te stellen op dezelfde waarde in alle toepassingen die dezelfde cache delen. In het eerste code voorbeeld in dit artikel ziet u hoe u de-methode gebruikt.

Eerder in dit artikel hebt u geleerd dat MSAL `$(AppIdentifierPrefix)` toevoegt wanneer u de `WithIosKeychainSecurityGroup()`-API gebruikt. MSAL voegt dit element toe omdat de team-ID `AppIdentifierPrefix` ervoor zorgt dat alleen toepassingen die door dezelfde uitgever zijn gemaakt, de sleutel hanger toegang kunnen delen.

> [!NOTE]
> De eigenschap `KeychainSecurityGroup` is afgeschaft.
> 
> Vanaf MSAL 2. x werden ontwikkel aars gedwongen het `TeamId`-voor voegsel toe te voegen wanneer ze de `KeychainSecurityGroup`-eigenschap gebruiken. Maar vanaf MSAL 2.7. x, wanneer u de eigenschap New `iOSKeychainSecurityGroup` gebruikt, wordt het `TeamId`-voor voegsel tijdens runtime door MSAL opgelost. Wanneer u deze eigenschap gebruikt, neemt u het voor voegsel van de `TeamId` niet op in de waarde. Het voor voegsel is niet vereist.
>
> Omdat de eigenschap `KeychainSecurityGroup` verouderd is, gebruikt u de eigenschap `iOSKeychainSecurityGroup`.

### <a name="use-microsoft-authenticator"></a>Microsoft Authenticator gebruiken

Uw toepassing kan Microsoft Authenticator als een Broker gebruiken om het volgende in te scha kelen:

- **SSO**: wanneer u SSO inschakelt, hoeven uw gebruikers zich niet aan te melden bij elke toepassing.
- **Apparaat-id**: gebruik apparaat-id om te verifiëren door toegang te krijgen tot het certificaat van het apparaat. Dit certificaat wordt gemaakt op het apparaat wanneer het is gekoppeld aan de werk plek. Uw toepassing is gereed als de Tenant beheerders voorwaardelijke toegang met betrekking tot de apparaten inschakelen.
- **Verificatie**van de toepassings-id: wanneer een toepassing de Broker aanroept, wordt de omleidings-URL door gegeven. De Broker controleert de omleidings-URL.

Zie [Microsoft Authenticator of Microsoft Intune bedrijfsportal in Xamarin IOS-en Android-toepassingen gebruiken](msal-net-use-brokers-with-xamarin-apps.md)voor meer informatie over het inschakelen van een Broker.

## <a name="known-issues-with-ios-12-and-authentication"></a>Bekende problemen met iOS 12 en verificatie
Micro soft heeft een [beveiligings advies](https://github.com/aspnet/AspNetCore/issues/4647) uitgebracht over een incompatibiliteit tussen IOS 12 en een type verificatie. De incompatibiliteit verbreekt sociale, WSFed-en OIDC-aanmeldingen. Het beveiligings advies helpt ontwikkel aars bij het verwijderen van beveiligings beperkingen van ASP.NET van hun toepassingen om ze compatibel te maken met iOS 12.  

Wanneer u MSAL.NET-toepassingen op Xamarin iOS ontwikkelt, ziet u mogelijk een oneindige lus wanneer u zich probeert aan te melden bij websites van iOS 12. Dit gedrag is vergelijkbaar met dit [ADAL-probleem](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/issues/1329). 

Mogelijk ziet u ook een afbreek ASP.NET Core OIDC-verificatie met iOS 12 Safari. Zie voor meer informatie dit [webkit-probleem](https://bugs.webkit.org/show_bug.cgi?id=188165).

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over eigenschappen voor Xamarin iOS de para graaf [IOS-specifieke overwegingen](https://github.com/Azure-Samples/active-directory-xamarin-native-v2/tree/master/1-Basic#ios-specific-considerations) van het README.MD-bestand van het volgende voor beeld:

Voorbeeld | Platform | Beschrijving
------ | -------- | -----------
[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, Universeel Windows-platform (UWP) | Een eenvoudige Xamarin Forms-app die laat zien hoe u MSAL kunt gebruiken om micro soft-persoonlijke accounts en Azure AD te verifiëren via het Azure AD 2,0-eind punt. De app laat ook zien hoe u het resulterende token gebruikt voor toegang tot Microsoft Graph.

<!--- https://github.com/Azure-Samples/active-directory-xamarin-native-v2/blob/master/ReadmeFiles/Topology.png -->