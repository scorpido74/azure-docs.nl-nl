---
author: alexeystrakh
ms.author: alstrakh
ms.date: 06/11/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: e67f53e26f2081e1aa12f7c0e6a219c240f5027a
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/07/2020
ms.locfileid: "86060415"
---
### <a name="configure-required-ios-packages"></a>Vereiste iOS-pakketten configureren

Het pakket wordt [automatisch gekoppeld](https://github.com/react-native-community/cli/blob/master/docs/autolinking.md) bij het compileren van de app. U hoeft alleen maar de systeemeigen pods te installeren:

```bash
npx pod-install
```

### <a name="configure-infoplist-and-entitlementsplist"></a>Info.plist en Entitlements.plist configureren

1. Ga naar de map 'PushDemo/ios' en open de werkruimte 'PushDemo.xcworkspace', selecteer het bovenste project 'PushDemo' en selecteer het tabblad 'Handtekeningen en mogelijkheden'.

1. Werk de bundel-id bij zodat deze overeenkomt met de waarde die wordt gebruikt in het inrichtingsprofiel.

1. Voeg twee nieuwe mogelijkheden toe met behulp van de knop '+':

    - Mogelijkheid van de achtergrondmodus en externe meldingen over tikken.
    - Mogelijkheid voor pushmeldingen

### <a name="handle-push-notifications-for-ios"></a>Pushmeldingen voor iOS verwerken

1. Open 'AppDelegate.h' en voeg de volgende import toe:

    ```objective-c
    #import <UserNotifications/UNUserNotificationCenter.h>
    ```

1. Werk de lijst met protocollen bij die wordt ondersteund door de 'AppDelegate' door `UNUserNotificationCenterDelegate` toe te voegen:

    ```objective-c
    @interface AppDelegate : UIResponder <UIApplicationDelegate, RCTBridgeDelegate, UNUserNotificationCenterDelegate>
    ```

1. Open 'AppDelegate.m' en configureer alle vereiste iOS-callbacks:

    ```objective-c
    #import <UserNotifications/UserNotifications.h>
    #import <RNCPushNotificationIOS.h>

    ...

    // Required to register for notifications
    - (void)application:(UIApplication *)application didRegisterUserNotificationSettings:(UIUserNotificationSettings *)notificationSettings
    {
     [RNCPushNotificationIOS didRegisterUserNotificationSettings:notificationSettings];
    }

    // Required for the register event.
    - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken
    {
     [RNCPushNotificationIOS didRegisterForRemoteNotificationsWithDeviceToken:deviceToken];
    }

    // Required for the notification event. You must call the completion handler after handling the remote notification.
    - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo
    fetchCompletionHandler:(void (^)(UIBackgroundFetchResult))completionHandler
    {
      [RNCPushNotificationIOS didReceiveRemoteNotification:userInfo fetchCompletionHandler:completionHandler];
    }

    // Required for the registrationError event.
    - (void)application:(UIApplication *)application didFailToRegisterForRemoteNotificationsWithError:(NSError *)error
    {
     [RNCPushNotificationIOS didFailToRegisterForRemoteNotificationsWithError:error];
    }

    // IOS 10+ Required for localNotification event
    - (void)userNotificationCenter:(UNUserNotificationCenter *)center
    didReceiveNotificationResponse:(UNNotificationResponse *)response
             withCompletionHandler:(void (^)(void))completionHandler
    {
      [RNCPushNotificationIOS didReceiveNotificationResponse:response];
      completionHandler();
    }

    // IOS 4-10 Required for the localNotification event.
    - (void)application:(UIApplication *)application didReceiveLocalNotification:(UILocalNotification *)notification
    {
     [RNCPushNotificationIOS didReceiveLocalNotification:notification];
    }

    //Called when a notification is delivered to a foreground app.
    -(void)userNotificationCenter:(UNUserNotificationCenter *)center willPresentNotification:(UNNotification *)notification withCompletionHandler:(void (^)(UNNotificationPresentationOptions options))completionHandler
    {
      completionHandler(UNAuthorizationOptionSound | UNAuthorizationOptionAlert | UNAuthorizationOptionBadge);
    }
    ```
