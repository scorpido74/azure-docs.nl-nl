---
title: Geplande meldingen verzenden | Microsoft Docs
description: In dit onderwerp wordt beschreven hoe u geplande meldingen met Azure Notification Hubs gebruikt.
services: notification-hubs
documentationcenter: .net
keywords: Push meldingen, Push meldingen, Push meldingen plannen
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 6b718c75-75dd-4c99-aee3-db1288235c1a
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: dotnet
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: ef5eedaa903480ae670f9bc48d0af89744a99d22
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/24/2019
ms.locfileid: "71213002"
---
# <a name="how-to-send-scheduled-notifications"></a>Procedure: Geplande meldingen verzenden

Als u een scenario hebt waarin u op een bepaald moment in de toekomst een melding wilt verzenden, maar geen eenvoudige manier hebt om uw back-end code te ontwaken om de melding te verzenden. De Standard-laag notification hubs ondersteunen een functie waarmee u in de toekomst meldingen kunt plannen die Maxi maal zeven dagen duren.


## <a name="schedule-your-notifications"></a>Uw meldingen plannen
Bij het verzenden van een melding gebruikt u [ `ScheduledNotification` ](https://msdn.microsoft.com/library/microsoft.azure.notificationhubs.schedulednotification.aspx) de-klasse in de notification hubs SDK, zoals wordt weer gegeven in het volgende voor beeld:

```csharp
Notification notification = new AppleNotification("{\"aps\":{\"alert\":\"Happy birthday!\"}}");
var scheduled = await hub.ScheduleNotificationAsync(notification, new DateTime(2014, 7, 19, 0, 0, 0));
```

## <a name="cancel-scheduled-notifications"></a>Geplande meldingen annuleren
U kunt ook een eerder geplande melding annuleren met behulp van de notificationId:

```csharp
await hub.CancelNotificationAsync(scheduled.ScheduledNotificationId);
```

Er zijn geen beperkingen voor het aantal geplande meldingen dat u kunt verzenden.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende zelfstudies:

 - [Push meldingen naar alle geregistreerde apparaten](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)
 - [Pushmeldingen verzenden naar specifieke apparaten](notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md)
 - [Gelokaliseerde pushmeldingen verzenden](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)
 - [Pushmeldingen verzenden naar specifieke gebruikers](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md) 
 - [Locatiegebaseerde pushmeldingen verzenden](notification-hubs-push-bing-spatial-data-geofencing-notification.md)
