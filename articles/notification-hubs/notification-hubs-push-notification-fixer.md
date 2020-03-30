---
title: Gedeisdeer de melding in Azure Notification Hubs diagnosticeren
description: Meer informatie over het diagnosticeren van veelvoorkomende problemen met gevallen meldingen in Azure Notification Hubs.
services: notification-hubs
documentationcenter: Mobile
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: b5c89a2a-63b8-46d2-bbed-924f5a4cce61
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: NA
ms.devlang: multiple
ms.topic: article
ms.date: 02/25/2020
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 04/04/2019
ms.openlocfilehash: 1f3c16e6fe1855cf7882d83e620c70d15ce3cb92
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77657531"
---
# <a name="diagnose-dropped-notifications-in-azure-notification-hubs"></a>Gedeisdeer de melding in Azure Notification Hubs diagnosticeren

Een veelvoorkomende vraag over Azure Notification Hubs is hoe u problemen oplossen wanneer meldingen van een toepassing niet worden weergegeven op clientapparaten. Klanten willen weten waar en waarom meldingen zijn verwijderd en hoe ze het probleem kunnen oplossen. In dit artikel wordt aangegeven waarom meldingen mogelijk worden verwijderd of niet door apparaten worden ontvangen. Het legt ook uit hoe de oorzaak te bepalen.

Het is essentieel om eerst te begrijpen hoe Meldingenhubs meldingen naar een apparaat pusht.

![Architectuur van Notificatiehubs][0]

In een typische berichtstroom voor verzenden wordt het bericht verzonden van de *back-end* van de toepassing naar Meldingshubs. Notification Hubs verwerkt alle registraties. Het houdt rekening met de geconfigureerde tags en tagexpressies om doelen te bepalen. Doelen zijn de registraties die de pushmelding moeten ontvangen. Deze registraties kunnen betrekking hebben op een van onze ondersteunde platforms: Android, Baidu (Android-apparaten in China), Fire OS (Amazon) iOS, Windows en Windows Phone.

Met de doelen vastgesteld, Notification Hubs duwt meldingen naar de *push-melding service* voor het apparaat platform. Voorbeelden hiervan zijn de Apple Push Notification-service (APNs) voor iOS en macOS en Firebase Cloud Messaging (FCM) voor Android-apparaten. Notification Hubs pusht meldingen verdeeld over meerdere batches van registraties. Deze wordt geverifieerd met de desbetreffende pushmeldingsservice, op basis van de referenties die u in de Azure-portal hebt ingesteld onder **Meldingshub configureren.** De pushmeldingsservice stuurt de meldingen vervolgens door naar de desbetreffende *clientapparaten.*

De laatste etappe van de melding levering is tussen de push-melding van het platform dienst en het apparaat. De levering van meldingen kan mislukken in een van de vier fasen van het pushmeldingsproces (client, back-end van toepassingen, meldingshubs en de pushmeldingsservice van het platform). Zie Overzicht van [meldingenhubs]voor meer informatie over de architectuur van Notification Hubs.

Een fout bij het leveren van meldingen kan optreden tijdens de eerste test/faseringsfase. Meldingen in dit stadium kunnen duiden op een configuratieprobleem. Als er geen meldingen worden geleverd in de productie, kunnen sommige of alle meldingen worden verwijderd. In dit geval wordt een dieper probleem met het toepassings- of berichtenpatroon aangegeven.

In de volgende sectie wordt gekeken naar scenario's waarin meldingen kunnen worden verwijderd, variërend van gemeenschappelijk tot zeldzaam.

## <a name="notification-hubs-misconfiguration"></a>Foutconfiguratie meldingshubs

Als u meldingen wilt verzenden naar de desbetreffende pushmeldingsservice, moeten notification hubs zichzelf verifiëren in het kader van uw toepassing. U moet een ontwikkelaarsaccount aanmaken met de meldingsservice van het doelplatform (Microsoft, Apple, Google, enz.). Vervolgens moet u uw toepassing registreren bij het besturingssysteem waar u een token of sleutel krijgt die u gebruikt om met de doel-PNS te werken.

