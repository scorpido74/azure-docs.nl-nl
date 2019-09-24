---
title: Wat is Azure Notification Hubs?
description: Leer hoe u mogelijkheden voor pushmeldingen toevoegt met Azure Notification Hubs.
author: sethmanheim
manager: femila
editor: jwargo
services: notification-hubs
documentationcenter: ''
ms.assetid: fcfb0ce8-0e19-4fa8-b777-6b9f9cdda178
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: overview
ms.custom: mvc
ms.date: 04/30/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 04/30/2019
ms.openlocfilehash: f92c9ac6942bfad8df57bb3887d1ba5b7c7190af
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/24/2019
ms.locfileid: "71213103"
---
# <a name="what-is-azure-notification-hubs"></a>Wat is Azure Notification Hubs?

Azure Notification Hubs biedt een eenvoudig te gebruiken en uitgeschaalde push-engine waarmee u vanuit elke back-end (cloud of on-premises) meldingen kunt verzenden naar ieder platform (iOS, Android, Windows, Kindle, Baidu, enzovoort). Notification Hubs is zeer geschikt voor zowel bedrijfs- als consumentenscenario's. Enkele voorbeelden:

- Meldingen van belangrijk nieuws met een lage latentie verzenden naar miljoenen gebruikers.
- Coupons op basis van locatie verzenden naar geïnteresseerde gebruikerssegmenten.
- Meldingen voor evenementen verzenden naar gebruikers of groepen voor bijvoorbeeld media-, sport- en financiële toepassingen.
- Aanbiedingen pushen naar toepassingen om klanten te benaderen en werven.
- Gebruikers van bedrijfsgebeurtenissen op de hoogte stellen, zoals nieuwe berichten en werkitems.
- Codes verzenden voor meervoudige verificatie.

## <a name="what-are-push-notifications"></a>Wat zijn pushmeldingen?

Push meldingen zijn een vorm van app-to-User-communicatie waarbij gebruikers van mobiele apps op de hoogte worden gebracht van bepaalde gewenste informatie, meestal in een pop-up of dialoog venster op een mobiel apparaat. Gebruikers kiezen het bericht meestal weer te geven of te verwijderen. Als u de voormalige selecteert, wordt de mobiele toepassing geopend die de melding heeft verstrekt. Sommige meldingen worden achter de schermen op de achtergrond geplaatst, zodat de app achter de schermen kan worden verwerkt en beslist wat er moet gebeuren.

Pushmeldingen zijn essentieel voor consumenten-apps om de betrokkenheid en het gebruik te vergroten. Voor zakelijke apps zijn ze belangrijk om actuele bedrijfsgegevens te communiceren. Het is de beste communicatie tussen gebruikers omdat het energie-efficiënt is voor mobiele apparaten, flexibel voor de afzenders van meldingen en beschikbaar is als de bijbehorende toepassingen niet actief zijn.

Zie de volgende onderwerpen voor meer informatie over pushmeldingen voor enkele populaire platforms:

