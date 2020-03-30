---
title: Hoe geplande kennisgevingen te verzenden | Microsoft Documenten
description: In dit onderwerp wordt beschreven met geplande meldingen met Azure Notification Hubs.
services: notification-hubs
documentationcenter: .net
keywords: pushmeldingen, pushmeldingen, pushmeldingen plannen
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71213002"
---
# <a name="how-to-send-scheduled-notifications"></a>How To: Geplande meldingen verzenden

Als u een scenario hebt waarin u op een bepaald moment in de toekomst een melding wilt verzenden, maar geen gemakkelijke manier hebt om uw back-endcode wakker te maken om de melding te verzenden. Standaardniveaumeldingshubs ondersteunen een functie waarmee u meldingen in de toekomst maximaal zeven dagen plannen.


## <a name="schedule-your-notifications"></a>Uw meldingen plannen
Wanneer u een melding [ `ScheduledNotification` ](https://msdn.microsoft.com/library/microsoft.azure.notificationhubs.schedulednotification.aspx) verzendt, gebruikt u gewoon de klasse in de SDK van meldingenhubs, zoals in het volgende voorbeeld wordt weergegeven:

```csharp
Notification notification = new AppleNotification("{\"aps\":{\"alert\":\"Happy birthday!\"}}");
var scheduled = await hub.ScheduleNotificationAsync(notification, new DateTime(2014, 7, 19, 0, 0, 0));
```

## <a name="cancel-scheduled-notifications"></a>Geplande meldingen annuleren
U ook een eerder geplande melding annuleren met de meldingId:

```csharp
await hub.CancelNotificationAsync(scheduled.ScheduledNotificationId);
```

Er zijn geen beperkingen op het aantal geplande meldingen dat u verzenden.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende zelfstudies:

 - [Pushmeldingen naar alle geregistreerde apparaten](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)
 - [Pushmeldingen verzenden naar specifieke apparaten](notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md)
 - [Gelokaliseerde pushmeldingen verzenden](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)
 - [Pushmeldingen verzenden naar specifieke gebruikers](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md) 
 - [Locatiegebaseerde pushmeldingen verzenden](notification-hubs-push-bing-spatial-data-geofencing-notification.md)
