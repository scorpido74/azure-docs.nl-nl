---
title: De iOS SDK gebruiken
description: IOS SDK gebruiken voor Azure Mobile Apps
ms.assetid: 4e8e45df-c36a-4a60-9ad4-393ec10b7eb9
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 1bf8f8e198f6c4a4a0af308262cd830685698a80
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249345"
---
# <a name="how-to-use-ios-client-library-for-azure-mobile-apps"></a>IOS-clientbibliotheek gebruiken voor Mobiele Azure-apps

[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

## <a name="overview"></a>Overzicht
In deze handleiding leert u veelvoorkomende scenario's uit te voeren met behulp van de nieuwste [Azure Mobile Apps iOS SDK.][1] Als u nieuw bent in Azure Mobile Apps, voltooit u Azure [Mobile Apps Snel aan de slag] om een backend te maken, een tabel te maken en een vooraf gebouwd iOS Xcode-project te downloaden. In deze gids richten we ons op de client-side iOS SDK. Zie de Server SDK HOWTo's voor meer informatie over de SDK aan de serverzijde voor de back-end.

## <a name="reference-documentation"></a>Referentiedocumentatie

De referentiedocumentatie voor de SDK van de iOS-client bevindt zich hier: [Azure Mobile Apps iOS Client Reference][2].

## <a name="supported-platforms"></a>Ondersteunde platformen

De iOS SDK ondersteunt Objective-C-projecten, Swift 2.2-projecten en Swift 2.3-projecten voor iOS-versies 8.0 of hoger.

De verificatie 'serverstroom' maakt gebruik van een WebView voor de gepresenteerde gebruikersinterface.  Als het apparaat geen WebView-gebruikersinterface kan presenteren, is een andere verificatiemethode vereist die buiten het bereik van het product valt.  
Deze SDK is dus niet geschikt voor Watch-type of soortgelijke beperkte apparaten.

## <a name="setup-and-prerequisites"></a><a name="Setup"></a>Instellingen en vereisten

In deze handleiding wordt ervan uitgegaan dat u een back-end met een tabel hebt gemaakt. In deze handleiding wordt ervan uitgegaan dat de tabel hetzelfde schema heeft als de tabellen in die zelfstudies. In deze handleiding wordt er ook `MicrosoftAzureMobile.framework` van `MicrosoftAzureMobile/MicrosoftAzureMobile.h`uitgegaan dat u in uw code verwijst en importeert.

## <a name="how-to-create-client"></a><a name="create-client"></a>Hoe: Client maken

Als u toegang wilt krijgen tot een `MSClient`back-end van Azure Mobile Apps in uw project, maakt u een . Vervang `AppUrl` door de URL van de app. Je mag `gatewayURLString` `applicationKey` vertrekken en leeg zijn. Als u een gateway voor `gatewayURLString` verificatie instelt, vult u de gateway-URL in.

**Doelstelling-C**:

```objc
MSClient *client = [MSClient clientWithApplicationURLString:@"AppUrl"];
```

**Swift**:

```swift
let client = MSClient(applicationURLString: "AppUrl")
```

## <a name="how-to-create-table-reference"></a><a name="table-reference"></a>Hoe: Tabelverwijzing maken

Maak een verwijzing naar de back-endtabel als u gegevens wilt bekijken of bijwerken. Vervang `TodoItem` door de naam van uw tabel

**Doelstelling-C**:

```objc
MSTable *table = [client tableWithName:@"TodoItem"];
```

**Swift**:

```swift
let table = client.tableWithName("TodoItem")
```

## <a name="how-to-query-data"></a><a name="querying"></a>How to: Querygegevens

Als u een databasequery `MSTable` wilt maken, vraagt u het object op. In de volgende query `TodoItem` worden alle items in de selectie opgenomen en wordt de tekst van elk item logboeken.

**Doelstelling-C**:

```objc
[table readWithCompletion:^(MSQueryResult *result, NSError *error) {
        if(error) { // error is nil if no error occurred
                NSLog(@"ERROR %@", error);
        } else {
                for(NSDictionary *item in result.items) { // items is NSArray of records that match query
                        NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
                }
        }
}];
```

**Swift**:

```swift
table.readWithCompletion { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let items = result?.items {
        for item in items {
            print("Todo Item: ", item["text"])
        }
    }
}
```

## <a name="how-to-filter-returned-data"></a><a name="filtering"></a>Hoe: Geretourneerde gegevens filteren

Om resultaten te filteren, zijn er veel beschikbare opties.

Als u wilt filteren `NSPredicate` met `readWithPredicate`een predicaat, gebruikt u een en . Met de volgende filters worden gegevens geretourneerd om alleen onvolledige Todo-items te vinden.

**Doelstelling-C**:

```objc
// Create a predicate that finds items where complete is false
NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];
// Query the TodoItem table
[table readWithPredicate:predicate completion:^(MSQueryResult *result, NSError *error) {
        if(error) {
                NSLog(@"ERROR %@", error);
        } else {
                for(NSDictionary *item in result.items) {
                        NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
                }
        }
}];
```

**Swift**:

```swift
// Create a predicate that finds items where complete is false
let predicate =  NSPredicate(format: "complete == NO")
// Query the TodoItem table
table.readWithPredicate(predicate) { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let items = result?.items {
        for item in items {
            print("Todo Item: ", item["text"])
        }
    }
}
```

## <a name="how-to-use-msquery"></a><a name="query-object"></a>Hoe: MSQuery gebruiken

Als u een complexe query wilt uitvoeren (inclusief sorteren en paging), maakt u een `MSQuery` object, rechtstreeks of met behulp van een predicaat:

**Doelstelling-C**:

```objc
MSQuery *query = [table query];
MSQuery *query = [table queryWithPredicate: [NSPredicate predicateWithFormat:@"complete == NO"]];
```

**Swift**:

```swift
let query = table.query()
let query = table.queryWithPredicate(NSPredicate(format: "complete == NO"))
```

`MSQuery`hiermee u verschillende querygedragingen beheren.

* Volgorde van de resultaten opgeven
* Beperken welke velden moeten worden retournerd
* Beperken hoeveel records moeten worden teruggekeerd
* Totaal aantal opgeven in antwoord
* Aangepaste querytekenreeksparameters opgeven in aanvraag
* Extra functies toepassen

Voer `MSQuery` een query `readWithCompletion` uit door een beroep te doen op het object.

## <a name="how-to-sort-data-with-msquery"></a><a name="sorting"></a>Hoe: Gegevens sorteren met MSQuery

Om resultaten te sorteren, laten we eens kijken naar een voorbeeld. Om te sorteren op veld 'tekst' oplopend, `MSQuery` dan door 'compleet' aflopend, aanroepen als volgt:

**Doelstelling-C**:

```objc
[query orderByAscending:@"text"];
[query orderByDescending:@"complete"];
[query readWithCompletion:^(MSQueryResult *result, NSError *error) {
        if(error) {
                NSLog(@"ERROR %@", error);
        } else {
                for(NSDictionary *item in result.items) {
                        NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
                }
        }
}];
```

**Swift**:

```swift
query.orderByAscending("text")
query.orderByDescending("complete")
query.readWithCompletion { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let items = result?.items {
        for item in items {
            print("Todo Item: ", item["text"])
        }
    }
}
```

## <a name="how-to-limit-fields-and-expand-query-string-parameters-with-msquery"></a><a name="selecting"></a><a name="parameters"></a>How to: Velden beperken en querytekenreeksparameters uitbreiden met MSQuery

Als u velden wilt beperken die in een query moeten worden geretourneerd, geeft u de namen op van de velden in de eigenschap **selectFields.** In dit voorbeeld worden alleen de tekst en de voltooide velden geretourneerd:

**Doelstelling-C**:

```objc
query.selectFields = @[@"text", @"complete"];
```

**Swift**:

```swift
query.selectFields = ["text", "complete"]
```

Als u aanvullende querytekenreeksparameters wilt opnemen in de serveraanvraag (bijvoorbeeld omdat `query.parameters` een aangepast serverscript deze gebruikt), vult u het volgende op:

**Doelstelling-C**:

```objc
query.parameters = @{
    @"myKey1" : @"value1",
    @"myKey2" : @"value2",
};
```

**Swift**:

```swift
query.parameters = ["myKey1": "value1", "myKey2": "value2"]
```

## <a name="how-to-configure-page-size"></a><a name="paging"></a>How to: Paginagrootte configureren

Met Azure Mobile Apps bepaalt het paginaformaat het aantal records dat tegelijk wordt opgehaald uit de backendtabellen. Een aanroep naar `pull` gegevens zou vervolgens gegevens batch-up, op basis van deze pagina grootte, totdat er niet meer records te trekken.

Het is mogelijk om een paginaformaat te configureren met **MSPullSettings** zoals hieronder weergegeven. Het standaardpaginaformaat is 50 en in het onderstaande voorbeeld wordt dit gewijzigd in 3.

U een andere paginagrootte configureren om prestatieredenen. Als u een groot aantal kleine gegevensrecords hebt, vermindert een hoge paginagrootte het aantal retourritten van de server.

Met deze instelling bepaalt u alleen het paginaformaat aan de clientzijde. Als de client vraagt om een groter paginaformaat dan de backend van Mobile Apps ondersteunt, is het paginaformaat beperkt tot het maximum dat de backend is geconfigureerd om te ondersteunen.

Deze instelling is ook het *aantal* gegevensrecords, niet de *bytegrootte*.

Als u de grootte van de clientpagina vergroot, moet u ook de paginagrootte op de server vergroten. Zie ['Hoe: de grootte van de tabelpaging aanpassen'](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) voor de stappen om dit te doen.

**Doelstelling-C**:

```objc
  MSPullSettings *pullSettings = [[MSPullSettings alloc] initWithPageSize:3];
  [table  pullWithQuery:query queryId:@nil settings:pullSettings
                        completion:^(NSError * _Nullable error) {
                               if(error) {
                    NSLog(@"ERROR %@", error);
                }
                           }];
```

**Swift**:

```swift
let pullSettings = MSPullSettings(pageSize: 3)
table.pullWithQuery(query, queryId:nil, settings: pullSettings) { (error) in
    if let err = error {
        print("ERROR ", err)
    }
}
```

## <a name="how-to-insert-data"></a><a name="inserting"></a>Hoe: Gegevens invoegen

Als u een nieuwe tabelrij wilt invoegen, maakt u een en roept u een `NSDictionary` aanroep aan `table insert`. Als [Dynamisch schema] is ingeschakeld, genereert de mobiele backend van `NSDictionary`Azure App Service automatisch nieuwe kolommen op basis van de .

Als `id` deze niet wordt geleverd, genereert de backend automatisch een nieuwe unieke ID. Geef uw `id` eigen e-mailadressen, gebruikersnamen of uw eigen aangepaste waarden als ID. Het verstrekken van uw eigen ID kan joins en business-georiënteerde database logica te vergemakkelijken.

Het `result` bevat het nieuwe item dat is ingevoegd. Afhankelijk van de serverlogica kan er aanvullende of gewijzigde gegevens worden weergegeven in vergelijking met wat er aan de server is doorgegeven.

**Doelstelling-C**:

```objc
NSDictionary *newItem = @{@"id": @"custom-id", @"text": @"my new item", @"complete" : @NO};
[table insert:newItem completion:^(NSDictionary *result, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
    }
}];
```

**Swift**:

```swift
let newItem = ["id": "custom-id", "text": "my new item", "complete": false]
table.insert(newItem) { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let item = result {
        print("Todo Item: ", item["text"])
    }
}
```

## <a name="how-to-modify-data"></a><a name="modifying"></a>Hoe: Gegevens wijzigen

Als u een bestaande rij wilt `update`bijwerken, wijzigt u een item en roept u op:

**Doelstelling-C**:

```objc
NSMutableDictionary *newItem = [oldItem mutableCopy]; // oldItem is NSDictionary
[newItem setValue:@"Updated text" forKey:@"text"];
[table update:newItem completion:^(NSDictionary *result, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
    }
}];
```

**Swift**:

```swift
if let newItem = oldItem.mutableCopy() as? NSMutableDictionary {
    newItem["text"] = "Updated text"
    table2.update(newItem as [NSObject: AnyObject], completion: { (result, error) -> Void in
        if let err = error {
            print("ERROR ", err)
        } else if let item = result {
            print("Todo Item: ", item["text"])
        }
    })
}
```

U ook de rij-id en het bijgewerkte veld leveren:

**Doelstelling-C**:

```objc
[table update:@{@"id":@"custom-id", @"text":"my EDITED item"} completion:^(NSDictionary *result, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
    }
}];
```

**Swift**:

```swift
table.update(["id": "custom-id", "text": "my EDITED item"]) { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let item = result {
        print("Todo Item: ", item["text"])
    }
}
```

Het `id` kenmerk moet minimaal worden ingesteld bij het uitvoeren van updates.

## <a name="how-to-delete-data"></a><a name="deleting"></a>Hoe: Gegevens verwijderen

Als u een `delete` item wilt verwijderen, roept u het item aan:

**Doelstelling-C**:

```objc
[table delete:item completion:^(id itemId, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item ID: %@", itemId);
    }
}];
```

**Swift**:

```swift
table.delete(newItem as [NSObject: AnyObject]) { (itemId, error) in
    if let err = error {
        print("ERROR ", err)
    } else {
        print("Todo Item ID: ", itemId)
    }
}
```

U ook verwijderen door een rij-id op te geven:

**Doelstelling-C**:

```objc
[table deleteWithId:@"37BBF396-11F0-4B39-85C8-B319C729AF6D" completion:^(id itemId, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item ID: %@", itemId);
    }
}];
```

**Swift**:

```swift
table.deleteWithId("37BBF396-11F0-4B39-85C8-B319C729AF6D") { (itemId, error) in
    if let err = error {
        print("ERROR ", err)
    } else {
        print("Todo Item ID: ", itemId)
    }
}
```

Het `id` kenmerk moet minimaal worden ingesteld bij het verwijderen.

## <a name="how-to-call-custom-api"></a><a name="customapi"></a>How to: Call Custom API

Met een aangepaste API u elke backendfunctionaliteit blootleggen. Het hoeft niet in kaart te brengen naar een tabelbewerking. Niet alleen krijgt u meer controle over messaging, u zelfs kopteksten lezen/instellen en de indeling van de reactietekst wijzigen.

Als u een aangepaste `MSClient.invokeAPI`API wilt aanroepen, aanroepen . De inhoud van het verzoek en de respons wordt behandeld als JSON. Als u andere mediatypen wilt gebruiken, [gebruikt u de andere overbelasting van `invokeAPI` ][5].  Als u `GET` een aanvraag `POST` wilt indienen `HTTPMethod` `"GET"` in `body` plaats `nil` van een aanvraag, stelt u de parameter in op en parameter in (omdat GET-aanvragen geen berichtinstanties hebben.) Als uw aangepaste API andere HTTP-werkwoorden ondersteunt, wijzigt u de juiste gegevens. `HTTPMethod`

**Doelstelling-C**:

```objc
[self.client invokeAPI:@"sendEmail"
                  body:@{ @"contents": @"Hello world!" }
            HTTPMethod:@"POST"
            parameters:@{ @"to": @"bill@contoso.com", @"subject" : @"Hi!" }
               headers:nil
            completion: ^(NSData *result, NSHTTPURLResponse *response, NSError *error) {
                if(error) {
                    NSLog(@"ERROR %@", error);
                } else {
                    // Do something with result
                }
            }];
```

**Swift**:

```swift
client.invokeAPI("sendEmail",
            body: [ "contents": "Hello World" ],
            HTTPMethod: "POST",
            parameters: [ "to": "bill@contoso.com", "subject" : "Hi!" ],
            headers: nil)
            {
                (result, response, error) -> Void in
                if let err = error {
                    print("ERROR ", err)
                } else if let res = result {
                          // Do something with result
                }
        }
```

## <a name="how-to-register-push-templates-to-send-cross-platform-notifications"></a><a name="templates"></a>How to: Pushtemplates registreren om cross-platform meldingen te verzenden

Als u sjablonen wilt registreren, geeft u sjablonen door met de **methode client.push registerDeviceToken** in uw client-app.

**Doelstelling-C**:

```objc
[client.push registerDeviceToken:deviceToken template:iOSTemplate completion:^(NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    }
}];
```

**Swift**:

```swift
client.push?.registerDeviceToken(NSData(), template: iOSTemplate, completion: { (error) in
    if let err = error {
        print("ERROR ", err)
    }
})
```

Uw sjablonen zijn van het type NSDictionary en kunnen meerdere sjablonen in de volgende indeling bevatten:

**Doelstelling-C**:

```objc
NSDictionary *iOSTemplate = @{ @"templateName": @{ @"body": @{ @"aps": @{ @"alert": @"$(message)" } } } };
```

**Swift**:

```swift
let iOSTemplate = ["templateName": ["body": ["aps": ["alert": "$(message)"]]]]
```

Alle tags zijn ontdaan van het verzoek om beveiliging.  Zie [Werken met de .NET-backendserver SDK voor Azure Mobile Apps][4]als u tags wilt toevoegen aan installaties of sjablonen in installaties.  Als u meldingen wilt verzenden met deze geregistreerde sjablonen, werkt u met [API's voor meldingshubs][3].

## <a name="how-to-handle-errors"></a><a name="errors"></a>Hoe: Fouten verwerken

Wanneer u een mobiele backend van Azure App `NSError` Service aanroept, bevat het voltooiingsblok een parameter. Wanneer er een fout optreedt, is deze parameter niet-nihil. In uw code moet u deze parameter controleren en de fout zo nodig afhandelen, zoals blijkt uit de voorgaande codefragmenten.

Het [`<WindowsAzureMobileServices/MSError.h>`][6] bestand definieert `MSErrorResponseKey` `MSErrorRequestKey`de `MSErrorServerItemKey`constanten , en . Ga als u meer gegevens met betrekking tot de fout om:

**Doelstelling-C**:

```objc
NSDictionary *serverItem = [error.userInfo objectForKey:MSErrorServerItemKey];
```

**Swift**:

```swift
let serverItem = error.userInfo[MSErrorServerItemKey]
```

Bovendien definieert het bestand constanten voor elke foutcode:

**Doelstelling-C**:

```objc
if (error.code == MSErrorPreconditionFailed) {
```

**Swift**:

```swift
if (error.code == MSErrorPreconditionFailed) {
```

## <a name="how-to-authenticate-users-with-the-active-directory-authentication-library"></a><a name="adal"></a>Hoe: gebruikers verifiëren met de Active Directory-verificatiebibliotheek

U de Active Directory Authentication Library (ADAL) gebruiken om gebruikers aan te melden bij uw toepassing met Azure Active Directory. Clientflowverificatie met behulp van een identiteitsprovider `loginWithProvider:completion:` SDK verdient de voorkeur boven het gebruik van de methode.  Clientflow-verificatie zorgt voor een meer native UX-gevoel en zorgt voor extra aanpassingen.

1. Configureer de backend van uw mobiele app voor AAD-aanmelding door de zelfstudie [App-service voor Active Directory-aanmelding te configureren.][7] Zorg ervoor dat u de optionele stap van het registreren van een native clienttoepassing voltooit. Voor iOS raden we aan dat de `<app-scheme>://<bundle-id>`omleiding URI van het formulier is. Zie de [ADAL iOS quickstart][8]voor meer informatie.
2. Installeer ADAL met cacaopotigen. Bewerk uw Podfile om de volgende definitie op te nemen en uw project te vervangen door de naam van uw **Xcode-project:**

        source 'https://github.com/CocoaPods/Specs.git'
        link_with ['YOUR-PROJECT']
        xcodeproj 'YOUR-PROJECT'

   en de Pod:

        pod 'ADALiOS'

3. Voer de terminal `pod install` uit vanaf de map met uw project en open vervolgens de gegenereerde Xcode-werkruimte (niet het project).
4. Voeg de volgende code toe aan uw toepassing, afhankelijk van de taal die u gebruikt. Maak in elk van deze vervangingen:

   * Vervang **INSERT-AUTHORITY-HERE** door de naam van de tenant waarin u uw aanvraag heeft ingericht. Het formaat https://login.microsoftonline.com/contoso.onmicrosoft.commoet zijn . Deze waarde kan worden gekopieerd van het tabblad Domein in uw Azure Active Directory in de [Azure-portal.]
   * Vervang **INSERT-RESOURCE-ID-HERE** door de client-ID voor uw mobiele app back-end. U de client-id verkrijgen via het tabblad **Geavanceerd** onder **Azure Active Directory-instellingen** in de portal.
   * Vervang **INSERT-CLIENT-ID-HERE** door de client-id die u hebt gekopieerd van de native clienttoepassing.
   * Vervang **INSERT-REDIRECT-URI-HERE** door het *eindpunt /.auth/login/done van* uw site, met behulp van het HTTPS-schema. Deze waarde moet *https://contoso.azurewebsites.net/.auth/login/done*vergelijkbaar zijn met .

**Doelstelling-C**:

```objc
#import <ADALiOS/ADAuthenticationContext.h>
#import <ADALiOS/ADAuthenticationSettings.h>
// ...
- (void) authenticate:(UIViewController*) parent
            completion:(void (^) (MSUser*, NSError*))completionBlock;
{
    NSString *authority = @"INSERT-AUTHORITY-HERE";
    NSString *resourceId = @"INSERT-RESOURCE-ID-HERE";
    NSString *clientId = @"INSERT-CLIENT-ID-HERE";
    NSURL *redirectUri = [[NSURL alloc]initWithString:@"INSERT-REDIRECT-URI-HERE"];
    ADAuthenticationError *error;
    ADAuthenticationContext *authContext = [ADAuthenticationContext authenticationContextWithAuthority:authority error:&error];
    authContext.parentController = parent;
    [ADAuthenticationSettings sharedInstance].enableFullScreen = YES;
    [authContext acquireTokenWithResource:resourceId
                                    clientId:clientId
                                redirectUri:redirectUri
                            completionBlock:^(ADAuthenticationResult *result) {
                                if (result.status != AD_SUCCEEDED)
                                {
                                    completionBlock(nil, result.error);;
                                }
                                else
                                {
                                    NSDictionary *payload = @{
                                                            @"access_token" : result.tokenCacheStoreItem.accessToken
                                                            };
                                    [client loginWithProvider:@"aad" token:payload completion:completionBlock];
                                }
                            }];
}
```

**Swift**:

```swift
// add the following imports to your bridging header:
//        #import <ADALiOS/ADAuthenticationContext.h>
//        #import <ADALiOS/ADAuthenticationSettings.h>

func authenticate(parent: UIViewController, completion: (MSUser?, NSError?) -> Void) {
    let authority = "INSERT-AUTHORITY-HERE"
    let resourceId = "INSERT-RESOURCE-ID-HERE"
    let clientId = "INSERT-CLIENT-ID-HERE"
    let redirectUri = NSURL(string: "INSERT-REDIRECT-URI-HERE")
    var error: AutoreleasingUnsafeMutablePointer<ADAuthenticationError?> = nil
    let authContext = ADAuthenticationContext(authority: authority, error: error)
    authContext.parentController = parent
    ADAuthenticationSettings.sharedInstance().enableFullScreen = true
    authContext.acquireTokenWithResource(resourceId, clientId: clientId, redirectUri: redirectUri) { (result) in
            if result.status != AD_SUCCEEDED {
                completion(nil, result.error)
            }
            else {
                let payload: [String: String] = ["access_token": result.tokenCacheStoreItem.accessToken]
                client.loginWithProvider("aad", token: payload, completion: completion)
            }
        }
}
```

## <a name="how-to-authenticate-users-with-the-facebook-sdk-for-ios"></a><a name="facebook-sdk"></a>Hoe: Gebruikers verifiëren met de Facebook SDK voor iOS

Je de Facebook SDK voor iOS gebruiken om gebruikers aan te melden bij je toepassing met Facebook.  Het gebruik van een client flow `loginWithProvider:completion:` authenticatie is de voorkeur boven het gebruik van de methode.  De client flow authenticatie zorgt voor een meer native UX-gevoel en zorgt voor extra aanpassingen.

1. Configureer de backend van je mobiele app voor Facebook-aanmelding door de [aanmeldingsles App-service voor Facebook te configureren.][9]
2. Installeer de Facebook SDK voor iOS door de [Facebook SDK voor iOS - Aan de slag documentatie te][10] volgen. In plaats van een app te maken, u het iOS-platform toevoegen aan uw bestaande registratie.
3. De documentatie van Facebook bevat een aantal Objective-C-code in de app-gemachtigde. Als u **Swift**gebruikt, u de volgende vertalingen gebruiken voor AppDelegate.swift:

    ```swift
    // Add the following import to your bridging header:
    //        #import <FBSDKCoreKit/FBSDKCoreKit.h>

    func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject : AnyObject]?) -> Bool {
        FBSDKApplicationDelegate.sharedInstance().application(application, didFinishLaunchingWithOptions: launchOptions)
        // Add any custom logic here.
        return true
    }

    func application(application: UIApplication, openURL url: NSURL, sourceApplication: String?, annotation: AnyObject?) -> Bool {
        let handled = FBSDKApplicationDelegate.sharedInstance().application(application, openURL: url, sourceApplication: sourceApplication, annotation: annotation)
        // Add any custom logic here.
        return handled
    }
    ```
4. Voeg naast `FBSDKCoreKit.framework` het toevoegen aan uw project `FBSDKLoginKit.framework` ook een verwijzing toe naar op dezelfde manier.
5. Voeg de volgende code toe aan uw toepassing, afhankelijk van de taal die u gebruikt.

    **Doelstelling-C**:

    ```objc
    #import <FBSDKLoginKit/FBSDKLoginKit.h>
    #import <FBSDKCoreKit/FBSDKAccessToken.h>
    // ...
    - (void) authenticate:(UIViewController*) parent
                completion:(void (^) (MSUser*, NSError*)) completionBlock;
    {
        FBSDKLoginManager *loginManager = [[FBSDKLoginManager alloc] init];
        [loginManager
            logInWithReadPermissions: @[@"public_profile"]
            fromViewController:parent
            handler:^(FBSDKLoginManagerLoginResult *result, NSError *error) {
                if (error) {
                    completionBlock(nil, error);
                } else if (result.isCancelled) {
                    completionBlock(nil, error);
                } else {
                    NSDictionary *payload = @{
                                            @"access_token":result.token.tokenString
                                            };
                    [client loginWithProvider:@"facebook" token:payload completion:completionBlock];
                }
            }];
    }
    ```

    **Swift**:

    ```swift
    // Add the following imports to your bridging header:
    //        #import <FBSDKLoginKit/FBSDKLoginKit.h>
    //        #import <FBSDKCoreKit/FBSDKAccessToken.h>

    func authenticate(parent: UIViewController, completion: (MSUser?, NSError?) -> Void) {
        let loginManager = FBSDKLoginManager()
        loginManager.logInWithReadPermissions(["public_profile"], fromViewController: parent) { (result, error) in
            if (error != nil) {
                completion(nil, error)
            }
            else if result.isCancelled {
                completion(nil, error)
            }
            else {
                let payload: [String: String] = ["access_token": result.token.tokenString]
                client.loginWithProvider("facebook", token: payload, completion: completion)
            }
        }
    }
    ```

## <a name="how-to-authenticate-users-with-twitter-fabric-for-ios"></a><a name="twitter-fabric"></a>How to: Gebruikers verifiëren met Twitter Fabric voor iOS

U Fabric voor iOS gebruiken om gebruikers aan te melden bij uw toepassing via Twitter. Client Flow-verificatie verdient de `loginWithProvider:completion:` voorkeur boven het gebruik van de methode, omdat het een meer native UX-gevoel biedt en extra aanpassingen mogelijk maakt.

1. Configureer de backend van uw mobiele app voor De aanmelding van Twitter door de [aanmeldingsvoor App-service voor Twitter-aanmelding te volgen.](../app-service/configure-authentication-provider-twitter.md)
2. Voeg Fabric toe aan je project door de [Fabric for iOS - Aan de slag te gaan] en TwitterKit in te stellen.

   > [!NOTE]
   > Fabric maakt standaard een Twitter-applicatie voor u. U voorkomen dat u een toepassing maakt door de consumentsleutel en het consumentengeheim te registreren die u eerder hebt gemaakt met behulp van de volgende codefragmenten.    U ook de waarden consumentensleutel en consumentengeheim die u aan App Service verstrekt, vervangen door de waarden die u in het [Fabric-dashboard ziet.] Als u deze optie kiest, moet u de terugbel-URL `https://<yoursitename>.azurewebsites.net/.auth/login/twitter/callback`instellen op een tijdelijke aanduiding, zoals .

    Als u ervoor kiest om de geheimen die u eerder hebt gemaakt, te gebruiken, voegt u de volgende code toe aan uw app-gemachtigde:

    **Doelstelling-C**:

    ```objc
    #import <Fabric/Fabric.h>
    #import <TwitterKit/TwitterKit.h>
    // ...
    - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
    {
        [[Twitter sharedInstance] startWithConsumerKey:@"your_key" consumerSecret:@"your_secret"];
        [Fabric with:@[[Twitter class]]];
        // Add any custom logic here.
        return YES;
    }
    ```

    **Swift**:

    ```swift
    import Fabric
    import TwitterKit
    // ...
    func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject : AnyObject]?) -> Bool {
        Twitter.sharedInstance().startWithConsumerKey("your_key", consumerSecret: "your_secret")
        Fabric.with([Twitter.self])
        // Add any custom logic here.
        return true
    }
    ```

3. Voeg de volgende code toe aan uw toepassing, afhankelijk van de taal die u gebruikt.

    **Doelstelling-C**:

    ```objc
    #import <TwitterKit/TwitterKit.h>
    // ...
    - (void)authenticate:(UIViewController*)parent completion:(void (^) (MSUser*, NSError*))completionBlock
    {
        [[Twitter sharedInstance] logInWithCompletion:^(TWTRSession *session, NSError *error) {
            if (session) {
                NSDictionary *payload = @{
                                            @"access_token":session.authToken,
                                            @"access_token_secret":session.authTokenSecret
                                        };
                [client loginWithProvider:@"twitter" token:payload completion:completionBlock];
            } else {
                completionBlock(nil, error);
            }
        }];
    }
    ```

    **Swift**:

    ```swift
    import TwitterKit
    // ...
    func authenticate(parent: UIViewController, completion: (MSUser?, NSError?) -> Void) {
        let client = self.table!.client
        Twitter.sharedInstance().logInWithCompletion { session, error in
            if (session != nil) {
                let payload: [String: String] = ["access_token": session!.authToken, "access_token_secret": session!.authTokenSecret]
                client.loginWithProvider("twitter", token: payload, completion: completion)
            } else {
                completion(nil, error)
            }
        }
    }
    ```

## <a name="how-to-authenticate-users-with-the-google-sign-in-sdk-for-ios"></a><a name="google-sdk"></a>Hoe: Gebruikers verifiëren met de Google Sign-In SDK voor iOS

U de Google Sign-In SDK voor iOS gebruiken om gebruikers aan te melden bij uw toepassing met een Google-account.  Google heeft onlangs wijzigingen aangekondigd in hun OAuth-beveiligingsbeleid.  Voor deze beleidswijzigingen moet de Google SDK in de toekomst worden gebruikt.

1. Configureer de backend van uw mobiele app voor Google-aanmelding door de [aanmeldingsvoor App-service voor Google te configureren.](../app-service/configure-authentication-provider-google.md)
2. Installeer de Google SDK voor iOS door de [Google Sign-In voor iOS te](https://developers.google.com/identity/sign-in/ios/start-integrating) volgen - Begin met het integreren van documentatie. U de sectie 'Verifiëren met een backendserver' overslaan.
3. Voeg het volgende toe `signIn:didSignInForUser:withError:` aan de methode van uw gemachtigde, afhankelijk van de taal die u gebruikt.

    **Doelstelling-C**:
    ```objc
    NSDictionary *payload = @{
                                @"id_token":user.authentication.idToken,
                                @"authorization_code":user.serverAuthCode
                                };

    [client loginWithProvider:@"google" token:payload completion:^(MSUser *user, NSError *error) {
        // ...
    }];
    ```

    **Swift**:

    ```swift
    let payload: [String: String] = ["id_token": user.authentication.idToken, "authorization_code": user.serverAuthCode]
    client.loginWithProvider("google", token: payload) { (user, error) in
        // ...
    }
    ```

4. Zorg ervoor dat u `application:didFinishLaunchingWithOptions:` ook het volgende toevoegt aan uw app-gemachtigde, waarbij u 'SERVER_CLIENT_ID' vervangt door dezelfde ID die u hebt gebruikt om App Service in stap 1 te configureren.

    **Doelstelling-C**:

    ```objc
    [GIDSignIn sharedInstance].serverClientID = @"SERVER_CLIENT_ID";
    ```

     **Swift**:

    ```swift
    GIDSignIn.sharedInstance().serverClientID = "SERVER_CLIENT_ID"
    ```

5. Voeg de volgende code toe aan uw toepassing in `GIDSignInUIDelegate` een UIViewController die het protocol implementeert, afhankelijk van de taal die u gebruikt.  U bent afgemeld voordat u opnieuw bent aangemeld en hoewel u uw referenties niet opnieuw hoeft in te voeren, ziet u een toestemmingsdialoogvenster.  Alleen deze methode aanroepen wanneer het sessietoken is verlopen.

   **Doelstelling-C**:

    ```objc
    #import <Google/SignIn.h>
    // ...
    - (void)authenticate
    {
            [GIDSignIn sharedInstance].uiDelegate = self;
            [[GIDSignIn sharedInstance] signOut];
            [[GIDSignIn sharedInstance] signIn];
    }
    ```

   **Swift**:

    ```swift
    // ...
    func authenticate() {
        GIDSignIn.sharedInstance().uiDelegate = self
        GIDSignIn.sharedInstance().signOut()
        GIDSignIn.sharedInstance().signIn()
    }
    ```

<!-- Anchors. -->

[What is Mobile Services]: #what-is
[Concepts]: #concepts
[Setup and Prerequisites]: #Setup
[How to: Create the Mobile Services client]: #create-client
[How to: Create a table reference]: #table-reference
[How to: Query data from a mobile service]: #querying
[Filter returned data]: #filtering
[Sort returned data]: #sorting
[Return data in pages]: #paging
[Select specific columns]: #selecting
[How to: Bind data to the user interface]: #binding
[How to: Insert data into a mobile service]: #inserting
[How to: Modify data in a mobile service]: #modifying
[How to: Authenticate users]: #authentication
[Cache authentication tokens]: #caching-tokens
[How to: Upload images and large files]: #blobs
[How to: Handle errors]: #errors
[How to: Design unit tests]: #unit-testing
[How to: Customize the client]: #customizing
[Customize request headers]: #custom-headers
[Customize data type serialization]: #custom-serialization
[Next Steps]: #next-steps
[How to: Use MSQuery]: #query-object

<!-- Images. -->

<!-- URLs. -->
[Snel aan de slag met Azure Mobile Apps]: app-service-mobile-ios-get-started.md

[Add Mobile Services to Existing App]: /develop/mobile/tutorials/get-started-data
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Validate and modify data in Mobile Services by using server scripts]: /develop/mobile/tutorials/validate-modify-and-augment-data-ios
[Mobile Services SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Authentication]: /develop/mobile/tutorials/get-started-with-users-ios
[iOS SDK]: https://developer.apple.com/xcode
[Azure-portal]: https://portal.azure.com/
[Handling Expired Tokens]: https://go.microsoft.com/fwlink/p/?LinkId=301955
[Live Connect SDK]: https://go.microsoft.com/fwlink/p/?LinkId=301960
[Permissions]: https://msdn.microsoft.com/library/windowsazure/jj193161.aspx
[Service-side Authorization]: mobile-services-javascript-backend-service-side-authorization.md
[Use scripts to authorize users]: /develop/mobile/tutorials/authorize-users-in-scripts-ios
[Dynamisch schema]: https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/dynamic-schema
[How to: access custom parameters]: /develop/mobile/how-to-guides/work-with-server-scripts#access-headers
[Create a table]: https://msdn.microsoft.com/library/windowsazure/jj193162.aspx
[NSDictionary object]: https://go.microsoft.com/fwlink/p/?LinkId=301965
[ASCII control codes C0 and C1]: https://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[CLI to manage Mobile Services tables]: /cli/azure/get-started-with-az-cli2
[Conflict-Handler]: mobile-services-ios-handling-conflicts-offline-data.md#add-conflict-handling

[Dashboard voor stof]: https://www.fabric.io/home
[Fabric voor iOS - Aan de slag]: https://docs.fabric.io/ios/fabric/getting-started.html
[1]: https://github.com/Azure/azure-mobile-apps-ios-client/blob/master/README.md#ios-client-sdk
[2]: https://azure.github.io/azure-mobile-apps-ios-client/
[3]: https://msdn.microsoft.com/library/azure/dn495101.aspx
[4]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#tags
[5]: https://azure.github.io/azure-mobile-services/iOS/v3/Classes/MSClient.html#//api/name/invokeAPI:data:HTTPMethod:parameters:headers:completion:
[6]: https://github.com/Azure/azure-mobile-services/blob/master/sdk/iOS/src/MSError.h
[7]: ../app-service/configure-authentication-provider-aad.md
[8]:../active-directory/develop/quickstart-v2-ios.md
[9]: ../app-service/configure-authentication-provider-facebook.md
[10]: https://developers.facebook.com/docs/ios/getting-started
