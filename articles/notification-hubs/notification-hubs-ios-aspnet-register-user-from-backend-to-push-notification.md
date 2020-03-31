---
title: De huidige gebruiker registreren voor pushmeldingen met behulp van web-API | Microsoft Documenten
description: Meer informatie over het aanvragen van pushmeldingsregistratie in een iOS-app met Azure Notification Hubs wanneer de registratie wordt uitgevoerd door ASP.NET Web API.
services: notification-hubs
documentationcenter: ios
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 4e3772cf-20db-4b9f-bb74-886adfaaa65d
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 3fec04a1a45f8b154e27a1e5303e44111f4cb421
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71211872"
---
# <a name="register-the-current-user-for-push-notifications-by-using-aspnet"></a>Registreer de huidige gebruiker voor pushmeldingen met behulp van ASP.NET

> [!div class="op_single_selector"]
> * [iOS](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)

## <a name="overview"></a>Overzicht

In dit onderwerp ziet u hoe u pushmeldingsregistratie aanvragen bij Azure Notification Hubs wanneer de registratie wordt uitgevoerd door ASP.NET Web API. In dit onderwerp wordt de zelfstudie [Uitgebreid Om gebruikers op de hoogte te stellen met Notification Hubs.] U moet de vereiste stappen in die zelfstudie al hebben voltooid om de geverifieerde mobiele service te maken. Zie Gebruikers op de hoogte [stellen met meldingshubs]voor meer informatie over het scenario gebruikers melden.

## <a name="update-your-app"></a>Uw app bijwerken

1. Voeg in uw MainStoryboard_iPhone.storyboard de volgende onderdelen uit de objectbibliotheek toe:

   * **Label:**"Push to User with Notification Hubs"
   * **Label**: "InstallationId"
   * **Label**: "Gebruiker"
   * **Tekstveld:**"Gebruiker"
   * **Label**: "Wachtwoord"
   * **Tekstveld:**"Wachtwoord"
   * **Knop**: "Login"

     Op dit moment ziet je storyboard er als volgt uit:

     ![][0]

2. Maak in de assistent-editor stopcontacten voor alle geschakelde besturingselementen en bel ze, verbind de tekstvelden met de weergavecontroller (gemachtigde) en maak een **actie** voor de **aanmeldingsknop.**

    ![][1]

    Uw bestand BreakingNewsViewController.h moet nu de volgende code bevatten:

    ```objc
    @property (weak, nonatomic) IBOutlet UILabel *installationId;
    @property (weak, nonatomic) IBOutlet UITextField *User;
    @property (weak, nonatomic) IBOutlet UITextField *Password;

    - (IBAction)login:(id)sender;
    ```
3. Maak een `DeviceInfo`klasse met de naam en kopieer de volgende code naar de interfacesectie van het bestand DeviceInfo.h:

    ```objc
    @property (readonly, nonatomic) NSString* installationId;
    @property (nonatomic) NSData* deviceToken;
    ```
4. Kopieer de volgende code in de implementatiesectie van het DeviceInfo.m-bestand:

    ```objc
    @synthesize installationId = _installationId;

    - (id)init {
        if (!(self = [super init]))
            return nil;

        NSUserDefaults *defaults = [NSUserDefaults standardUserDefaults];
        _installationId = [defaults stringForKey:@"PushToUserInstallationId"];
        if(!_installationId) {
            CFUUIDRef newUUID = CFUUIDCreate(kCFAllocatorDefault);
            _installationId = (__bridge_transfer NSString *)CFUUIDCreateString(kCFAllocatorDefault, newUUID);
            CFRelease(newUUID);

            //store the install ID so we don't generate a new one next time
            [defaults setObject:_installationId forKey:@"PushToUserInstallationId"];
            [defaults synchronize];
        }

        return self;
    }

    - (NSString*)getDeviceTokenInHex {
        const unsigned *tokenBytes = [[self deviceToken] bytes];
        NSString *hexToken = [NSString stringWithFormat:@"%08X%08X%08X%08X%08X%08X%08X%08X",
                                ntohl(tokenBytes[0]), ntohl(tokenBytes[1]), ntohl(tokenBytes[2]),
                                ntohl(tokenBytes[3]), ntohl(tokenBytes[4]), ntohl(tokenBytes[5]),
                                ntohl(tokenBytes[6]), ntohl(tokenBytes[7])];
        return hexToken;
    }
    ```

