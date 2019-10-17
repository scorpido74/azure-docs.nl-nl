---
title: Verificatie op iOS toevoegen met Azure Mobile Apps
description: Informatie over het gebruik van Azure Mobile Apps om gebruikers van uw iOS-app te verifiëren via verschillende id-providers, waaronder AAD, Google, Facebook, Twitter en micro soft.
services: app-service\mobile
documentationcenter: ios
author: elamalani
manager: crdun
editor: ''
ms.assetid: ef3d3cbe-e7ca-45f9-987f-80c44209dc06
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 800d86750f091404ee7f940d7cf8f6631e3fbbeb
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388700"
---
# <a name="add-authentication-to-your-ios-app"></a>Verificatie toevoegen aan uw iOS-app
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

> [!NOTE]
> Visual Studio App Center ondersteunt end-to-end-services en geïntegreerde services die een centrale rol spelen bij het ontwikkelen van mobiele apps. Ontwikkelaars kunnen services **bouwen**, **testen** en **distribueren** om een CI/CD-pijplijn (continue integratie en continue levering) in te stellen. Zodra de app is geïmplementeerd, kunnen ontwikkelaars de status en het gebruik van hun app controleren met behulp van de **analyseservice** en de **diagnoseservice** en communiceren met gebruikers met behulp van de **pushservice**. Ontwikkelaars kunnen ook gebruikmaken van **Auth** voor het verifiëren van gebruikers en van **Data** Service voor het persistent maken en synchroniseren van app-gegevens in de cloud.
>
> Als u Cloud Services wilt integreren in uw mobiele toepassing, meldt u zich aan bij [app Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) vandaag.

In deze zelf studie voegt u verificatie toe aan het [Quick start voor iOS] -project met behulp van een ondersteunde ID-provider. Deze zelf studie is gebaseerd op de zelf studie voor [Quick start voor iOS] , die u eerst moet volt ooien.

## <a name="register"></a>Registreer uw app voor verificatie en configureer de App Service
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Uw app toevoegen aan de toegestane externe omleidings-Url's

Voor beveiligde verificatie moet u een nieuw URL-schema definiëren voor uw app.  Hierdoor kan het verificatie systeem terugkeren naar uw app nadat het verificatie proces is voltooid.  In deze zelf studie gebruiken we het URL-schema _AppName_ in.  U kunt echter elk gewenst URL-schema gebruiken.  Deze moet uniek zijn voor uw mobiele toepassing.  De omleiding inschakelen op de server zijde:

1. Selecteer uw App Service in de [Azure-portal].

2. Klik op de menu optie voor **verificatie/autorisatie** .

3. Klik op **Azure Active Directory** in het gedeelte **verificatie providers** .

4. Stel de **beheer modus** in op **Geavanceerd**.

5. Voer in de **toegestane externe omleidings-url's**`appname://easyauth.callback` in.  De _AppName_ in deze teken reeks is het URL-schema voor uw mobiele toepassing.  De standaard URL-specificatie voor een protocol moet volgen (alleen letters en cijfers gebruiken en beginnen met een letter).  U moet een notitie maken van de teken reeks die u kiest, omdat u de code van uw mobiele toepassing moet aanpassen aan het URL-schema op verschillende locaties.

6. Klik op **OK**.

7. Klik op **Opslaan**.

## <a name="permissions"></a>Machtigingen voor geverifieerde gebruikers beperken
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

Druk in Xcode op **uitvoeren** om de app te starten. Er wordt een uitzonde ring gegenereerd omdat de app toegang probeert te krijgen tot de back-end als niet-geverifieerde gebruiker, maar voor de tabel *TodoItem* nu verificatie is vereist.

## <a name="add-authentication"></a>Verificatie toevoegen aan de app
**Doel-C**:

1. Open *QSTodoListViewController. m* in Xcode in uw Mac en voeg de volgende methode toe:

    ```Objective-C
    - (void)loginAndGetData
    {
        QSAppDelegate *appDelegate = (QSAppDelegate *)[UIApplication sharedApplication].delegate;
        appDelegate.qsTodoService = self.todoService;

        [self.todoService.client loginWithProvider:@"google" urlScheme:@"appname" controller:self animated:YES completion:^(MSUser * _Nullable user, NSError * _Nullable error) {
            if (error) {
                NSLog(@"Login failed with error: %@, %@", error, [error userInfo]);
            }
            else {
                self.todoService.client.currentUser = user;
                NSLog(@"User logged in: %@", user.userId);

                [self refresh];
            }
        }];
    }
    ```

    Wijzig *Google* in *MicrosoftAccount*, *Twitter*, *Facebook*of *windowsazureactivedirectory* als u Google niet als uw ID-provider gebruikt. Als u Facebook gebruikt, moet u [Facebook-domeinen white list][1] in uw app.

    Vervang de **urlScheme** door een unieke naam voor uw toepassing.  De urlScheme moet hetzelfde zijn als het URL-schema protocol dat u hebt opgegeven in het veld **toegestane externe omleidings-url's** in de Azure Portal. De urlScheme wordt gebruikt door de aanroep van de verificatie om terug te gaan naar uw toepassing nadat de verificatie aanvraag is voltooid.

2. Vervang `[self refresh]` in `viewDidLoad` in *QSTodoListViewController. m* door de volgende code:

    ```Objective-C
    [self loginAndGetData];
    ```

3. Open het bestand `QSAppDelegate.h` en voeg de volgende code toe:

    ```Objective-C
    #import "QSTodoService.h"

    @property (strong, nonatomic) QSTodoService *qsTodoService;
    ```