U moet platformreferenties toevoegen aan de Azure-portal. Als er geen meldingen het apparaat bereiken, is de eerste stap om ervoor te zorgen dat de juiste referenties zijn geconfigureerd in Meldingenhubs. De referenties moeten overeenkomen met de toepassing die is gemaakt onder een platformspecifiek ontwikkelaarsaccount.

Zie Aan [de slag met Azure Notification Hubs]voor stapsgewijze instructies om dit proces te voltooien.

Hier zijn enkele veelvoorkomende verkeerde configuraties om te controleren op:

### <a name="notification-hub-name-location"></a>Locatie van de naam van de meldingshub

Zorg ervoor dat de naam van uw meldingshub (zonder typefouten) op elk van deze locaties hetzelfde is:

* Waar u zich registreert bij de klant
* Waar u meldingen van de back-end verzendt
* Waar u de referenties van de pushmeldingsservice hebt geconfigureerd

Zorg ervoor dat u de juiste configuratietekenreeksen voor gedeelde toegangshandtekeningen gebruikt op de client en de back-end van de toepassing. Over het algemeen moet u **DefaultListenSharedAccessSignature** gebruiken op de client en **DefaultFullSharedAccessSignature** op de back-end van de toepassing. Hiermee worden machtigingen verleend om meldingen naar meldingshubs te verzenden.

### <a name="apn-configuration"></a>APN-configuratie

U moet twee verschillende hubs onderhouden: een voor productie en een voor het testen. U moet het certificaat dat u in een sandbox-omgeving gebruikt uploaden naar een aparte hub dan het certificaat/de hub die u in de productie gebruikt. Probeer geen verschillende soorten certificaten naar dezelfde hub te uploaden. Dit zal meldingenfouten veroorzaken.

Als u per ongeluk verschillende soorten certificaten uploadt naar dezelfde hub, moet u de hub verwijderen en opnieuw beginnen met een nieuwe hub. Als u om de een of andere reden de hub niet verwijderen, moet u op zijn minst alle bestaande registraties uit de hub verwijderen.

### <a name="fcm-configuration"></a>FCM-configuratie

1. Controleer of de *serversleutel* die u van Firebase hebt verkregen overeenkomt met de serversleutel die u hebt geregistreerd in de Azure-portal.

   ![Firebase-serversleutel][3]

2. Controleer of u **Project-ID** op de client hebt geconfigureerd. U de waarde voor **Project ID** verkrijgen via het Firebase-dashboard.

   ![Firebase Project ID][1]

## <a name="application-issues"></a>Toepassingsproblemen

### <a name="tags-and-tag-expressions"></a>Tags en tagexpressies

Als je tags of tagexpressies gebruikt om je doelgroep te segmenteren, is het mogelijk dat wanneer je de melding verzendt, er geen doel wordt gevonden. Deze fout is gebaseerd op de opgegeven tags of tagexpressies in uw verzendoproep.

Controleer uw registraties om ervoor te zorgen dat de tags overeenkomen wanneer u een melding verzendt. Controleer vervolgens het meldingsbewijs van alleen de clients die deze registraties hebben.

Stel dat al uw registraties met Notification Hubs de tag 'Politiek' gebruiken. Als u vervolgens een melding stuurt met de tag 'Sport', wordt de melding niet naar een apparaat verzonden. Een complexe aanvraag kan betrekking hebben op tagexpressies waarbij u zich hebt geregistreerd met 'Tag A' *of* 'Tag B', maar u hebt 'Tag A && Tag B' getarget. De zelfdiagnose tips sectie later in het artikel laat u zien hoe u uw registraties en hun tags te herzien.

### <a name="template-issues"></a>Sjabloonproblemen

Als u sjablonen gebruikt, moet u ervoor zorgen dat u de richtlijnen volgt die in [Sjablonen zijn]beschreven.

### <a name="invalid-registrations"></a>Ongeldige registraties

Als de meldingshub correct is geconfigureerd en tags of tagexpressies correct zijn gebruikt, worden geldige doelen gevonden. Meldingen moeten naar deze doelen worden verzonden. Notification Hubs vuurt vervolgens meerdere verwerkingsbatches parallel af. Elke batch stuurt berichten naar een reeks registraties.

