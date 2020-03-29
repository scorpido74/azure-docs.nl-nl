---
title: Routerings- en tagexpressies in Azure Notification Hubs
description: Meer informatie over het routeren en taggen van expressies voor Azure Notification Hubs.
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
ms.lastreviewed: 12/04/2019
ms.openlocfilehash: 2432ac41645e373ea3a87ff7e69ef02a4e30c81d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062317"
---
# <a name="routing-and-tag-expressions"></a>Expressies routeren en taggen

## <a name="overview"></a>Overzicht

Met tagexpressies u specifieke sets apparaten targeten, of meer specifiek op registraties, wanneer u een pushmelding verzendt via Notification Hubs.

## <a name="targeting-specific-registrations"></a>Specifieke registraties targeten

De enige manier om specifieke meldingsregistraties te targeten, is door tags aan hen te koppelen en vervolgens deze tags te targeten. Zoals besproken in [Registratiebeheer](notification-hubs-push-notification-registration-management.md), moet een app een apparaatgreep registreren op een meldingshub om pushmeldingen te ontvangen. Zodra de app een registratie maakt op een meldingshub, kan de backend van de toepassing pushmeldingen naar de app verzenden. De backend van de toepassing kan de registraties kiezen die u op de volgende manier met een specifieke melding wilt targeten:

1. **Broadcast**: alle registraties in de meldingshub ontvangen de melding.
2. **Tag:** alle registraties die de opgegeven tag bevatten, ontvangen de melding.
3. **Tagexpressie:** alle registraties waarvan de set tags overeenkomen met de opgegeven expressie, ontvangen de melding.

## <a name="tags"></a>Tags

Een tag kan elke tekenreeks zijn, tot 120 tekens, met alfanumerieke en`_`de`@`volgende niet-alfanumerieke tekens: '' ''`#`'' ''`.`'' '' ''`:`'' ''`-`'' '' '' '. In het volgende voorbeeld ziet u een toepassing waaruit u pop-upmeldingen over specifieke muziekgroepen ontvangen. In dit scenario is een eenvoudige manier om meldingen te routeren het labelen van registraties met tags die de verschillende banden vertegenwoordigen, zoals in de volgende afbeelding:

![Overzicht van tags](./media/notification-hubs-tags-segment-push-message/notification-hubs-tags.png)

In de figuur, het bericht gelabeld met **Beatles** bereikt alleen de tablet die geregistreerd bij de tag **Beatles**.

Zie [Registratiebeheer](notification-hubs-push-notification-registration-management.md)voor meer informatie over het maken van registraties voor tags.

U meldingen verzenden naar tags met `Microsoft.Azure.NotificationHubs.NotificationHubClient` behulp van de methoden voor meldingen verzenden van de klasse in de [Microsoft Azure Notification Hubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) SDK. U ook Node.js of de API's voor pushmeldingen GEBRUIKEN.  Hier is een voorbeeld met behulp van de SDK.

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

Tags mogen niet vooraf zijn ingericht en kunnen verwijzen naar meerdere app-specifieke concepten. Gebruikers van deze voorbeeldtoepassing kunnen bijvoorbeeld commentaar geven op bands en willen toastontvangen, niet alleen voor de opmerkingen over hun favoriete bands, maar ook voor alle opmerkingen van hun vrienden, ongeacht de band waarop ze reageren. In het volgende cijfer wordt een voorbeeld van dit scenario belicht:

![Tags vrienden](./media/notification-hubs-tags-segment-push-message/notification-hubs-tags2.png)

In dit voorbeeld, Alice is geïnteresseerd in updates voor de Beatles, en Bob is geïnteresseerd in updates voor de Wailers. Bob is ook geïnteresseerd in charlie's opmerkingen, en Charlie is geïnteresseerd in de Wailers. Wanneer een melding wordt verzonden voor Charlie's commentaar op de Beatles, stuurt Notification Hubs deze naar zowel Alice als Bob.

Hoewel u meerdere problemen in tags kunt `band_Beatles` `follows_Charlie`coderen (bijvoorbeeld of ), zijn tags eenvoudige tekenreeksen en geen eigenschappen met waarden. Een registratie komt alleen overeen met de aanwezigheid of afwezigheid van een specifieke tag.

Zie [Breaking News](notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md)voor een volledige stapsgewijze zelfstudie over het gebruik van tags voor het verzenden naar belangengroepen.

> [!NOTE]
> Azure Notification Hubs ondersteunt maximaal 60 tags per registratie.

## <a name="using-tags-to-target-users"></a>Tags gebruiken om gebruikers te targeten

Een andere manier om tags te gebruiken is om alle apparaten die zijn gekoppeld aan een bepaalde gebruiker te identificeren. U een registratie taggen met een tag die de gebruikersnaam bevat, zoals in de volgende afbeelding:

![Gebruikers taggen](./media/notification-hubs-tags-segment-push-message/notification-hubs-tags3.png)

In de afbeelding bereikt `user_Alice` het gelabelde `user_Alice`bericht alle apparaten die zijn getagd met .

## <a name="tag-expressions"></a>Expressies taggen

Er zijn gevallen waarin meldingen moeten worden gericht op een set registraties die niet door één tag zijn geïdentificeerd, maar door een Booleaanse expressie met behulp van tags.

Overweeg een sportapplicatie die iedereen in Boston een herinnering stuurt over een wedstrijd tussen de Red Sox en Cardinals. Als de client-app tags registreert over interesse in teams en locatie, moet de melding worden gericht op iedereen in Boston die geïnteresseerd is in de Red Sox of de Cardinals. Deze voorwaarde kan worden uitgedrukt met de volgende Booleaanse expressie:

```csharp
(follows_RedSox || follows_Cardinals) && location_Boston
```

![Expressies taggen](./media/notification-hubs-tags-segment-push-message/notification-hubs-tags4.png)

Tagexpressies ondersteunen gemeenschappelijke `AND` Booleaanse operatoren zoals`&&`( `OR` ), (`||`) en `NOT` (`!`); ze kunnen ook haakjes bevatten. Tagexpressies `OR` met alleen operatoren kunnen verwijzen naar 20 tags; expressie `AND` met operatoren, maar geen `OR` operatoren kunnen verwijzen naar 10 tags; anders zijn tagexpressies beperkt tot 6 tags.

Hier is een voorbeeld voor het verzenden van meldingen met tagexpressies met de SDK:

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
