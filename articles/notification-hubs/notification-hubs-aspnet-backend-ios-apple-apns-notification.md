---
title: Pushmeldingen verzenden naar specifieke gebruikers via Azure Notification Hubs | Microsoft Documenten
description: Leer hoe u pushmeldingen kunt verzenden naar specifieke gebruikers met behulp van Azure Notification Hubs.
documentationcenter: ios
author: sethm
manager: femila
editor: jwargo
services: notification-hubs
ms.assetid: 1f7d1410-ef93-4c4b-813b-f075eed20082
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 48135ea614bbab4ca6649a83895ae5f632918c61
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72387480"
---
# <a name="tutorial-send-push-notifications-to-specific-users-using-azure-notification-hubs"></a>Zelfstudie: Pushmeldingen verzenden naar specifieke gebruikers met Azure Notification Hubs

[!INCLUDE [notification-hubs-selector-aspnet-backend-notify-users](../../includes/notification-hubs-selector-aspnet-backend-notify-users.md)]

In deze zelfstudie wordt uitgelegd hoe u met Azure Notification Hubs pushmeldingen kunt verzenden naar een specifieke app-gebruiker op een specifiek apparaat. Een ASP.NET WebAPI-backend wordt gebruikt om clients te verifiëren en meldingen te genereren, zoals wordt weergegeven in het richtlijnenonderwerp [Registreren vanaf uw backend van de app.](notification-hubs-push-notification-registration-management.md#registration-management-from-a-backend)

In deze zelfstudie voert u de volgende stappen uit:

> [!div class="checklist"]
> * Het WebAPI-project maken
> * Clients verifiëren bij de WebAPI-back-end
> * Registreren voor meldingen met behulp van de WebAPI-back-end
> * Meldingen verzenden vanuit de WebAPI-back-end
> * De nieuwe WebAPI-back-end publiceren
> * Uw iOS-app wijzigen
> * De toepassing testen

## <a name="prerequisites"></a>Vereisten

In deze zelfstudie wordt ervan uitgegaan dat u uw meldingshub hebt gemaakt en geconfigureerd zoals beschreven in [Aan de slag met meldingenhubs (iOS).](notification-hubs-ios-apple-push-notification-apns-get-started.md) Deze zelfstudie is ook de voorwaarde voor de [Secure Push (iOS)](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md) tutorial.
Als u Mobiele apps als backendservice wilt gebruiken, raadpleegt u de [mobiele apps aan de slag met Push](../app-service-mobile/app-service-mobile-ios-get-started-push.md).

[!INCLUDE [notification-hubs-aspnet-backend-notifyusers](../../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## <a name="modify-your-ios-app"></a>Uw iOS-app wijzigen

1. Open de app met één paginaweergave die u hebt gemaakt in de zelfstudie Aan de [slag met meldingenhubs (iOS).](notification-hubs-ios-apple-push-notification-apns-get-started.md)

   > [!NOTE]
   > In deze sectie wordt ervan uitgegaan dat uw project is geconfigureerd met een lege organisatienaam. Als dit niet het zo is, moet u de naam van uw organisatie voorbereiden op alle klassenamen.

2. Voeg `Main.storyboard` in het bestand de onderdelen toe die in de schermafbeelding van de objectbibliotheek worden weergegeven.

    ![Storyboard bewerken in Xcode-interfacebouwer][1]

   * **Gebruikersnaam**: Een UITextField met tijdelijke tekst, *Gebruikersnaam invoeren,* direct onder het label Resultaten verzenden en beperkt tot de linker- en rechtermarges en onder het label Resultaten verzenden.
   * **Wachtwoord**: Een UITextField met tijdelijke tekst, *Wachtwoord invoeren*, direct onder het tekstveld gebruikersnaam en beperkt tot de linker- en rechtermarges en onder het tekstveld gebruikersnaam. Schakel de optie **Beveiligde tekstinvoer in** de kenmerkcontrole in onder *Retoursleutel*.
   * **Inloggen:** een UIButton die direct onder het tekstveld wachtwoord is gelabeld en schakel de optie **Ingeschakeld** in de eigenschappencontrole uit onder *Controle-inhoud*
   * **WNS:** label en switch om het verzenden van de melding Windows Notification Service in te schakelen als deze is ingesteld op de hub. Zie de [zelfstudie Windows Aan het begin.](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)
   * **GCM:** label en switch om het verzenden van de melding naar Google Cloud Messaging mogelijk te maken als deze is ingesteld op de hub. Zie [de zelfstudie Android Getting Started.](notification-hubs-android-push-notification-google-gcm-get-started.md)
   * **APNS**: Label en switch om het verzenden van de melding naar de Apple Platform Notification Service mogelijk te maken.
   * **Gebruikersnaam voor geadresseerden: een** uitextveld met tijdelijke tekst, *gebruikersnaamtag voor geadresseerden*, direct onder het GCM-label en beperkt tot de linker- en rechtermarges en onder het GCM-label.

     Sommige onderdelen zijn toegevoegd in de zelfstudie aan de [slag met Notification Hubs (iOS).](notification-hubs-ios-apple-push-notification-apns-get-started.md)

3. **Ctrl** sleep van de componenten `ViewController.h` in de weergave naar deze nieuwe verkooppunten en voeg deze toe.

    ```objc
    @property (weak, nonatomic) IBOutlet UITextField *UsernameField;
    @property (weak, nonatomic) IBOutlet UITextField *PasswordField;
    @property (weak, nonatomic) IBOutlet UITextField *RecipientField;
    @property (weak, nonatomic) IBOutlet UITextField *NotificationField;

    // Used to enable the buttons on the UI
    @property (weak, nonatomic) IBOutlet UIButton *LogInButton;
    @property (weak, nonatomic) IBOutlet UIButton *SendNotificationButton;

    // Used to enabled sending notifications across platforms
    @property (weak, nonatomic) IBOutlet UISwitch *WNSSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *GCMSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *APNSSwitch;

    - (IBAction)LogInAction:(id)sender;
    ```

4. Voeg `ViewController.h`daarbij het `#define` volgende toe na uw importoverzichten. Vervang `<Enter Your Backend Endpoint>` de tijdelijke aanduiding door de bestemmings-URL die u hebt gebruikt om uw back-end van de app in de vorige sectie te implementeren. Bijvoorbeeld `http://your_backend.azurewebsites.net`.

    ```objc
    #define BACKEND_ENDPOINT @"<Enter Your Backend Endpoint>"
    ```

5. Maak in uw project een nieuwe `RegisterClient` Klasse Cocoa Touch die is vernoemd naar de interface met de ASP.NET back-end die u hebt gemaakt. De klasse maken `NSObject`die overtovert van . Voeg vervolgens de volgende `RegisterClient.h`code toe in de .

    ```objc
    @interface RegisterClient : NSObject

    @property (strong, nonatomic) NSString* authenticationHeader;

    -(void) registerWithDeviceToken:(NSData*)token tags:(NSSet*)tags
        andCompletion:(void(^)(NSError*))completion;

    -(instancetype) initWithEndpoint:(NSString*)Endpoint;

    @end
    ```

6. In `RegisterClient.m`de sectie `@interface` , update de sectie:

    ```objc
    @interface RegisterClient ()

    @property (strong, nonatomic) NSURLSession* session;
    @property (strong, nonatomic) NSURLSession* endpoint;

    -(void) tryToRegisterWithDeviceToken:(NSData*)token tags:(NSSet*)tags retry:(BOOL)retry
                andCompletion:(void(^)(NSError*))completion;
    -(void) retrieveOrRequestRegistrationIdWithDeviceToken:(NSString*)token
                completion:(void(^)(NSString*, NSError*))completion;
    -(void) upsertRegistrationWithRegistrationId:(NSString*)registrationId deviceToken:(NSString*)token
                tags:(NSSet*)tags andCompletion:(void(^)(NSURLResponse*, NSError*))completion;

    @end
    ```

7. Vervang `@implementation` de sectie in registerClient.m door de volgende code:

    ```objc
    @implementation RegisterClient

    // Globals used by RegisterClient
    NSString *const RegistrationIdLocalStorageKey = @"RegistrationId";

    -(instancetype) initWithEndpoint:(NSString*)Endpoint
    {
        self = [super init];
        if (self) {
            NSURLSessionConfiguration* config = [NSURLSessionConfiguration defaultSessionConfiguration];
            _session = [NSURLSession sessionWithConfiguration:config delegate:nil delegateQueue:nil];
            _endpoint = Endpoint;
        }
        return self;
    }

    -(void) registerWithDeviceToken:(NSData*)token tags:(NSSet*)tags
                andCompletion:(void(^)(NSError*))completion
    {
        [self tryToRegisterWithDeviceToken:token tags:tags retry:YES andCompletion:completion];
    }

    -(void) tryToRegisterWithDeviceToken:(NSData*)token tags:(NSSet*)tags retry:(BOOL)retry
                andCompletion:(void(^)(NSError*))completion
    {
        NSSet* tagsSet = tags?tags:[[NSSet alloc] init];

        NSString *deviceTokenString = [[token description]
            stringByTrimmingCharactersInSet:[NSCharacterSet characterSetWithCharactersInString:@"<>"]];
        deviceTokenString = [[deviceTokenString stringByReplacingOccurrencesOfString:@" " withString:@""]
                                uppercaseString];

        [self retrieveOrRequestRegistrationIdWithDeviceToken: deviceTokenString
            completion:^(NSString* registrationId, NSError *error) {
            NSLog(@"regId: %@", registrationId);
            if (error) {
                completion(error);
                return;
            }

            [self upsertRegistrationWithRegistrationId:registrationId deviceToken:deviceTokenString
                tags:tagsSet andCompletion:^(NSURLResponse * response, NSError *error) {
                if (error) {
                    completion(error);
                    return;
                }

                NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*)response;
                if (httpResponse.statusCode == 200) {
                    completion(nil);
                } else if (httpResponse.statusCode == 410 && retry) {
                    [self tryToRegisterWithDeviceToken:token tags:tags retry:NO andCompletion:completion];
                } else {
                    NSLog(@"Registration error with response status: %ld", (long)httpResponse.statusCode);

                    completion([NSError errorWithDomain:@"Registration" code:httpResponse.statusCode
                                userInfo:nil]);
                }

            }];
        }];
    }

    -(void) upsertRegistrationWithRegistrationId:(NSString*)registrationId deviceToken:(NSData*)token
                tags:(NSSet*)tags andCompletion:(void(^)(NSURLResponse*, NSError*))completion
    {
        NSDictionary* deviceRegistration = @{@"Platform" : @"apns", @"Handle": token,
                                                @"Tags": [tags allObjects]};
        NSData* jsonData = [NSJSONSerialization dataWithJSONObject:deviceRegistration
                            options:NSJSONWritingPrettyPrinted error:nil];

        NSLog(@"JSON registration: %@", [[NSString alloc] initWithData:jsonData
                                            encoding:NSUTF8StringEncoding]);

        NSString* endpoint = [NSString stringWithFormat:@"%@/api/register/%@", _endpoint,
                                registrationId];
        NSURL* requestURL = [NSURL URLWithString:endpoint];
        NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
        [request setHTTPMethod:@"PUT"];
        [request setHTTPBody:jsonData];
        NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@",
                                                self.authenticationHeader];
        [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];
        [request setValue:@"application/json" forHTTPHeaderField:@"Content-Type"];

        NSURLSessionDataTask* dataTask = [self.session dataTaskWithRequest:request
            completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
        {
            if (!error)
            {
                completion(response, error);
            }
            else
            {
                NSLog(@"Error request: %@", error);
                completion(nil, error);
            }
        }];
        [dataTask resume];
    }

    -(void) retrieveOrRequestRegistrationIdWithDeviceToken:(NSString*)token
                completion:(void(^)(NSString*, NSError*))completion
    {
        NSString* registrationId = [[NSUserDefaults standardUserDefaults]
                                    objectForKey:RegistrationIdLocalStorageKey];

        if (registrationId)
        {
            completion(registrationId, nil);
            return;
        }

        // request new one & save
        NSURL* requestURL = [NSURL URLWithString:[NSString stringWithFormat:@"%@/api/register?handle=%@",
                                _endpoint, token]];
        NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
        [request setHTTPMethod:@"POST"];
        NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@",
                                                self.authenticationHeader];
        [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

        NSURLSessionDataTask* dataTask = [self.session dataTaskWithRequest:request
            completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
        {
            NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
            if (!error && httpResponse.statusCode == 200)
            {
                NSString* registrationId = [[NSString alloc] initWithData:data
                    encoding:NSUTF8StringEncoding];

                // remove quotes
                registrationId = [registrationId substringWithRange:NSMakeRange(1,
                                    [registrationId length]-2)];

                [[NSUserDefaults standardUserDefaults] setObject:registrationId
                    forKey:RegistrationIdLocalStorageKey];
                [[NSUserDefaults standardUserDefaults] synchronize];

                completion(registrationId, nil);
            }
            else
            {
                NSLog(@"Error status: %ld, request: %@", (long)httpResponse.statusCode, error);
                if (error)
                    completion(nil, error);
                else {
                    completion(nil, [NSError errorWithDomain:@"Registration" code:httpResponse.statusCode
                                userInfo:nil]);
                }
            }
        }];
        [dataTask resume];
    }

    @end
    ```

    Deze code implementeert de logica die is uitgelegd in het begeleidingsartikel [Registreren vanuit uw app-backend](notification-hubs-push-notification-registration-management.md#registration-management-from-a-backend) met NSURLSession om REST-oproepen uit te voeren naar uw app-backend, en NSUserDefaults om de registratieid die door de meldingshub wordt geretourneerd, lokaal op te slaan.

    Deze klasse vereist `authorizationHeader` dat haar eigendom wordt ingesteld om goed te kunnen werken. Deze eigenschap wordt `ViewController` ingesteld door de klasse na de login.

8. In `ViewController.h`, `#import` voeg `RegisterClient.h`een verklaring toe voor . Voeg vervolgens een declaratie toe `RegisterClient` voor het `@interface` apparaattoken en verwijzing naar een instantie in de sectie:

    ```objc
    #import "RegisterClient.h"

    @property (strong, nonatomic) NSData* deviceToken;
    @property (strong, nonatomic) RegisterClient* registerClient;
    ```

9. Voeg in ViewController.m een aangifte `@interface` voor privémethoden toe in de sectie:

    ```objc
    @interface ViewController () <UITextFieldDelegate, NSURLConnectionDataDelegate, NSXMLParserDelegate>

    // create the Authorization header to perform Basic authentication with your app back-end
    -(void) createAndSetAuthenticationHeaderWithUsername:(NSString*)username
                    AndPassword:(NSString*)password;

    @end
    ```

    > [!NOTE]
    > Het volgende fragment is geen beveiligde verificatieregeling, u `createAndSetAuthenticationHeaderWithUsername:AndPassword:` moet de implementatie van het verificatiemechanisme vervangen door uw specifieke verificatiemechanisme dat een verificatietoken genereert dat moet worden verbruikt door de registerclientklasse, bijvoorbeeld OAuth, Active Directory.

10. Voeg vervolgens `@implementation` in `ViewController.m`het gedeelte van , voeg de volgende code toe, die de implementatie toevoegt voor het instellen van de apparaattoken- en verificatiekop.

    ```objc
    -(void) setDeviceToken: (NSData*) deviceToken
    {
        _deviceToken = deviceToken;
        self.LogInButton.enabled = YES;
    }

    -(void) createAndSetAuthenticationHeaderWithUsername:(NSString*)username
                    AndPassword:(NSString*)password;
    {
        NSString* headerValue = [NSString stringWithFormat:@"%@:%@", username, password];

        NSData* encodedData = [[headerValue dataUsingEncoding:NSUTF8StringEncoding] base64EncodedDataWithOptions:NSDataBase64EncodingEndLineWithCarriageReturn];

        self.registerClient.authenticationHeader = [[NSString alloc] initWithData:encodedData
                                                    encoding:NSUTF8StringEncoding];
    }

    -(BOOL)textFieldShouldReturn:(UITextField *)textField
    {
        [textField resignFirstResponder];
        return YES;
    }
    ```

    Let op hoe het instellen van het apparaattoken de inlogknop inschakelt. Het is omdat als onderdeel van de login actie, de view controller registreert voor push-meldingen met de app backend. Daarom wilt u niet dat de aanmeldingsactie toegankelijk is totdat het apparaattoken goed is ingesteld. U de login loskoppelen van de push registratie, zolang de eerste gebeurt voordat de laatste.

11. Gebruik in ViewController.m de volgende fragmenten om de actiemethode voor uw **aanmeldingsknop** te implementeren en een methode om het meldingsbericht te verzenden met de ASP.NET backend.

    ```objc
    - (IBAction)LogInAction:(id)sender {
        // create authentication header and set it in register client
        NSString* username = self.UsernameField.text;
        NSString* password = self.PasswordField.text;

        [self createAndSetAuthenticationHeaderWithUsername:username AndPassword:password];

        __weak ViewController* selfie = self;
        [self.registerClient registerWithDeviceToken:self.deviceToken tags:nil
            andCompletion:^(NSError* error) {
            if (!error) {
                dispatch_async(dispatch_get_main_queue(),
                ^{
                    selfie.SendNotificationButton.enabled = YES;
                    [self MessageBox:@"Success" message:@"Registered successfully!"];
                });
            }
        }];
    }

    - (void)SendNotificationASPNETBackend:(NSString*)pns UsernameTag:(NSString*)usernameTag
                Message:(NSString*)message
    {
        NSURLSession* session = [NSURLSession
            sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration] delegate:nil
            delegateQueue:nil];

        // Pass the pns and username tag as parameters with the REST URL to the ASP.NET backend
        NSURL* requestURL = [NSURL URLWithString:[NSString
            stringWithFormat:@"%@/api/notifications?pns=%@&to_tag=%@", BACKEND_ENDPOINT, pns,
            usernameTag]];

        NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
        [request setHTTPMethod:@"POST"];

        // Get the mock authenticationheader from the register client
        NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@",
            self.registerClient.authenticationHeader];
        [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

        //Add the notification message body
        [request setValue:@"application/json;charset=utf-8" forHTTPHeaderField:@"Content-Type"];
        [request setHTTPBody:[message dataUsingEncoding:NSUTF8StringEncoding]];

        // Execute the send notification REST API on the ASP.NET Backend
        NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request
            completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
        {
            NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
            if (error || httpResponse.statusCode != 200)
            {
                NSString* status = [NSString stringWithFormat:@"Error Status for %@: %d\nError: %@\n",
                                    pns, httpResponse.statusCode, error];
                dispatch_async(dispatch_get_main_queue(),
                ^{
                    // Append text because all 3 PNS calls may also have information to view
                    [self.sendResults setText:[self.sendResults.text stringByAppendingString:status]];
                });
                NSLog(status);
            }

            if (data != NULL)
            {
                xmlParser = [[NSXMLParser alloc] initWithData:data];
                [xmlParser setDelegate:self];
                [xmlParser parse];
            }
        }];
        [dataTask resume];
    }
    ```

12. Werk de actie bij voor de knop **Melding verzenden** om de back-end van de ASP.NET te gebruiken en naar elke PNS te verzenden die door een switch is ingeschakeld.

    ```objc
    - (IBAction)SendNotificationMessage:(id)sender
    {
        //[self SendNotificationRESTAPI];
        [self SendToEnabledPlatforms];
    }

    -(void)SendToEnabledPlatforms
    {
        NSString* json = [NSString stringWithFormat:@"\"%@\"",self.notificationMessage.text];

        [self.sendResults setText:@""];

        if ([self.WNSSwitch isOn])
            [self SendNotificationASPNETBackend:@"wns" UsernameTag:self.RecipientField.text Message:json];

        if ([self.GCMSwitch isOn])
            [self SendNotificationASPNETBackend:@"gcm" UsernameTag:self.RecipientField.text Message:json];

        if ([self.APNSSwitch isOn])
            [self SendNotificationASPNETBackend:@"apns" UsernameTag:self.RecipientField.text Message:json];
    }
    ```

13. Voeg `ViewDidLoad` in de functie het volgende toe `RegisterClient` om de instantie te instantiëren en de gemachtigde voor uw tekstvelden in te stellen.

    ```objc
    self.UsernameField.delegate = self;
    self.PasswordField.delegate = self;
    self.RecipientField.delegate = self;
    self.registerClient = [[RegisterClient alloc] initWithEndpoint:BACKEND_ENDPOINT];
    ```

14. `AppDelegate.m`Verwijder nu alle inhoud van `application:didRegisterForPushNotificationWithDeviceToken:` de methode en vervang deze door het volgende (om ervoor te zorgen dat de weergavecontroller het nieuwste apparaattoken bevat dat is opgehaald uit APN's):

    ```objc
    // Add import to the top of the file
    #import "ViewController.h"

    - (void)application:(UIApplication *)application
                didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken
    {
        ViewController* rvc = (ViewController*) self.window.rootViewController;
        rvc.deviceToken = deviceToken;
    }
    ```

15. Tot `AppDelegate.m`slot in , zorg ervoor dat u de volgende methode:

    ```objc
    - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
        NSLog(@"%@", userInfo);
        [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
    }
    ```

## <a name="test-the-application"></a>De toepassing testen

1. Voer de app in XCode uit op een fysiek iOS-apparaat (pushmeldingen werken niet in de simulator).
2. Voer in de gebruikersinterface van de iOS-app dezelfde waarde in voor zowel gebruikersnaam als wachtwoord. Klik vervolgens op **Aanmelden**.

    ![iOS-testtoepassing][2]

3. U zou een pop-up moeten zien die u van registratiesucces informeert. Klik op **OK**.

    ![iOS-testmelding weergegeven][3]

4. Voer in het tekstveld * gebruikersnaamtag van*geadresseerde* het gebruikersnaamlabel in dat wordt gebruikt bij de registratie vanaf een ander apparaat.
5. Voer een meldingsbericht in en klik op **Melding verzenden**. Alleen de apparaten met een registratie met de gebruikersnaamtag van de ontvanger ontvangen het meldingsbericht. Het wordt alleen verzonden naar die gebruikers.

    ![iOS-test gelabelde melding][4]

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u pushmeldingen kunt verzenden naar specifieke gebruikers door een tag te koppelen aan hun registraties. Als u wilt weten hoe u locatiegebaseerde pushmeldingen kunt verzenden, gaat u verder met de volgende zelfstudie: 

> [!div class="nextstepaction"]
>[Locatiegebaseerde pushmeldingen verzenden](notification-hubs-push-bing-spatial-data-geofencing-notification.md)

[1]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-interface.png
[2]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-enter-user-pwd.png
[3]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-registered.png
[4]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-enter-msg.png