> [!NOTE]
> Omdat Notification Hubs batches parallel verwerkt, is de volgorde waarin de meldingen worden geleverd niet gegarandeerd.

Notification Hubs is geoptimaliseerd voor een "most-once" berichtleveringsmodel. We proberen deduplicatie te ontdubbelen, zodat er geen meldingen meer dan één keer naar een apparaat worden geleverd. Registraties worden gecontroleerd om ervoor te zorgen dat slechts één bericht per apparaat-id wordt verzonden voordat het naar de pushmeldingsservice wordt verzonden.

Elke batch wordt verzonden naar de push-meldingsservice, die op zijn beurt de registraties accepteert en valideert. Tijdens dit proces is het mogelijk dat de pushmeldingsservice een fout detecteert met een of meer registraties in een batch. De pushmeldingsservice retourneert vervolgens een fout in Meldingenhubs en het proces stopt. De pushmeldingsservice laat die batch volledig vallen. Dit geldt vooral voor APNs, dat een TCP-streamprotocol gebruikt.

In dit geval wordt de foutregistratie uit de database verwijderd. Vervolgens proberen we de meldingslevering opnieuw uit te leveren voor de rest van de apparaten in die batch.

Als u meer foutinformatie wilt krijgen over de mislukte leveringspoging tegen een registratie, u de API's voor de rest van de [meldingsknooppunten per berichttelemetrie: telemetrie](https://docs.microsoft.com/rest/api/notificationhubs/get-notification-message-telemetry) van het bericht en [pns-feedback](https://msdn.microsoft.com/library/azure/mt705560.aspx)ontvangen. Zie het voorbeeld [REST verzenden](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/SendRestExample/)voor voorbeeldcode .

## <a name="push-notification-service-issues"></a>Problemen met pushmeldingen

Nadat de pushmeldingsservice de melding heeft ontvangen, wordt de melding naar het apparaat geleverd. Op dit moment heeft Notification Hubs geen controle over de levering van de melding aan het apparaat.

Omdat platformmeldingsservices robuust zijn, hebben meldingen de neiging om apparaten binnen een paar seconden te bereiken. Als de pushmeldingsservice beperking is, past Notification Hubs een exponentiële back-offstrategie toe. Als de pushmeldingsservice 30 minuten onbereikbaar blijft, is er een beleid dat verloopt en de berichten permanent laat vallen.

Als een pushmeldingsservice probeert een melding te leveren, maar het apparaat offline is, wordt de melding opgeslagen door de pushmeldingsservice. Het wordt slechts voor een beperkte periode opgeslagen. De melding wordt naar het apparaat geleverd wanneer het apparaat beschikbaar komt.

Elke app slaat slechts één recente melding op. Als er meerdere meldingen worden verzonden terwijl een apparaat offline is, zorgt elke nieuwe melding ervoor dat de laatste wordt verwijderd. Het bijhouden van alleen de nieuwste melding wordt *coalescing* in APNs genoemd en *instort* in FCM. (FCM gebruikt een instortende sleutel.) Wanneer het apparaat lange tijd offline blijft, worden meldingen die voor het apparaat zijn opgeslagen, verwijderd. Zie [APNs-overzicht en] [Over FCM-berichten]voor meer informatie.

Met Notification Hubs u een coalescing-sleutel doorgeven via een HTTP-header met behulp van de generieke SendNotification API. Voor de .NET SDK gebruikt u `SendNotificationAsync`bijvoorbeeld . De SendNotification API neemt ook HTTP-headers die worden doorgegeven aan de desbetreffende push-meldingsservice.

## <a name="self-diagnosis-tips"></a>Zelfdiagnose tips

Hier volgen paden om de hoofdoorzaak van gevallen meldingen in meldingenhubs te diagnosticeren.

### <a name="verify-credentials"></a>Referenties verifiëren

#### <a name="push-notification-service-developer-portal"></a>Ontwikkelaarsportal voor pushmeldingenservice

