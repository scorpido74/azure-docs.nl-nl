---
title: Verificatie toevoegen op iOS
description: Meer informatie over het gebruik van Azure Mobile Apps om gebruikers van uw iOS-app te verifiëren via identiteitsproviders zoals AAD, Google, Facebook, Twitter en Microsoft.
ms.assetid: ef3d3cbe-e7ca-45f9-987f-80c44209dc06
ms.tgt_pltfrm: mobile-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: fd7860053e8c04ca9d5e355a721afd834835a441
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77459020"
---
# <a name="add-authentication-to-your-ios-app"></a>Verificatie toevoegen aan uw iOS-app
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

In deze zelfstudie voegt u verificatie toe aan het [project iOS quick start] met behulp van een ondersteunde identiteitsprovider. Deze zelfstudie is gebaseerd op de [iOS quick start] tutorial, die je eerst moet voltooien.

## <a name="register-your-app-for-authentication-and-configure-the-app-service"></a><a name="register"></a>Uw app registreren voor verificatie en de App-service configureren
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="add-your-app-to-the-allowed-external-redirect-urls"></a><a name="redirecturl"></a>Uw app toevoegen aan de toegestane URL's voor omleiding

Veilige verificatie vereist dat u een nieuw URL-schema voor uw app definieert.  Hierdoor kan het verificatiesysteem worden omgeleid naar uw app zodra het verificatieproces is voltooid.  In deze zelfstudie gebruiken we de _URL-appname_ overal.  U echter elk URL-schema gebruiken dat u kiest.  Het moet uniek zijn voor uw mobiele applicatie.  Ga als u de omleiding aan de kant van de server inschakelen:

1. Selecteer uw App-service in de [Azure-portal.]

2. Klik op de optie **Verificatie / Autorisatie.**

3. Klik op **Azure Active Directory** onder de sectie **Verificatieproviders.**

4. Stel de **beheermodus** in op **Geavanceerd**.

5. Voer in de toegestane externe `appname://easyauth.callback` **omleidings-URL's**.  De _appnaam_ in deze tekenreeks is het URL-schema voor uw mobiele toepassing.  Het moet de normale URL-specificatie voor een protocol volgen (gebruik alleen letters en cijfers en begin met een letter).  U moet een notitie maken van de tekenreeks die u kiest, omdat u uw mobiele toepassingscode op verschillende plaatsen moet aanpassen met het URL-schema.

6. Klik op **OK**.

7. Klik op **Opslaan**.

## <a name="restrict-permissions-to-authenticated-users"></a><a name="permissions"></a>Machtigingen beperken tot geverifieerde gebruikers
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

Druk in Xcode op **Uitvoeren** om de app te starten. Er wordt een uitzondering gemaakt omdat de app probeert toegang te krijgen tot de backend als niet-geverifieerde gebruiker, maar de *TodoItem-tabel* vereist nu verificatie.

## <a name="add-authentication-to-app"></a><a name="add-authentication"></a>Verificatie toevoegen aan de app
**Doelstelling-C**:

1. Open *QSTodoListViewController.m* op je Mac in Xcode en voeg de volgende methode toe:

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

    Wijzig *google* in *microsoftaccount,* *twitter,* *facebook*of *windowsazureactivedirectory* als u Google niet gebruikt als uw identiteitsprovider. Als je Facebook gebruikt, moet je [Facebook-domeinen][1] in je app op de witte lijst zetten.

    Vervang de **urlScheme** door een unieke naam voor uw toepassing.  Het url-schema moet hetzelfde zijn als het URL-schemaprotocol dat u hebt opgegeven in het veld **Toegestane URL's voor extern omleiding in** de Azure-portal. De urlRegeling wordt gebruikt door de verificatie callback om terug te schakelen naar uw toepassing nadat het verificatieverzoek is voltooid.

2. Vervang `[self refresh]` `viewDidLoad` in *QSTodoListViewController.m* de volgende code:

    ```Objective-C
    [self loginAndGetData];
    ```

