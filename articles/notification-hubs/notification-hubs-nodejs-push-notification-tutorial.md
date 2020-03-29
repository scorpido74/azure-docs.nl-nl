---
title: Pushmeldingen verzenden met Azure Notification Hubs en Node.js
description: Meer informatie over het gebruik van meldingshubs om pushmeldingen te verzenden vanuit een Node.js-toepassing.
keywords: pushnotificatie, pushnotificaties, node.js push, ios push
services: notification-hubs
documentationcenter: nodejs
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: ded4749c-6c39-4ff8-b2cf-1927b3e92f93
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 6e109c5a7f4911893c81c88ae84322fb962fff6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71213188"
---
# <a name="sending-push-notifications-with-azure-notification-hubs-and-nodejs"></a>Pushmeldingen verzenden met Azure Notification Hubs en Node.js

[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

## <a name="overview"></a>Overzicht

> [!IMPORTANT]
> U hebt een actief Azure-account nodig om deze zelfstudie te voltooien. Als u geen account hebt, maakt u in slechts een paar minuten een gratis proefaccount via de gratis proefversie van [Azure.](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-nodejs-how-to-use-notification-hubs)

In deze handleiding ziet u hoe u pushmeldingen verzendt met behulp van Azure Notification Hubs rechtstreeks vanuit een [Node.js-toepassing.](https://nodejs.org)

De scenario's die onder meer worden behandeld, zijn het verzenden van pushmeldingen naar toepassingen op de volgende platforms:

- Android
- iOS
- Universeel Windows-platform
- Windows Phone

## <a name="notification-hubs"></a>Notification Hubs

Azure Notification Hubs bieden een gebruiksvriendelijke, multi-platform, schaalbare infrastructuur voor het verzenden van pushmeldingen naar mobiele apparaten. Zie de pagina [Azure Notification Hubs](https://msdn.microsoft.com/library/windowsazure/jj927170.aspx) voor meer informatie over de service-infrastructuur.

## <a name="create-a-nodejs-application"></a>Een node.js-toepassing maken

De eerste stap in deze zelfstudie is het maken van een nieuwe lege Node.js-toepassing. Zie [Een Node.js-toepassing maken en implementeren voor][nodejswebsite]instructies voor het maken van een Node.js-toepassing naar Azure-website, [Node.js Cloud Service][Node.js Cloud Service] met Windows PowerShell of Website met [WebMatrix][webmatrix].

## <a name="configure-your-application-to-use-notification-hubs"></a>Uw toepassing configureren om meldingshubs te gebruiken

Als u Azure Notification Hubs wilt gebruiken, moet u het [Azure-pakket](https://www.npmjs.com/package/azure)Node.js downloaden en gebruiken, dat een ingebouwde set helperbibliotheken bevat die communiceren met de REST-services voor pushmeldingen.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Node Package Manager (NPM) gebruiken om het pakket te verkrijgen

1. Gebruik een command-line interface zoals **PowerShell** (Windows), **Terminal** (Mac) of **Bash** (Linux) en navigeer naar de map waar u uw lege toepassing hebt gemaakt.
2. Uitvoeren `npm install azure-sb` in het opdrachtvenster.
3. U de `ls` opdracht `dir` of opdracht `node_modules` handmatig uitvoeren om te controleren of er een map is gemaakt.
4. Zoek in die map het **azure-pakket,** dat de bibliotheken bevat die u nodig hebt om toegang te krijgen tot de meldingshub.

> [!NOTE]
> U meer informatie over het installeren van NPM op de officiële [NPM blog](https://blog.npmjs.org/post/85484771375/how-to-install-npm).

### <a name="import-the-module"></a>De module importeren
Voeg met behulp van een teksteditor `server.js` het volgende toe aan de bovenkant van het bestand van de toepassing:

```javascript
var azure = require('azure-sb');
```

### <a name="set-up-an-azure-notification-hub-connection"></a>Een Azure Notification Hub-verbinding instellen

Met `NotificationHubService` het object u werken met meldingshubs. Met de volgende `NotificationHubService` code wordt een `hubname`object gemaakt voor de meldingshub met de naam . Voeg het toe aan `server.js` de bovenkant van het bestand, na de instructie om de azure-module te importeren:

```javascript
var notificationHubService = azure.createNotificationHubService('hubname','connectionstring');
```

Verkrijg `connectionstring` de verbindingswaarde van de [Azure-portal] door de volgende stappen uit te voeren:

1. Klik in het linkernavigatiedeelvenster op **Bladeren**.
2. Selecteer **Meldingshubs**en zoek vervolgens de hub die u voor het voorbeeld wilt gebruiken. U verwijzen naar de [zelfstudie aan](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) de slag in de Windows Store als u hulp nodig hebt bij het maken van een nieuwe meldingshub.
3. Selecteer **Instellingen**.
4. Klik op **Toegangsbeleid**. U ziet zowel gedeelde als volledige toegangsverbindingstekenreeksen.

![Azure-portal - Meldingshubs](./media/notification-hubs-nodejs-how-to-use-notification-hubs/notification-hubs-portal.png)

> [!NOTE]
> U de verbindingstekenreeks ook ophalen met de cmdlet **Get-AzureSbNamespace** van [Azure PowerShell](/powershell/azureps-cmdlets-docs) of de opdracht **Azure SB-naamruimte** met de [Azure Command-Line Interface (Azure CLI).](../cli-install-nodejs.md)

## <a name="general-architecture"></a>Algemene architectuur

Het `NotificationHubService` object stelt de volgende objectinstanties bloot voor het verzenden van pushmeldingen naar specifieke apparaten en toepassingen:

- **Android** - `GcmService` gebruik het object, dat beschikbaar is op`notificationHubService.gcm`
- **iOS** - `ApnsService` gebruik het object, dat toegankelijk is op`notificationHubService.apns`
- **Windows Phone** - `MpnsService` gebruik het object, dat beschikbaar is op`notificationHubService.mpns`
- **Universal Windows Platform** `WnsService` - gebruik het object, dat beschikbaar is op`notificationHubService.wns`

### <a name="how-to-send-push-notifications-to-android-applications"></a>How to: Pushmeldingen naar Android-applicaties verzenden

Het `GcmService` object `send` biedt een methode die kan worden gebruikt om pushmeldingen naar Android-toepassingen te verzenden. De `send` methode accepteert de volgende parameters:

- **Tags** - de tag-id. Als er geen tag wordt opgegeven, wordt de melding naar alle clients verzonden.
- **Payload** - json van het bericht of ruwe string payload.
- **Callback** - de callback-functie.

Zie de [Payload-documentatie](https://distriqt.github.io/ANE-PushNotifications/m.FCM-GCM%20Payload)voor meer informatie over het payload-formaat.

De volgende code `GcmService` maakt gebruik `NotificationHubService` van de instantie blootgesteld door de om een push-melding te sturen naar alle geregistreerde clients.

```javascript
var payload = {
  data: {
    message: 'Hello!'
  }
};
notificationHubService.gcm.send(null, payload, function(error){
  if(!error){
    //notification sent
  }
});
```

### <a name="how-to-send-push-notifications-to-ios-applications"></a>How to: Pushmeldingen verzenden naar iOS-toepassingen

Net als bij Android-toepassingen `ApnsService` die `send` hierboven worden beschreven, biedt het object een methode die kan worden gebruikt om pushmeldingen naar iOS-toepassingen te verzenden. De `send` methode accepteert de volgende parameters:

- **Tags** - de tag-id. Als er geen tag wordt opgegeven, wordt de melding naar alle clients verzonden.
- **Payload** - het bericht JSON of string payload.
- **Callback** - de callback-functie.

Zie de sectie **Meldingpayload** van het document [Lokale en PushMeldingsprogrammering](https://developer.apple.com/library/ios/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/ApplePushService/ApplePushService.html) voor meer informatie over de payload-indeling.

De volgende code `ApnsService` gebruikt de `NotificationHubService` instantie die wordt blootgesteld door de om een waarschuwingsbericht te verzenden naar alle clients:

```javascript
var payload={
    alert: 'Hello!'
  };
notificationHubService.apns.send(null, payload, function(error){
  if(!error){
      // notification sent
  }
});
```

### <a name="how-to-send-push-notifications-to-windows-phone-applications"></a>Hoe: Pushmeldingen verzenden naar Windows Phone-toepassingen

Het `MpnsService` object `send` biedt een methode die kan worden gebruikt om pushmeldingen naar Windows Phone-toepassingen te verzenden. De `send` methode accepteert de volgende parameters:

- **Tags** - de tag-id. Als er geen tag wordt opgegeven, wordt de melding naar alle clients verzonden.
- **Payload** - de XML-payload van het bericht.
- **TargetName**  -  `toast` voor pop-upmeldingen. `token`voor tegelmeldingen.
- **NotificationClass** - De prioriteit van de melding. Zie de sectie **HTTP-kopelementen** van [pushmeldingen van een serverdocument](https://msdn.microsoft.com/library/hh221551.aspx) voor geldige waarden.
- **Opties** - optionele aanvraagkoppen.
- **Callback** - de callback-functie.

Voor een lijst `TargetName` `NotificationClass` met geldige en koptekstopties raadpleegt u de [pushmeldingen van een serverpagina.](https://msdn.microsoft.com/library/hh221551.aspx)

De volgende voorbeeldcode `MpnsService` gebruikt de `NotificationHubService` instantie die wordt blootgesteld door de om een pop-up push melding te verzenden:

```javascript
var payload = '<?xml version="1.0" encoding="utf-8"?><wp:Notification xmlns:wp="WPNotification"><wp:Toast><wp:Text1>string</wp:Text1><wp:Text2>string</wp:Text2></wp:Toast></wp:Notification>';
notificationHubService.mpns.send(null, payload, 'toast', 22, function(error){
  if(!error){
    //notification sent
  }
});
```

### <a name="how-to-send-push-notifications-to-universal-windows-platform-uwp-applications"></a>How to: Pushmeldingen verzenden naar UWP-toepassingen (Universal Windows Platform)

Het `WnsService` object `send` biedt een methode die kan worden gebruikt om pushmeldingen naar Universal Windows Platform-toepassingen te verzenden.  De `send` methode accepteert de volgende parameters:

- **Tags** - de tag-id. Als er geen tag wordt opgegeven, wordt de melding verzonden naar alle geregistreerde clients.
- **Payload** - de XML-bericht payload.
- **Type** - het meldingstype.
- **Opties** - optionele aanvraagkoppen.
- **Callback** - de callback-functie.

Zie [Pushmeldingsserviceaanvraag en antwoordkoppen](https://msdn.microsoft.com/library/windows/apps/hh465435.aspx)voor een lijst met geldige typen en aanvraagkoppen.

De volgende code `WnsService` gebruikt de `NotificationHubService` instantie die wordt blootgesteld door de om een pop-up push-melding naar een UWP-app te sturen:

```javascript
var payload = '<toast><visual><binding template="ToastText01"><text id="1">Hello!</text></binding></visual></toast>';
notificationHubService.wns.send(null, payload , 'wns/toast', function(error){
  if(!error){
      // notification sent
  }
});
```

## <a name="next-steps"></a>Volgende stappen

Met de bovenstaande voorbeeldfragmenten u eenvoudig service-infrastructuur bouwen om pushmeldingen te leveren aan een breed scala aan apparaten. Nu je de basisbeginselen van het gebruik van Notification Hubs met node.js hebt geleerd, volg je deze links om meer te weten te komen over hoe je deze mogelijkheden verder uitbreiden.

- Zie de MSDN-verwijzing voor [Azure Notification Hubs](https://msdn.microsoft.com/library/azure/jj927170.aspx).
- Ga naar de [Azure SDK for Node-repository] op GitHub voor meer voorbeelden en implementatiedetails.

[Azure SDK voor knooppunt]: https://github.com/WindowsAzure/azure-sdk-for-node
[Next Steps]: #nextsteps
[What are Service Bus Topics and Subscriptions?]: #what-are-service-bus-topics
[Create a Service Namespace]: #create-a-service-namespace
[Obtain the Default Management Credentials for the Namespace]: #obtain-default-credentials
[Create a Node.js Application]: #Create_a_Nodejs_Application
[Configure Your Application to Use Service Bus]: #Configure_Your_Application_to_Use_Service_Bus
[How to: Create a Topic]: #How_to_Create_a_Topic
[How to: Create Subscriptions]: #How_to_Create_Subscriptions
[How to: Send Messages to a Topic]: #How_to_Send_Messages_to_a_Topic
[How to: Receive Messages from a Subscription]: #How_to_Receive_Messages_from_a_Subscription
[How to: Handle Application Crashes and Unreadable Messages]: #How_to_Handle_Application_Crashes_and_Unreadable_Messages
[How to: Delete Topics and Subscriptions]: #How_to_Delete_Topics_and_Subscriptions
[1]: #Next_Steps
[Topic Concepts]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-topics-01.png
[image]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-03.png
[2]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-04.png
[3]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-05.png
[4]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-06.png
[5]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-07.png
[SqlFilter.SqlExpression]: https://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
[Azure Service Bus Notification Hubs]: https://msdn.microsoft.com/library/windowsazure/jj927170.aspx
[SqlFilter]: https://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.aspx
[Web Site with WebMatrix]: /develop/nodejs/tutorials/web-site-with-webmatrix/
[Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Previous Management Portal]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/previous-portal.png
[nodejswebsite]: https://docs.microsoft.com/azure/app-service/app-service-web-get-started-nodejs
[webmatrix]: https://docs.microsoft.com/aspnet/web-pages/videos/introduction/create-a-website-using-webmatrix
[Node.js Cloud Service with Storage]: /develop/nodejs/tutorials/web-app-with-storage/
[Node.js Web Application with Storage]: /develop/nodejs/tutorials/web-site-with-storage/
[Azure-portal]: https://portal.azure.com
