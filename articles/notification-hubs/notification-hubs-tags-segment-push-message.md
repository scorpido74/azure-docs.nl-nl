---
title: Routerings-en label expressies in azure Notification Hubs
description: Meer informatie over het omleiden en labelen van expressies voor Azure Notification Hubs.
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 0fffb3bb-8ed8-4e0f-89e8-0de24a47f644
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 12/09/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/23/2019
ms.openlocfilehash: 254517cc1d9cc042387b63147b2a3fd9bdeece5e
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/17/2020
ms.locfileid: "76263775"
---
# <a name="routing-and-tag-expressions"></a>Routerings-en label expressies

## <a name="overview"></a>Overzicht

Met label expressies kunt u specifieke sets van apparaten of meer specifieke registraties richten bij het verzenden van een push melding via Notification Hubs.

## <a name="targeting-specific-registrations"></a>Specifieke registraties richten

De enige manier om specifieke meldings registraties te richten, is door labels te koppelen en vervolgens de labels te richten. Zoals beschreven in [registratie beheer](notification-hubs-push-notification-registration-management.md), om Push meldingen te ontvangen, moet een app een apparaat-handle registreren op een notification hub. Zodra een registratie is gemaakt op een notification hub, kan de back-end van de toepassing push meldingen verzenden naar de app. De back-end van de toepassing kan op de volgende manieren de registraties voor doel met een specifieke melding kiezen:

1. **Broadcast**: alle registraties in de notification hub ontvangen de melding.
2. **Tag**: alle registraties die het opgegeven label bevatten ontvangen de melding.
3. **Tag-expressie**: alle registraties waarvan de set labels overeenkomen met de opgegeven expressie ontvangen de melding.

## <a name="tags"></a>Tags

Een tag kan een wille keurige teken reeks zijn, Maxi maal 120 tekens, bevatten alfanumeriek en de volgende niet-alfanumerieke tekens: ' _ ', ' @ ', ' # ', '. ', ': ', '-'. In het volgende voor beeld ziet u een toepassing waarvan u pop-upmeldingen over specifieke muziek groepen kunt ontvangen. In dit scenario is een eenvoudige manier om meldingen te routeren met label registraties met tags die de verschillende banden vertegenwoordigen, zoals in de volgende afbeelding:

![Overzicht van Tags](./media/notification-hubs-tags-segment-push-message/notification-hubs-tags.png)

In deze afbeelding bereikt het bericht **Beatles** alleen de Tablet die is geregistreerd bij de tag **Beatles**.

Zie [registratie beheer](notification-hubs-push-notification-registration-management.md)voor meer informatie over het maken van registraties voor Tags.

U kunt meldingen naar Tags verzenden met behulp van de methoden voor het verzenden van meldingen van de klasse `Microsoft.Azure.NotificationHubs.NotificationHubClient` in de [Microsoft Azure notification hubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) SDK. U kunt ook node. js of de REST-Api's voor push meldingen gebruiken.  Hier volgt een voor beeld van het gebruik van de SDK.

```csharp
Microsoft.Azure.NotificationHubs.NotificationOutcome outcome = null;

// Windows 8.1 / Windows Phone 8.1
var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
"You requested a Beatles notification</text></binding></visual></toast>";
outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, "Beatles");

// Windows 10
toast = @"<toast><visual><binding template=""ToastGeneric""><text id=""1"">" +
"You requested a Wailers notification</text></binding></visual></toast>";
outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, "Wailers");
```

Tags hoeven niet vooraf te worden ingericht en kunnen verwijzen naar meerdere app-specifieke concepten. Gebruikers van deze voorbeeld toepassing kunnen bijvoorbeeld opmerkingen maken op banden en pop-upmeldingen ontvangen, niet alleen voor de opmerkingen op hun favoriete banden, maar ook voor alle opmerkingen van hun vrienden, ongeacht de band waarop ze opmerkingen maken. In de volgende afbeelding ziet u een voor beeld van dit scenario:

![Tags voor vrienden](./media/notification-hubs-tags-segment-push-message/notification-hubs-tags2.png)

In deze afbeelding is Lisa geïnteresseerd in updates voor het Beatles en is Bob geïnteresseerd in updates voor de Wailers. Bob is ook geïnteresseerd in de opmerkingen van Charlie en Charlie is geïnteresseerd in de Wailers. Wanneer er een melding wordt verzonden voor de opmerking van Charlie op de Beatles, ontvangen zowel Alice als Bob deze.

Hoewel u meerdere problemen in tags kunt coderen (bijvoorbeeld ' band_Beatles ' of ' follows_Charlie '), zijn Tags eenvoudige teken reeksen en geen eigenschappen met waarden. Een registratie komt alleen overeen met de aanwezigheid of het ontbreken van een specifieke tag.

Zie voor een volledige stapsgewijze zelf studie over het gebruik van tags voor het verzenden van belangen groepen, het [afbreken van nieuws](notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md).

> [!NOTE]
> Azure Notification Hubs ondersteunt Maxi maal 60 Tags per registratie.

## <a name="using-tags-to-target-users"></a>Labels gebruiken voor doel gebruikers

Een andere manier om tags te gebruiken is het identificeren van alle apparaten van een bepaalde gebruiker. Registraties kunnen worden gelabeld met een tag die een gebruikers-ID bevat, zoals in de volgende afbeelding:

![Gebruikers labelen](./media/notification-hubs-tags-segment-push-message/notification-hubs-tags3.png)

In deze afbeelding heeft het bericht Tagged UID: Anne alle registraties met de label ' UID: Alice ' bereikt; dus alle apparaten van Alice.

## <a name="tag-expressions"></a>Label expressies

Er zijn gevallen waarin een melding moet worden gericht op een set registraties die niet door één tag worden geïdentificeerd, maar door een booleaanse expressie voor Tags.

Overweeg een sport toepassing die een herinnering stuurt naar iedereen in Boston over een spel tussen de Rode Sox en de Cardinals. Als de client-app Tags registreert over interesse in teams en locatie, moet de melding worden gericht op iedereen in Boston die geïnteresseerd is in de Rode Sox of de Cardinals. Deze voor waarde kan worden weer gegeven met de volgende booleaanse expressie:

```csharp
(follows_RedSox || follows_Cardinals) && location_Boston
```

![Label expressies](./media/notification-hubs-tags-segment-push-message/notification-hubs-tags4.png)

Label expressies kunnen alle Booleaanse Opera tors bevatten, zoals en (& &) of (| |), en niet (!). Ze kunnen ook haakjes bevatten. Label expressies zijn beperkt tot 20 Tags als ze alleen or bevatten. anders zijn ze beperkt tot 6 tags.

Hier volgt een voor beeld van het verzenden van meldingen met label expressies met behulp van de SDK.

```csharp
Microsoft.Azure.NotificationHubs.NotificationOutcome outcome = null;

String userTag = "(location_Boston && !follows_Cardinals)";

// Windows 8.1 / Windows Phone 8.1
var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
"You want info on the Red Sox</text></binding></visual></toast>";
outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);

// Windows 10
toast = @"<toast><visual><binding template=""ToastGeneric""><text id=""1"">" +
"You want info on the Red Sox</text></binding></visual></toast>";
outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);
```