5. Voeg in PushToUserAppDelegate.h de volgende eigenschapsingleton toe:

    ```objc
    @property (strong, nonatomic) DeviceInfo* deviceInfo;
    ```
6. Voeg `didFinishLaunchingWithOptions` in de methode in PushToUserAppDelegate.m de volgende code toe:

    ```objc
    self.deviceInfo = [[DeviceInfo alloc] init];

    [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
    ```

    De eerste regel initialiseert de `DeviceInfo` singleton. De tweede regel start de registratie voor pushmeldingen, die al aanwezig is als u de zelfstudie Aan de [slag met Meldingenhubs] al hebt voltooid.
7. Implementeer in PushToUserAppDelegate.m `didRegisterForRemoteNotificationsWithDeviceToken` de methode in uw AppDelegate en voeg de volgende code toe:

    ```objc
    self.deviceInfo.deviceToken = deviceToken;
    ```

    Hiermee wordt het apparaattoken voor de aanvraag ingesteld.

   > [!NOTE]
   > Op dit punt mag er geen andere code in deze methode. Als u al een `registerNativeWithDeviceToken` aanroep hebt naar de methode die is toegevoegd toen u de zelfstudie Aan de [slag met meldingenhubs](notification-hubs-ios-apple-push-notification-apns-get-started.md) hebt voltooid, moet u dat gesprek opgeven of verwijderen.

8. Voeg `PushToUserAppDelegate.m` in het bestand de volgende handlermethode toe:

    ```objc
    * (void) application:(UIApplication *) application didReceiveRemoteNotification:(NSDictionary *)userInfo {
       NSLog(@"%@", userInfo);
       UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
                             [userInfo objectForKey:@"inAppMessage"] delegate:nil cancelButtonTitle:
                             @"OK" otherButtonTitles:nil, nil];
       [alert show];
    }
    ```

    Met deze methode wordt een waarschuwing weergegeven in de gebruikersinterface wanneer uw app meldingen ontvangt terwijl deze wordt uitgevoerd.

9. Open `PushToUserViewController.m` het bestand en retourneer het toetsenbord in de volgende implementatie:

    ```objc
    - (BOOL)textFieldShouldReturn:(UITextField *)theTextField {
        if (theTextField == self.User || theTextField == self.Password) {
            [theTextField resignFirstResponder];
        }
        return YES;
    }
    ```

10. In `viewDidLoad` de methode `PushToUserViewController.m` in het bestand `installationId` het label als volgt initialiseren:

    ```objc
    DeviceInfo* deviceInfo = [(PushToUserAppDelegate*)[[UIApplication sharedApplication]delegate] deviceInfo];
    Self.installationId.text = deviceInfo.installationId;
    ```

11. Voeg de volgende eigenschappen `PushToUserViewController.m`toe in de interface in :

    ```objc
    @property (readonly) NSOperationQueue* downloadQueue;
    - (NSString*)base64forData:(NSData*)theData;
    ```