Verificatiereferenties verifiëren in de respectievelijke push-notification service developer portal (APNs, FCM, Windows Notification Service, enzovoort). Zie [Zelfstudie: Meldingen verzenden naar universele Windows Platform-apps met Azure Notification Hubs](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification)voor meer informatie.

#### <a name="azure-portal"></a>Azure Portal

Als u de referenties wilt bekijken en matchen met de referenties die u hebt verkregen van de ontwikkelaarsportal voor pushmeldingenservice, gaat u naar het tabblad **Toegangsbeleid** in de Azure-portal.

![Azure-portaltoegangsbeleid][4]

### <a name="verify-registrations"></a>Registraties verifiëren

#### <a name="visual-studio"></a>Visual Studio

In Visual Studio u verbinding maken met Azure via Server Explorer om meerdere Azure-services te bekijken en te beheren, waaronder Meldingenhubs. Deze snelkoppeling is vooral handig voor uw ontwikkel-/testomgeving.

![Visual Studio Server Explorer][9]

![Server Explorer](media/notification-hubs-push-notification-fixer/vsserverexplorer2.png)

U alle registraties in uw hub bekijken en beheren. De registraties kunnen worden gecategoriseerd op platform-, native- of sjabloonregistratie, tag, pushmeldingsservice-id, registratie-id en vervaldatum. U ook een registratie bewerken op deze pagina. Het is vooral handig voor het bewerken van tags.

Klik met de rechtermuisknop op de meldingshub in **Server Explorer**en selecteer **Diagnosticeren**. 

![Visual Studio Server Explorer: menu Diagnose](./media/notification-hubs-push-notification-fixer/diagnose-menu.png)

U ziet de volgende pagina:

![Visual Studio: Pagina Diagnose](./media/notification-hubs-push-notification-fixer/diagnose-page.png)

Ga naar de pagina **Apparaatregistraties:**

![Visual Studio: Apparaatregistraties](./media/notification-hubs-push-notification-fixer/VSRegistrations.png)

U de pagina **Testverzenden** gebruiken om een testmeldingbericht te verzenden:

![Visual Studio: Send testen](./media/notification-hubs-push-notification-fixer/test-send-vs.png)

