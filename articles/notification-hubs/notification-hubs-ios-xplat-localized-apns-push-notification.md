---
title: Gelokaliseerde pushmeldingen naar iOS verzenden met Azure Notification Hubs | Microsoft Documenten
description: Meer informatie over het gebruik van gelokaliseerde meldingen naar iOS-apparaten met Behulp van Azure Notification Hubs.
services: notification-hubs
documentationcenter: ios
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 484914b5-e081-4a05-a84a-798bbd89d428
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: a8614156be5d516d16aff698b604cf0e661d7311
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72385645"
---
# <a name="tutorial-send-localized-push-notifications-to-ios-using-azure-notification-hubs"></a>Zelfstudie: Gelokaliseerde pushmeldingen naar iOS verzenden met Azure Notification Hubs

> [!div class="op_single_selector"]
> * [Windows Store C #](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)
> * [iOS](notification-hubs-ios-xplat-localized-apns-push-notification.md)

In deze zelfstudie ziet u hoe u de [sjablonenfunctie](notification-hubs-templates-cross-platform-push-messages.md) van Azure Notification Hubs gebruiken om nieuwsmeldingen uit te zenden die zijn gelokaliseerd per taal en apparaat. In deze zelfstudie begint u met de iOS-app die is gemaakt in [Meldingenhubs gebruiken om het laatste nieuws te verzenden.] Wanneer u klaar bent, u zich registreren voor categorieën waarin u geïnteresseerd bent, een taal opgeven waarin u de meldingen wilt ontvangen en alleen pushmeldingen ontvangen voor de geselecteerde categorieën in die taal.

Dit scenario bestaat uit twee delen:

* met de iOS-app kunnen clientapparaten een taal opgeven en zich abonneren op verschillende nieuwscategorieën voor het breken van nieuws;
* De back-end zendt de meldingen uit met behulp van de **tag-** en **sjabloonfuncties** van Azure Notification Hubs.

In deze zelfstudie voert u de volgende stappen uit:

> [!div class="checklist"]
> * De gebruikersinterface van de app bijwerken
> * De iOS-app bouwen
> * Gelokaliseerde sjabloonmeldingen verzenden vanuit de .NET-console-app
> * Gelokaliseerde sjabloonmeldingen vanaf het apparaat verzenden

## <a name="overview"></a>Overzicht

In [Notificatiehubs gebruiken om het laatste nieuws te verzenden,]hebt u een app gebouwd waarmee **u zich** abonneert op meldingen voor verschillende nieuwscategorieën. Veel apps richten zich echter op meerdere markten en vereisen lokalisatie. Het betekent dat de inhoud van de meldingen zelf moet worden gelokaliseerd en geleverd aan de juiste set apparaten. In deze zelfstudie ziet u hoe u de **sjabloonfunctie** van Notification Hubs gebruiken om eenvoudig gelokaliseerde nieuwsmeldingen te leveren.

> [!NOTE]
> Een manier om gelokaliseerde meldingen te verzenden is door meerdere versies van elke tag te maken. Bijvoorbeeld, om Engels, Frans en Mandarijn te ondersteunen, zou je drie verschillende tags nodig hebben voor wereldnieuws: "world_en", "world_fr" en "world_ch". U zou dan een gelokaliseerde versie van het wereldnieuws naar elk van deze markeringen moeten verzenden. In dit onderwerp gebruikt u sjablonen om de verspreiding van tags en de vereiste van het verzenden van meerdere berichten te voorkomen.

Sjablonen zijn een manier om op te geven hoe een specifiek apparaat een melding moet ontvangen. De sjabloon bepaalt de exacte indeling van de payload door te verwijzen naar eigenschappen die deel uitmaken van het bericht dat is verzonden door uw back-endapp. In uw geval stuurt u een locale-agnostisch bericht met alle ondersteunde talen:

```json
{
    "News_English": "...",
    "News_French": "...",
    "News_Mandarin": "..."
}
```

Vervolgens zorgt u ervoor dat apparaten zich registreren met een sjabloon die verwijst naar de juiste eigenschap. Een iOS-app die zich bijvoorbeeld wil registreren voor Franse nieuwsregisters met de volgende syntaxis:

```json
{
    aps: {
        alert: "$(News_French)"
    }
}
```

Zie Artikel [Sjablonen](notification-hubs-templates-cross-platform-push-messages.md) voor meer informatie over sjablonen.

## <a name="prerequisites"></a>Vereisten

* Vul de [pushmeldingen in op specifieke iOS-apparaten](notification-hubs-ios-xplat-segmented-apns-push-notification.md) en heb de code beschikbaar, omdat deze zelfstudie rechtstreeks op die code wordt gebaseerd.
* Visual Studio 2019 is optioneel.

## <a name="update-the-app-user-interface"></a>De gebruikersinterface van de app bijwerken

In deze sectie wijzigt u de Breaking News-app die u hebt gemaakt in het onderwerp [Meldingshubs gebruiken om het laatste nieuws] te verzenden om gelokaliseerd nieuws te verzenden met sjablonen.

Voeg `MainStoryboard_iPhone.storyboard`in uw , voeg een gesegmenteerd besturingselement toe met de drie talen: Engels, Frans en Mandarijn.

![Het iOS UI-storyboard maken][13]

Zorg er vervolgens voor dat u een IBOutlet toevoegt aan uw ViewController.h, zoals in de volgende afbeelding wordt weergegeven:

![Stopcontacten maken voor de schakelaars][14]

## <a name="build-the-ios-app"></a>De iOS-app bouwen

1. Voeg `Notification.h`in de `retrieveLocale` volgende code de methode toe en wijzig de methoden voor het opslaan en abonneren zoals in de volgende code:

    ```objc
    - (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet*) categories completion: (void (^)(NSError* error))completion;

    - (void) subscribeWithLocale:(int) locale categories:(NSSet*) categories completion:(void (^)(NSError *))completion;

    - (NSSet*) retrieveCategories;

    - (int) retrieveLocale;
    ```
    Wijzig `Notification.m`in uw `storeCategoriesAndSubscribe` methode de `locale` methode door de parameter toe te voegen en op te slaan in de standaardwaarden van de gebruiker:

    ```objc
    - (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet *)categories completion:(void (^)(NSError *))completion {
        NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
        [defaults setValue:[categories allObjects] forKey:@"BreakingNewsCategories"];
        [defaults setInteger:locale forKey:@"BreakingNewsLocale"];
        [defaults synchronize];

        [self subscribeWithLocale: locale categories:categories completion:completion];
    }
    ```

    Wijzig vervolgens de *inschrijfmethode* om de landinvoeging op te nemen:

    ```objc
    - (void) subscribeWithLocale: (int) locale categories:(NSSet *)categories completion:(void (^)(NSError *))completion{
        SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:@"<connection string>" notificationHubPath:@"<hub name>"];

        NSString* localeString;
        switch (locale) {
            case 0:
                localeString = @"English";
                break;
            case 1:
                localeString = @"French";
                break;
            case 2:
                localeString = @"Mandarin";
                break;
        }

        NSString* template = [NSString stringWithFormat:@"{\"aps\":{\"alert\":\"$(News_%@)\"},\"inAppMessage\":\"$(News_%@)\"}", localeString, localeString];

        [hub registerTemplateWithDeviceToken:self.deviceToken name:@"localizednewsTemplate" jsonBodyTemplate:template expiryTemplate:@"0" tags:categories completion:completion];
    }
    ```

    U gebruikt `registerTemplateWithDeviceToken`de methode, in plaats van `registerNativeWithDeviceToken`. Wanneer u zich registreert voor een sjabloon, moet u de json-sjabloon en ook een naam voor de sjabloon opgeven (omdat de app mogelijk verschillende sjablonen wilt registreren). Zorg ervoor dat u uw categorieën registreert als tags, omdat u ervoor wilt zorgen dat u de meldingen voor dat nieuws ontvangt.

    Voeg een methode toe om de landinstelling op te halen uit de standaardinstellingen van de gebruiker:

    ```objc
    - (int) retrieveLocale {
        NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];

        int locale = [defaults integerForKey:@"BreakingNewsLocale"];

        return locale < 0?0:locale;
    }
    ```

2. Nu dat je `Notifications` de klasse gewijzigd, moet `ViewController` je ervoor `UISegmentControl`zorgen dat de maakt gebruik van de nieuwe . Voeg de volgende `viewDidLoad` regel toe aan de methode om ervoor te zorgen dat de landintlijn die momenteel is geselecteerd, wordt weergegeven:

    ```objc
    self.Locale.selectedSegmentIndex = [notifications retrieveLocale];
    ```

    Wijzig vervolgens `subscribe` in uw methode uw `storeCategoriesAndSubscribe` aanroep naar de volgende code:

    ```objc
    [notifications storeCategoriesAndSubscribeWithLocale: self.Locale.selectedSegmentIndex categories:[NSSet setWithArray:categories] completion: ^(NSError* error) {
        if (!error) {
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
                                    @"Subscribed!" delegate:nil cancelButtonTitle:
                                    @"OK" otherButtonTitles:nil, nil];
            [alert show];
        } else {
            NSLog(@"Error subscribing: %@", error);
        }
    }];
    ```

3. Ten slotte moet u `didRegisterForRemoteNotificationsWithDeviceToken` de methode in uw AppDelegate.m bijwerken, zodat u uw registratie correct vernieuwen wanneer uw app wordt gestart. Wijzig uw aanroep in de `subscribe` methode van meldingen met de volgende code:

    ```obj-c
    NSSet* categories = [self.notifications retrieveCategories];
    int locale = [self.notifications retrieveLocale];
    [self.notifications subscribeWithLocale: locale categories:categories completion:^(NSError* error) {
        if (error != nil) {
            NSLog(@"Error registering for notifications: %@", error);
        }
    }];
    ```

## <a name="optional-send-localized-template-notifications-from-net-console-app"></a>(facultatief) Gelokaliseerde sjabloonmeldingen verzenden vanuit de .NET-console-app

[!INCLUDE [notification-hubs-localized-back-end](../../includes/notification-hubs-localized-back-end.md)]

## <a name="optional-send-localized-template-notifications-from-the-device"></a>(facultatief) Gelokaliseerde sjabloonmeldingen vanaf het apparaat verzenden

Als u geen toegang hebt tot Visual Studio of als u gewoon wilt testen of het verzenden van de gelokaliseerde sjabloonmeldingen rechtstreeks vanuit de app op het apparaat wilt testen. U de gelokaliseerde sjabloonparameters toevoegen aan de `SendNotificationRESTAPI` methode die u in de vorige zelfstudie hebt gedefinieerd.

```objc
- (void)SendNotificationRESTAPI:(NSString*)categoryTag
{
    NSURLSession* session = [NSURLSession sessionWithConfiguration:[NSURLSessionConfiguration
                                defaultSessionConfiguration] delegate:nil delegateQueue:nil];

    NSString *json;

    // Construct the messages REST endpoint
    NSURL* url = [NSURL URLWithString:[NSString stringWithFormat:@"%@%@/messages/%@", HubEndpoint,
                                        HUBNAME, API_VERSION]];

    // Generated the token to be used in the authorization header.
    NSString* authorizationToken = [self generateSasToken:[url absoluteString]];

    //Create the request to add the template notification message to the hub
    NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:url];
    [request setHTTPMethod:@"POST"];

    // Add the category as a tag
    [request setValue:categoryTag forHTTPHeaderField:@"ServiceBusNotification-Tags"];

    // Template notification
    json = [NSString stringWithFormat:@"{\"messageParam\":\"Breaking %@ News : %@\","
            \"News_English\":\"Breaking %@ News in English : %@\","
            \"News_French\":\"Breaking %@ News in French : %@\","
            \"News_Mandarin\":\"Breaking %@ News in Mandarin : %@\","
            categoryTag, self.notificationMessage.text,
            categoryTag, self.notificationMessage.text,  // insert English localized news here
            categoryTag, self.notificationMessage.text,  // insert French localized news here
            categoryTag, self.notificationMessage.text]; // insert Mandarin localized news here

    // Signify template notification format
    [request setValue:@"template" forHTTPHeaderField:@"ServiceBusNotification-Format"];

    // JSON Content-Type
    [request setValue:@"application/json;charset=utf-8" forHTTPHeaderField:@"Content-Type"];

    //Authenticate the notification message POST request with the SaS token
    [request setValue:authorizationToken forHTTPHeaderField:@"Authorization"];

    //Add the notification message body
    [request setHTTPBody:[json dataUsingEncoding:NSUTF8StringEncoding]];

    // Send the REST request
    NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request
                completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
        {
        NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
            if (error || httpResponse.statusCode != 200)
            {
                NSLog(@"\nError status: %d\nError: %@", httpResponse.statusCode, error);
            }
            if (data != NULL)
            {
                //xmlParser = [[NSXMLParser alloc] initWithData:data];
                //[xmlParser setDelegate:self];
                //[xmlParser parse];
            }
        }];

    [dataTask resume];
}
```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u gelokaliseerde meldingen naar iOS-apparaten verzonden. Ga naar de volgende zelfstudie voor meer informatie over het pushen van meldingen naar specifieke gebruikers van iOS-apps:

> [!div class="nextstepaction"]
>[Pushmeldingen verzenden naar specifieke gebruikers](notification-hubs-aspnet-backend-ios-apple-apns-notification.md)

<!-- Images. -->
[13]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized1.png
[14]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized2.png

<!-- URLs. -->
[How To: Service Bus Notification Hubs (iOS Apps)]: https://msdn.microsoft.com/library/jj927168.aspx
[Notification Hubs gebruiken om belangrijk nieuws te verzenden]: notification-hubs-ios-xplat-segmented-apns-push-notification.md
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notify users with Notification Hubs: ASP.NET]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Notify users with Notification Hubs: Mobile Services]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: https://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/#create-new-service
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-ios
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-ios
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-ios
[Push notifications to app users]: /develop/mobile/tutorials/push-notifications-to-users-ios
[Authorize users with scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-ios
[JavaScript and HTML]: ../get-started-with-push-js.md
[Windows Developer Preview registration steps for Mobile Services]: ../mobile-services-windows-developer-preview-registration.md
[wns object]: https://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs Guidance]: https://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for iOS]: https://msdn.microsoft.com/library/jj927168.aspx