12. Voeg vervolgens de volgende implementatie toe:

    ```objc
    - (NSOperationQueue *)downloadQueue {
        if (!_downloadQueue) {
            _downloadQueue = [[NSOperationQueue alloc] init];
            _downloadQueue.name = @"Download Queue";
            _downloadQueue.maxConcurrentOperationCount = 1;
        }
        return _downloadQueue;
    }

    // base64 encoding
    - (NSString*)base64forData:(NSData*)theData
    {
        const uint8_t* input = (const uint8_t*)[theData bytes];
        NSInteger length = [theData length];

        static char table[] = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/=";

        NSMutableData* data = [NSMutableData dataWithLength:((length + 2) / 3) * 4];
        uint8_t* output = (uint8_t*)data.mutableBytes;

        NSInteger i;
        for (i=0; i < length; i += 3) {
            NSInteger value = 0;
            NSInteger j;
            for (j = i; j < (i + 3); j++) {
                value <<= 8;

                if (j < length) {
                    value |= (0xFF & input[j]);
                }
            }

            NSInteger theIndex = (i / 3) * 4;
            output[theIndex + 0] =                    table[(value >> 18) & 0x3F];
            output[theIndex + 1] =                    table[(value >> 12) & 0x3F];
            output[theIndex + 2] = (i + 1) < length ? table[(value >> 6)  & 0x3F] : '=';
            output[theIndex + 3] = (i + 2) < length ? table[(value >> 0)  & 0x3F] : '=';
        }

        return [[NSString alloc] initWithData:data encoding:NSASCIIStringEncoding];
    }
    ```

13. Kopieer de volgende `login` code naar de handlermethode die door XCode is gemaakt:

    ```objc
    DeviceInfo* deviceInfo = [(PushToUserAppDelegate*)[[UIApplication sharedApplication]delegate] deviceInfo];

    // build JSON
    NSString* json = [NSString stringWithFormat:@"{\"platform\":\"ios\", \"instId\":\"%@\", \"deviceToken\":\"%@\"}", deviceInfo.installationId, [deviceInfo getDeviceTokenInHex]];

    // build auth string
    NSString* authString = [NSString stringWithFormat:@"%@:%@", self.User.text, self.Password.text];

    NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:[NSURL URLWithString:@"http://nhnotifyuser.azurewebsites.net/api/register"]];
    [request setHTTPMethod:@"POST"];
    [request setHTTPBody:[json dataUsingEncoding:NSUTF8StringEncoding]];
    [request addValue:[@([json lengthOfBytesUsingEncoding:NSUTF8StringEncoding]) description] forHTTPHeaderField:@"Content-Length"];
    [request addValue:@"application/json" forHTTPHeaderField:@"Content-Type"];
    [request addValue:[NSString stringWithFormat:@"Basic %@",[self base64forData:[authString dataUsingEncoding:NSUTF8StringEncoding]]] forHTTPHeaderField:@"Authorization"];

    // connect with POST
    [NSURLConnection sendAsynchronousRequest:request queue:[self downloadQueue] completionHandler:^(NSURLResponse* response, NSData* data, NSError* error) {
        // add UIAlert depending on response.
        if (error != nil) {
            NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*)response;
            if ([httpResponse statusCode] == 200) {
                UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Back-end registration" message:@"Registration successful" delegate:nil cancelButtonTitle: @"OK" otherButtonTitles:nil, nil];
                [alert show];
            } else {
                NSLog(@"status: %ld", (long)[httpResponse statusCode]);
            }
        } else {
            NSLog(@"error: %@", error);
        }
    }];
    ```

    Deze methode krijgt zowel een installatie-id als een kanaal voor pushmeldingen en stuurt deze, samen met het apparaattype, naar de geverifieerde Web API-methode die een registratie maakt in Notification Hubs. Deze web-API is gedefinieerd in [Gebruikers melden met meldingshubs].

Nu de client-app is bijgewerkt, gaat u terug naar de [Meldingshubs en] werkt u de mobiele service bij om meldingen te verzenden met behulp van Notification Hubs.

<!-- Anchors. -->

<!-- Images. -->
[0]: ./media/notification-hubs-ios-aspnet-register-user-push-notifications/notification-hub-user-aspnet-ios1.png
[1]: ./media/notification-hubs-ios-aspnet-register-user-push-notifications/notification-hub-user-aspnet-ios2.png

<!-- URLs. -->
[Gebruikers op de hoogte stellen met Meldingshubs]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Aan de slag met meldingshubs]: notification-hubs-ios-apple-push-notification-apns-get-started.md