3. Open bestand `QSAppDelegate.h` en voeg de volgende code toe:

    ```Objective-C
    #import "QSTodoService.h"

    @property (strong, nonatomic) QSTodoService *qsTodoService;
    ```

4. Open bestand `QSAppDelegate.m` en voeg de volgende code toe:

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

   Voeg deze code direct `#pragma mark - Core Data stack`voor het lezen van de regel toe.  Vervang de _appnaam_ door de urlScheme-waarde die u in stap 1 hebt gebruikt.

5. Open `AppName-Info.plist` het bestand (AppName vervangen door de naam van uw app) en voeg de volgende code toe:

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

    Deze code moet in `<dict>` het element worden geplaatst.  Vervang de _tekenreeks appname_ (binnen de array voor **CFBundleURLSchemes)** door de app-naam die u in stap 1 hebt gekozen.  U deze wijzigingen ook aanbrengen in de `AppName-Info.plist` lijsteditor - klik op het bestand in XCode om de lijsteditor te openen.

    Vervang `com.microsoft.azure.zumo` de tekenreeks voor **CFBundleURLName** door uw Apple-bundel-id.

6. Druk op *Uitvoeren* om de app te starten en log vervolgens in. Wanneer u bent ingelogd, u de Todo-lijst bekijken en updates uitvoeren.

**Swift**:

1. Open Op uw Mac *ToDoTableViewController.swift* in Xcode en voeg de volgende methode toe:

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

    Wijzig *google* in *microsoftaccount,* *twitter,* *facebook*of *windowsazureactivedirectory* als u Google niet gebruikt als uw identiteitsprovider. Als je Facebook gebruikt, moet je [Facebook-domeinen][1] in je app op de witte lijst zetten.

    Vervang de **urlScheme** door een unieke naam voor uw toepassing.  Het url-schema moet hetzelfde zijn als het URL-schemaprotocol dat u hebt opgegeven in het veld **Toegestane URL's voor extern omleiding in** de Azure-portal. De urlRegeling wordt gebruikt door de verificatie callback om terug te schakelen naar uw toepassing nadat het verificatieverzoek is voltooid.

2. Verwijder de `self.refreshControl?.beginRefreshing()` `self.onRefresh(self.refreshControl)` lijnen en `viewDidLoad()` aan het einde van in *ToDoTableViewController.swift*. Voeg een `loginAndGetData()` oproep toe aan op hun plaats:

    ```swift
    loginAndGetData()
    ```

3. Open `AppDelegate.swift` het bestand en voeg `AppDelegate` de volgende regel toe aan de klasse:

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

    Vervang de _appnaam_ door de urlScheme-waarde die u in stap 1 hebt gebruikt.

4. Open `AppName-Info.plist` het bestand (AppName vervangen door de naam van uw app) en voeg de volgende code toe:

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

    Deze code moet in `<dict>` het element worden geplaatst.  Vervang de _tekenreeks appname_ (binnen de array voor **CFBundleURLSchemes)** door de app-naam die u in stap 1 hebt gekozen.  U deze wijzigingen ook aanbrengen in de `AppName-Info.plist` lijsteditor - klik op het bestand in XCode om de lijsteditor te openen.

    Vervang `com.microsoft.azure.zumo` de tekenreeks voor **CFBundleURLName** door uw Apple-bundel-id.

5. Druk op *Uitvoeren* om de app te starten en log vervolgens in. Wanneer u bent ingelogd, u de Todo-lijst bekijken en updates uitvoeren.

App Service Authentication maakt gebruik van Apples Inter-App Communication.  Zie voor meer informatie over dit onderwerp de [Apple Documentation][2]
<!-- URLs. -->

[1]: https://developers.facebook.com/docs/ios/ios9#whitelist
[2]: https://developer.apple.com/library/content/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/Inter-AppCommunication/Inter-AppCommunication.html
[Azure-portal]: https://portal.azure.com

[iOS snel aan de slag]: app-service-mobile-ios-get-started.md

