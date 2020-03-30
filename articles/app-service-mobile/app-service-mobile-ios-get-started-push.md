---
title: Pushmeldingen toevoegen aan iOS
description: Meer informatie over het gebruik van Azure Mobile Apps om pushmeldingen naar uw iOS-app te verzenden.
ms.assetid: fa503833-d23e-4925-8d93-341bb3fbab7d
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: ad58ee158a2e1842c1b41db281bdd9f04b9b2ca4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461501"
---
# <a name="add-push-notifications-to-your-ios-app"></a>Pushmeldingen toevoegen aan je iOS-app

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Overzicht

In deze zelfstudie voegt u pushmeldingen toe aan het [iOS quickstart-project,] zodat er elke keer dat een record wordt ingevoegd een pushmelding naar het apparaat wordt verzonden.

Als u het gedownloade quickstartserverproject niet gebruikt, hebt u het uitbreidingspakket voor pushmeldingen nodig. Zie Werken met de handleiding [.NET backend server SDK voor Azure Mobile Apps voor](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) meer informatie.

De [iOS-simulator ondersteunt geen pushmeldingen.](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html) Je hebt een fysiek iOS-apparaat en een [Apple Developer Program-lidmaatschap](https://developer.apple.com/programs/ios/)nodig.

## <a name="configure-notification-hub"></a><a name="configure-hub"></a>Meldingshub configureren

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="register-app-for-push-notifications"></a><a id="register"></a>App registreren voor pushmeldingen

[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

## <a name="configure-azure-to-send-push-notifications"></a>Azure configureren om pushmeldingen te verzenden

[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

## <a name="update-backend-to-send-push-notifications"></a><a id="update-server"></a>Backend bijwerken om pushmeldingen te verzenden

[!INCLUDE [app-service-mobile-dotnet-backend-configure-push-apns](../../includes/app-service-mobile-dotnet-backend-configure-push-apns.md)]

## <a name="add-push-notifications-to-app"></a><a id="add-push"></a>Pushmeldingen toevoegen aan de app

[!INCLUDE [app-service-mobile-add-push-notifications-to-ios-app.md](../../includes/app-service-mobile-add-push-notifications-to-ios-app.md)]

## <a name="test-push-notifications"></a><a id="test"></a>Pushmeldingen testen

[!INCLUDE [Test Push Notifications in App](../../includes/test-push-notifications-in-app.md)]

## <a name="more"></a><a id="more"></a>Meer

* Sjablonen bieden u flexibiliteit om pushs en gelokaliseerde pushes te verzenden. [Hoe u iOS-clientbibliotheek voor Azure Mobile Apps gebruikt,](app-service-mobile-ios-how-to-use-client-library.md#templates) u zien hoe u sjablonen registreren.

<!-- Anchors.  -->

<!-- Images. -->

<!-- URLs. -->
[iOS snelaan de start]: app-service-mobile-ios-get-started.md
