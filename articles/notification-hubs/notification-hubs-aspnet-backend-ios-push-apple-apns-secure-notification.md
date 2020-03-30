---
title: Azure Notification Hubs Secure Push voor iOS
description: Meer informatie over het verzenden van beveiligde pushmeldingen naar een iOS-app vanuit Azure. Codevoorbeelden geschreven in Objective-C en C#.
documentationcenter: ios
author: sethmanheim
manager: femila
editor: jwargo
services: notification-hubs
ms.assetid: 17d42b0a-2c80-4e35-a1ed-ed510d19f4b4
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 96d1dd514f6fb9c11d7194714337583d6b4387cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75530745"
---
# <a name="azure-notification-hubs-secure-push"></a>Azure Notification Hubs Secure Push

> [!div class="op_single_selector"]
> * [Windows Universal](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
> * [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
> * [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)

## <a name="overview"></a>Overzicht

Push-notificatie-ondersteuning in Microsoft Azure stelt u in staat om toegang te krijgen tot een gebruiksvriendelijke, multi-platform, geschaalde push-infrastructuur, wat de implementatie van pushmeldingen voor zowel consumenten- als bedrijfstoepassingen voor mobiele platforms.

Vanwege wettelijke of beveiligingsbeperkingen wil een toepassing soms iets opnemen in de melding dat niet kan worden verzonden via de standaard push-notificatie-infrastructuur. In deze zelfstudie wordt beschreven hoe u dezelfde ervaring bereiken door gevoelige informatie te verzenden via een beveiligde, geverifieerde verbinding tussen het clientapparaat en de back-end van de app.

Op een hoog niveau is de stroom als volgt:

1. De back-end van de app:
   * Slaat veilige payload op in back-end database.
   * Hiermee wordt de id van deze melding naar het apparaat verzonden (er wordt geen beveiligde informatie verzonden).
2. De app op het apparaat, bij ontvangst van de melding:
   * Het apparaat neemt contact op met de back-end met het verzoek om de veilige lading.
   * De app kan de payload weergeven als een melding op het apparaat.

Het is belangrijk op te merken dat in de voorafgaande stroom (en in deze zelfstudie), we aannemen dat het apparaat een verificatietoken opslaat in lokale opslag, nadat de gebruiker zich heeft aanmeldt. Dit garandeert een naadloze ervaring, omdat het apparaat de beveiligde payload van de melding kan ophalen met behulp van dit token. Als uw toepassing geen verificatietokens op het apparaat opslaat of als deze tokens kunnen worden verlopen, moet de apparaat-app, na ontvangst van de melding, een algemene melding weergeven waarin de gebruiker wordt gevraagd de app te starten. De app verifieert vervolgens de gebruiker en toont de payload van de melding.

Deze secure push-zelfstudie laat zien hoe je een pushmelding veilig verzenden. De zelfstudie bouwt voort op de zelfstudie [Gebruikers melden,](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) dus u moet eerst de stappen in die zelfstudie voltooien.

> [!NOTE]
> In deze zelfstudie wordt ervan uitgegaan dat u uw meldingshub hebt gemaakt en geconfigureerd zoals beschreven in [Aan de slag met meldingenhubs (iOS).](notification-hubs-ios-apple-push-notification-apns-get-started.md)

[!INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-ios-project"></a>Het iOS-project wijzigen

Nu u uw back-end van uw app hebt gewijzigd om alleen de *id* van een melding te verzenden, moet u uw iOS-app wijzigen om die melding te verwerken en uw back-end terugbellen om het beveiligde bericht op te halen dat moet worden weergegeven.

Om dit doel te bereiken, moeten we de logica schrijven om de beveiligde inhoud uit de back-end van de app op te halen.

1. Zorg `AppDelegate.m`er in, zorg ervoor dat de app registreert op stille meldingen, zodat deze de meldings-ID verwerkt die vanaf de backend wordt verzonden. Voeg `UIRemoteNotificationTypeNewsstandContentAvailability` de optie toe in didFinishLaunchingWithOptions:

    ```objc
    [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeNewsstandContentAvailability];
    ```
2. Voeg `AppDelegate.m` bovenaan een implementatiesectie toe met de volgende verklaring:

    ```objc
    @interface AppDelegate ()
    - (void) retrieveSecurePayloadWithId:(int)payloadId completion: (void(^)(NSString*, NSError*)) completion;
    @end
    ```
3. Voeg vervolgens in de implementatiesectie de volgende `{back-end endpoint}` code toe, waarbij de tijdelijke aanduiding wordt vervangen door het eindpunt voor uw eerder verkregen back-end:

    ```objc
    NSString *const GetNotificationEndpoint = @"{back-end endpoint}/api/notifications";

    - (void) retrieveSecurePayloadWithId:(int)payloadId completion: (void(^)(NSString*, NSError*)) completion;
    {
        // check if authenticated
        ANHViewController* rvc = (ANHViewController*) self.window.rootViewController;
        NSString* authenticationHeader = rvc.registerClient.authenticationHeader;
        if (!authenticationHeader) return;

        NSURLSession* session = [NSURLSession
                                    sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration]
                                    delegate:nil
                                    delegateQueue:nil];

        NSURL* requestURL = [NSURL URLWithString:[NSString stringWithFormat:@"%@/%d", GetNotificationEndpoint, payloadId]];
        NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
        [request setHTTPMethod:@"GET"];
        NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@", authenticationHeader];
        [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

        NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request completionHandler:^(NSData *data, NSURLResponse *response, NSError *error) {
            NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
            if (!error && httpResponse.statusCode == 200)
            {
                NSLog(@"Received secure payload: %@", [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding]);

                NSMutableDictionary *json = [NSJSONSerialization JSONObjectWithData:data options: NSJSONReadingMutableContainers error: &error];

                completion([json objectForKey:@"Payload"], nil);
            }
            else
            {
                NSLog(@"Error status: %ld, request: %@", (long)httpResponse.statusCode, error);
                if (error)
                    completion(nil, error);
                else {
                    completion(nil, [NSError errorWithDomain:@"APICall" code:httpResponse.statusCode userInfo:nil]);
                }
            }
        }];
        [dataTask resume];
    }
    ```

    Met deze methode wordt uw app back-end aanroept om de meldingsinhoud op te halen met behulp van de referenties die zijn opgeslagen in de gedeelde voorkeuren.

4. Nu moeten we de binnenkomende melding afhandelen en de bovenstaande methode gebruiken om de inhoud op te halen die wordt weergegeven. Eerst moeten we uw iOS-app op de achtergrond laten draaien wanneer u een pushmelding ontvangt. Selecteer in **XCode**uw app-project in het linkerdeelvenster en klik vervolgens op uw belangrijkste app-doel in het gedeelte **Doelen** in het centrale deelvenster.
5. Klik vervolgens op het tabblad **Mogelijkheden** boven aan het centrale deelvenster en schakel het selectievakje **Externe meldingen** in.

    ![][IOS1]

6. Voeg `AppDelegate.m` bij Het toevoegen van de volgende methode om pushmeldingen te verwerken:

    ```objc
    -(void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult))completionHandler
    {
        NSLog(@"%@", userInfo);

        [self retrieveSecurePayloadWithId:[[userInfo objectForKey:@"secureId"] intValue] completion:^(NSString * payload, NSError *error) {
            if (!error) {
                // show local notification
                UILocalNotification* localNotification = [[UILocalNotification alloc] init];
                localNotification.fireDate = [NSDate dateWithTimeIntervalSinceNow:0];
                localNotification.alertBody = payload;
                localNotification.timeZone = [NSTimeZone defaultTimeZone];
                [[UIApplication sharedApplication] scheduleLocalNotification:localNotification];

                completionHandler(UIBackgroundFetchResultNewData);
            } else {
                completionHandler(UIBackgroundFetchResultFailed);
            }
        }];

    }
    ```

    Houd er rekening mee dat het beter is om de gevallen van ontbrekende verificatieheadereigenschap of afwijzing door de back-end af te handelen. De specifieke afhandeling van deze gevallen hangt meestal af van uw doelgebruikerservaring. Een optie is om een melding weer te geven met een algemene prompt voor de gebruiker om te verifiëren om de werkelijke melding op te halen.

## <a name="run-the-application"></a>De toepassing uitvoeren

Ga als volgt te werk om de toepassing uit te voeren:

1. Voer de app in XCode uit op een fysiek iOS-apparaat (pushmeldingen werken niet in de simulator).
2. Voer in de gebruikersinterface van de iOS-app een gebruikersnaam en wachtwoord in. Dit kan elke string zijn, maar ze moeten dezelfde waarde hebben.
3. Klik in de gebruikersinterface van de iOS-app op **Aanmelden**. Klik vervolgens op **Push verzenden**. U ziet dat de beveiligde melding wordt weergegeven in uw meldingscentrum.

[IOS1]: ./media/notification-hubs-aspnet-backend-ios-secure-push/secure-push-ios-1.png