> [!NOTE]
> Gebruik Visual Studio om registraties alleen te bewerken tijdens de ontwikkeling/test en met een beperkt aantal registraties. Als u uw registraties in bulk moet bewerken, u overwegen de export- en importregistratiefunctionaliteit te gebruiken die is beschreven in [Hoe: Registraties in bulk exporteren en wijzigen.](https://msdn.microsoft.com/library/dn790624.aspx)

#### <a name="service-bus-explorer"></a>Service Bus Explorer

Veel klanten gebruiken [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer) om hun meldingshubs te bekijken en te beheren. Service Bus Explorer is een open-source project. 

### <a name="verify-message-notifications"></a>Berichtmeldingen verifiëren

#### <a name="azure-portal"></a>Azure Portal

Als u een testmelding naar uw klanten wilt verzenden zonder dat een back-up van een service wordt beëindigd, selecteert u Onder **ONDERSTEUNING + PROBLEEMOPLOSSING**de optie **Verzenden testen**.

![Functies verzenden testen in Azure][7]

#### <a name="visual-studio"></a>Visual Studio

U ook testmeldingen verzenden vanuit Visual Studio.

![Functies verzenden testen in Visual Studio][10]

Zie de volgende artikelen voor meer informatie over het gebruik van meldingshubs met Visual Studio Server Explorer:

* [Apparaatregistraties voor meldingshubs weergeven](https://docs.microsoft.com/previous-versions/windows/apps/dn792122(v=win.10))
* [Diepe duik: Visual Studio 2013 Update 2 RC en Azure SDK 2.3]
* [Aankondiging van de release van Visual Studio 2013 Update 3 en Azure SDK 2.4]

### <a name="debug-failed-notifications-and-review-notification-outcome"></a>Foutopsporing mislukt meldingen en controle melding resultaat

#### <a name="enabletestsend-property"></a>InschakelenTestSend, eigenschap

Wanneer u een melding verzendt via Notification Hubs, wordt de melding in eerste instantie in de wachtrij geplaatst. Notification Hubs bepaalt de juiste doelen en stuurt de melding vervolgens naar de pushmeldingsservice. Als u de REST API of een van de CLIENT-SDK's gebruikt, betekent het retourneren van uw oproep alleen dat het bericht in de wachtrij staat met Meldinghubs. Het geeft geen inzicht in wat er gebeurde toen Notification Hubs de melding uiteindelijk naar de pushmeldingsservice stuurden.

Als uw melding niet op het clientapparaat wordt ontvangen, is er mogelijk een fout opgetreden toen Notification Hubs deze probeerden te leveren aan de pushmeldingsservice. De laadbelastingsgrootte kan bijvoorbeeld hoger zijn dan het maximum dat is toegestaan door de pushmeldingsservice of de referenties die zijn geconfigureerd in meldingshubs kunnen ongeldig zijn.

Als u inzicht wilt krijgen in fouten in pushmeldingsservice, u de eigenschap [EnableTestSend] gebruiken. Deze eigenschap wordt automatisch ingeschakeld wanneer u testberichten verzendt vanaf de portal of Visual Studio-client. U deze eigenschap gebruiken om gedetailleerde foutopsporingsinformatie te zien en ook via API's. Momenteel u het gebruiken in de .NET SDK. Het zal uiteindelijk worden toegevoegd aan alle client SDKs.

Als u `EnableTestSend` de eigenschap wilt gebruiken met de REST-aanroep, wordt een querytekenreeksparameter toegevoegd die *test* wordt genoemd aan het einde van uw verzendgesprek. Bijvoorbeeld:

```text
https://mynamespace.servicebus.windows.net/mynotificationhub/messages?api-version=2013-10&test
```

#### <a name="net-sdk-example"></a>Voorbeeld van .NET SDK

Hier is een voorbeeld van het gebruik van de .NET SDK om een native pop-up (toast) melding te verzenden:

```csharp
NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName);
var result = await hub.SendWindowsNativeNotificationAsync(toast);
Console.WriteLine(result.State);
```

Aan het einde van `result.State` de `Enqueued`uitvoering, gewoon staten . De resultaten geven geen inzicht in wat er met uw pushmelding is gebeurd.

Vervolgens u `EnableTestSend` de eigenschap Booleaanse gebruiken. Gebruik `EnableTestSend` de eigenschap terwijl `NotificationHubClient` u initialiseert om een gedetailleerde status te krijgen over fouten in de pushmeldingsservice die optreden wanneer de melding wordt verzonden. Het verzenden van oproepen neemt extra tijd in beslag omdat het eerst Meldingshubs nodig heeft om de melding aan de pushmeldingsservice te leveren.

```csharp
    bool enableTestSend = true;
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName, enableTestSend);

    var outcome = await hub.SendWindowsNativeNotificationAsync(toast);
    Console.WriteLine(outcome.State);

    foreach (RegistrationResult result in outcome.Results)
    {
        Console.WriteLine(result.ApplicationPlatform + "\n" + result.RegistrationId + "\n" + result.Outcome);
    }
```

#### <a name="sample-output"></a>Voorbeelduitvoer

```text
DetailedStateAvailable
windows
7619785862101227384-7840974832647865618-3
The Token obtained from the Token Provider is wrong
```

Dit bericht geeft aan dat de referenties die zijn geconfigureerd in meldingshubs ongeldig zijn of dat er een probleem is met de registraties in de hub. Verwijder deze registratie en laat de klant de registratie opnieuw maken voordat u het bericht verzendt.

> [!NOTE]
> Het gebruik `EnableTestSend` van de woning wordt zwaar beperkt. Gebruik deze optie alleen in een ontwikkel-/testomgeving en met een beperkte set registraties. Foutopsporingsmeldingen worden verzonden naar slechts 10 apparaten. Er is ook een limiet op het verwerken van foutopsporing stuurt, op 10 per minuut.

### <a name="review-telemetry"></a>Telemetrie bekijken

#### <a name="azure-portal"></a>Azure Portal

In de portal u een snel overzicht krijgen van alle activiteiten in uw meldingshub.

1. Op het tabblad **Overzicht** ziet u een geaggregeerde weergave van registraties, meldingen en fouten per platform.

   ![Overzichtsdashboard van Meldingenhubs][5]

2. Op het tabblad **Monitor** u veel andere platformspecifieke statistieken toevoegen voor een diepere blik. U specifiek kijken naar fouten die worden geretourneerd wanneer Notification Hubs de melding naar de pushmeldingsservice probeert te verzenden.

   ![Azure-portalactiviteitslogboek][6]

3. Begin met het controleren van **binnenkomende berichten,** **registratiebewerkingen**en **geslaagde meldingen**. Ga vervolgens naar het tabblad per platform om fouten te bekijken die specifiek zijn voor de pushmeldingsservice.

4. Als de verificatie-instellingen voor uw meldingshub onjuist zijn, wordt de **bericht-PNS-verificatiefout** weergegeven. Het is een goede indicatie om de push-notificatie service referenties te controleren.

#### <a name="programmatic-access"></a>Toegang op programmeerniveau

Zie [Programmatische toegang](https://docs.microsoft.com/previous-versions/azure/azure-services/dn458823(v=azure.100))voor meer informatie over programmatische toegang.

> [!NOTE]
> Verschillende telemetriegerelateerde functies, zoals het exporteren en importeren van registraties en telemetrietoegang via API's, zijn alleen beschikbaar op de standaardservicelaag. Als u deze functies probeert te gebruiken vanuit de servicelaag Gratis of Basic, ontvangt u een uitzonderingsbericht als u de SDK gebruikt. U krijgt een HTTP 403-fout (Verboden) als u de functies rechtstreeks van de REST-API's gebruikt.
>
> Als u telemetriegerelateerde functies wilt gebruiken, moet u eerst in de Azure-portal controleren of u de standaardservicelaag gebruikt.  

<!-- IMAGES -->
[0]: ./media/notification-hubs-push-notification-fixer/Architecture.png
[1]: ./media/notification-hubs-push-notification-fixer/FCMConfigure.png
[3]: ./media/notification-hubs-push-notification-fixer/FCMServerKey.png
[4]: ../../includes/media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png
[5]: ./media/notification-hubs-push-notification-fixer/PortalDashboard.png
[6]: ./media/notification-hubs-push-notification-fixer/PortalAnalytics.png
[7]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png
[8]: ./media/notification-hubs-push-notification-fixer/VSRegistrations.png
[9]: ./media/notification-hubs-push-notification-fixer/vsserverexplorer.png
[10]: ./media/notification-hubs-push-notification-fixer/VSTestNotification.png

<!-- LINKS -->
[Overzicht van meldingshubs]: notification-hubs-push-notification-overview.md
[Aan de slag met Azure Notification Hubs]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Sjablonen]: https://msdn.microsoft.com/library/dn530748.aspx
[Overzicht VAN APNs]: https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html
[Over FCM-berichten]: https://firebase.google.com/docs/cloud-messaging/concept-options
[Export and modify registrations in bulk]: https://msdn.microsoft.com/library/dn790624.aspx
[Service Bus Explorer code]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Explorer-f2abca5a
[View device registrations for notification hubs]: https://msdn.microsoft.com/library/windows/apps/xaml/dn792122.aspx
[Diepe duik: Visual Studio 2013 Update 2 RC en Azure SDK 2.3]: https://azure.microsoft.com/blog/2014/04/09/deep-dive-visual-studio-2013-update-2-rc-and-azure-sdk-2-3/#NotificationHubs
[Aankondiging van de release van Visual Studio 2013 Update 3 en Azure SDK 2.4]: https://azure.microsoft.com/blog/2014/08/04/announcing-release-of-visual-studio-2013-update-3-and-azure-sdk-2-4/
[TestSend inschakelen]: https://docs.microsoft.com/dotnet/api/microsoft.azure.notificationhubs.notificationhubclient.enabletestsend?view=azure-dotnet
[Programmatic telemetry access]: https://msdn.microsoft.com/library/azure/dn458823.aspx
