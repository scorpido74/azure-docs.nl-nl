---
title: Push meldingen toevoegen aan iOS
description: Meer informatie over het gebruik van Azure Mobile Apps voor het verzenden van push meldingen naar uw iOS-app.
ms.assetid: fa503833-d23e-4925-8d93-341bb3fbab7d
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: ad58ee158a2e1842c1b41db281bdd9f04b9b2ca4
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/19/2020
ms.locfileid: "77461501"
---
# <a name="add-push-notifications-to-your-ios-app"></a>Push meldingen toevoegen aan uw iOS-app

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Overzicht

In deze zelf studie voegt u push meldingen toe aan het [Quick start voor iOS] start-project, zodat een push melding wordt verzonden naar het apparaat telkens wanneer een record wordt ingevoegd.

Als u het gedownloade Quick Start-Server project niet gebruikt, hebt u het uitbreidings pakket voor push meldingen nodig. Zie [werken met de .net back-end server SDK voor Azure Mobile apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) Guide (Engelstalig) voor meer informatie.

De [IOS-simulator biedt geen ondersteuning voor push meldingen](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html). U hebt een fysiek iOS-apparaat en een [Apple Developer Program-lidmaatschap](https://developer.apple.com/programs/ios/)nodig.

## <a name="configure-hub"></a>Notification hub configureren

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a id="register"></a>App registreren voor push meldingen

[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

## <a name="configure-azure-to-send-push-notifications"></a>Azure configureren voor het verzenden van push meldingen

[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

## <a id="update-server"></a>Back-end bijwerken om Push meldingen te verzenden

[!INCLUDE [app-service-mobile-dotnet-backend-configure-push-apns](../../includes/app-service-mobile-dotnet-backend-configure-push-apns.md)]

## <a id="add-push"></a>Push meldingen toevoegen aan de app

[!INCLUDE [app-service-mobile-add-push-notifications-to-ios-app.md](../../includes/app-service-mobile-add-push-notifications-to-ios-app.md)]

## <a id="test"></a>Push meldingen testen

[!INCLUDE [Test Push Notifications in App](../../includes/test-push-notifications-in-app.md)]

## <a id="more"></a>Grotere

* Sjablonen bieden u de mogelijkheid om platformoverschrijdende pushes en gelokaliseerde pushes te verzenden. [Het gebruik van de IOS-client bibliotheek voor Azure Mobile apps](app-service-mobile-ios-how-to-use-client-library.md#templates) laat zien hoe u sjablonen kunt registreren.

<!-- Anchors.  -->

<!-- Images. -->

<!-- URLs. -->
[Quick start voor iOS]: app-service-mobile-ios-get-started.md
