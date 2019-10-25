---
title: Xamarin iOS-overwegingen (micro soft-verificatie bibliotheek voor .NET)
titleSuffix: Microsoft identity platform
description: Meer informatie over specifieke overwegingen bij het gebruik van Xamarin iOS met de micro soft Authentication Library voor .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/16/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 64524960e584907b1e761a36f8ceb1461a7771c7
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72802609"
---
# <a name="xamarin-ios-specific-considerations-with-msalnet"></a>Xamarin iOS-specifieke overwegingen met MSAL.NET
Op Xamarin iOS zijn er verschillende overwegingen die u moet meenemen bij het gebruik van MSAL.NET

- [Bekende problemen met iOS 12 en verificatie](#known-issues-with-ios-12-and-authentication)
- [De functie `OpenUrl` in het `AppDelegate`overschrijven en implementeren](#implement-openurl)
- [Sleutel hanger groepen inschakelen](#enable-keychain-access)
- [Het delen van token cache inschakelen](#enable-token-cache-sharing-across-ios-applications)
- [Sleutel hanger toegang inschakelen](#enable-keychain-access)

## <a name="known-issues-with-ios-12-and-authentication"></a>Bekende problemen met iOS 12 en verificatie
Micro soft heeft een [beveiligings advies](https://github.com/aspnet/AspNetCore/issues/4647) uitgebracht om informatie te geven over een incompatibiliteit tussen iOS12 en een type verificatie. De incompatibiliteit verbreekt sociale, WSFed-en OIDC-aanmeldingen. Dit advies bevat ook richt lijnen voor het verwijderen van de huidige beveiligings beperkingen die door ASP.NET aan hun toepassingen worden toegevoegd, zodat deze compatibel zijn met iOS12.  

Wanneer u MSAL.NET-toepassingen ontwikkelt op Xamarin iOS, ziet u mogelijk een oneindige lus wanneer u zich probeert aan te melden bij websites van iOS 12 (vergelijkbaar met dit [ADAL-probleem](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/issues/1329). 

Mogelijk ziet u ook een afbreek ASP.NET Core OIDC-verificatie met iOS 12 Safari, zoals beschreven in dit [webkit-probleem](https://bugs.webkit.org/show_bug.cgi?id=188165).

## <a name="implement-openurl"></a>OpenUrl implementeren

Eerst moet u de `OpenUrl` methode van de `FormsApplicationDelegate` afgeleide klasse overschrijven en `AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs`aanroepen.

```CSharp
public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
{
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url);
    return true;
}
```

U moet ook een URL-schema definiëren, machtigingen voor uw app vereisen voor het aanroepen van een andere app, een specifiek formulier voor de omleidings-URL hebben en deze omleidings-URL registreren in de [Azure Portal](https://portal.azure.com)

### <a name="enable-keychain-access"></a>Sleutel hanger toegang inschakelen

Als u de toegang tot de sleutel keten wilt inschakelen, moet uw toepassing een toegangs groep voor de sleutel hanger hebben.
U kunt de toegangs groep voor de sleutel hanger instellen met behulp van de `WithIosKeychainSecurityGroup()`-API wanneer u uw toepassing maakt, zoals hieronder wordt weer gegeven:

Om eenmalige aanmelding in te scha kelen, moet u de eigenschap `PublicClientApplication.iOSKeychainSecurityGroup` instellen op dezelfde waarde in alle toepassingen.

Een voor beeld hiervan is het gebruik van MSAL v3. x:
```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
     .WithIosKeychainSecurityGroup("com.microsoft.msalrocks")
     .Build();
```

De rechten. plist moet worden bijgewerkt om te zien als het volgende XML-fragment:

Deze wijziging is een *aanvulling* op het inschakelen van sleutel hanger toegang in het `Entitlements.plist` bestand, met behulp van de onderstaande toegangs groep of uw eigen:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
  <key>keychain-access-groups</key>
  <array>
    <string>$(AppIdentifierPrefix)com.microsoft.msalrocks</string>
  </array>
</dict>
</plist>
```

Een voor beeld hiervan is het gebruik van MSAL v4. x:

```csharp
PublicClientApplication.iOSKeychainSecurityGroup = "com.microsoft.msalrocks";
```

Wanneer u de `WithIosKeychainSecurityGroup()`-API gebruikt, voegt MSAL automatisch uw beveiligings groep toe aan het einde van de team-ID (AppIdentifierPrefix) van de toepassing omdat u de toepassing ook kunt bouwen met behulp van Xcode. [Zie de documentatie voor IOS-rechten voor meer informatie](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps). Daarom moet u de rechten bijwerken om $ (AppIdentifierPrefix) op te nemen vóór de toegangs groep voor de sleutel hanger in de rechten. plist.

### <a name="enable-token-cache-sharing-across-ios-applications"></a>Het delen van tokens in de cache inschakelen voor iOS-toepassingen

Vanuit MSAL 2. x kunt u een toegangs groep voor de sleutel hanger opgeven die moet worden gebruikt voor het persistent maken van de token cache in meerdere toepassingen. Met deze instelling kunt u de token cache delen tussen verschillende toepassingen die dezelfde toegangs groep voor de sleutel hanger hebben, waaronder die zijn ontwikkeld met [ADAL.net](https://aka.ms/adal-net), MSAL.net Xamarin. IOS-toepassingen en systeem eigen IOS-toepassingen die zijn ontwikkeld met [ ADAL. objc](https://github.com/AzureAD/azure-activedirectory-library-for-objc) of [MSAL. objc](https://github.com/AzureAD/microsoft-authentication-library-for-objc)).

Door de token cache te delen, kunt u eenmalige aanmelding toestaan tussen alle toepassingen die gebruikmaken van de toegangs groep voor de sleutel hanger.

Als u deze cache wilt delen, moet u de methode ' WithIosKeychainSecurityGroup () ' gebruiken om de toegangs groep voor de sleutel hanger in te stellen op dezelfde waarde in alle toepassingen die dezelfde cache delen, zoals in het bovenstaande voor beeld wordt weer gegeven.

Voorheen werd vermeld dat MSAL $ (AppIdentifierPrefix) heeft toegevoegd wanneer u de `WithIosKeychainSecurityGroup()`-API gebruikt. Dit komt doordat de AppIdentifierPrefix of de ' team-ID ' wordt gebruikt om ervoor te zorgen dat alleen toepassingen die door dezelfde uitgever zijn gemaakt, de sleutel van het toegangs rechten kunnen delen.

> [!NOTE]
> **De eigenschap `KeychainSecurityGroup` is afgeschaft.**
> 
> Voorheen waren de ontwikkel aars van MSAL 2. x gedwongen het TeamId-voor voegsel toe te voegen bij het gebruik van de eigenschap `KeychainSecurityGroup`.
>
>  Van MSAL 2.7. x, wanneer de nieuwe `iOSKeychainSecurityGroup` eigenschap wordt gebruikt, zal MSAL het TeamId-voor voegsel tijdens runtime oplossen. Wanneer u deze eigenschap gebruikt, mag de waarde niet het TeamId-voor voegsel bevatten.
>  Gebruik de nieuwe eigenschap `iOSKeychainSecurityGroup`, waarvoor u niet de TeamId hoeft op te geven, omdat de vorige `KeychainSecurityGroup` eigenschap nu verouderd is.

### <a name="use-microsoft-authenticator"></a>Microsoft Authenticator gebruiken

Uw toepassing kan Microsoft Authenticator (een Broker) gebruiken om het volgende in te scha kelen:

- Eenmalige aanmelding (SSO). Uw gebruikers hoeven zich niet aan te melden bij elke toepassing.
- Apparaat-id. Door toegang te krijgen tot het certificaat van het apparaat, dat is gemaakt op het apparaat toen het is gekoppeld aan de werk plek. Uw toepassing is gereed als de Tenant beheerders voorwaardelijke toegang met betrekking tot de apparaten inschakelen.
- Verificatie van de toepassings-id. Wanneer een toepassing de Broker aanroept, wordt de omleidings-URL door gegeven en wordt deze door de Broker gecontroleerd.

Zie voor meer informatie over het inschakelen van de Broker [Microsoft Authenticator gebruiken of Microsoft intune bedrijfs portal in Xamarin IOS-en Android-toepassingen](msal-net-use-brokers-with-xamarin-apps.md).

### <a name="sample-illustrating-xamarin-ios-specific-properties"></a>Voor beeld van het illustreren van specifieke eigenschappen van Xamarin iOS

Meer informatie vindt u in de para graaf [specifieke overwegingen voor IOS](https://github.com/azure-samples/active-directory-xamarin-native-v2#ios-specific-considerations) van het README.MD-bestand van het volgende voor beeld:

Voorbeeld | Platform | Beschrijving
------ | -------- | -----------
[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, UWP | Een eenvoudige Xamarin Forms-app die laat zien hoe u MSAL kunt gebruiken om MSA en Azure AD te verifiëren via het Azure AD V 2.0-eind punt en toegang te krijgen tot de Microsoft Graph met het resulterende token.

<!--- https://github.com/Azure-Samples/active-directory-xamarin-native-v2/blob/master/ReadmeFiles/Topology.png -->
