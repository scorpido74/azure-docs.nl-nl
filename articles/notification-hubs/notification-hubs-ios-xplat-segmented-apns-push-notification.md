---
title: Pushmeldingen verzenden naar specifieke iOS-apparaten met Azure Notification Hubs | Microsoft Documenten
description: In deze zelfstudie leert u hoe u Azure Notification Hubs gebruiken om pushmeldingen naar specifieke iOS-apparaten te verzenden.
services: notification-hubs
documentationcenter: ios
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 6ead4169-deff-4947-858c-8c6cf03cc3b2
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 11/07/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 11/07/2019
ms.openlocfilehash: a775963f1b0fa19cd687c839f527f4a078c76864
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80126997"
---
# <a name="tutorial-send-push-notifications-to-specific-ios-devices-using-azure-notification-hubs"></a>Zelfstudie: Pushmeldingen verzenden naar specifieke iOS-apparaten met Azure Notification Hubs

[!INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

## <a name="overview"></a>Overzicht

In deze zelfstudie ziet u hoe u Azure Notification Hubs gebruiken om nieuwsmeldingen uit te zenden naar een iOS-app. Wanneer u klaar bent, u zich registreren voor nieuwscategorieën waarin u geïnteresseerd bent en alleen pushmeldingen ontvangen voor die categorieën. Dit scenario is een algemeen patroon voor veel apps die meldingen moeten verzenden naar groepen gebruikers die eerder hebben aangegeven in bepaalde onderwerpen geïnteresseerd te zijn, zoals een RSS-lezer, apps voor muziekfans, enzovoort.

Broadcast-scenario's zijn mogelijk door een of meer *tags* (of labels) toe te voegen wanneer u een registratie maakt in Notifications Hub. Wanneer meldingen naar een tag worden verzonden, ontvangen apparaten die zich hebben geregistreerd voor de tag de melding. Omdat tags niet meer dan tekenreeksen zijn, hoeven ze niet vooraf te worden opgesteld. Zie [Notification Hubs-routering en tagexpressies](notification-hubs-tags-segment-push-message.md) voor meer informatie over tags.

In deze zelfstudie voert u de volgende stappen uit:

> [!div class="checklist"]
> * Een categorieselectie toevoegen aan de app
> * Getagde meldingen verzenden
> * Meldingen verzenden vanaf het apparaat
> * De app uitvoeren en meldingen genereren

## <a name="prerequisites"></a>Vereisten

Dit onderwerp bouwt voort op de app die u hebt gemaakt in [Zelfstudie: Pushmeldingen naar iOS-apps met Azure Notification Hubs.][get-started] Voordat u met deze zelfstudie begint, moet u [zelfstudie: Pushmeldingen naar iOS-apps al][get-started]hebben voltooid met Azure Notification Hubs.

## <a name="add-category-selection-to-the-app"></a>Categorieselectie toevoegen aan de app

De eerste stap is het toevoegen van de UI-elementen aan uw bestaande storyboard waarmee de gebruiker categorieën kan selecteren om te registreren. De geselecteerde categorieën worden op het apparaat opgeslagen. Wanneer de app wordt gestart, wordt er een apparaatregistratie gemaakt in uw meldingshub, met de geselecteerde categorieën als tags.

1. Voeg in uw **MainStoryboard_iPhone.storyboard** de volgende onderdelen uit de objectbibliotheek toe:

   * Een label met "Breaking News" tekst,
   * Labels met categorieteksten "World", "Politics", "Business", "Technology", "Science", "Sports",
   * Zes switches, één per categorie, stellen elke **switchstatus** standaard **in.**
   * Eén knop met het label 'Abonneren'

     Je storyboard moet er als volgt uitzien:

     ![Xcode-interfacebouwer][3]

2. Maak in de assistent-editor verkooppunten voor alle switches en noem ze "WorldSwitch", "PoliticsSwitch", "BusinessSwitch", "TechnologySwitch", "ScienceSwitch", "SportsSwitch"

3. Maak een actie voor `subscribe`uw knop genaamd ; uw `ViewController.h` moet de volgende code bevatten:

    ```objc
    @property (weak, nonatomic) IBOutlet UISwitch *WorldSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *PoliticsSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *BusinessSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *TechnologySwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *ScienceSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *SportsSwitch;

    - (IBAction)subscribe:(id)sender;
    ```

4. Maak een nieuwe Cocoa `Notifications` **Touch-klasse** genaamd . Kopieer de volgende code in de interfacesectie van het bestand Notifications.h:

    ```objc
    @property NSData* deviceToken;

    - (id)initWithConnectionString:(NSString*)listenConnectionString HubName:(NSString*)hubName;

    - (void)storeCategoriesAndSubscribeWithCategories:(NSArray*)categories
                completion:(void (^)(NSError* error))completion;

    - (NSSet*)retrieveCategories;

    - (void)subscribeWithCategories:(NSSet*)categories completion:(void (^)(NSError *))completion;
    ```

5. Voeg de volgende importrichtlijn toe aan Notifications.m:

    ```objc
    #import <WindowsAzureMessaging/WindowsAzureMessaging.h>
    ```

6. Kopieer de volgende code in het implementatiegedeelte van het bestand Notifications.m.

    ```objc
    SBNotificationHub* hub;

    - (id)initWithConnectionString:(NSString*)listenConnectionString HubName:(NSString*)hubName{

        hub = [[SBNotificationHub alloc] initWithConnectionString:listenConnectionString
                                    notificationHubPath:hubName];

        return self;
    }

    - (void)storeCategoriesAndSubscribeWithCategories:(NSSet *)categories completion:(void (^)(NSError *))completion {
        NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
        [defaults setValue:[categories allObjects] forKey:@"BreakingNewsCategories"];

        [self subscribeWithCategories:categories completion:completion];
    }

    - (NSSet*)retrieveCategories {
        NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];

        NSArray* categories = [defaults stringArrayForKey:@"BreakingNewsCategories"];

        if (!categories) return [[NSSet alloc] init];
        return [[NSSet alloc] initWithArray:categories];
    }

    - (void)subscribeWithCategories:(NSSet *)categories completion:(void (^)(NSError *))completion
    {
        //[hub registerNativeWithDeviceToken:self.deviceToken tags:categories completion: completion];

        NSString* templateBodyAPNS = @"{\"aps\":{\"alert\":\"$(messageParam)\"}}";

        [hub registerTemplateWithDeviceToken:self.deviceToken name:@"simpleAPNSTemplate" 
            jsonBodyTemplate:templateBodyAPNS expiryTemplate:@"0" tags:categories completion:completion];
    }
    ```

    Deze klasse maakt gebruik van lokale opslag om de categorieën nieuws op te slaan en op te halen die dit apparaat ontvangt. Het bevat ook een methode om te registreren voor deze categorieën met behulp van [een sjabloonregistratie.](notification-hubs-templates-cross-platform-push-messages.md)

7. Voeg `AppDelegate.h` in het bestand een `Notifications.h` importinstructie toe voor en `Notifications` voeg een eigenschap toe voor een instantie van de klasse:

    ```objc
    #import "Notifications.h"

    @property (nonatomic) Notifications* notifications;
    ```

8. Voeg `didFinishLaunchingWithOptions` in `AppDelegate.m`de methode in de code de code toe om de instantie meldingen aan het begin van de methode te initialiseren.  
    `HUBNAME`en `HUBLISTENACCESS` (gedefinieerd `hubinfo.h`in ) moeten `<hub name>` `<connection string with listen access>` de tijdelijke aanduidingen en tijdelijke aanduidingen al vervangen door uw naam van de meldingshub en de verbindingstekenreeks voor *DefaultListenSharedAccessSignature* die u eerder hebt verkregen

    ```objc
    self.notifications = [[Notifications alloc] initWithConnectionString:HUBLISTENACCESS HubName:HUBNAME];
    ```

    > [!NOTE]
    > Omdat referenties die worden gedistribueerd met een client-app meestal niet beveiligd zijn, moet u met uw client-app alleen de sleutel voor listen-toegang distribueren. Uw app kan dan worden geregistreerd voor meldingen, maar bestaande registraties kunnen niet worden gewijzigd, en er kunnen geen meldingen worden verzonden. De sleutel voor volledige toegang wordt gebruikt in een beveiligde back-endservice voor het verzenden van meldingen en het wijzigen van bestaande registraties.

9. Vervang `didRegisterForRemoteNotificationsWithDeviceToken` in `AppDelegate.m`de methode in de code de volgende code om `notifications` het apparaattoken door te geven aan de klasse. De `notifications` klasse voert de registratie uit voor meldingen met de categorieën. Als de gebruiker categorieselecties `subscribeWithCategories` wijzigt, roept u de methode aan als reactie op de knop **Abonneren** om deze bij te werken.

    > [!NOTE]
    > Omdat het apparaattoken dat is toegewezen door de Apple Push Notification Service (APNS) op elk gewenst moment kan worden gewijzigd, moet u zich regelmatig registreren voor meldingen om meldingsfouten te voorkomen. In dit voorbeeld wordt er elke keer dat de app wordt gestart een registratie voor meldingen vastgelegd. Voor apps die u regelmatig uitvoert (meer dan één keer per dag), kunt u de registratie waarschijnlijk overslaan om bandbreedte te besparen als er minder dan een dag is verstreken sinds de vorige registratie.

    ```objc
    self.notifications.deviceToken = deviceToken;

    // Retrieves the categories from local storage and requests a registration for these categories
    // each time the app starts and performs a registration.

    NSSet* categories = [self.notifications retrieveCategories];
    [self.notifications subscribeWithCategories:categories completion:^(NSError* error) {
        if (error != nil) {
            NSLog(@"Error registering for notifications: %@", error);
        }
    }];
    ```

    Op dit punt mag er geen `didRegisterForRemoteNotificationsWithDeviceToken` andere code in de methode.

10. De volgende methoden moeten al `AppDelegate.m` aanwezig zijn bij het voltooien van de zelfstudie Aan de [slag met Notification Hubs.][get-started] Zo niet, voeg ze dan toe.

    ```objc
    - (void)MessageBox:(NSString *)title message:(NSString *)messageText
    {

        UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title message:messageText delegate:self
            cancelButtonTitle:@"OK" otherButtonTitles: nil];
        [alert show];
    }

    - (void)application:(UIApplication *)application didReceiveRemoteNotification:
       (NSDictionary *)userInfo {
       NSLog(@"%@", userInfo);
       [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
     }
    ```

    Met deze methode worden meldingen verwerkt die worden ontvangen wanneer de app wordt uitgevoerd door een eenvoudige **UIAlert**weer te geven.

11. Voeg `ViewController.m`in `import` een `AppDelegate.h` instructie toe voor en kopieer de `subscribe` volgende code naar de door XCode gegenereerde methode. Deze code werkt de meldingsregistratie bij om de nieuwe categorietags te gebruiken die de gebruiker in de gebruikersinterface heeft gekozen.

    ```objc
    #import "Notifications.h"

    NSMutableArray* categories = [[NSMutableArray alloc] init];

    if (self.WorldSwitch.isOn) [categories addObject:@"World"];
    if (self.PoliticsSwitch.isOn) [categories addObject:@"Politics"];
    if (self.BusinessSwitch.isOn) [categories addObject:@"Business"];
    if (self.TechnologySwitch.isOn) [categories addObject:@"Technology"];
    if (self.ScienceSwitch.isOn) [categories addObject:@"Science"];
    if (self.SportsSwitch.isOn) [categories addObject:@"Sports"];

    Notifications* notifications = [(AppDelegate*)[[UIApplication sharedApplication]delegate] notifications];

    [notifications storeCategoriesAndSubscribeWithCategories:categories completion: ^(NSError* error) {
        if (!error) {
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:"Notification" message:"Subscribed" delegate:self
            cancelButtonTitle:@"OK" otherButtonTitles: nil];
            [alert show];
        } else {
            NSLog(@"Error subscribing: %@", error);
        }
    }];
    ```

    Met deze `NSMutableArray` methode wordt een `Notifications` van de categorieën gemaakt en wordt de klasse gebruikt om de lijst op te slaan in de lokale opslag en registreert u de bijbehorende tags met uw meldingshub. Wanneer categorieën worden gewijzigd, wordt de registratie opnieuw gemaakt met de nieuwe categorieën.

12. Voeg `ViewController.m`in de volgende `viewDidLoad` code in de methode de gebruikersinterface in op basis van de eerder opgeslagen categorieën.

    ```objc
    // This updates the UI on startup based on the status of previously saved categories.

    Notifications* notifications = [(AppDelegate*)[[UIApplication sharedApplication]delegate] notifications];

    NSSet* categories = [notifications retrieveCategories];

    if ([categories containsObject:@"World"]) self.WorldSwitch.on = true;
    if ([categories containsObject:@"Politics"]) self.PoliticsSwitch.on = true;
    if ([categories containsObject:@"Business"]) self.BusinessSwitch.on = true;
    if ([categories containsObject:@"Technology"]) self.TechnologySwitch.on = true;
    if ([categories containsObject:@"Science"]) self.ScienceSwitch.on = true;
    if ([categories containsObject:@"Sports"]) self.SportsSwitch.on = true;
    ```

De app kan nu een set categorieën opslaan in de lokale opslag van het apparaat die wordt gebruikt om te registreren bij de meldingshub wanneer de app wordt gestart. De gebruiker kan de selectie van categorieën `subscribe` tijdens runtime wijzigen en op de methode klikken om de registratie voor het apparaat bij te werken. Vervolgens werk je de app bij om de laatste nieuwsmeldingen rechtstreeks in de app zelf te verzenden.

## <a name="optional-send-tagged-notifications"></a>(facultatief) Gecodeerde meldingen verzenden

Als u geen toegang hebt tot Visual Studio, u naar de volgende sectie gaan en meldingen van de app zelf verzenden. U ook de juiste sjabloonmelding vanuit de [Azure-portal] verzenden via het tabblad foutopsporing voor uw meldingshub.

[!INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

## <a name="optional-send-notifications-from-the-device"></a>(facultatief) Meldingen verzenden vanaf het apparaat

Normaal gesproken worden meldingen verzonden door een backend-service, maar u meldingen rechtstreeks vanuit de app verzenden. Om dit te doen, werkt u de `SendNotificationRESTAPI` methode bij die u hebt gedefinieerd in de zelfstudie Aan de slag met Notification [Hubs.][get-started]

1. Werk `ViewController.m`in `SendNotificationRESTAPI` , werk de methode als volgt, zodat het accepteert een parameter voor de categorie tag en stuurt de juiste [sjabloon](notification-hubs-templates-cross-platform-push-messages.md) kennisgeving.

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
        json = [NSString stringWithFormat:@"{\"messageParam\":\"Breaking %@ News : %@\"}",
                categoryTag, self.notificationMessage.text];

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

2. Werk `ViewController.m`in `Send Notification` , werk de actie zoals weergegeven in de code die volgt. Zodat het stuurt de meldingen met behulp van elke tag afzonderlijk en stuurt naar meerdere platforms.

    ```objc
    - (IBAction)SendNotificationMessage:(id)sender
    {
        self.sendResults.text = @"";

        NSArray* categories = [NSArray arrayWithObjects: @"World", @"Politics", @"Business",
                                @"Technology", @"Science", @"Sports", nil];

        // Lets send the message as breaking news for each category to WNS, FCM, and APNS
        // using a template.
        for(NSString* category in categories)
        {
            [self SendNotificationRESTAPI:category];
        }
    }
    ```

3. Bouw uw project opnieuw samen en zorg ervoor dat u geen bouwfouten hebt.

## <a name="run-the-app-and-generate-notifications"></a>De app uitvoeren en meldingen genereren

1. Druk op de knop Uitvoeren om het project te bouwen en de app te starten. Selecteer een aantal nieuwsopties waarop u zich wilt abonneren en druk op de knop **Abonneren.** U ziet een dialoogvenster waarin wordt aangegeven dat de meldingen zijn geabonneerd.

    ![Voorbeeldmelding op iOS][1]

    Wanneer u **Abonneren**kiest, zet de app de geselecteerde categorieën om in tags en vraagt een nieuwe apparaatregistratie voor de geselecteerde tags uit de meldingshub.

2. Voer een bericht in dat als laatste nieuws moet worden verzonden en druk op de knop **Melding verzenden.** U ook de .NET-console-app uitvoeren om meldingen te genereren.

    ![Meldingsvoorkeuren wijzigen in iOS][2]

3. Elk apparaat dat is geabonneerd op het laatste nieuws ontvangt de laatste nieuwsmeldingen die u zojuist hebt verzonden.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u broadcastmeldingen verzonden naar specifieke iOS-apparaten die zich hebben geregistreerd voor de categorieën. Ga naar de volgende zelfstudie voor meer informatie over het pushen van gelokaliseerde meldingen:

> [!div class="nextstepaction"]
>[Gelokaliseerde pushmeldingen verzenden](notification-hubs-ios-xplat-localized-apns-push-notification.md)

<!-- Images. -->
[1]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-subscribed.png
[2]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios1.png
[3]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios2.png

<!-- URLs. -->
[How To: Service Bus Notification Hubs (iOS Apps)]: https://msdn.microsoft.com/library/jj927168.aspx
[Use Notification Hubs to broadcast localized breaking news]: notification-hubs-ios-xplat-localized-apns-push-notification.md
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notify users with Notification Hubs]: notification-hubs-aspnet-backend-ios-notify-users.md
[Notification Hubs Guidance]: https://msdn.microsoft.com/library/dn530749.aspx
[Notification Hubs How-To for iOS]: https://msdn.microsoft.com/library/jj927168.aspx
[get-started]: notification-hubs-ios-apple-push-notification-apns-get-started.md
[Azure-portal]: https://portal.azure.com
