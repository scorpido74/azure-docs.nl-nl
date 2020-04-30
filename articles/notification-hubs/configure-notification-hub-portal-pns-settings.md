---
title: Push meldingen instellen in azure Notification Hubs | Microsoft Docs
description: Meer informatie over het instellen van Azure Notification Hubs in het Azure Portal met behulp van PNS-instellingen (platform Notification System).
services: notification-hubs
author: sethmanheim
manager: femila
editor: dbradish-microsoft
ms.service: notification-hubs
ms.workload: mobile
ms.topic: quickstart
ms.date: 02/14/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 02/14/2019
ms.openlocfilehash: 78afb124ee1d1ab9b212197fb7a7140f88de9940
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80349517"
---
# <a name="quickstart-set-up-push-notifications-in-a-notification-hub"></a>Snelstartgids: Push meldingen instellen in een notification hub

Azure Notification Hubs biedt een push-engine die eenvoudig te gebruiken is en die kan worden geschaald. Gebruik Notification Hubs om meldingen te verzenden naar elk platform (iOS, Android, Windows, Baidu) en een back-end (in de Cloud of on-premises). Zie [Wat is Azure notification hubs?](notification-hubs-push-notification-overview.md)voor meer informatie.

In deze Quick Start gebruikt u de PNS-instellingen (platform Notification System) in Notification Hubs om Push meldingen in te stellen op meerdere platforms. In de Quick start ziet u de stappen die in de Azure Portal worden uitgevoerd.  [Google Firebase Cloud Messa ging](/azure/notification-hubs/configure-notification-hub-portal-pns-settings?tabs=azure-cli#google-firebase-cloud-messaging-fcm) bevat instructies voor het gebruik van de Azure cli.

Als u nog geen notification hub hebt gemaakt, maakt u er nu een. Zie [een Azure notification hub maken in het Azure Portal](create-notification-hub-portal.md) of [een Azure notification hub maken met behulp van de Azure cli](create-notification-hub-azure-cli.md) voor meer informatie.

## <a name="apple-push-notification-service"></a>Apple Push Notification Service

Apple Push Notification Service instellen (APNS):

1. Selecteer in het Azure Portal op de pagina **Notification hub** **Apple (APNS)** in het menu links.

1. Voor de **verificatie modus**selecteert u **certificaat** of **token**.

   a. Als u **certificaat**selecteert:
   * Selecteer het pictogram bestand en selecteer vervolgens het *. p12* -bestand dat u wilt uploaden.
   * Voer een wacht woord in.
   * Selecteer de modus **Sandbox**. Als u push meldingen wilt verzenden naar gebruikers die uw app uit de Store hebben gekocht, selecteert u **productie** modus.

     ![Scherm opname van een configuratie van een APNS-certificaat in de Azure Portal](./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png)

   b. Als u **token**selecteert:

   * Voer de waarden in voor de **sleutel-id**, de **bundel-id**, de **Team-ID**en het **token**.
   * Selecteer de modus **Sandbox**. Als u push meldingen wilt verzenden naar gebruikers die uw app uit de Store hebben gekocht, selecteert u **productie** modus.

     ![Scherm afbeelding van een configuratie van een APNS-token in de Azure Portal](./media/configure-notification-hub-portal-pns-settings/notification-hubs-apple-config-token.png)

Zie [Push meldingen naar IOS met Azure notification hubs](notification-hubs-ios-apple-push-notification-apns-get-started.md)voor meer informatie.

## <a name="google-firebase-cloud-messaging-fcm"></a>Google Firebase Cloud Messaging (FCM)

# <a name="portal"></a>[Portal](#tab/azure-portal)

Push meldingen instellen voor Google FCM:

1. Selecteer in de Azure Portal op de pagina **Notification hub** **Google (GCM/FCM)** in het menu links.
2. Plak de **API-sleutel** voor het Google FCM-project dat u eerder hebt opgeslagen.
3. Selecteer **Opslaan**.

   ![Scherm afbeelding die laat zien hoe u Notification Hubs configureert voor Google FCM](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)

Wanneer u deze stappen hebt voltooid, geeft een waarschuwing aan dat de notification hub is bijgewerkt. De knop **Opslaan** kan niet worden gekozen.

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

### <a name="prerequisites"></a>Vereisten

Voordat u begint, moet u het volgende hebben:

* De [Azure cli](/cli/azure/install-azure-cli) -versie 2.0.67 of hoger.

* De Azure CLI- [extensie voor notification hubs](/cli/azure/ext/notification-hub/notification-hub).
* De **API-sleutel** voor een Google Firebase Cloud Messa ging (FCM)-project.

### <a name="set-up-push-notifications-for-google-fcm"></a>Push meldingen instellen voor Google FCM

1. Gebruik de opdracht [AZ notification-hub Credential GCM update](/cli/azure/ext/notification-hub/notification-hub/credential/gcm#ext-notification-hub-az-notification-hub-credential-gcm-update) om uw Google API-sleutel toe te voegen aan uw notification hub.

   ```azurecli
   az notification-hub credential gcm update --resource-group spnhubrg --namespace-name spnhubns    --notification-hub-name spfcmtutorial1nhub --google-api-key myKey
   ```

2. De Android-app heeft een connection string nodig om verbinding te maken met de notification hub.  Gebruik de opdracht [AZ notification-hub Authorization-Rule List](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list) om het beschik bare toegangs beleid weer te geven.  Gebruik de opdracht [AZ notification-hub Authorization-Rules-Keys](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys) om de verbindings reeksen voor het toegangs beleid op te halen.  Geef de **primaryConnectionString** of **secondaryConnectionString** in de `--query` para meter op om de primaire Connection String rechtstreeks te verkrijgen.

   ```azurecli
   #list access policies for a notification hub
   az notification-hub authorization-rule list --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --output table

   #list keys and connection strings for a notification hub access policy
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name myAccessPolicyName --output json

   #get the primaryConnectionString for an access policy
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name myAccessPolicyName --query primaryConnectionString
   ```

3. Gebruik de opdracht [AZ notification-hub test-Send](/cli/azure/ext/notification-hub/notification-hub#ext-notification-hub-az-notification-hub-test-send) om het verzenden van berichten naar de Android-app te testen.

   ```azurecli
   #test with message body
   az notification-hub test-send --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --notification-format gcm --message "my message body"

   #test with JSON string
   az notification-hub test-send --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --notification-format gcm --payload "{\"data\":{\"message\":\"my JSON string\"}}"
   ```

U kunt Azure CLI-verwijzingen voor andere platforms ophalen met de opdracht [AZ notification-hub Credential](/cli/azure/ext/notification-hub/notification-hub/credential) .

---

Zie [Push meldingen naar Android-apparaten verzenden met Firebase](notification-hubs-android-push-notification-google-fcm-get-started.md)voor meer informatie over het pushen van meldingen naar een Android-toepassing.

## <a name="windows-push-notification-service"></a>Windows Push Notification Service

Windows Push Notification Service (WNS) instellen:

1. Selecteer in het Azure Portal op de pagina **Notification hub** de optie **Windows (WNS)** in het menu links.
2. Voer waarden in voor **Pakket-SID** en **Beveiligingssleutel**.
3. Selecteer **Opslaan**.

   ![Scherm opname van de vakken SID en beveiligings sleutel van het pakket](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

Zie [meldingen naar UWP-apps verzenden met behulp van Azure notification hubs](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)voor meer informatie.

## <a name="microsoft-push-notification-service-for-windows-phone"></a>Micro soft Push Notification Service voor Windows Phone

Micro soft Push Notification Service (MPNS) voor Windows Phone instellen:

1. Selecteer in het Azure Portal op de pagina **Notification Hub** **Windows Phone (MPNS)** in het menu links.
1. Niet-geverifieerde of geverifieerde push meldingen inschakelen:

   a. Als u niet-geverifieerde push meldingen wilt inschakelen, selecteert u niet- **geverifieerde push** > **Opslaan**inschakelen.

      ![Scherm afbeelding die laat zien hoe u niet-geverifieerde push meldingen inschakelt](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

   b. Geverifieerde push meldingen inschakelen:
      * Selecteer **certificaat uploaden**op de werk balk.
      * Selecteer het pictogram bestand en selecteer vervolgens het certificaat bestand.
      * Voer het wachtwoord voor het certificaat in.
      * Selecteer **OK**.
      * Selecteer op de pagina **Windows Phone (MPNS)** de optie **Opslaan**.

Zie [Push meldingen naar Windows Phone-apps met behulp van Notification hubs](notification-hubs-windows-mobile-push-notifications-mpns.md)voor meer informatie.

## <a name="baidu-android-china"></a>Baidu (Android China)

Push meldingen instellen voor Baidu:

1. Selecteer in het Azure Portal op de pagina **Notification hub** **Baidu (Android China)** in het menu links.
2. Voer de **API-sleutel** in die u hebt verkregen via de Baidu-console in het Baidu Cloud push-project.
3. Voer de **geheime sleutel** in die u hebt verkregen via de Baidu-console in het Baidu Cloud push-project.
4. Selecteer **Opslaan**.

    ![Scherm afbeelding van Notification Hubs waarin de configuratie van de Baidu (Android China) voor push meldingen wordt weer gegeven](./media/notification-hubs-baidu-get-started/AzureNotificationServicesBaidu.png)

Wanneer u deze stappen hebt voltooid, geeft een waarschuwing aan dat de notification hub is bijgewerkt. De knop **Opslaan** kan niet worden gekozen.

Zie [aan de slag met Notification hubs met Baidu](notification-hubs-baidu-china-android-notifications-get-started.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u geleerd hoe u de systeem instellingen voor platform meldingen kunt configureren voor een notification hub in de Azure Portal.

Zie deze zelf studies voor meer informatie over het pushen van meldingen naar verschillende platforms:

-[Push meldingen naar IOS-apparaten met behulp van Notification hubs-en APNS](notification-hubs-ios-apple-push-notification-apns-get-started.md)
--[Push meldingen naar Android-apparaten door gebruik te maken van Notification hubs-en Google FCM](notification-hubs-android-push-notification-google-fcm-get-started.md)
-[Push meldingen naar een UWP-app die wordt uitgevoerd op een Windows](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)
--push meldingen[naar een Windows Phone 8-app](notification-hubs-windows-mobile-push-notifications-mpns.md)
-met behulp van MPNS-[Push meldingen door middel van Notification hubs en Baidu Cloud push](notification-hubs-baidu-china-android-notifications-get-started.md)
