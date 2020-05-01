---
title: Push meldingen verzenden naar iOS-apps met behulp van Azure Notification Hubs | Microsoft Docs
description: In deze zelfstudie leert u hoe u met Azure Notification Hubs pushmeldingen verzendt naar een iOS-toepassing.
services: notification-hubs
documentationcenter: ios
keywords: pushmelding,pushmeldingen,ios-pushmeldingen
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: b7fcd916-8db8-41a6-ae88-fc02d57cb914
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: tutorial
ms.custom: mvc
ms.date: 11/21/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 05/21/2019
ms.openlocfilehash: 032ca8d4ecbcf1fc7f3c22cbe5a0ee934fc5e17c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "74407213"
---
# <a name="tutorial-send-push-notifications-to-ios-apps-using-azure-notification-hubs"></a>Zelf studie: Push meldingen verzenden naar iOS-apps met behulp van Azure Notification Hubs

> [!div class="op_single_selector"]
> * [Objective-C](notification-hubs-ios-apple-push-notification-apns-get-started.md)
> * [Swift](notification-hubs-ios-push-notifications-swift-apps-get-started.md)

In deze zelf studie gebruikt u Azure Notification Hubs om Push meldingen naar een iOS-toepassing te verzenden. U maakt een lege iOS-app die pushmeldingen ontvangt met de [Apple Push Notification Service (APNs)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1).

In deze zelfstudie voert u de volgende stappen uit:

> [!div class="checklist"]
> * Het bestand met de aanvraag voor certificaatondertekening genereren
> * Uw app registreren voor pushmeldingen
> * Een inrichtingsprofiel voor de app maken
> * De Notification Hub configureren voor iOS-pushmeldingen
> * Uw iOS-app verbinden met Notification Hubs
> * Testpushmeldingen verzenden
> * Controleren of uw app meldingen ontvangt

