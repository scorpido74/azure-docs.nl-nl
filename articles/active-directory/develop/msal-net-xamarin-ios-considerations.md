---
title: Xamarin iOS overwegingen (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over overwegingen voor het gebruik van Xamarin iOS met Microsoft Authentication Library voor .NET (MSAL.NET).
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262709"
---
# <a name="considerations-for-using-xamarin-ios-with-msalnet"></a>Overwegingen voor het gebruik van Xamarin iOS met MSAL.NET
Wanneer u Microsoft Authentication Library voor .NET (MSAL.NET) op Xamarin iOS gebruikt, moet u het andere doen: 

- Overschrijven en `OpenUrl` implementeren `AppDelegate`van de functie in .
- Schakel sleutelhangergroepen in.
- Tokencache delen inschakelen.
- Toegang tot sleutelhangers inschakelen.
- Begrijp bekende problemen met iOS 12 en verificatie.

## <a name="implement-openurl"></a>OpenUrl implementeren

Overschrijf `OpenUrl` de `FormsApplicationDelegate` methode van `AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs`de afgeleide klasse en aanroep . Hier volgt een voorbeeld:

```csharp
public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
{
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url);
    return true;
}
```

Doe ook de volgende taken: 
* Een URL-schema definiëren.
* Machtigingen vereisen voor uw app om een andere app te bellen.
* Heb een specifiek formulier voor de omleidings-URL.
* Registreer de omleidings-URL in de [Azure-portal](https://portal.azure.com).

### <a name="enable-keychain-access"></a>Toegang tot sleutelhanger inschakelen

Als u toegang tot sleutelhangers wilt inschakelen, moet u ervoor zorgen dat uw toepassing een sleutelhangertoegangsgroep heeft. U de toegangsgroep sleutelhanger instellen wanneer u `WithIosKeychainSecurityGroup()` uw toepassing maakt met behulp van de API.

Als u wilt profiteren van de cache en enkele aanmelding (SSO), stelt u de sleutelhangertoegangsgroep in op dezelfde waarde in al uw toepassingen.

In dit voorbeeld van de installatie wordt MSAL 4.x gebruikt:
```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
     .WithIosKeychainSecurityGroup("com.microsoft.adalcache")
     .Build();
```

Schakel ook toegang tot `Entitlements.plist` de sleutelhanger in het bestand in. Gebruik de volgende toegangsgroep of uw eigen toegangsgroep.

```xml
<dict>
  <key>keychain-access-groups</key>
  <array>
    <string>$(AppIdentifierPrefix)com.microsoft.adalcache</string>
  </array>
</dict>
```

Wanneer u `WithIosKeychainSecurityGroup()` de API gebruikt, voegt MSAL uw beveiligingsgroep automatisch toe aan`AppIdentifierPrefix`het einde van de *team-id* van de toepassing ( ). MSAL voegt uw beveiligingsgroep toe, want wanneer u uw toepassing in Xcode bouwt, zal deze hetzelfde doen. Daarom moeten de rechten in `Entitlements.plist` het bestand `$(AppIdentifierPrefix)` worden opgenomen voordat de toegangsgroep voor sleutelhangers wordt opgenomen.

Zie de documentatie [over iOS-rechten](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps)voor meer informatie. 

### <a name="enable-token-cache-sharing-across-ios-applications"></a>Delen van tokencache in iOS-toepassingen inschakelen

Vanaf MSAL 2.x u een sleutelhangertoegangsgroep opgeven om de tokencache voor meerdere toepassingen te blijven bestaan. Met deze instelling u de tokencache delen tussen verschillende toepassingen met dezelfde sleutelhangertoegangsgroep. U het tokengeld delen tussen [ADAL.NET](https://aka.ms/adal-net) toepassingen, MSAL.NET Xamarin.iOS-toepassingen en native iOS-toepassingen die zijn ontwikkeld in [ADAL.objc](https://github.com/AzureAD/azure-activedirectory-library-for-objc) of [MSAL.objc.](https://github.com/AzureAD/microsoft-authentication-library-for-objc)

Door de tokencache te delen, staat u eenmalige aanmelding (SSO) toe tussen alle toepassingen die dezelfde sleutelhangertoegangsgroep gebruiken.

Als u het delen `WithIosKeychainSecurityGroup()` van deze cache wilt inschakelen, gebruikt u de methode om de toegangsgroep sleutelhanger in te stellen op dezelfde waarde in alle toepassingen die dezelfde cache delen. Het eerste codevoorbeeld in dit artikel laat zien hoe u de methode gebruikt.

Eerder in dit artikel, je geleerd `$(AppIdentifierPrefix)` dat MSAL `WithIosKeychainSecurityGroup()` toevoegt wanneer u de API gebruikt. MSAL voegt dit element `AppIdentifierPrefix` toe omdat de team-ID ervoor zorgt dat alleen toepassingen die door dezelfde uitgever worden gemaakt, toegang tot sleutelhangers kunnen delen.

> [!NOTE]
> Het `KeychainSecurityGroup` pand is afgeschaft.
> 
> Vanaf MSAL 2.x werden ontwikkelaars gedwongen `TeamId` om het voorvoegsel op te nemen toen ze de `KeychainSecurityGroup` eigenschap gebruikten. Maar vanaf MSAL 2.7.x, wanneer `iOSKeychainSecurityGroup` u de nieuwe eigenschap `TeamId` gebruikt, lost MSAL het voorvoegsel op tijdens runtime. Wanneer u deze eigenschap gebruikt, `TeamId` neemt u het voorvoegsel niet op in de waarde. Het voorvoegsel is niet vereist.
>
> Omdat `KeychainSecurityGroup` de eigenschap verouderd `iOSKeychainSecurityGroup` is, gebruik maken van de eigenschap.

### <a name="use-microsoft-authenticator"></a>Microsoft Authenticator gebruiken

Uw toepassing kan Microsoft Authenticator als broker gebruiken om:

- **SSO:** Wanneer u SSO inschakelt, hoeven uw gebruikers zich niet bij elke toepassing aan te melden.
- **Apparaatidentificatie**: Gebruik apparaatidentificatie om te verifiëren door toegang te krijgen tot het apparaatcertificaat. Dit certificaat wordt gemaakt op het apparaat wanneer het is verbonden met de werkplek. Uw toepassing is gereed als de tenantbeheerders voorwaardelijke toegang met betrekking tot de apparaten inschakelen.
- **Verificatie van toepassingsidentificatie:** Wanneer een toepassing de makelaar aanroept, passeert deze de omleidings-URL. De makelaar verifieert de omleidings-URL.

Zie [Microsoft Authenticator of Microsoft Intune Company Portal gebruiken op Xamarin iOS- en Android-toepassingen voor](msal-net-use-brokers-with-xamarin-apps.md)meer informatie over het inschakelen van een broker.

## <a name="known-issues-with-ios-12-and-authentication"></a>Bekende problemen met iOS 12 en authenticatie
Microsoft heeft een [beveiligingsadvies](https://github.com/aspnet/AspNetCore/issues/4647) uitgebracht over een onverenigbaarheid tussen iOS 12 en bepaalde soorten verificatie. De onverenigbaarheid breekt sociale, WSFed en OIDC-aanmeldingen. Het beveiligingsadvies helpt ontwikkelaars te begrijpen hoe ze ASP.NET beveiligingsbeperkingen uit hun toepassingen kunnen verwijderen om ze compatibel te maken met iOS 12.  

Wanneer u MSAL.NET-toepassingen op Xamarin iOS ontwikkelt, ziet u mogelijk een oneindige lus wanneer u zich probeert aan te melden bij websites van iOS 12. Dit gedrag is vergelijkbaar met deze [ADAL-kwestie](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/issues/1329). 

Mogelijk ziet u ook een onderbreking in ASP.NET Core OIDC-verificatie met iOS 12 Safari. Zie dit [WebKit-probleem](https://bugs.webkit.org/show_bug.cgi?id=188165)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Zie de alinea [iOS-specifieke overwegingen](https://github.com/Azure-Samples/active-directory-xamarin-native-v2/tree/master/1-Basic#ios-specific-considerations) van het volgende voorbeeld README.md bestand voor informatie over eigenschappen voor Xamarin iOS:

Voorbeeld | Platform | Beschrijving
------ | -------- | -----------
[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, Universal Windows Platform (UWP) | Een eenvoudige App Xamarin Forms die laat zien hoe u MSAL gebruikt om persoonlijke accounts van Microsoft en Azure AD te verifiëren via het Azure AD 2.0-eindpunt. De app laat ook zien hoe u het resulterende token gebruiken om toegang te krijgen tot Microsoft Graph.

<!--- https://github.com/Azure-Samples/active-directory-xamarin-native-v2/blob/master/ReadmeFiles/Topology.png -->