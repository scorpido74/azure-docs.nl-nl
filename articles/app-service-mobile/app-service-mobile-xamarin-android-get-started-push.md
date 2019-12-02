---
title: Pushmeldingen toevoegen aan uw Xamarin.Android-app
description: Meer informatie over het gebruik van Azure App Service en Azure Notification Hubs voor het verzenden van push meldingen naar uw Xamarin. Android-app.
ms.assetid: 6f7e8517-e532-4559-9b07-874115f4c65b
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: c9dc4c825d65287f152522868a2b9e6a38ea70bb
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2019
ms.locfileid: "74668749"
---
# <a name="add-push-notifications-to-your-xamarinandroid-app"></a>Pushmeldingen toevoegen aan uw Xamarin.Android-app

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

> [!NOTE]
> Visual Studio App Center ondersteunt end-to-end-services en geïntegreerde services die een centrale rol spelen bij het ontwikkelen van mobiele apps. Ontwikkelaars kunnen services **bouwen**, **testen** en **distribueren** om een CI/CD-pijplijn (continue integratie en continue levering) in te stellen. Zodra de app is geïmplementeerd, kunnen ontwikkelaars de status en het gebruik van hun app controleren met behulp van de **analyseservice** en de **diagnoseservice** en communiceren met gebruikers met behulp van de **pushservice**. Ontwikkelaars kunnen ook gebruikmaken van **Auth** voor het verifiëren van gebruikers en van **Data** Service voor het persistent maken en synchroniseren van app-gegevens in de cloud.
>
> Als u cloudservices wilt integreren in uw mobiele toepassing, meldt u zich aan bij [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc).

## <a name="overview"></a>Overzicht

In deze zelf studie voegt u push meldingen toe aan het Quick start-project van [Xamarin. Android](app-service-mobile-windows-store-dotnet-get-started.md) , zodat een push melding wordt verzonden naar het apparaat wanneer een record wordt ingevoegd.

Als u het gedownloade Quick Start-Server project niet gebruikt, hebt u het uitbreidings pakket voor push meldingen nodig. Zie voor meer informatie het [werk met de .net back-end server SDK voor Azure Mobile apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) Guide.

## <a name="prerequisites"></a>Vereisten

Voor deze zelf studie hebt u het volgende nodig:

* Een actief Google-account. U kunt zich registreren voor een Google-account op [accounts.Google.com](https://go.microsoft.com/fwlink/p/?LinkId=268302).
* [Google Cloud messaging-client onderdeel](https://components.xamarin.com/view/GCMClient/).

## <a name="configure-hub"></a>Een notification hub configureren

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a id="register"></a>Firebase Cloud Messa ging inschakelen

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-azure-to-send-push-requests"></a>Azure configureren voor het verzenden van push-aanvragen

[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push-for-firebase.md)]

## <a id="update-server"></a>Het server project bijwerken voor het verzenden van push meldingen

[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a id="configure-app"></a>Het client project configureren voor push meldingen

[!INCLUDE [mobile-services-xamarin-android-push-configure-project](../../includes/mobile-services-xamarin-android-push-configure-project.md)]

## <a id="add-push"></a>Code voor push meldingen toevoegen aan uw app

[!INCLUDE [app-service-mobile-xamarin-android-push-add-to-app](../../includes/app-service-mobile-xamarin-android-push-add-to-app.md)]

## <a name="test"></a>Push meldingen in uw app testen

U kunt de app testen met behulp van een virtueel apparaat in de emulator. Er zijn aanvullende configuratie stappen vereist bij het uitvoeren van een emulator.

1. Het virtuele apparaat moet Google Api's hebben ingesteld als het doel in het Android Virtual Device (AVD) Manager.

    ![](./media/app-service-mobile-xamarin-android-get-started-push/google-apis-avd-settings.png)

2. Voeg een Google-account toe aan het Android-apparaat door te klikken op **Apps** > **instellingen** > **account toevoegen**en volg de aanwijzingen.

    ![](./media/app-service-mobile-xamarin-android-get-started-push/add-google-account.png)

3. Voer de ToDoList-app net als voorheen uit en voeg een nieuw TODO-item toe. Deze keer verschijnt een meldings pictogram in het systeemvak. U kunt de meldings lade openen om de volledige tekst van de melding weer te geven.

    ![](./media/app-service-mobile-xamarin-android-get-started-push/android-notifications.png)

<!-- URLs. -->
[Xamarin.Android quick start]: app-service-mobile-xamarin-android-get-started.md
[Google Cloud Messaging Client Component]: https://components.xamarin.com/view/GCMClient/
[Azure Mobile Services Component]: https://components.xamarin.com/view/azure-mobile-services/
