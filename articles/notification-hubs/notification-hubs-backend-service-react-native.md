---
title: Pushmeldingen verzenden naar React Native-apps met Azure Notification Hubs via een back-endservice | Microsoft Docs
description: Leer hoe u meldingen kunt pushen naar React Native-apps die gebruikmaken van Azure Notification Hubs via een back-endservice.
author: alexeystrakh
ms.service: notification-hubs
ms.topic: tutorial
ms.date: 06/11/2020
ms.author: alstrakh
ms.openlocfilehash: 30cf22d99a675d4525d972aeb889cec30a2e9b94
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/07/2020
ms.locfileid: "86060448"
---
# <a name="tutorial-send-push-notifications-to-react-native-apps-using-azure-notification-hubs-via-a-backend-service"></a>Zelfstudie: Pushmeldingen verzenden naar React Native-apps met Azure Notification Hubs via een back-endservice  

[![Voorbeeld downloaden](./media/notification-hubs-backend-service-react-native/download.png) Het voorbeeld downloaden](https://github.com/xamcat/mobcat-samples/tree/master/notification_hub_backend_service)  

> [!div class="op_single_selector"]
>
> * [Xamarin.Forms](notification-hubs-backend-service-xamarin-forms.md)
> * [React Native](notification-hubs-backend-service-react-native.md)

In deze zelfstudie gebruikt u [Azure Notificatie Hubs](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-overview) om meldingen naar een [React Native](https://reactnative.dev/)-app naar **Android** en **iOS** te pushen.  

[!INCLUDE [Notification Hubs Backend Service Introduction](../../includes/notification-hubs-backend-service-introduction.md)]

In deze zelfstudie voert u de volgende stappen uit:

> [!div class="checklist"]
>
> * [Pushmeldingservices en Azure Notification Hubs instellen.](#set-up-push-notification-services-and-azure-notification-hub)
> * [Een ASP.NET Core Web API-back-end-app maken.](#create-an-aspnet-core-web-api-backend-application)
> * [Een React Native-app voor meerdere platforms maken.](#create-a-cross-platform-react-native-application)
> * [Het systeemeigen Android-project voor pushmeldingen configureren.](#configure-the-native-android-project-for-push-notifications)
> * [Het systeemeigen iOS-project voor pushmeldingen configureren.](#configure-the-native-ios-project-for-push-notifications)
> * [De oplossing testen.](#test-the-solution)

## <a name="prerequisites"></a>Vereisten

Als u wilt volgen, hebt u het volgende nodig:

* Een [Azure-abonnement](https://portal.azure.com) waarmee u resources kunt maken en beheren.
* Een Mac waarop [Visual Studio voor Mac](https://visualstudio.microsoft.com/vs/mac/) is geïnstalleerd (of een pc met [Visual Studio 2019](https://visualstudio.microsoft.com/vs) met de **Mobile Development met .NET** workload).
* De mogelijkheid om de app uit te voeren op **Android** (fysieke of emulator-apparaten) of **iOS** (alleen voor fysieke apparaten).

Voor Android hebt u het volgende nodig:

* Een ontwikkelaar heeft een niet-vergrendeld fysiek apparaat of een emulator *(waarop API 26 of hoger wordt uitgevoerd en waarop Google Play Services zijn geïnstalleerd)* .

Voor iOS hebt u het volgende nodig:

* Een actief [Apple Developer](https://developer.apple.com)-account.
* Een fysiek iOS-apparaat dat is [geregistreerd bij uw ontwikkelaarsaccount](https://help.apple.com/developer-account/#/dev40df0d9fa) *(met iOS 13.0 en hoger)* .
* Een **.p12** [-ontwikkelingscertificaat](https://help.apple.com/developer-account/#/dev04fd06d56) geïnstalleerd in uw **sleutelketen** zodat u [een app kunt uitvoeren op een fysiek apparaat](https://help.apple.com/xcode/mac/current/#/dev5a825a1ca).

> [!NOTE]
> De iOS-Simulator ondersteunt geen externe meldingen en een fysiek apparaat is vereist voor het verkennen van dit voorbeeld op iOS. Het is echter niet nodig om de app uit te voeren op zowel **Android** als **iOS** om deze zelfstudie te voltooien.

U kunt de stappen in dit voorbeeld met basisprincipes volgen zonder eerdere ervaring. U kunt echter profiteren van uw kennis van de volgende aspecten.

* [Apple-ontwikkelaarsportal](https://developer.apple.com)
* [ASP.NET Core](https://docs.microsoft.com/aspnet/core/introduction-to-aspnet-core?view=aspnetcore-3.1)
* [Google Firebase Console](https://console.firebase.google.com/u/0/)
* [Microsoft Azure](https://portal.azure.com) en [Pushmeldingen verzenden naar iOS-apps met Azure Notification Hubs](ios-sdk-get-started.md).
* [React Native](https://reactnative.dev/docs/getting-started)

De beschikbare stappen zijn voor [Visual Studio voor Mac](https://visualstudio.microsoft.com/vs/mac/) en [Visual Studio Code](https://code.visualstudio.com/download), maar u kunt het proces ook volgen met [Visual Studio 2019](https://visualstudio.microsoft.com/vs).

## <a name="set-up-push-notification-services-and-azure-notification-hub"></a>Push Notification Services en Azure Notification Hubs instellen

In deze sectie stelt u **[Firebase Cloud Messaging (FCM)](https://firebase.google.com/docs/cloud-messaging)** en **[Apple Push Notification Services (APNS)](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html)** in. Vervolgens maakt en configureert u een notification hub om met deze services te werken.

[!INCLUDE [Create a Firebase project and enable Firebase Cloud Messaging](../../includes/notification-hubs-common-enable-firebase-cloud-messaging.md)]

[!INCLUDE [Enable Apple Push Notifications](../../includes/notification-hubs-common-enable-apple-push-notifications.md)]

[!INCLUDE [Create a notification hub](../../includes/notification-hubs-common-create-notification-hub.md)]

[!INCLUDE [Configure your notification hub with APNs information](../../includes/notification-hubs-common-configure-with-apns-information.md)]

[!INCLUDE [Configure your notification hub with FCM information](../../includes/notification-hubs-common-configure-with-fcm-information.md)]

## <a name="create-an-aspnet-core-web-api-backend-application"></a>Een ASP.NET Core Web API-back-endtoepassing maken

In deze sectie maakt u de [ASP.NET Core Web API](https://dotnet.microsoft.com/apps/aspnet/apis)-back-end om de [registratie van het apparaat](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-registration-management#what-is-device-registration) en het versturen van meldingen naar de mobiele React Native-app af te handelen.

[!INCLUDE [Create an ASP.NET Core Web API backend application](../../includes/notification-hubs-backend-service-web-api.md)]

## <a name="create-a-cross-platform-react-native-application"></a>Een React Native-app voor meerdere platforms maken

In deze sectie maakt u een mobiele [React Native](https://reactnative.dev/)-app die pushmeldingen implementeert op verschillende platforms.

[!INCLUDE [Sample application generic overview](../../includes/notification-hubs-backend-service-sample-app-overview.md)]

[!INCLUDE [Create React Native application](../../includes/notification-hubs-backend-service-sample-app-reactnative.md)]

## <a name="configure-the-native-android-project-for-push-notifications"></a>Het systeemeigen Android-project voor pushmeldingen configureren

[!INCLUDE [Configure the native Android project](../../includes/notification-hubs-backend-service-configure-reactnative-android.md)]

## <a name="configure-the-native-ios-project-for-push-notifications"></a>Het systeemeigen iOS-project voor pushmeldingen configureren

[!INCLUDE [Configure the native iOS project](../../includes/notification-hubs-backend-service-configure-reactnative-ios.md)]

## <a name="test-the-solution"></a>De oplossing testen

U kunt nu de verzending van meldingen via de back-endservice testen.

[!INCLUDE [Testing the solution](../../includes/notification-hubs-backend-service-testing.md)]

## <a name="next-steps"></a>Volgende stappen

U zou nu een eenvoudige React Native-app moeten hebben die via een back-endservice is verbonden met een notification hub en die meldingen kan verzenden en ontvangen.

[!INCLUDE [Next steps](../../includes/notification-hubs-backend-service-next-steps.md)]

## <a name="troubleshooting"></a>Problemen oplossen

[!INCLUDE [Troubleshooting](../../includes/notification-hubs-backend-service-troubleshooting.md)]

## <a name="related-links"></a>Verwante koppelingen

* [Overzicht van Azure Notification Hubs](notification-hubs-push-notification-overview.md)
* [Visual Studio voor Mac installeren](https://docs.microsoft.com/visualstudio/mac/installation?view=vsmac-2019)
* [Visual Studio Code installeren](https://code.visualstudio.com/download)
* [De React Native-ontwikkelomgeving instellen](https://reactnative.dev/docs/environment-setup)
* [Notification Hubs SDK voor back-endbewerkingen](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)
* [Notification Hubs SDK op GitHub](https://github.com/Azure/azure-notificationhubs)
* [Registreren bij back-end van toepassing](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)
* [Registratiebeheer](notification-hubs-push-notification-registration-management.md)
* [Werken met tags](notification-hubs-tags-segment-push-message.md)
* [Werken met aangepaste sjablonen](notification-hubs-templates-cross-platform-push-messages.md)
