---
title: Push meldingen toevoegen aan uw Android-app
description: Meer informatie over het gebruik van Mobile Apps voor het verzenden van push meldingen naar uw Android-app.
ms.assetid: 9058ed6d-e871-4179-86af-0092d0ca09d3
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: ce4ebe9e8874e779b8da16e9c30fcfc3ca46754e
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2019
ms.locfileid: "74668613"
---
# <a name="add-push-notifications-to-your-android-app"></a>Push meldingen toevoegen aan uw Android-app

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

> [!NOTE]
> Visual Studio App Center ondersteunt end-to-end-services en geïntegreerde services die een centrale rol spelen bij het ontwikkelen van mobiele apps. Ontwikkelaars kunnen services **bouwen**, **testen** en **distribueren** om een CI/CD-pijplijn (continue integratie en continue levering) in te stellen. Zodra de app is geïmplementeerd, kunnen ontwikkelaars de status en het gebruik van hun app controleren met behulp van de **analyseservice** en de **diagnoseservice** en communiceren met gebruikers met behulp van de **pushservice**. Ontwikkelaars kunnen ook gebruikmaken van **Auth** voor het verifiëren van gebruikers en van **Data** Service voor het persistent maken en synchroniseren van app-gegevens in de cloud.
>
> Als u cloudservices wilt integreren in uw mobiele toepassing, meldt u zich aan bij [App Center](https://appcenter.ms/signup?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc).

## <a name="overview"></a>Overzicht

In deze zelf studie voegt u push meldingen toe aan het [Android Quick Start] -project, zodat een push melding wordt verzonden naar het apparaat telkens wanneer een record wordt ingevoegd.

Als u het gedownloade Quick Start Server-project niet gebruikt, hebt u het uitbreidings pakket voor push meldingen nodig. Zie [werken met de .net back-end server SDK voor Azure Mobile apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)voor meer informatie.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig:

* Een IDE, afhankelijk van de back-end van uw project:

  * [Android Studio](https://developer.android.com/sdk/index.html) als deze app een node. js-back-end heeft.
  * [Visual Studio Community 2013](https://go.microsoft.com/fwLink/p/?LinkID=391934) of hoger als deze app een Microsoft .net back-end heeft.
* Android 2,3 of hoger, Google repository Revision 27 of hoger en Google Play Services 9.0.2 of hoger voor Firebase Cloud Messa ging.
* Voltooi de [Android Quick Start].

## <a name="create-a-project-that-supports-firebase-cloud-messaging"></a>Een project maken dat Firebase Cloud Messaging ondersteunt

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-a-notification-hub"></a>Een Notification Hub configureren

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="configure-azure-to-send-push-notifications"></a>Azure configureren voor het verzenden van push meldingen

[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push-for-firebase.md)]

## <a name="enable-push-notifications-for-the-server-project"></a>Push meldingen inschakelen voor het server project

[!INCLUDE [app-service-mobile-dotnet-backend-configure-push-google](../../includes/app-service-mobile-dotnet-backend-configure-push-google.md)]

## <a name="add-push-notifications-to-your-app"></a>Pushmeldingen toevoegen aan uw app

In deze sectie werkt u de Android-app van de client bij om Push meldingen te verwerken.

### <a name="verify-android-sdk-version"></a>Android SDK-versie controleren

[!INCLUDE [app-service-mobile-verify-android-sdk-version](../../includes/app-service-mobile-verify-android-sdk-version.md)]

De volgende stap is het installeren van Google Play Services. Firebase Cloud Messa ging heeft enkele minimale vereisten voor het API-niveau voor ontwikkeling en testen, waaraan de eigenschap **minSdkVersion** in het manifest moet voldoen.

Als u met een ouder apparaat wilt testen, raadpleegt u [Firebase toevoegen aan uw Android-project] om te bepalen hoe laag u deze waarde kunt instellen en deze op de juiste wijze instellen.

### <a name="add-firebase-cloud-messaging-to-the-project"></a>Firebase Cloud Messa ging toevoegen aan het project

[!INCLUDE [Add Firebase Cloud Messaging](../../includes/app-service-mobile-add-firebase-cloud-messaging.md)]

### <a name="add-code"></a>Code toevoegen

[!INCLUDE [app-service-mobile-android-getting-started-with-push](../../includes/app-service-mobile-android-getting-started-with-push.md)]

## <a name="test-the-app-against-the-published-mobile-service"></a>De app testen op de gepubliceerde mobiele service

U kunt de app testen door rechtstreeks een Android-telefoon te koppelen aan een USB-kabel of door een virtueel apparaat in de emulator te gebruiken.

## <a name="next-steps"></a>Volgende stappen

Nu u deze zelf studie hebt voltooid, kunt u door gaan met een van de volgende zelf studies:

* [Voeg verificatie toe aan uw Android-app](app-service-mobile-android-get-started-users.md).
  Meer informatie over hoe u verificatie kunt toevoegen aan het ToDoList Quick start-project op Android met behulp van een ondersteunde ID-provider.
* [Offline synchronisatie inschakelen voor uw Android-app](app-service-mobile-android-get-started-offline-data.md).
  Meer informatie over het toevoegen van offline ondersteuning aan uw app met behulp van een Mobile Apps back-end. Met offline synchronisatie kunnen gebruikers met een mobiele app communiceren&mdash;gegevens weer geven, toevoegen of wijzigen&mdash;zelfs wanneer er geen netwerk verbinding is.

<!-- URLs -->
[Android Quick Start]: app-service-mobile-android-get-started.md
[Firebase toevoegen aan uw Android-project]: https://firebase.google.com/docs/android/setup
