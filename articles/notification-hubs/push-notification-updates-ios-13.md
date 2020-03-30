---
title: Azure Notification Hubs iOS 13-updates | Microsoft Documenten
description: Meer informatie over iOS 13-wijzigingen in Azure Notification Hubs
author: sethmanheim
ms.author: sethm
ms.date: 10/16/2019
ms.topic: article
ms.service: notification-hubs
ms.reviewer: jowargo
ms.lastreviewed: 10/16/2019
ms.openlocfilehash: 697e8ba9c9f27e8d5644e3a78950ff006290efe7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74228147"
---
# <a name="azure-notification-hubs-updates-for-ios-13"></a>Azure Notification Hubs-updates voor iOS 13

Apple heeft onlangs enkele wijzigingen aangebracht in hun openbare pushservice; de wijzigingen zijn grotendeels afgestemd op de releases van iOS 13 en Xcode. In dit artikel wordt de impact van deze wijzigingen op Azure Notification Hubs beschreven.

## <a name="apns-push-payload-changes"></a>APNS push payload veranderingen

### <a name="apns-push-type"></a>APNS-pushtype

Apple vereist nu dat ontwikkelaars meldingen identificeren als een `apns-push-type` waarschuwing of achtergrondmeldingen via de nieuwe header in de APNS-API. Volgens [de documentatie van Apple:](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/sending_notification_requests_to_apns)"De waarde van deze header moet nauwkeurig de inhoud van de payload van uw melding weergeven. Als er een mismatch is of als de header ontbreekt op vereiste systemen, kunnen APN's een fout retourneren, de levering van de melding vertragen of helemaal laten vallen."

Ontwikkelaars moeten deze header nu instellen in toepassingen die meldingen verzenden via Azure Notification Hubs. Vanwege een technische beperking moeten klanten tokengebaseerde verificatie gebruiken voor APNS-referenties met aanvragen die dit kenmerk bevatten. Als u verificatie op basis van certificaten gebruikt voor uw APNS-referenties, moet u overschakelen naar verificatie op basis van tokens.

In de volgende codevoorbeelden wordt uitgelegd hoe u dit headerkenmerk instelt in meldingsaanvragen die via Azure Notification Hubs worden verzonden.

#### <a name="template-notifications---net-sdk"></a>Sjabloonmeldingen - .NET SDK

```csharp
var hub = NotificationHubClient.CreateFromConnectionString(...);
var headers = new Dictionary<string, string> {{"apns-push-type", "alert"}};
var tempprop = new Dictionary<string, string> {{"message", "value"}};
var notification = new TemplateNotification(tempprop);
notification.Headers = headers;
await hub.SendNotificationAsync(notification);
```

#### <a name="native-notifications---net-sdk"></a>Native meldingen - .NET SDK

```csharp
var hub = NotificationHubClient.CreateFromConnectionString(...);
var headers = new Dictionary<string, string> {{"apns-push-type", "alert"}};
var notification = new AppleNotification("notification text", headers);
await hub.SendNotificationAsync(notification);
```

#### <a name="direct-rest-calls"></a>Directe REST-oproepen

```csharp
var request = new HttpRequestMessage(method, $"<resourceUri>?api-version=2017-04");
request.Headers.Add("Authorization", createToken(resourceUri, KEY_NAME, KEY_VALUE));
request.Headers.Add("ServiceBusNotification-Format", "apple");
request.Headers.Add("apns-push-type", "alert");
```

Wanneer Azure Notification Hubs een melding detecteert die niet over `apns-push-type` de set beschikt, leidt de service het pushtype af van de meldingsaanvraag en stelt de waarde automatisch in. Houd er rekening mee dat u Azure Notification Hubs moet configureren om tokengebaseerde verificatie te gebruiken om de vereiste header in te stellen. Zie [Op tokens gebaseerde (HTTP/2) verificatie voor APNS voor](notification-hubs-push-notification-http2-token-authentification.md)meer informatie .

## <a name="apns-priority"></a>APNS-prioriteit

Een andere kleine wijziging, maar een die een wijziging vereist in de backend-toepassing die meldingen verzendt, is de vereiste dat voor achtergrondmeldingen de `apns-priority` koptekst nu moet worden ingesteld op 5. Veel toepassingen `apns-priority` stellen de koptekst in op 10 (wat duidt op onmiddellijke levering), of stel deze niet in en krijgen de standaardwaarde (die ook 10 is).

Als u deze waarde instelt op 10, is dit niet langer toegestaan voor achtergrondmeldingen en moet u de waarde voor elk verzoek instellen. Apple levert geen achtergrondmeldingen als deze waarde ontbreekt. Bijvoorbeeld:

```csharp
var hub = NotificationHubClient.CreateFromConnectionString(...);
var headers = new Dictionary<string, string> {{"apns-push-type", "background"}, { "apns-priority", "5" }};
var notification = new AppleNotification("notification text", headers);
await hub.SendNotificationAsync(notification);
```

## <a name="sdk-changes"></a>SDK-wijzigingen

Jarenlang gebruikten iOS-ontwikkelaars `description` het `deviceToken` kenmerk van de gegevens die naar de pushtokengemachtigde zijn verzonden om het pushtoken te extraheren dat een backend-toepassing gebruikt om meldingen naar het apparaat te verzenden. Met Xcode 11 `description` is dat kenmerk gewijzigd in een andere indeling. Bestaande code die ontwikkelaars voor dit kenmerk hebben gebruikt, is nu verbroken. We hebben de Azure Notification Hubs SDK bijgewerkt om deze wijziging aan te passen, dus update de SDK die door uw toepassingen wordt gebruikt naar versie 2.0.4 of nieuwer van de [Azure Notification Hubs iOS SDK.](https://github.com/Azure/azure-notificationhubs-ios)
