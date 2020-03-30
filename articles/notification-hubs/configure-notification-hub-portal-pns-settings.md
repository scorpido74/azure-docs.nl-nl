---
title: Pushmeldingen instellen in Azure Notification Hubs | Microsoft Documenten
description: Meer informatie over het instellen van Azure Notification Hubs in de Azure-portal met behulp van PNS-instellingen (Platform Notification System).
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
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349517"
---
# <a name="quickstart-set-up-push-notifications-in-a-notification-hub"></a>Snelstart: pushmeldingen instellen in een meldingshub

Azure Notification Hubs biedt een push-engine die eenvoudig te gebruiken is en die uitschaalt. Gebruik Notification Hubs om meldingen te verzenden naar elk platform (iOS, Android, Windows, Baidu) en vanaf elke back-end (cloud of on-premises). Zie Wat is Azure Notification Hubs voor meer [informatie?](notification-hubs-push-notification-overview.md).

In deze quickstart gebruikt u de pns-instellingen (platformmeldingssysteem) in Meldingenhubs om pushmeldingen op meerdere platforms in te stellen. In de snelstart ziet u de stappen die u moet nemen in de Azure-portal.  [Google Firebase Cloud Messaging](/azure/notification-hubs/configure-notification-hub-portal-pns-settings?tabs=azure-cli#google-firebase-cloud-messaging-fcm) bevat instructies voor het gebruik van de Azure CLI.

Als u nog geen meldingshub hebt gemaakt, maakt u er nu een. Zie [Een Azure-meldingshub maken in de Azure-portal](create-notification-hub-portal.md) of [Een Azure-meldingshub maken met de Azure CLI](create-notification-hub-azure-cli.md) voor meer informatie.

## <a name="apple-push-notification-service"></a>Apple Push Notification Service

Apple Push Notification Service (APNS) instellen:

1. Selecteer **Apple (APNS)** in de Azure-portal op de pagina **Meldingshub** in het linkermenu.

1. Selecteer **Certificaat** of **Token**voor **verificatiemodus.**

   a. Als u **Certificaat**selecteert:
   * Selecteer het bestandspictogram en selecteer vervolgens het *.p12-bestand* dat u wilt uploaden.
   * Voer een wachtwoord in.
   * Selecteer de modus **Sandbox**. Of als u pushmeldingen wilt verzenden naar gebruikers die uw app in de Store hebben gekocht, selecteert u **de productiemodus.**

     ![Schermafbeelding van een APNS-certificaatconfiguratie in de Azure-portal](./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png)

   b. Als u **Token**selecteert:

   * Voer de waarden in voor **Sleutel-id,** **Bundel-id,** **Team-id**en **Token**.
   * Selecteer de modus **Sandbox**. Of als u pushmeldingen wilt verzenden naar gebruikers die uw app in de Store hebben gekocht, selecteert u **de productiemodus.**

     ![Schermafbeelding van een APNS-tokenconfiguratie in de Azure-portal](./media/configure-notification-hub-portal-pns-settings/notification-hubs-apple-config-token.png)

Zie [Pushmeldingen naar iOS voor](notification-hubs-ios-apple-push-notification-apns-get-started.md)meer informatie met Azure Notification Hubs.

## <a name="google-firebase-cloud-messaging-fcm"></a>Google Firebase Cloud Messaging (FCM)

# <a name="portal"></a>[Portal](#tab/azure-portal)

Pushmeldingen instellen voor Google FCM:

1. Selecteer **google (GCM/FCM)** in de Azure-portal op de pagina **Meldingshub** in het linkermenu.
2. Plak de **API-sleutel** voor het Google FCM-project dat u eerder hebt opgeslagen.
3. Selecteer **Opslaan**.

   ![Schermafbeelding van het configureren van meldingshubs voor Google FCM](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)

Wanneer u deze stappen uitvoert, geeft een waarschuwing aan dat de meldingshub is bijgewerkt. De knop **Opslaan** kan niet worden gekozen.

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

### <a name="prerequisites"></a>Vereisten

Voordat u begint, moet u het volgende hebben:

* De [Azure CLI-versie](/cli/azure/install-azure-cli) 2.0.67 of hoger.

* De Azure [CLI-extensie voor meldingshubs](/cli/azure/ext/notification-hub/notification-hub).
* De **API-sleutel** voor een FCM-project (Google Firebase Cloud Messaging).

### <a name="set-up-push-notifications-for-google-fcm"></a>Pushmeldingen instellen voor Google FCM

1. Gebruik de [gcm-updateopdracht voor az-meldingshub](/cli/azure/ext/notification-hub/notification-hub/credential/gcm#ext-notification-hub-az-notification-hub-credential-gcm-update) om uw Google API-sleutel toe te voegen aan uw meldingshub.

   ```azurecli
   az notification-hub credential gcm update --resource-group spnhubrg --namespace-name spnhubns    --notification-hub-name spfcmtutorial1nhub --google-api-key myKey
   ```

2. De Android-app heeft een verbindingstekenreeks nodig om verbinding te maken met de meldingshub.  Gebruik de [opdracht lijst met autorisatieregel voor az-hub](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list) om het beschikbare toegangsbeleid weer te geven.  Gebruik de opdracht [lijstsleutels voor autorisatie-regels voor az-hub-hub](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys) om de verbindingstekenreeksen voor toegangsbeleid te krijgen.  Geef de **primaire ConnectionString** of `--query` **secondaryConnectionString** op in de parameter om de primaire verbindingstekenreeks rechtstreeks te krijgen.

   ```azurecli
   #list access policies for a notification hub
   az notification-hub authorization-rule list --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --output table

   #list keys and connection strings for a notification hub access policy
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name myAccessPolicyName --output json

   #get the primaryConnectionString for an access policy
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name myAccessPolicyName --query primaryConnectionString
   ```

3. Gebruik de [test-send-opdracht az-meldingshub](/cli/azure/ext/notification-hub/notification-hub#ext-notification-hub-az-notification-hub-test-send) om berichten naar de Android-app te testen.

   ```azurecli
   #test with message body
   az notification-hub test-send --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --notification-format gcm --message "my message body"

   #test with JSON string
   az notification-hub test-send --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --notification-format gcm --payload "{\"data\":{\"message\":\"my JSON string\"}}"
   ```

Ontvang Azure CLI-verwijzingen voor andere platforms met de opdracht [AZ-meldingshub-referenties.](/cli/azure/ext/notification-hub/notification-hub/credential)

---

Zie [Pushmeldingen naar Android-apparaten verzenden met Firebase](notification-hubs-android-push-notification-google-fcm-get-started.md)voor meer informatie over het pushen van meldingen naar een Android-toepassing.

## <a name="windows-push-notification-service"></a>Windows Push Notification Service

Windows Push Notification Service (WNS) instellen:

1. Selecteer **Windows (WNS)** in de Azure-portal op de pagina **Meldingshub** in het linkermenu.
2. Voer waarden in voor **Pakket-SID** en **Beveiligingssleutel**.
3. Selecteer **Opslaan**.

   ![Schermafbeelding van de vakken Package SID en Security Key](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

Zie Meldingen [naar UWP-apps verzenden met Azure Notification Hubs](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)voor meer informatie.

## <a name="microsoft-push-notification-service-for-windows-phone"></a>Microsoft Push Notification Service voor Windows Phone

Ga als lidvan de Microsoft Push Notification Service (MPNS) voor Windows Phone:

1. Selecteer **Windows Phone (MPNS)** in de Azure-portal op de pagina **Meldingshub** in het linkermenu.
1. Niet-geverifieerde of geverifieerde pushmeldingen inschakelen:

   a. Als u niet-geverifieerde pushmeldingen wilt inschakelen, selecteert u **Niet-geverifieerde push** > **opslaan inschakelen**.

      ![Schermafbeelding van het inschakelen van niet-geverifieerde pushmeldingen](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

   b. Ga als u geverifieerde pushmeldingen in:
      * Selecteer op de werkbalk **Certificaat uploaden**.
      * Selecteer het bestandspictogram en selecteer vervolgens het certificaatbestand.
      * Voer het wachtwoord voor het certificaat in.
      * Selecteer **OK**.
      * Selecteer op de pagina **Windows Phone (MPNS)** de optie **Opslaan**.

Zie [Pushmeldingen naar Windows Phone-apps voor](notification-hubs-windows-mobile-push-notifications-mpns.md)meer informatie met Behulp van Meldingenhubs .

## <a name="baidu-android-china"></a>Baidu (Android China)

Pushmeldingen instellen voor Baidu:

1. Selecteer In de Azure-portal op de pagina **Meldingshub** de optie **Baidu (Android China)** in het linkermenu.
2. Voer de **Api-sleutel** in die u hebt verkregen van de Baidu-console in het Baidu-cloudpushproject.
3. Voer de **geheime sleutel** in die u hebt verkregen van de Baidu-console in het Baidu-cloudpushproject.
4. Selecteer **Opslaan**.

    ![Schermafbeelding van Notification Hubs die de Baidu -configuratie (Android China) toont voor pushmeldingen](./media/notification-hubs-baidu-get-started/AzureNotificationServicesBaidu.png)

Wanneer u deze stappen uitvoert, geeft een waarschuwing aan dat de meldingshub is bijgewerkt. De knop **Opslaan** kan niet worden gekozen.

Zie [Aan de slag met Meldingenhubs met Baidu](notification-hubs-baidu-china-android-notifications-get-started.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u geleerd hoe u systeeminstellingen voor platformmeldingen configureert voor een meldingshub in de Azure-portal.

Zie de volgende tutorials voor meer informatie over het pushen van meldingen naar verschillende platforms:

-[Pushmeldingen naar iOS-apparaten met behulp van Notification Hubs en APNS](notification-hubs-ios-apple-push-notification-apns-get-started.md)
-[Pushmeldingen naar Android-apparaten met behulp van Notification Hubs en Google FCM](notification-hubs-android-push-notification-google-fcm-get-started.md)
-[Pushmeldingen naar een UWP-app die wordt uitgevoerd op een Windows-apparaat](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)
-[Pushmeldingen naar een Windows Phone 8-app met behulp van MPNS](notification-hubs-windows-mobile-push-notifications-mpns.md)
-[Pushmeldingen met behulp van Notification Hubs en Baidu cloud push](notification-hubs-baidu-china-android-notifications-get-started.md)
