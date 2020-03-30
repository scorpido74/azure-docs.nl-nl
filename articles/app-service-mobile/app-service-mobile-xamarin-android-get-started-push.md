---
title: Pushmeldingen toevoegen aan uw Xamarin.Android-app
description: Meer informatie over het gebruik van Azure App Service en Azure Notification Hubs om pushmeldingen naar uw Xamarin.Android-app te verzenden.
ms.assetid: 6f7e8517-e532-4559-9b07-874115f4c65b
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 5657be0dbaeb46f8f899a9b4a2f8ba9b4fe9ebaa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249306"
---
# <a name="add-push-notifications-to-your-xamarinandroid-app"></a>Pushmeldingen toevoegen aan uw Xamarin.Android-app

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Overzicht

In deze zelfstudie voegt u pushmeldingen toe aan het [Quickstart-project Xamarin.Android,](app-service-mobile-windows-store-dotnet-get-started.md) zodat er elke keer dat een record wordt ingevoegd een pushmelding naar het apparaat wordt verzonden.

Als u het gedownloade quickstartserverproject niet gebruikt, hebt u het uitbreidingspakket voor pushmeldingen nodig. Zie Voor meer informatie de handleiding [Werken met de .NET-backendserver SDK voor Azure Mobile Apps.](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)

## <a name="prerequisites"></a>Vereisten

Deze zelfstudie vereist de installatie:

* Een actief Google-account. U zich aanmelden voor een Google-account op [accounts.google.com.](https://go.microsoft.com/fwlink/p/?LinkId=268302)
* [Google Cloud Messaging-clientcomponent](https://components.xamarin.com/view/GCMClient/).

## <a name="configure-a-notification-hub"></a><a name="configure-hub"></a>Een meldingshub configureren

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="enable-firebase-cloud-messaging"></a><a id="register"></a>Firebase Cloud-berichten inschakelen

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-azure-to-send-push-requests"></a>Azure configureren om pushaanvragen te verzenden

[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push-for-firebase.md)]

## <a name="update-the-server-project-to-send-push-notifications"></a><a id="update-server"></a>Het serverproject bijwerken om pushmeldingen te verzenden

[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="configure-the-client-project-for-push-notifications"></a><a id="configure-app"></a>Het clientproject configureren voor pushmeldingen

[!INCLUDE [mobile-services-xamarin-android-push-configure-project](../../includes/mobile-services-xamarin-android-push-configure-project.md)]

## <a name="add-push-notifications-code-to-your-app"></a><a id="add-push"></a>Code pushmeldingen toevoegen aan uw app

[!INCLUDE [app-service-mobile-xamarin-android-push-add-to-app](../../includes/app-service-mobile-xamarin-android-push-add-to-app.md)]

## <a name="test-push-notifications-in-your-app"></a><a name="test"></a>Pushmeldingen testen in uw app

U de app testen met behulp van een virtueel apparaat in de emulator. Er zijn extra configuratiestappen vereist wanneer u op een emulator wordt uitgevoerd.

1. Het virtuele apparaat moet Google API's hebben ingesteld als het doel in de Android Virtual Device (AVD) manager.

    ![](./media/app-service-mobile-xamarin-android-get-started-push/google-apis-avd-settings.png)

2. Een Google-account toevoegen aan het Android-apparaat door op**Instellingen** >  **apps** > **Te klikken Voeg account toe**en volg de aanwijzingen.

    ![](./media/app-service-mobile-xamarin-android-get-started-push/add-google-account.png)

3. Voer de todolist-app uit zoals voorheen en voeg een nieuw todo-item in. Deze keer wordt een meldingspictogram weergegeven in het meldingsgebied. U de meldingsla openen om de volledige tekst van de melding weer te geven.

    ![](./media/app-service-mobile-xamarin-android-get-started-push/android-notifications.png)

<!-- URLs. -->
[Xamarin.Android quick start]: app-service-mobile-xamarin-android-get-started.md
[Google Cloud Messaging Client Component]: https://components.xamarin.com/view/GCMClient/
[Azure Mobile Services Component]: https://components.xamarin.com/view/azure-mobile-services/