De volledige code voor deze zelf studie vindt u [op github](https://github.com/Azure/azure-notificationhubs-ios/tree/master/Samples).

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie moet aan de volgende vereisten worden voldaan:

* Een actief Azure-account. Als u geen account hebt, kunt u [een gratis Azure-account maken](https://azure.microsoft.com/free).
* [Windows Azure Messaging Framework]
* Meest recente versie van [Xcode]
* Een apparaat dat compatibel is met iOS-versie 10 (of hoger)
* [Apple Developer Program](https://developer.apple.com/programs/)-lidmaatschap
  
  > [!NOTE]
  > Vanwege configuratievereisten voor pushmeldingen moet u pushmeldingen op een fysiek iOS-apparaat (iPhone of iPad) implementeren en testen in plaats van in de iOS-simulator.
  
Het voltooien van deze zelfstudie is een vereiste voor alle andere Notification Hubs-zelfstudies voor iOS-apps.

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="connect-your-ios-app-to-notification-hubs"></a>Uw iOS-app verbinden met Notification Hubs

1. Maak in Xcode een nieuw iOS-project en selecteer de sjabloon **Single View Application** (Toepassing met één weergave).

    ![Xcode - Toepassing voor één weergave][8]

2. Bij het instellen van de opties voor het nieuwe project moet u dezelfde **productnaam** en **organisatie-id** gebruiken als bij het instellen van de bundel-id in de Apple Developer-portal.

3. Selecteer onder project Navigator uw project naam onder **doelen**en selecteer vervolgens het tabblad **handtekeningen & mogelijkheden** . Zorg ervoor dat u het juiste **team** voor uw Apple Developer-account selecteert. XCode moet automatisch het profiel voor inrichting openen dat u eerder op basis van uw bundel-id hebt gemaakt.

    Als u het nieuwe profiel voor inrichting dat u hebt gemaakt in Xcode niet ziet, vernieuwt u de profielen voor uw identiteit voor ondertekening. Klik op **Xcode** in de menubalk, klik op **Preferences** (Voorkeuren), klik op het tabblad **Account** en klik op de knop **View Details** (Details weergeven), klik op uw identiteit voor ondertekening en klik vervolgens op de knop voor vernieuwen in de rechterbenedenhoek.

    ![Xcode - Profiel voor inrichting][9]

4. Selecteer op het tabblad **functies voor ondertekening &** **+ mogelijkheid**.  Dubbel klik op **Push meldingen** om deze in te scha kelen.

    ![Xcode - pushmogelijkheden][12]

5. Voeg de Azure Notification Hubs SDK-modules toe.

   U kunt de Azure Notification Hubs SDK in uw app integreren met behulp van [Cocoapods](https://cocoapods.org) of door de binaire bestanden hand matig toe te voegen aan uw project.

   - Integratie via Cocoapods

     Voeg de volgende afhankelijkheden toe `podfile` aan uw app voor het toevoegen van Azure notification hubs SDK.

     ```ruby
     pod 'AzureNotificationHubs-iOS'
     ```

     Voer `pod install` uit om uw zojuist gedefinieerde pod te installeren en `.xcworkspace`open uw.

     > [!NOTE]
     > Als er een fout wordt weer geven zoals **[!] Kan geen specificatie vinden voor AzureNotificationHubs-iOS** tijdens het uitvoeren `pod install`. Voer de `pod repo update` opdracht uit om het nieuwste verschil op te halen uit de Cocoapods- `pod install`opslag plaats en voer het vervolgens uit.

   - Integratie via Carthage

     Voeg de volgende afhankelijkheden toe `Cartfile` aan uw app voor het toevoegen van Azure notification hubs SDK.

     ```ruby
     github "Azure/azure-notificationhubs-ios"
     ```

     Volgende, update en build-afhankelijkheden:

     ```shell
     $ carthage update
     ```

     Zie de [Carthage github-opslag plaats](https://github.com/Carthage/Carthage)voor meer informatie over het gebruik van Carthage.

   - Integratie door de binaire bestanden te kopiëren naar uw project

     1. Down load het [Azure notification hubs SDK](https://github.com/Azure/azure-notificationhubs-ios/releases) -Framework dat is opgenomen als een zip-bestand en pak het uit.

     2. Klik met de rechtermuisknop op uw project in Xcode en klik op de optie **Add Files to** (Bestanden toevoegen aan) om de map **WindowsAzureMessaging.framework** aan uw Xcode-project toe te voegen. Selecteer **Options** (Opties), zorg ervoor dat **Copy items if needed** (Copy items if needed) is geselecteerd en klik op **Add** (Toevoegen).

        ![Azure SDK uitpakken][10]

6. Voeg een nieuw header bestand toe aan uw project met de naam **constanten. h**. Hiertoe klikt u met de rechter muisknop op de naam van het project en selecteert u **nieuw bestand...**. Selecteer vervolgens **header-bestand**. Dit bestand bevat de constanten voor uw Notification Hub. Selecteer **volgende**. Noem de bestands **constanten. h**.

7. Voeg de volgende code toe aan het bestand constanten. h:

    ```objc
    #ifndef Constants_h
    #define Constants_h

    extern NSString* const NHInfoConnectionString;
    extern NSString* const NHInfoHubName;
    extern NSString* const NHUserDefaultTags;

    #endif /* Constants_h */
    ```

8. Voeg het implementatie bestand voor constanten. h toe. Hiertoe klikt u met de rechter muisknop op de naam van het project en selecteert u **nieuw bestand...**. Selecteer **objectief-C-bestand**en selecteer vervolgens **volgende**. Noem de bestands **constanten. m**.

    ![M-bestand toevoegen](media/notification-hubs-ios-get-started/new-file-objc.png)

9. Open het bestand **constanten. m** en vervang de inhoud door de volgende code. Vervang de tijdelijke aanduidingen voor `NotificationHubConnectionString` teken `NotificationHubConnectionString` reeksen en de naam van de hub en de **DefaultListenSharedAccessSignature**, zoals u eerder hebt verkregen via de portal:

    ```objc
    #import <Foundation/Foundation.h>
    #import "Constants.h"

    NSString* const NHInfoConnectionString = @"NotificationHubConnectionString";
    NSString* const NHInfoHubName = @"NotificationHubName";
    NSString* const NHUserDefaultTags = @"notification_tags";
    ```

10. Open het bestand **AppDelegate. h** van het project en vervang de inhoud door de volgende code:

    ```objc
    #import <UIKit/UIKit.h>
    #import <WindowsAzureMessaging/WindowsAzureMessaging.h>
    #import <UserNotifications/UserNotifications.h> 

    @interface AppDelegate : UIResponder <UIApplicationDelegate,UNUserNotificationCenterDelegate>

    @property (strong, nonatomic) UIWindow *window;

    - (void)handleRegister;
    - (void)handleUnregister;

    @end

    ```

11. Voeg in het bestand **AppDelegate. m** van het project de volgende `import` instructies toe:

    ```objc
    #import "Constants.h"
    #import "NotificationDetailViewController.h"
    ```

12. Voeg in het bestand **AppDelegate. m** ook de volgende regel code toe aan de `didFinishLaunchingWithOptions` methode op basis van uw versie van Ios. Deze code registreert uw apparaatingang met APNs:

    ```objc
    [[UNUserNotificationCenter currentNotificationCenter] setDelegate:self];
    ```

13. Vervang in hetzelfde **AppDelegate. m** -bestand alle code na `didFinishLaunchingWithOptions` met de volgende code:

    ```objc
    // Tells the app that a remote notification arrived that indicates there is data to be fetched.

    - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))completionHandler {
        NSLog(@"Received remote (silent) notification");
        [self logNotificationDetails:userInfo];

        //
        // Let the system know the silent notification has been processed.
        //
        completionHandler(UIBackgroundFetchResultNoData);
    }

    // Tells the delegate that the app successfully registered with Apple Push Notification service (APNs).

    - (void) application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken {
        NSMutableSet *tags = [[NSMutableSet alloc] init];

        // Load and parse stored tags
        NSString *unparsedTags = [[NSUserDefaults standardUserDefaults] valueForKey:NHUserDefaultTags];
        if (unparsedTags.length > 0) {
            NSArray *tagsArray = [unparsedTags componentsSeparatedByString: @","];
            [tags addObjectsFromArray:tagsArray];
        }

        // Register the device with the Notification Hub.
        // If the device has not already been registered, this will create the registration.
        // If the device has already been registered, this will update the existing registration.
        //
        SBNotificationHub* hub = [self getNotificationHub];
        [hub registerNativeWithDeviceToken:deviceToken tags:tags completion:^(NSError* error) {
            if (error != nil) {
                NSLog(@"Error registering for notifications: %@", error);
            } else {
                [self showAlert:@"Registered" withTitle:@"Registration Status"];
            }
        }];
    }

    // UNUserNotificationCenterDelegate methods
    //
    // Asks the delegate how to handle a notification that arrived while the app was running in the  foreground.

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center willPresentNotification:(UNNotification *)notification withCompletionHandler:(void (^)(UNNotificationPresentationOptions options))completionHandler {
        NSLog(@"Received notification while the application is in the foreground");

        // The system calls this delegate method when the app is in the foreground. This allows the app to handle the notification
        // itself (and potentially modify the default system behavior).

        // Handle the notification by displaying custom UI.
        //
        [self showNotification:notification.request.content.userInfo];

        // Use 'options' to specify which default behaviors to enable.
        // - UNAuthorizationOptionBadge: Apply the notification's badge value to the app’s icon.
        // - UNAuthorizationOptionSound: Play the sound associated with the notification.
        // - UNAuthorizationOptionAlert: Display the alert using the content provided by the notification.
        //
        // In this case, do not pass UNAuthorizationOptionAlert because the notification was handled by the app.
        //
        completionHandler(UNAuthorizationOptionBadge | UNAuthorizationOptionSound);
    }

    // Asks the delegate to process the user's response to a delivered notification.
    //

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center didReceiveNotificationResponse:(UNNotificationResponse *)response withCompletionHandler:(void(^)(void))completionHandler {
        NSLog(@"Received notification while the application is in the background");

        // The system calls this delegate method when the user taps or responds to the system notification.

        // Handle the notification response by displaying custom UI
        //
        [self showNotification:response.notification.request.content.userInfo];

        // Let the system know the response has been processed.
        //
        completionHandler();
    }

    // App logic and helpers

    - (SBNotificationHub *)getNotificationHub {
        NSString *hubName = [[NSBundle mainBundle] objectForInfoDictionaryKey:NHInfoHubName];
        NSString *connectionString = [[NSBundle mainBundle] objectForInfoDictionaryKey:NHInfoConnectionString];

        return [[SBNotificationHub alloc] initWithConnectionString:connectionString notificationHubPath:hubName];
    }

    - (void)handleRegister {
        UNUserNotificationCenter *center = [UNUserNotificationCenter currentNotificationCenter];

        UNAuthorizationOptions options =  UNAuthorizationOptionAlert | UNAuthorizationOptionSound | UNAuthorizationOptionBadge;
        [center requestAuthorizationWithOptions:(options) completionHandler:^(BOOL granted, NSError * _Nullable error) {
            if (error != nil) {
                NSLog(@"Error requesting for authorization: %@", error);
            }
        }];
        [[UIApplication sharedApplication] registerForRemoteNotifications];
    }

    - (void)handleUnregister {
        //
        // Unregister the device with the Notification Hub.
        //
        SBNotificationHub *hub = [self getNotificationHub];
        [hub unregisterNativeWithCompletion:^(NSError* error) {
            if (error != nil) {
                NSLog(@"Error unregistering for push: %@", error);
            } else {
                [self showAlert:@"Unregistered" withTitle:@"Registration Status"];
            }
        }];
    }

    - (void)logNotificationDetails:(NSDictionary *)userInfo {
        if (userInfo != nil) {
            UIApplicationState state = [UIApplication sharedApplication].applicationState;
            BOOL background = state != UIApplicationStateActive;
            NSLog(@"Received %@notification: \n%@", background ? @"(background) " : @"", userInfo);
        }
    }

    - (void)showAlert:(NSString *)message withTitle:(NSString *)title {
        if (title == nil) {
            title = @"Alert";
        }
        UIAlertController *alert = [UIAlertController alertControllerWithTitle:title message:message preferredStyle:UIAlertControllerStyleAlert];
        [alert addAction:[UIAlertAction actionWithTitle:@"OK" style:UIAlertActionStyleDefault handler:nil]];
        [[[[UIApplication sharedApplication] keyWindow] rootViewController] presentViewController:alert animated:YES completion:nil];
    }

    - (void)showNotification:(NSDictionary *)userInfo {
        [self logNotificationDetails:userInfo];

        NotificationDetailViewController *notificationDetail = [[NotificationDetailViewController alloc] initWithUserInfo:userInfo];
        [[[[UIApplication sharedApplication] keyWindow] rootViewController] presentViewController:notificationDetail animated:YES completion:nil];
    }

    @end
    ```

    Deze code maakt verbinding met de notification hub met behulp van de verbindings gegevens die u hebt opgegeven in **constanten. h**. Er wordt vervolgens een apparaattoken aan de Notification Hub toegekend, zodat de Notification Hub meldingen kan verzenden.

### <a name="notificationdetailviewcontroller"></a>NotificationDetailViewController

1. Net zoals in de vorige instructies, voegt u nog een veldnamen bestand toe met de naam **NotificationDetailViewController. h**. Vervang de inhoud van het nieuwe header bestand door de volgende code:

    ```objc
    #import <UIKit/UIKit.h>

    NS_ASSUME_NONNULL_BEGIN

    @interface NotificationDetailViewController : UIViewController

    @property (strong, nonatomic) IBOutlet UILabel *titleLabel;
    @property (strong, nonatomic) IBOutlet UILabel *bodyLabel;
    @property (strong, nonatomic) IBOutlet UIButton *dismissButton;

    @property (strong, nonatomic) NSDictionary *userInfo;

    - (id)initWithUserInfo:(NSDictionary *)userInfo;

    @end

    NS_ASSUME_NONNULL_END
    ```

2. Voeg het implementatie bestand **NotificationDetailViewController. m**toe. Vervang de inhoud van het bestand door de volgende code, waarmee de `UIViewController` methoden worden geïmplementeerd:

    ```objc
    #import "NotificationDetailViewController.h"

    @interface NotificationDetailViewController ()

    @end

    @implementation NotificationDetailViewController

    - (id)initWithUserInfo:(NSDictionary *)userInfo {
        self = [super initWithNibName:@"NotificationDetail" bundle:nil];
        if (self) {
            _userInfo = userInfo;
        }
        return self;
    }

    - (void)viewDidLayoutSubviews {
        [self.titleLabel sizeToFit];
        [self.bodyLabel sizeToFit];
    }

    - (void)viewDidLoad {
        [super viewDidLoad];

        NSString *title = nil;
        NSString *body = nil;

        NSDictionary *aps = [_userInfo valueForKey:@"aps"];
        NSObject *alertObject = [aps valueForKey:@"alert"];
        if (alertObject != nil) {
            if ([alertObject isKindOfClass:[NSDictionary class]]) {
                NSDictionary *alertDict = (NSDictionary *)alertObject;
                title = [alertDict valueForKey:@"title"];
                body = [alertObject valueForKey:@"body"];
            } else if ([alertObject isKindOfClass:[NSString class]]) {
                body = (NSString *)alertObject;
            } else {
                NSLog(@"Unable to parse notification content. Unexpected format: %@", alertObject);
            }
        }

        if (title == nil) {
            title = @"<unset>";
        }

        if (body == nil) {
            body = @"<unset>";
        }

        self.titleLabel.text = title;
        self.bodyLabel.text = body;
    }

    - (IBAction)handleDismiss:(id)sender {
        [self dismissViewControllerAnimated:YES completion:nil];
    }

    @end
    ```

### <a name="viewcontroller"></a>View Controller

1. Voeg in het bestand **View Controller. h** van het project de volgende `import` instructies toe:

    ```objc
    #import <WindowsAzureMessaging/WindowsAzureMessaging.h>
    #import <UserNotifications/UserNotifications.h>
    ```

2. Voeg ook in **View Controller. h**de volgende eigenschaps declaraties toe `@interface` na de declaratie:

    ```objc
    @property (strong, nonatomic) IBOutlet UITextField *tagsTextField;
    @property (strong, nonatomic) IBOutlet UIButton *registerButton;
    @property (strong, nonatomic) IBOutlet UIButton *unregisterButton;
    ```

3. Vervang in het implementatie bestand **View Controller. m** van het project de inhoud van het bestand door de volgende code:

    ```objc
    #import "ViewController.h"
    #import "Constants.h"
    #import "AppDelegate.h"

    @interface ViewController ()

    @end

    @implementation ViewController

    // UIViewController methods

    - (void)touchesBegan:(NSSet<UITouch *> *)touches withEvent:(UIEvent *)event {
        // Simple method to dismiss keyboard when user taps outside of the UITextField.
        [self.view endEditing:YES];
    }

    - (void)viewDidLoad {
        [super viewDidLoad];

        // Load raw tags text from storage and initialize the text field
        self.tagsTextField.text = [[NSUserDefaults standardUserDefaults] valueForKey:NHUserDefaultTags];
    }

    - (IBAction)handleRegister:(id)sender {
        // Save raw tags text in storage
        [[NSUserDefaults standardUserDefaults] setValue:self.tagsTextField.text forKey:NHUserDefaultTags];

    // Delegate processing the register action to the app delegate.
    [[[UIApplication sharedApplication] delegate] performSelector:@selector(handleRegister)];
    }

    - (IBAction)handleUnregister:(id)sender {
        [[[UIApplication sharedApplication] delegate] performSelector:@selector(handleUnregister)];
    }

    @end
    ```

4. Ontwikkel en voer de app op uw apparaat uit om te controleren of er geen fouten zijn.

## <a name="send-test-push-notifications"></a>Testpushmeldingen verzenden

U kunt ontvangst van meldingen in uw app testen met de optie *Test verzenden* in [Azure Portal]. Er wordt dan een pushmelding als test naar uw apparaat verzonden.

![Azure Portal - Test verzenden][30]

[!INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]

## <a name="verify-that-your-app-receives-push-notifications"></a>Controleren of uw app pushmeldingen ontvangt

Als u pushmeldingen op iOS wilt testen, moet u de app implementeren op een fysiek iOS-apparaat. U kunt geen Apple pushmeldingen verzenden via de iOS-simulator.

1. Voer de app uit en controleer of de registratie is gelukt en druk vervolgens op **OK**.

    ![De registratie van pushmeldingen in iOS-apps testen][33]

2. Vervolgens verstuurt u als test een pushmelding vanuit [Azure Portal], zoals in de vorige sectie wordt beschreven.

3. De pushmelding wordt verzonden naar alle apparaten die zijn geregistreerd voor het ontvangen van meldingen van de specifieke Notification Hub.

    ![Het ontvangen van pushmeldingen in iOS-apps testen][35]

## <a name="next-steps"></a>Volgende stappen

In dit eenvoudige voorbeeld hebt u pushmeldingen uitgezonden naar al uw geregistreerde iOS-apparaten. Als u wilt weten hoe u pushmeldingen kunt verzenden naar specifieke iOS-apparaten, gaat u verder met de volgende zelfstudie:

> [!div class="nextstepaction"]
>[Pushmeldingen verzenden naar specifieke apparaten](notification-hubs-ios-xplat-segmented-apns-push-notification.md)

<!-- Images. -->
[8]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app.png
[9]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app2.png
[10]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app3.png
[11]: ./media/notification-hubs-ios-get-started/notification-hubs-xcode-product-name.png
[12]: ./media/notification-hubs-ios-get-started/notification-hubs-enable-push.png
[30]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png
[33]: ./media/notification-hubs-ios-get-started/notification-hubs-test1.png
[35]: ./media/notification-hubs-ios-get-started/notification-hubs-test3.png

<!-- URLs. -->
[Windows Azure Messaging Framework]: https://go.microsoft.com/fwlink/?LinkID=799698&clcid=0x409
[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/?LinkID=266533
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: https://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Notification Hubs Guidance]: https://msdn.microsoft.com/library/jj927170.aspx
[Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: https://go.microsoft.com/fwlink/p/?LinkId=272456
[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-ios-get-started-push.md
[Azure Notification Hubs Notify Users for iOS with .NET backend]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-ios-xplat-segmented-apns-push-notification.md
[Local and Push Notification Programming Guide]: https://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1
[Azure Portal]: https://portal.azure.com
