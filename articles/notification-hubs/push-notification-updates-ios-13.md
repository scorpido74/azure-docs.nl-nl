---
title: Updates voor Azure Notification Hubs iOS 13 | Microsoft Docs
description: Meer informatie over wijzigingen in de Azure-Notification Hubs voor iOS 13
author: sethmanheim
ms.author: sethm
ms.date: 10/16/2019
ms.topic: article
ms.service: notification-hubs
ms.reviewer: jowargo
ms.lastreviewed: 10/16/2019
ms.openlocfilehash: 697e8ba9c9f27e8d5644e3a78950ff006290efe7
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74228147"
---
# <a name="azure-notification-hubs-updates-for-ios-13"></a>Updates van Azure Notification Hubs voor iOS 13

Er zijn onlangs enkele wijzigingen aangebracht in de open bare push service van Apple. de wijzigingen zijn vooral afgestemd op de releases van iOS 13 en Xcode. In dit artikel wordt de impact van deze wijzigingen op Azure Notification Hubs beschreven.

## <a name="apns-push-payload-changes"></a>Wijzigingen in APNS-push nettoladingen

### <a name="apns-push-type"></a>Type APNS-push

Apple vereist nu dat ontwikkel aars meldingen identificeren als waarschuwings-of achtergrond meldingen via de nieuwe `apns-push-type`-header in de APNS API. Volgens de [documentatie van Apple](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/sending_notification_requests_to_apns): ' de waarde van deze header moet nauw keurig overeenkomen met de inhoud van de nettolading van uw melding. Als er een mismatch is, of als de header ontbreekt op vereiste systemen, kunnen APN's een fout geven, de levering van de melding vertragen of deze helemaal verwijderen."

Ontwikkel aars moeten deze header nu instellen in toepassingen die meldingen verzenden via Azure Notification Hubs. Vanwege een technische beperking moeten klanten verificatie op basis van tokens gebruiken voor APNS-referenties met aanvragen die dit kenmerk bevatten. Als u verificatie op basis van certificaten gebruikt voor uw APNS-referenties, moet u overschakelen op verificatie op basis van tokens.

De volgende voorbeeld code laat zien hoe u dit header-kenmerk instelt in meldings aanvragen die via Azure Notification Hubs worden verzonden.

#### <a name="template-notifications---net-sdk"></a>Sjabloon meldingen-.NET SDK

```csharp
var hub = NotificationHubClient.CreateFromConnectionString(...);
var headers = new Dictionary<string, string> {{"apns-push-type", "alert"}};
var tempprop = new Dictionary<string, string> {{"message", "value"}};
var notification = new TemplateNotification(tempprop);
notification.Headers = headers;
await hub.SendNotificationAsync(notification);
```

#### <a name="native-notifications---net-sdk"></a>Systeem eigen meldingen-.NET SDK

```csharp
var hub = NotificationHubClient.CreateFromConnectionString(...);
var headers = new Dictionary<string, string> {{"apns-push-type", "alert"}};
var notification = new AppleNotification("notification text", headers);
await hub.SendNotificationAsync(notification);
```

#### <a name="direct-rest-calls"></a>Directe REST-aanroepen

```csharp
var request = new HttpRequestMessage(method, $"<resourceUri>?api-version=2017-04");
request.Headers.Add("Authorization", createToken(resourceUri, KEY_NAME, KEY_VALUE));
request.Headers.Add("ServiceBusNotification-Format", "apple");
request.Headers.Add("apns-push-type", "alert");
```

Wanneer Azure Notification Hubs een melding detecteert dat niet de `apns-push-type` is ingesteld, wordt door de service het push-type van de meldings aanvraag afgezet en wordt de waarde automatisch ingesteld. Houd er rekening mee dat u Azure Notification Hubs moet configureren voor het gebruik van verificatie op basis van tokens om de vereiste header in te stellen. Zie [op tokens gebaseerde verificatie (http/2) voor APNS](notification-hubs-push-notification-http2-token-authentification.md)voor meer informatie.

## <a name="apns-priority"></a>Prioriteit van APNS

Een andere kleine wijziging, maar een wijziging in de back-end-toepassing die meldingen verzendt, is de vereiste dat voor achtergrond meldingen de `apns-priority` header nu moet worden ingesteld op 5. Veel toepassingen stellen de `apns-priority`-header in op 10 (hetgeen aangeeft directe levering), of ze niet instellen en de standaard waarde ontvangen (ook 10).

Het instellen van deze waarde op 10 is niet meer toegestaan voor achtergrond meldingen en u moet de waarde voor elke aanvraag instellen. Apple levert geen achtergrond meldingen als deze waarde ontbreekt. Bijvoorbeeld:

```csharp
var hub = NotificationHubClient.CreateFromConnectionString(...);
var headers = new Dictionary<string, string> {{"apns-push-type", "background"}, { "apns-priority", "5" }};
var notification = new AppleNotification("notification text", headers);
await hub.SendNotificationAsync(notification);
```

## <a name="sdk-changes"></a>SDK-wijzigingen

Voor jaren gebruikt iOS-ontwikkel aars het `description` kenmerk van de `deviceToken` gegevens die naar de gemachtigde van het push-token zijn verzonden om het push token op te halen dat een back-end-toepassing gebruikt om meldingen naar het apparaat te verzenden. Met Xcode 11 heeft dat `description` kenmerk gewijzigd in een andere indeling. Bestaande code die ontwikkel aars voor dit kenmerk gebruiken, is nu verbroken. We hebben de Azure Notification Hubs SDK bijgewerkt zodat deze wijziging kan worden doorgevoerd. werk dus de SDK bij die door uw toepassingen wordt gebruikt voor versie 2.0.4 of hoger van de [Azure notification hubs IOS SDK](https://github.com/Azure/azure-notificationhubs-ios).