- [Android](https://developer.android.com/guide/topics/ui/notifiers/notifications.html)
- [iOS](https://developer.apple.com/notifications/)
- [Windows](https://msdn.microsoft.com/library/windows/apps/hh779725.aspx)

## <a name="how-push-notifications-work"></a>Hoe werken pushmeldingen?

Pushmeldingen worden bezorgd via platformspecifieke infrastructuren, *PNS (Platform Notification System)* genoemd. Ze bieden essentiële pushfunctionaliteit voor het bezorgen van een bericht op een apparaat met een opgegeven ingang, en hebben geen algemene interface. Als u een melding wilt verzenden naar alle klanten in de Android-, iOS-en Windows-versies van een app, moet de ontwikkelaar afzonderlijke werken met Apple Push Notification Service (APNS), Firebase Cloud Messa ging (FCM) en Windows Notification Service (WNS).

Dit zijn de algemene stappen voor het verzenden van pushberichten:

1. Een toepassing bepaalt dat deze melding wil ontvangen, zodat deze contact opneemt met PNS voor het doel platform waarop de app wordt uitgevoerd en die een unieke en tijdelijke push ingang aanvraagt. Het ingangs type is afhankelijk van het systeem (bijvoorbeeld WNS maakt gebruik van Uri's terwijl APNS tokens gebruikt).
2. De client-app slaat deze ingang op in de back-end van de app of in de provider.
3. Voor het verzenden van een push melding neemt de back-end van de app contact op met de PNS met behulp van de ingang voor een specifieke client-app.
4. De PNS stuurt de melding door naar het apparaat dat is opgegeven door de ingang.

![Werkstroom voor pushmeldingen](./media/notification-hubs-overview/registration-diagram.png)

## <a name="the-challenges-of-push-notifications"></a>Aandachtspunten voor pushmeldingen

Platform Notification System zijn krachtig. App-ontwikkelaars moeten echter zelfs voor meer algemene scenario’s voor pushmeldingen nog heel wat werk verrichten om deze te implementeren, bijvoorbeeld voor het broadcasten van pushmeldingen naar gesegmenteerde gebruikers.

Pushmeldingen vereisen een complexe infrastructuur die geen verband houdt met de voornaamste bedrijfslogica van de toepassing. Enkele aandachtspunten voor de infrastructuur:

- **Platformafhankelijkheid**
  - De back-end vereist complexe en moeilijk te onderhouden platform afhankelijke logica voor het verzenden van meldingen naar apparaten op verschillende platforms, aangezien PNSes niet Unified zijn.
- **Schalen**
  - Volgens de PNS-richtlijnen moeten apparaattokens steeds worden vernieuwd als de app wordt gestart. De back-end behandelt een grote hoeveelheid verkeer en toegang tot data bases om de tokens up-to-date te houden. Wanneer het aantal apparaten groeit tot honderden, duizenden of miljoenen, zijn de kosten voor het maken en onderhouden van deze infra structuur enorm.
  - De meeste Platform Notification Systems bieden geen ondersteuning voor broadcasten naar meerdere apparaten. Om één bericht te broadcasten naar een miljoen apparaten, zijn een miljoen aanroepen naar de Platform Notification Systems nodig. Het is zeer lastig om een dergelijke hoeveelheid verkeer met een minimale latentie te schalen.
- **Routering**
  - Hoewel Platform Notification Systems een manier bieden om berichten naar apparaten te verzenden, worden de meeste app-meldingen gericht op bepaalde gebruikers of belangengroepen. In de back-end moet een register worden bijgehouden om apparaten te koppelen aan belangengroepen, gebruikers, eigenschappen, enzovoort. Deze overhead verlengt de implementatietijd en verhoogt de onderhoudskosten van een app.

## <a name="why-use-azure-notification-hubs"></a>Waarom kiezen voor Azure Notification Hubs?

Notification Hubs elimineert alle complexen die zijn gekoppeld aan het pushen van meldingen op uw app-back-end. De uitgeschaalde infrastructuur ondersteunt meerdere platforms, waardoor er minder push-gerelateerde code nodig is en de back-end kan worden vereenvoudigd. Met Notification Hubs hoeven apparaten alleen maar hun PNS-ingangen te registreren bij een hub, terwijl de back-end berichten verstuurt naar gebruikers of belangengroepen, zoals wordt weergegeven in de volgende afbeelding:

![Schema van Notification Hubs](./media/notification-hubs-overview/notification-hub-diagram.png)

Notification Hubs is een gebruiksklare push-engine met de volgende voordelen:

- **Platformoverschrijdend**
  - Ondersteuning voor alle belangrijke push-platforms, waaronder iOS, Android, Windows, en Kindle en Baidu.
  - Een algemene interface om te pushen naar alle platforms in platformspecifieke of platformonafhankelijk indelingen zonder platformspecifieke taken.
  - Beheer van apparaatingang op één plaats.
- **Ondersteuning voor verschillende back-ends**
  - Cloud of on-premises
  - .NET, node. js, Java, Python, enzovoort.
- **Veel verschillende bezorgingsmethoden**
  - Broadcasten naar een of meer platforms: U kunt direct met één API-aanroep naar miljoenen apparaten op verschillende platforms uitzenden.
  - Pushen naar apparaat: U kunt meldingen op afzonderlijke apparaten richten.
  - Pushen naar gebruiker: Met de functies Tags en sjablonen kunt u alle platformoverschrijdende apparaten van een gebruiker bereiken.
  - Pushen naar segment met dynamische Labels: Met de functie Tags kunt u apparaten segmenteren en naar hen pushen op basis van uw behoeften, of u nu naar één segment of een expressie van segmenten verzendt (bijvoorbeeld actief en leven in Seattle geen nieuwe gebruiker). U bent niet beperkt tot pub sub, maar kunt tags van apparaten altijd en overal bijwerken.
  - Gelokaliseerde push: De functie sjablonen helpt lokalisatie te behaalen zonder de back-end-code te beïnvloeden.
  - Silent push: U kunt het push-to-pull-patroon inschakelen door Silent meldingen naar apparaten te verzenden en ze te activeren om bepaalde Pulls of acties te volt ooien.
  - Geplande push: U kunt plannen voor het verzenden van meldingen op elk gewenst moment.
  - Directe push: U kunt het registreren van apparaten met de Notification Hubs-service en rechtstreeks batch pushen overs laan naar een lijst met apparaat-ingangen.
  - Aangepaste push: Met de variabelen voor het pushen van apparaten kunt u apparaatspecifieke persoonlijke push meldingen verzenden met aangepaste sleutel-waardeparen.
- **Uitgebreide telemetrie**
  - Er zijn via Azure Portal en programmacode telemetriegegevens beschikbaar voor pushen, apparaten, fouten en bewerkingen.
  - Via de functie voor het bijhouden van telemetrie per bericht kunt u elk pushbericht volgen vanaf de eerste aanvraag tot het moment dat de Notification Hubs-service de pushberichten via een batchverwerking verstuurt.
  - Met de functie Feedback van Platform Notification System wordt alle feedback van Platform Notification Systems gecommuniceerd om te helpen bij foutopsporing.
- **Schaalbaarheid**
  - U kunt snel berichten versturen naar miljoenen apparaten zonder dat u de architectuur hoeft aan te passen of sharding hoeft toe te passen.
- **Beveiliging**
  - Shared Access Secret (SAS) of federatieve verificatie.

## <a name="next-steps"></a>Volgende stappen

Volg de zelf studie om aan de slag te gaan met het [maken en gebruiken van een notification hub: Push meldingen naar mobiele toepassingen](notification-hubs-android-push-notification-google-fcm-get-started.md).

[0]: ./media/notification-hubs-overview/registration-diagram.png
[1]: ./media/notification-hubs-overview/notification-hub-diagram.png

[How customers are using Notification Hubs]: https://azure.microsoft.com/services/notification-hubs
[Notification Hubs tutorials and guides]: https://azure.microsoft.com/documentation/services/notification-hubs
[iOS]: https://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started
[Android]: https://azure.microsoft.com/documentation/articles/notification-hubs-android-get-started
[Windows Universal]: https://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started
[Windows Phone]: https://azure.microsoft.com/documentation/articles/notification-hubs-windows-phone-get-started
[Kindle]: https://azure.microsoft.com/documentation/articles/notification-hubs-kindle-get-started
[Xamarin.iOS]: https://azure.microsoft.com/documentation/articles/partner-xamarin-notification-hubs-ios-get-started
[Xamarin.Android]: https://azure.microsoft.com/documentation/articles/partner-xamarin-notification-hubs-android-get-started
[Microsoft.WindowsAzure.Messaging.NotificationHub]: https://msdn.microsoft.com/library/microsoft.windowsazure.messaging.notificationhub.aspx
[Microsoft.ServiceBus.Notifications]: https://msdn.microsoft.com/library/microsoft.servicebus.notifications.aspx
[App Service Mobile Apps]: https://azure.microsoft.com/documentation/articles/app-service-mobile-value-prop/
[templates]: notification-hubs-templates-cross-platform-push-messages.md
[Azure portal]: https://portal.azure.com
[tags]: (https://msdn.microsoft.com/library/azure/dn530749.aspx)
