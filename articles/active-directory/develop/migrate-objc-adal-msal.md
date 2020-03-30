---
title: ADAL naar MSAL-migratiegids (MSAL iOS/macOS) | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over de verschillen tussen MSAL voor iOS/macOS en de Azure AD-verificatiebibliotheek voor ObjectiveC (ADAL. ObjC) en hoe te migreren naar MSAL voor iOS/macOS.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev
ms.openlocfilehash: 6050bdc8c2600998b9804b04b62102e74612719f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77085172"
---
# <a name="migrate-applications-to-msal-for-ios-and-macos"></a>Applicaties migreren naar MSAL voor iOS en macOS

De Azure Active Directory Authentication Library[(ADAL Objective-C)](https://github.com/AzureAD/azure-activedirectory-library-for-objc)is gemaakt om te werken met Azure Active Directory-accounts via het v1.0-eindpunt.

De Microsoft-verificatiebibliotheek voor iOS en macOS (MSAL) is gebouwd om te werken met alle Microsoft-identiteiten, zoals Azure Active Directory-accounts (Azure AD), persoonlijke Microsoft-accounts en Azure AD B2C-accounts via het Microsoft-identiteitsplatform (formeel het Azure AD v2.0-eindpunt).

Het Microsoft-identiteitsplatform heeft een paar belangrijke verschillen met Azure Active Directory v1.0. In dit artikel worden deze verschillen belicht en wordt richtlijnen gegeven om een app te migreren van ADAL naar MSAL.

## <a name="adal-and-msal-app-capability-differences"></a>Verschillen tussen ADAL en MSAL-app

### <a name="who-can-sign-in"></a>Wie kan zich aanmelden

* ADAL ondersteunt alleen werk- en schoolaccounts, ook wel Azure AD-accounts genoemd.
* MSAL ondersteunt persoonlijke Microsoft-accounts (MSA-accounts) zoals Hotmail.com, Outlook.com en Live.com.
* MSAL ondersteunt werk- en schoolaccounts en Azure AD B2C-accounts.

### <a name="standards-compliance"></a>Naleving van normen

* Het eindpunt van het Microsoft Identity Platform volgt de OAuth 2.0- en OpenId Connect-standaarden.

### <a name="incremental-and-dynamic-consent"></a>Incrementele en dynamische toestemming

* Het Azure Active Directory v1.0-eindpunt vereist dat alle machtigingen vooraf worden gedeclareerd tijdens de registratie van toepassingen. Dit betekent dat deze machtigingen statisch zijn.
* Met het Microsoft-identiteitsplatform u machtigingen dynamisch aanvragen. Apps kunnen alleen machtigingen aanvragen als dat nodig is en meer aanvragen als de app ze nodig heeft.

Zie [Waarom bijwerken naar Microsoft-identiteitsplatform (v2.0)?](https://docs.microsoft.com/azure/active-directory/develop/azure-ad-endpoint-comparison)

## <a name="adal-and-msal-library-differences"></a>ADAL- en MSAL-bibliotheekverschillen

De OPENBARE API van MSAL weerspiegelt een paar belangrijke verschillen tussen Azure AD v1.0 en het Microsoft-identiteitsplatform.

### <a name="msalpublicclientapplication-instead-of-adauthenticationcontext"></a>MSALPublicClientApplication in plaats van ADAuthenticationContext

`ADAuthenticationContext`is het eerste object dat een ADAL-app maakt. Het vertegenwoordigt een instantiatie van ADAL. Apps maken een `ADAuthenticationContext` nieuw exemplaar van voor elke Azure Active Directory-cloud en tenant (autoriteit) combinatie. Hetzelfde `ADAuthenticationContext` kan worden gebruikt om tokens te krijgen voor meerdere openbare clienttoepassingen.

In MSAL is de belangrijkste `MSALPublicClientApplication` interactie via een object, dat is gemodelleerd naar [OAuth 2.0 Public Client.](https://tools.ietf.org/html/rfc6749#section-2.1) Een exemplaar `MSALPublicClientApplication` van kan worden gebruikt om te communiceren met meerdere AAD-clouds en tenants, zonder dat u voor elke instantie een nieuw exemplaar hoeft te maken. Voor de meeste `MSALPublicClientApplication` apps is één exemplaar voldoende.

### <a name="scopes-instead-of-resources"></a>Scopes in plaats van resources

In ADAL moest een app een `https://graph.microsoft.com` *resource-id* bieden die graag tokens kan aanschaffen uit het Azure Active Directory v1.0-eindpunt. Een resource kan een aantal scopes definiëren, of oAuth2Permissions in het app-manifest, dat het begrijpt. Hierdoor konden client-apps tokens aanvragen bij die bron voor een bepaalde set scopes die vooraf zijn gedefinieerd tijdens app-registratie.

In MSAL bieden apps in plaats van één resource-id een set scopes per aanvraag. Een bereik is een resource-id, gevolgd door een machtigingsnaam in de formulierbron/-machtiging. Bijvoorbeeld: `https://graph.microsoft.com/user.read`

Er zijn twee manieren om scopes in MSAL te bieden:

* Geef een lijst op met alle machtigingen die uw apps nodig hebben. Bijvoorbeeld: 

    `@[@"https://graph.microsoft.com/directory.read", @"https://graph.microsoft.com/directory.write"]`

    In dit geval vraagt `directory.read` de `directory.write` app de machtigingen en machtigingen. De gebruiker wordt gevraagd toestemming te geven voor deze machtigingen als hij/zij er nog niet eerder toestemming voor heeft gegeven voor deze app. De toepassing kan ook aanvullende machtigingen ontvangen waarmee de gebruiker al heeft ingestemd voor de toepassing. De gebruiker wordt alleen gevraagd toestemming te geven voor nieuwe machtigingen of machtigingen die niet zijn verleend.

* De `/.default` scope.

Dit is de ingebouwde scope voor elke toepassing. Het verwijst naar de statische lijst met machtigingen die zijn geconfigureerd toen de toepassing werd geregistreerd. Zijn gedrag is vergelijkbaar `resource`met dat van . Dit kan handig zijn bij het migreren om ervoor te zorgen dat een vergelijkbare set scopes en gebruikerservaring behouden blijft.

Als u `/.default` het bereik `/.default` wilt gebruiken, wordt u toegevoegd aan de resource-id. Bijvoorbeeld: `https://graph.microsoft.com/.default`. Als uw resource eindigt`/`met een slash `/.default`( ), moet u nog steeds toevoegen , met inbegrip`//`van de leading forward slash, wat resulteert in een bereik dat een dubbele voorwaartse slash ( ) in het heeft.

U [hier](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#the-default-scope) meer informatie lezen over het gebruik van de scope /.default.

### <a name="supporting-different-webview-types--browsers"></a>Ondersteuning voor verschillende WebView-typen & browsers

ADAL ondersteunt alleen UIWebView/WKWebView voor iOS en WebView voor macOS. MSAL voor iOS ondersteunt meer opties voor het weergeven van webinhoud bij het aanvragen van een autorisatiecode en ondersteunt `UIWebView`deze niet langer; die de gebruikerservaring en beveiliging kunnen verbeteren.

Standaard maakt MSAL op iOS gebruik van [ASWebAuthenticationSession](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession?language=objc), de webcomponent die Apple aanbeveelt voor authenticatie op iOS 12+ apparaten. Het biedt Single Sign-On (SSO) voordelen door het delen van cookies tussen apps en de Safari-browser.

U ervoor kiezen om een andere webcomponent te gebruiken, afhankelijk van de vereisten van de app en de gewenste ervaring voor de eindgebruiker. Bekijk [ondersteunde webweergavetypen](customize-webviews.md) voor meer opties.

Wanneer u migreert van ADAL naar MSAL, `WKWebView` biedt u de gebruikerservaring die het meest lijkt op ADAL op iOS en macOS. We raden u `ASWebAuthenticationSession` aan om te migreren naar op iOS, indien mogelijk. Voor macOS raden we `WKWebView`je aan om .

### <a name="account-management-api-differences"></a>API-verschillen accountbeheer

Wanneer u de `acquireToken()` ADAL-methoden aanroept of `acquireTokenSilent()`een `ADUserInformation` object ontvangt `id_token` met een lijst met claims van het account dat wordt geverifieerd. Bovendien, `ADUserInformation` retourneert `userId` een `upn` op basis van de vordering. Na de eerste interactieve token-acquisitie `userId` verwacht ADAL dat de ontwikkelaar alle stille gesprekken aanbiedt.

ADAL biedt geen API om bekende gebruikersidentiteiten op te halen. Het is afhankelijk van de app om deze accounts op te slaan en te beheren.

MSAL biedt een set API's om alle accounts te vermelden die bekend zijn bij MSAL zonder dat ze een token hoeven te verwerven.

Net als ADAL retourneert MSAL accountgegevens `id_token`met een lijst met claims van de . Het maakt deel `MSALAccount` uit van `MSALResult` het object in het object.

MSAL biedt een set API's om accounts te verwijderen, waardoor de verwijderde accounts niet toegankelijk zijn voor de app. Nadat het account is verwijderd, zullen latere tokenacquisitieoproepen de gebruiker vragen om interactieve tokenacquisitie te doen. Het verwijderen van een account is alleen van toepassing op de clienttoepassing die het is gestart en verwijdert het account niet uit de andere apps die op het apparaat of uit de systeembrowser worden uitgevoerd. Dit zorgt ervoor dat de gebruiker een SSO-ervaring op het apparaat blijft hebben, zelfs nadat hij zich heeft afmelden bij een individuele app.

Daarnaast retourneert MSAL ook een account-id die later kan worden gebruikt om een token in stilte aan te vragen. De account-id (toegankelijk `identifier` via `MSALAccount` eigenschap in het object) is echter niet weer te geven en u niet aannemen in welke indeling het zich bevindt en u moet het ook niet proberen te interpreteren of te ontleden.

### <a name="migrating-the-account-cache"></a>De accountcache migreren

Bij het migreren van ADAL slaan apps `userId`normaal gesproken ADAL's op, wat niet de `identifier` vereiste is van MSAL. Als een eenmalige migratiestap kan een app een MSAL-account opvragen met de userId van ADAL met de volgende API:

`- (nullable MSALAccount *)accountForUsername:(nonnull NSString *)username error:(NSError * _Nullable __autoreleasing * _Nullable)error;`

Deze API leest zowel de cache van MSAL als ADAL om het account van ADAL userId (UPN) te vinden.

Als het account wordt gevonden, moet de ontwikkelaar het account gebruiken om stille tokenacquisitie te doen. De eerste stille token-acquisitie zal het account effectief upgraden en de ontwikkelaar krijgt een`identifier`MSAL-compatibele account-id in het MSAL-resultaat ( ). Daarna mag `identifier` alleen worden gebruikt voor het opzoeken van een account met behulp van de volgende API:

`- (nullable MSALAccount *)accountForIdentifier:(nonnull NSString *)identifier error:(NSError * _Nullable __autoreleasing * _Nullable)error;`

Hoewel het mogelijk is om ADAL's `userId` te blijven gebruiken `userId` voor alle bewerkingen in MSAL, omdat het is gebaseerd op UPN, is het onderhevig aan meerdere beperkingen die resulteren in een slechte gebruikerservaring. Als de UPN bijvoorbeeld verandert, moet de gebruiker zich opnieuw aanmelden. We raden alle apps aan om `identifier` het niet-weergavebare account voor alle bewerkingen te gebruiken.

Lees meer over [migratie van cachestatus](sso-between-adal-msal-apps-macos-ios.md).

### <a name="token-acquisition-changes"></a>Wijzigingen in tokenacquisitie

MSAL introduceert enkele wijzigingen in tokenacquisitieoproepen:

* Net als `acquireTokenSilent` ADAL, resulteert altijd in een stille verzoek.
* In tegenstelling `acquireToken` tot ADAL resulteert dit altijd in een gebruikersinterface die via de webweergave of de Microsoft Authenticator-app kan worden aangedaan. Afhankelijk van de SSO-status in webview/Microsoft Authenticator kan de gebruiker worden gevraagd zijn of haar referenties in te voeren.
* In ADAL `acquireToken` `AD_PROMPT_AUTO` probeert u eerst stille token-acquisitie en geeft ui alleen weer als de stille aanvraag mislukt. In MSAL kan deze logica worden `acquireTokenSilent` bereikt `acquireToken` door eerst te bellen en alleen te bellen als stille acquisitie mislukt. Hierdoor kunnen ontwikkelaars de gebruikerservaring aanpassen voordat ze beginnen met het werven van interactieve token.

### <a name="error-handling-differences"></a>Verschillen in foutafhandeling

MSAL biedt meer duidelijkheid tussen fouten die door uw app kunnen worden verwerkt en fouten die tussenkomst van de gebruiker vereisen. Er zijn een beperkt aantal fouten die de ontwikkelaar moet verwerken:

* `MSALErrorInteractionRequired`: De gebruiker moet een interactief verzoek doen. Dit kan worden veroorzaakt om verschillende redenen, zoals een verlopen verificatiesessie, het beleid voor voorwaardelijke toegang is gewijzigd, een vernieuwingstoken is verlopen of is ingetrokken, er zijn geen geldige tokens in de cache, enzovoort.
* `MSALErrorServerDeclinedScopes`: Het verzoek is niet volledig voltooid en sommige scopes kregen geen toegang. Dit kan worden veroorzaakt doordat een gebruiker de toestemming voor een of meer scopes weigert.

Het verwerken van [ `MSALError` ](https://github.com/AzureAD/microsoft-authentication-library-for-objc/blob/master/MSAL/src/public/MSALError.h#L128) alle andere fouten in de lijst is optioneel. U de informatie in die fouten gebruiken om de gebruikerservaring te verbeteren.

Zie [Uitzonderingen en fouten verwerken met MSAL](msal-handling-exceptions.md) voor meer informatie over MSAL-foutafhandeling.

### <a name="broker-support"></a>Ondersteuning voor broker

MSAL, te beginnen met versie 0.3.0, biedt ondersteuning voor gebrokereerde verificatie met behulp van de Microsoft Authenticator-app. Microsoft Authenticator maakt ook ondersteuning voor voorwaardelijke toegangsscenario's mogelijk. Voorbeelden van scenario's voor voorwaardelijke toegang zijn het nalevingsbeleid van apparaten dat vereist dat de gebruiker het apparaat inschrijft via Intune of zich registreert bij AAD om een token te krijgen. En het beleid voor voorwaardelijke toegang (Mobile Application Management), waarvoor een bewijs van naleving vereist is voordat uw app een token kan krijgen.

Ga als object in op uw toepassing:

1. Registreer een broker compatibele redirect URI-indeling voor de toepassing. De makelaar compatibele redirect `msauth.<app.bundle.id>://auth`URI-formaat is . Vervang `<app.bundle.id>` door de bundel-ID van uw toepassing. Als u migreert van ADAL en uw toepassing was al makelaar staat, er is niets extra's die je hoeft te doen. Uw vorige omleiding URI is volledig compatibel met MSAL, zodat u overgaan naar stap 3.

2. Voeg het omleidingsschema van uw toepassing toe aan uw info.plist-bestand. Voor de standaard MSAL redirect `msauth.<app.bundle.id>`URI is de indeling . Bijvoorbeeld:

    ```xml
    <key>CFBundleURLSchemes</key>
    <array>
        <string>msauth.<app.bundle.id></string>
    </array>
    ```

3. Volgende schema's toevoegen aan de Info.plist van uw app onder LSApplicationQueriesSchemes:

    ```xml
    <key>LSApplicationQueriesSchemes</key>
    <array>
         <string>msauthv2</string>
         <string>msauthv3</string>
    </array>
    ```

4. Voeg het volgende toe aan uw appDelegate.m-bestand om terugbellen af te handelen: Doelstelling-C:
    
    ```objc
    - (BOOL)application:(UIApplication *)app openURL:(NSURL *)url options:(NSDictionary<NSString *,id> *)options`
    {
        return [MSALPublicClientApplication handleMSALResponse:url sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
    }
    ```
    
    Swift:
    
    ```swift
    func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
    }
    ```

### <a name="business-to-business-b2b"></a>Business to business (B2B)

In ADAL maakt u `ADAuthenticationContext` afzonderlijke exemplaren van voor elke tenant waarvoor de app tokens opvraagt. Dit is niet langer een vereiste in MSAL. In MSAL u één `MSALPublicClientApplication` exemplaar van en deze gebruiken voor elke AAD-cloud en -organisatie door een andere autoriteit voor het verwerven van Token en acquireTokenSilent-gesprekken op te geven.

## <a name="sso-in-partnership-with-other-sdks"></a>SSO in samenwerking met andere SDK's

MSAL voor iOS kan SSO bereiken via een uniforme cache met de volgende SDK's:

- ADAL Objective-C 2.7.x+
- MSAL.NET voor Xamarin 2.4.x+
- ADAL.NET voor Xamarin 4.4.x+

SSO wordt bereikt via iOS-sleutelhangers en is alleen beschikbaar tussen apps die zijn gepubliceerd vanuit hetzelfde Apple Developer-account.

SSO via iOS keychain sharing is het enige stille SSO-type.

Op macOS kan MSAL SSO bereiken met andere MSAL voor iOS- en macOS-gebaseerde toepassingen en ADAL Objective-C-gebaseerde toepassingen.

MSAL op iOS ondersteunt ook twee andere soorten SSO:

* SSO via de webbrowser. MSAL voor iOS ondersteunt `ASWebAuthenticationSession`, die SSO biedt via cookies die worden gedeeld tussen andere apps op het apparaat en in het bijzonder de Safari-browser.
* SSO via een verificatiemakelaar. Op een iOS-apparaat fungeert Microsoft Authenticator als de verificatiemakelaar. Het kan het beleid voor voorwaardelijke toegang volgen, zoals het vereisen van een compatibel apparaat en biedt SSO voor geregistreerde apparaten. MSAL SDK's die beginnen met versie 0.3.0 ondersteunen standaard een broker.

## <a name="intune-mam-sdk"></a>Intune MAM SDK

De [Intune MAM SDK](https://docs.microsoft.com/intune/app-sdk-get-started) ondersteunt MSAL voor iOS vanaf versie [11.1.2](https://github.com/msintuneappsdk/ms-intune-app-sdk-ios/releases/tag/11.1.2)

## <a name="msal-and-adal-in-the-same-app"></a>MSAL en ADAL in dezelfde app

ADAL versie 2.7.0, en hoger, kan niet naast MSAL bestaan in dezelfde toepassing. De belangrijkste reden is vanwege de gedeelde submodule gemeenschappelijke code. Omdat Objective-C geen naamruimten ondersteunt, zijn er twee exemplaren van dezelfde klasse als u zowel ADAL- als MSAL-frameworks aan uw toepassing toevoegt. Er is geen garantie waarvoor men wordt geplukt tijdens runtime. Als beide SDK's dezelfde versie van de conflicterende klasse gebruiken, werkt uw app mogelijk nog steeds. Als het echter een andere versie is, kan uw app onverwachte crashes ondervinden die moeilijk te diagnosticeren zijn.

Het uitvoeren van ADAL en MSAL in dezelfde productietoepassing wordt niet ondersteund. Als u uw gebruikers echter alleen test en migreert van ADAL Objective-C naar MSAL voor iOS en macOS, u [ADAL Objective-C 2.6.10](https://github.com/AzureAD/azure-activedirectory-library-for-objc/releases/tag/2.6.10)blijven gebruiken. Het is de enige versie die werkt met MSAL in dezelfde toepassing. Er zullen geen nieuwe functie-updates voor deze ADAL-versie zijn, dus deze moet alleen worden gebruikt voor migratie- en testdoeleinden. Uw app mag niet afhankelijk zijn van adal en MSAL coëxistentie op lange termijn.

ADAL en MSAL coëxistentie in dezelfde toepassing wordt niet ondersteund.
ADAL en MSAL coëxistentie tussen meerdere toepassingen wordt volledig ondersteund.

## <a name="practical-migration-steps"></a>Praktische migratiestappen

### <a name="app-registration-migration"></a>Migratie van app-registratie

U hoeft uw bestaande AAD-toepassing niet te wijzigen om over te schakelen naar MSAL en AAD-accounts in te schakelen. Als uw ADAL-gebaseerde toepassing echter geen verificatie met een bemiddelde ondersteuning biedt, moet u een nieuwe omleidinguri voor de toepassing registreren voordat u overschakelen naar MSAL.

De omleiding URI moet worden `msauth.<app.bundle.id>://auth`in dit formaat: . Vervang `<app.bundle.id>` door de bundel-ID van uw toepassing. Geef de omleidingsURI op in de [Azure-portal](https://aka.ms/MobileAppReg).

Alleen voor iOS moet een extra omleidingsuri worden geregistreerd in uw toepassing en de Azure-portal in de volgende indeling: `msauth://code/<broker-redirect-uri-in-url-encoded-form>`. Bijvoorbeeld: `msauth://code/msauth.com.microsoft.mybundleId%3A%2F%2Fauth`

We raden aan dat alle apps beide omleidings-URI's registreren.

Als u ondersteuning voor incrementele toestemming wilt toevoegen, selecteert u de API's en machtigingen die uw app is geconfigureerd om toegang te vragen in uw app-registratie onder het tabblad **API-machtigingen.**

Als u migreert vanuit ADAL en zowel AAD- als MSA-accounts wilt ondersteunen, moet uw bestaande toepassingsregistratie worden bijgewerkt om beide te ondersteunen. We raden u niet aan uw bestaande productie-app bij te werken om zowel AAD als MSA meteen te ondersteunen. Maak in plaats daarvan een andere client-id die zowel AAD als MSA ondersteunt voor het testen en nadat u hebt geverifieerd dat alle scenario's werken, werkt u de bestaande app bij.

### <a name="add-msal-to-your-app"></a>MSAL toevoegen aan uw app

U MSAL SDK aan uw app toevoegen met uw favoriete pakketbeheertool. Zie [gedetailleerde instructies hier](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki/Installation).

### <a name="update-your-apps-infoplist-file"></a>Het Info.plist-bestand van uw app bijwerken

Alleen voor iOS het omleidingsschema van uw toepassing toevoegen aan uw info.plist-bestand. Voor ADAL makelaar compatibele apps, moet het er al zijn. De standaard MSAL redirect URI-regeling `msauth.<app.bundle.id>`is in de indeling: .  

```xml
<key>CFBundleURLSchemes</key>
<array>
    <string>msauth.<app.bundle.id></string>
</array>
```

Volgende schema's toevoegen aan de Info.plist van uw app onder `LSApplicationQueriesSchemes`.

```xml
<key>LSApplicationQueriesSchemes</key>
<array>
     <string>msauthv2</string>
     <string>msauthv3</string>
</array>
```

### <a name="update-your-appdelegate-code"></a>Uw AppDelegate-code bijwerken

Voeg alleen voor iOS het volgende toe aan uw appDelegate.m-bestand:

Doelstelling-C:

```objc
- (BOOL)application:(UIApplication *)app openURL:(NSURL *)url options:(NSDictionary<NSString *,id> *)options`
{
    return [MSALPublicClientApplication handleMSALResponse:url sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
}
```

Swift:

```swift
func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
    return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
}
```

**Als u Xcode 11 gebruikt,** moet u in `SceneDelegate` plaats daarvan MSAL-callback in het bestand plaatsen.
Als u zowel UISceneDelegate als UIApplicationDelegate ondersteunt voor compatibiliteit met oudere iOS, moet MSAL-terugroepen in beide bestanden worden geplaatst.

Doelstelling-C:

```objc
 - (void)scene:(UIScene *)scene openURLContexts:(NSSet<UIOpenURLContext *> *)URLContexts
 {
     UIOpenURLContext *context = URLContexts.anyObject;
     NSURL *url = context.URL;
     NSString *sourceApplication = context.options.sourceApplication;
     
     [MSALPublicClientApplication handleMSALResponse:url sourceApplication:sourceApplication];
 }
```

Swift:

```swift
func scene(_ scene: UIScene, openURLContexts URLContexts: Set<UIOpenURLContext>) {
        
        guard let urlContext = URLContexts.first else {
            return
        }
        
        let url = urlContext.url
        let sourceApp = urlContext.options.sourceApplication
        
        MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApp)
    }
```

Hierdoor kan MSAL de reacties van de broker en webcomponent verwerken.
Dit was niet nodig in ADAL omdat het "swizzled" app delegate methoden automatisch. Het handmatig toevoegen is minder foutgevoelig en geeft de toepassing meer controle.

### <a name="enable-token-caching"></a>Tokencaching inschakelen

Msal slaat standaard de tokens van uw app in de iOS- of macOS-sleutelhanger. 

Tokencache inschakelen:
1. Zorg ervoor dat uw aanvraag correct is ondertekend
2. Ga naar de pagina Xcode-projectinstellingen > **mogelijkheden** > **inschakelen Het delen van sleutelhangers inschakelen**
3. Klik **+** op een volgende vermelding **sleutelhangergroepen:** 3.a `com.microsoft.adalcache` Voor iOS voert u 3.b In voor macOS enter`com.microsoft.identity.universalstorage`

### <a name="create-msalpublicclientapplication-and-switch-to-its-acquiretoken-and-acquiretokesilent-calls"></a>MSALPublicClientApplication maken en overschakelen naar de overnameToken en verwervenTokeSilent calls

U kunt `MSALPublicClientApplication` maken met de volgende code:

Doelstelling-C:

```objc
NSError *error = nil;
MSALPublicClientApplicationConfig *configuration = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"];
    
MSALPublicClientApplication *application =
[[MSALPublicClientApplication alloc] initWithConfiguration:configuration
                                                     error:&error];
```

Swift:

```swift
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>")
do {
  let application = try MSALPublicClientApplication(configuration: config)
  // continue on with application
            
} catch let error as NSError {
  // handle error here
}
```

Bel vervolgens de API voor accountbeheer om te zien of er accounts in de cache zijn:

Doelstelling-C:

```objc
NSString *accountIdentifier = nil /*previously saved MSAL account identifier */;
NSError *error = nil;
MSALAccount *account = [application accountForIdentifier:accountIdentifier error:&error];
```

Swift:

```swift
// definitions that need to be initialized
let application: MSALPublicClientApplication!
let accountIdentifier: String! /*previously saved MSAL account identifier */

do {
  let account = try application.account(forIdentifier: accountIdentifier)
  // continue with account usage
} catch let error as NSError {
  // handle error here
}
```



of lees alle rekeningen:

Doelstelling-C:

```objc
NSError *error = nil;
NSArray<MSALAccount *> *accounts = [application allAccounts:&error];
```

Swift:

```swift
let application: MSALPublicClientApplication!
do {
  let accounts = try application.allAccounts()
  // continue with account usage
} catch let error as NSError {
  // handle error here
}
```



Als er een account wordt `acquireTokenSilent` gevonden, roept u de MSAL API aan:

Doelstelling-C:

```objc
MSALSilentTokenParameters *silentParameters = [[MSALSilentTokenParameters alloc] initWithScopes:@[@"<your-resource-here>/.default"] account:account];
    
[application acquireTokenSilentWithParameters:silentParameters
                              completionBlock:^(MSALResult *result, NSError *error)
{
    if (result)
    {
        NSString *accessToken = result.accessToken;
        // Use your token
    }
    else
    {
        // Check the error
        if ([error.domain isEqual:MSALErrorDomain] && error.code == MSALErrorInteractionRequired)
        {
            // Interactive auth will be required
        }
            
        // Other errors may require trying again later, or reporting authentication problems to the user
    }
}];
```

Swift:

```swift
let application: MSALPublicClientApplication!
let account: MSALAccount!
        
let silentParameters = MSALSilentTokenParameters(scopes: ["<your-resource-here>/.default"], 
                                                 account: account)
application.acquireTokenSilent(with: silentParameters) {
  (result: MSALResult?, error: Error?) in
  if let accessToken = result?.accessToken {
     // use accessToken
  }
  else {
    // Check the error
    guard let error = error else {
      assert(true, "callback should contain a valid result or error")
      return
    }
    
    let nsError = error as NSError
    if (nsError.domain == MSALErrorDomain
        && nsError.code == MSALError.interactionRequired.rawValue) {
      // Interactive auth will be required
    }
                
    // Other errors may require trying again later, or reporting authentication problems to the user
  }
}
```



## <a name="next-steps"></a>Volgende stappen

Meer informatie over [verificatiestromen en toepassingsscenario's](authentication-flows-app-scenarios.md)