4. Open het bestand `QSAppDelegate.m` en voeg de volgende code toe:

    ```Objective-C
    - (BOOL)application:(UIApplication *)application openURL:(NSURL *)url options:(NSDictionary<UIApplicationOpenURLOptionsKey,id> *)options
    {
        if ([[url.scheme lowercaseString] isEqualToString:@"appname"]) {
            // Resume login flow
            return [self.qsTodoService.client resumeWithURL:url];
        }
        else {
            return NO;
        }
    }
    ```

   Voeg deze code rechtstreeks toe voordat de regel `#pragma mark - Core Data stack` wordt gelezen.  Vervang de _AppName_ door de urlScheme-waarde die u in stap 1 hebt gebruikt.

5. Open het bestand `AppName-Info.plist` (waarbij AppName wordt vervangen door de naam van uw app) en voeg de volgende code toe:

    ```XML
    <key>CFBundleURLTypes</key>
    <array>
        <dict>
            <key>CFBundleURLName</key>
            <string>com.microsoft.azure.zumo</string>
            <key>CFBundleURLSchemes</key>
            <array>
                <string>appname</string>
            </array>
        </dict>
    </array>
    ```

    Deze code moet in het element `<dict>` worden geplaatst.  Vervang de teken reeks _AppName_ (binnen de matrix voor **CFBundleURLSchemes**) door de naam van de app die u in stap 1 hebt gekozen.  U kunt deze wijzigingen ook aanbrengen in de plist-editor: Klik op het `AppName-Info.plist`-bestand in XCode om de plist-editor te openen.

    Vervang de teken reeks @no__t 0 voor **CFBundleURLName** met uw Apple bundel-id.

6. Druk op *uitvoeren* om de app te starten en meld u vervolgens aan. Wanneer u bent aangemeld, kunt u de taken lijst weer geven en updates maken.

**Swift**:

1. Open *ToDoTableViewController. Swift* in Xcode in uw Mac en voeg de volgende methode toe:

    ```swift
    func loginAndGetData() {

        guard let client = self.table?.client, client.currentUser == nil else {
            return
        }

        let appDelegate = UIApplication.shared.delegate as! AppDelegate
        appDelegate.todoTableViewController = self

        let loginBlock: MSClientLoginBlock = {(user, error) -> Void in
            if (error != nil) {
                print("Error: \(error?.localizedDescription)")
            }
            else {
                client.currentUser = user
                print("User logged in: \(user?.userId)")
            }
        }

        client.login(withProvider:"google", urlScheme: "appname", controller: self, animated: true, completion: loginBlock)

    }
    ```

    Wijzig *Google* in *MicrosoftAccount*, *Twitter*, *Facebook*of *windowsazureactivedirectory* als u Google niet als uw ID-provider gebruikt. Als u Facebook gebruikt, moet u [Facebook-domeinen white list][1] in uw app.

    Vervang de **urlScheme** door een unieke naam voor uw toepassing.  De urlScheme moet hetzelfde zijn als het URL-schema protocol dat u hebt opgegeven in het veld **toegestane externe omleidings-url's** in de Azure Portal. De urlScheme wordt gebruikt door de aanroep van de verificatie om terug te gaan naar uw toepassing nadat de verificatie aanvraag is voltooid.

2. Verwijder de regels `self.refreshControl?.beginRefreshing()` en `self.onRefresh(self.refreshControl)` aan het einde van `viewDidLoad()` in *ToDoTableViewController. Swift*. Voeg op hun plaats een aanroep toe aan `loginAndGetData()`:

    ```swift
    loginAndGetData()
    ```

3. Open het bestand `AppDelegate.swift` en voeg de volgende regel toe aan de klasse `AppDelegate`:

    ```swift
    var todoTableViewController: ToDoTableViewController?

    func application(_ application: UIApplication, openURL url: NSURL, options: [UIApplicationOpenURLOptionsKey : Any] = [:]) -> Bool {
        if url.scheme?.lowercased() == "appname" {
            return (todoTableViewController!.table?.client.resume(with: url as URL))!
        }
        else {
            return false
        }
    }
    ```

    Vervang de _AppName_ door de urlScheme-waarde die u in stap 1 hebt gebruikt.

4. Open het bestand `AppName-Info.plist` (waarbij AppName wordt vervangen door de naam van uw app) en voeg de volgende code toe:

    ```xml
    <key>CFBundleURLTypes</key>
    <array>
        <dict>
            <key>CFBundleURLName</key>
            <string>com.microsoft.azure.zumo</string>
            <key>CFBundleURLSchemes</key>
            <array>
                <string>appname</string>
            </array>
        </dict>
    </array>
    ```

    Deze code moet in het element `<dict>` worden geplaatst.  Vervang de teken reeks _AppName_ (binnen de matrix voor **CFBundleURLSchemes**) door de naam van de app die u in stap 1 hebt gekozen.  U kunt deze wijzigingen ook aanbrengen in de plist-editor: Klik op het `AppName-Info.plist`-bestand in XCode om de plist-editor te openen.

    Vervang de teken reeks @no__t 0 voor **CFBundleURLName** met uw Apple bundel-id.

5. Druk op *uitvoeren* om de app te starten en meld u vervolgens aan. Wanneer u bent aangemeld, kunt u de taken lijst weer geven en updates maken.

Bij App Service-verificatie wordt gebruikgemaakt van Apple-communicatie tussen apps.  Raadpleeg de [Apple-documentatie][2] voor meer informatie over dit onderwerp.
<!-- URLs. -->

[1]: https://developers.facebook.com/docs/ios/ios9#whitelist
[2]: https://developer.apple.com/library/content/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/Inter-AppCommunication/Inter-AppCommunication.html
[Azure-portal]: https://portal.azure.com

[Quick start voor iOS]: app-service-mobile-ios-get-started.md

