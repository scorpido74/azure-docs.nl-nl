---
title: Pushmeldingen toevoegen aan je Android-app
description: Meer informatie over het gebruik van mobiele apps om pushmeldingen naar je Android-app te verzenden.
ms.assetid: 9058ed6d-e871-4179-86af-0092d0ca09d3
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 6fec85c028e992c15fb9503ffb599023e668c58f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77459936"
---
# <a name="add-push-notifications-to-your-android-app"></a>Pushmeldingen toevoegen aan je Android-app

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Overzicht

In deze zelfstudie voegt u pushmeldingen toe aan het [Android-project voor snelle start,] zodat er elke keer dat een record wordt ingevoegd een pushmelding naar het apparaat wordt verzonden.

Als u het gedownloade quick start-serverproject niet gebruikt, hebt u het uitbreidingspakket voor pushmeldingen nodig. Zie [Werken met de .NET-backendserver SDK voor Azure Mobile Apps voor](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)meer informatie.

## <a name="prerequisites"></a>Vereisten

Je hebt het volgende nodig:

* Een IDE, afhankelijk van de back-end van uw project:

  * [Android Studio](https://developer.android.com/sdk/index.html) als deze app een Node.js back-end heeft.
  * [Visual Studio Community 2013](https://go.microsoft.com/fwLink/p/?LinkID=391934) of hoger als deze app een Microsoft .NET back-end heeft.
* Android 2.3 of hoger, Google Repository revisie 27 of hoger en Google Play Services 9.0.2 of hoger voor Firebase Cloud Messaging.
* Voltooi de [Android snel start].

## <a name="create-a-project-that-supports-firebase-cloud-messaging"></a>Een project maken dat Firebase Cloud Messaging ondersteunt

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-a-notification-hub"></a>Een Notification Hub configureren

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="configure-azure-to-send-push-notifications"></a>Azure configureren om pushmeldingen te verzenden

[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push-for-firebase.md)]

## <a name="enable-push-notifications-for-the-server-project"></a>Pushmeldingen voor het serverproject inschakelen

[!INCLUDE [app-service-mobile-dotnet-backend-configure-push-google](../../includes/app-service-mobile-dotnet-backend-configure-push-google.md)]

## <a name="add-push-notifications-to-your-app"></a>Pushmeldingen toevoegen aan uw app

In deze sectie werk je je android-app van je client bij om pushmeldingen te verwerken.

### <a name="verify-android-sdk-version"></a>Android SDK-versie verifiëren

[!INCLUDE [app-service-mobile-verify-android-sdk-version](../../includes/app-service-mobile-verify-android-sdk-version.md)]

Uw volgende stap is het installeren van Google Play-services. Firebase Cloud Messaging heeft een aantal minimale API-vereisten voor ontwikkeling en testen, waaraan de eigenschap **minSdkVersion** in het manifest moet voldoen.

Als u test met een ouder apparaat, raadpleegt u [Firebase toevoegen aan uw Android-project] om te bepalen hoe laag u deze waarde instellen en deze op de juiste manier in te stellen.

### <a name="add-firebase-cloud-messaging-to-the-project"></a>Firebase Cloud Messaging toevoegen aan het project

[!INCLUDE [Add Firebase Cloud Messaging](../../includes/app-service-mobile-add-firebase-cloud-messaging.md)]

### <a name="add-code"></a>Code toevoegen

[!INCLUDE [app-service-mobile-android-getting-started-with-push](../../includes/app-service-mobile-android-getting-started-with-push.md)]

## <a name="test-the-app-against-the-published-mobile-service"></a>Test de app tegen de gepubliceerde mobiele service

U de app testen door een Android-telefoon rechtstreeks te koppelen met een USB-kabel of door een virtueel apparaat in de emulator te gebruiken.

## <a name="next-steps"></a>Volgende stappen

Nu dat u deze tutorial voltooid, overwegen verder te gaan naar een van de volgende tutorials:

* [Verificatie toevoegen aan uw Android-app](app-service-mobile-android-get-started-users.md).
  Meer informatie over het toevoegen van verificatie aan het snelstartproject van de todolist op Android met behulp van een ondersteunde identiteitsprovider.
* [Offline synchronisatie inschakelen voor uw Android-app](app-service-mobile-android-get-started-offline-data.md).
  Meer informatie over het toevoegen van offline ondersteuning aan uw app met behulp van een back-end van Mobiele apps. Met offline synchronisatie kunnen gebruikers communiceren&mdash;met een mobiele app&mdash;die gegevens bekijkt, toevoegt of wijzigt, zelfs als er geen netwerkverbinding is.

<!-- URLs -->
[Android snel aan de slag]: app-service-mobile-android-get-started.md
[Firebase toevoegen aan je Android-project]:https://firebase.google.com/docs/android/setup
