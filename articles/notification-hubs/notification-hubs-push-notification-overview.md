---
title: Wat is Azure Notification Hubs?
description: Leer hoe u mogelijkheden voor pushmeldingen toevoegt met Azure Notification Hubs.
author: sethmanheim
manager: femila
editor: tjsomasundaram
services: notification-hubs
documentationcenter: ''
ms.assetid: fcfb0ce8-0e19-4fa8-b777-6b9f9cdda178
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: overview
ms.custom: mvc
ms.date: 02/20/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 04/30/2019
ms.openlocfilehash: dad71d473ecb6107a5cf0d8f5953667f07bfce5c
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/21/2020
ms.locfileid: "77526770"
---
# <a name="what-is-azure-notification-hubs"></a>Wat is Azure Notification Hubs?

Azure Notification Hubs bieden een gebruiks vriendelijke en uitgeschaalde push engine waarmee u meldingen kunt verzenden naar elk platform (iOS, Android, Windows, enzovoort) vanuit elke back-end (Cloud of on-premises). Notification Hubs is zeer geschikt voor zowel bedrijfs- als consumentenscenario's. Enkele voorbeelden:

- Meldingen van belangrijk nieuws met een lage latentie verzenden naar miljoenen gebruikers.
- Coupons op basis van locatie verzenden naar geïnteresseerde gebruikerssegmenten.
- Meldingen voor evenementen verzenden naar gebruikers of groepen voor bijvoorbeeld media-, sport- en financiële toepassingen.
- Aanbiedingen pushen naar toepassingen om klanten te benaderen en werven.
- Gebruikers op de hoogte stellen van ondernemings gebeurtenissen, zoals nieuwe berichten en werk items.
- Codes verzenden voor meervoudige verificatie.

## <a name="what-are-push-notifications"></a>Wat zijn pushmeldingen?

Push meldingen zijn een vorm van app-to-User-communicatie waarbij gebruikers van mobiele apps op de hoogte worden gebracht van bepaalde gewenste informatie, meestal in een pop-up of dialoog venster op een mobiel apparaat. Gebruikers kiezen het bericht meestal weer te geven of te verwijderen. Als u de voormalige selecteert, wordt de mobiele toepassing geopend die de melding heeft verstrekt. Sommige meldingen worden achter de schermen op de achtergrond geplaatst zodat de app kan verwerken en bepalen wat er moet gebeuren.

Pushmeldingen zijn essentieel voor consumenten-apps om de betrokkenheid en het gebruik te vergroten. Voor zakelijke apps zijn ze belangrijk om actuele bedrijfsgegevens te communiceren. Het is de beste communicatie tussen gebruikers omdat het energie-efficiënt is voor mobiele apparaten, flexibel voor de afzenders van meldingen en beschikbaar is als de bijbehorende toepassingen niet actief zijn.

> [!NOTE]
> Azure Notification Hubs biedt geen ondersteuning voor VOIP-push meldingen (Voice-over-Internet Protocol).

Zie de volgende onderwerpen voor meer informatie over pushmeldingen voor enkele populaire platforms:

- [Android](https://developer.android.com/guide/topics/ui/notifiers/notifications.html)
- [iOS](https://developer.apple.com/notifications/)
- [Windows](https://msdn.microsoft.com/library/windows/apps/hh779725.aspx)

## <a name="how-do-push-notifications-work"></a>Hoe werkt push meldingen?

Pushmeldingen worden bezorgd via platformspecifieke infrastructuren, *PNS (Platform Notification System)* genoemd. Ze bieden elementaire push functies om een bericht te leveren aan een apparaat met een gegeven ingang, en hebben geen gemeen schappelijke interface. Als u een melding wilt verzenden naar alle klanten in de Android-, iOS-en Windows-versies van een app, moet de ontwikkelaar afzonderlijk samen werken met Apple Push Notification Service (APNS), Firebase Cloud Messa ging (FCM) en Windows Notification Service (WNS).

Dit zijn de algemene stappen voor het verzenden van pushberichten:

1. Een toepassing wil een melding ontvangen, zodat deze contact opneemt met de PNS voor het doel platform waarop de app wordt uitgevoerd en die een unieke en tijdelijke push ingang aanvraagt. Het ingangs type is afhankelijk van het systeem (bijvoorbeeld WNS maakt gebruik van Uri's terwijl APNS tokens gebruikt).
2. De client-app slaat deze ingang op in de back-end van de app of in de provider.
3. Voor het verzenden van een push melding neemt de back-end van de app contact op met de PNS met behulp van de ingang voor een specifieke client-app.
4. De PNS stuurt de melding door naar het apparaat dat is opgegeven door de ingang.

![Werkstroom voor pushmeldingen](./media/notification-hubs-overview/registration-diagram.png)

## <a name="the-challenges-of-push-notifications"></a>Aandachtspunten voor pushmeldingen

Platform Notification System zijn krachtig. App-ontwikkelaars moeten echter zelfs voor meer algemene scenario’s voor pushmeldingen nog heel wat werk verrichten om deze te implementeren, bijvoorbeeld voor het broadcasten van pushmeldingen naar gesegmenteerde gebruikers.

Voor het verzenden van push meldingen is een complexe infra structuur vereist die niet gerelateerd is aan de belangrijkste bedrijfs logica van de toepassing. Enkele van de infrastructuur problemen zijn:

- **Platformafhankelijkheid**
  - De back-end vereist complexe en moeilijk te onderhouden platform afhankelijke logica voor het verzenden van meldingen naar apparaten op verschillende platforms, aangezien PNSes niet Unified zijn.
- **Schalen**
  - Per PNS-richt lijnen moeten de tokens van het apparaat worden vernieuwd tijdens het starten van elke app. De back-end behandelt een grote hoeveelheid verkeer en toegang tot data bases om de tokens up-to-date te houden. Wanneer het aantal apparaten groeit tot honderden, duizenden of miljoenen, zijn de kosten voor het maken en onderhouden van deze infra structuur enorm.
  - De meeste Platform Notification Systems bieden geen ondersteuning voor broadcasten naar meerdere apparaten. Om één bericht te broadcasten naar een miljoen apparaten, zijn een miljoen aanroepen naar de Platform Notification Systems nodig. Het is zeer lastig om een dergelijke hoeveelheid verkeer met een minimale latentie te schalen.
- **Routering**
  - Hoewel PNSes een manier biedt om berichten te verzenden naar apparaten, zijn de meeste app-meldingen gericht op gebruikers of belangen groepen. De back-end moet een REGI ster onderhouden om apparaten te koppelen aan belangen groepen, gebruikers, eigenschappen, enzovoort. Deze overhead wordt toegevoegd aan de kosten voor de markt en het onderhoud van een app.

## <a name="why-use-azure-notification-hubs"></a>Waarom kiezen voor Azure Notification Hubs?

Notification Hubs elimineert alle complexen die zijn gekoppeld aan het verzenden van push meldingen op uw eigen app-back-end. De uitgeschaalde infrastructuur ondersteunt meerdere platforms, waardoor er minder push-gerelateerde code nodig is en de back-end kan worden vereenvoudigd. Met Notification Hubs hoeven apparaten alleen maar hun PNS-ingangen te registreren bij een hub, terwijl de back-end berichten verstuurt naar gebruikers of belangengroepen, zoals wordt weergegeven in de volgende afbeelding:

![Schema van Notification Hubs](./media/notification-hubs-overview/notification-hub-diagram.png)

Notification Hubs is uw kant-en-klare Push Engine met de volgende voor delen:

- **Platformoverschrijdend**
  - Ondersteuning voor alle belang rijke push-platforms.
  - Een algemene interface om te pushen naar alle platforms in platformspecifieke of platformonafhankelijk indelingen zonder platformspecifieke taken.
  - Beheer van apparaatingang op één plaats.
- **Ondersteuning voor verschillende back-ends**
  - Cloud of on-premises.
  - .NET, node. js, Java, Python, enzovoort.
- **Veel verschillende bezorgingsmethoden**
  - Broadcasten naar een of meer platforms: u kunt direct broadcasten naar miljoenen apparaten op verschillende platforms met één API-oproep.
  - Pushen naar apparaat: u kunt meldingen op afzonderlijke apparaten richten.
  - Pushen naar gebruiker: Tags en sjablonen helpen u bij het bereiken van alle platformoverschrijdende apparaten voor een gebruiker.
  - Pushen naar segment met dynamische Tags: met de functie Tags kunt u apparaten segmenteren en naar hen pushen op basis van uw behoeften, of u nu naar één segment of een expressie van segmenten verzendt (bijvoorbeeld actief en woont in Seattle geen nieuwe gebruiker). In plaats van te worden beperkt tot publiceren-abonneren, kunt u de labels overal en op elk moment bijwerken.
  - Gelokaliseerde push: de sjabloon functie helpt lokalisatie te behaalen zonder de back-end-code te beïnvloeden.
  - Pushen op de achtergrond: u kunt het push-naar-pull-patroon inschakelen door op de achtergrond meldingen te verzenden naar apparaten en deze zo opdracht te geven om bepaalde pulls of acties te voltooien.
  - Geplande push: u kunt plannen dat meldingen op elk gewenst moment worden verzonden.
  - Direct pushen: u kunt het registreren van apparaten bij de Notification Hubs-service overslaan en rechtstreeks batchgewijs pushen naar een lijst met apparaatingangen.
  - Gepersonaliseerde push: met behulp van apparaten push variabelen kunt u apparaatspecifieke persoonlijke push meldingen verzenden met aangepaste sleutel-waardeparen.
- **Uitgebreide telemetrie**
  - De algemene push, het apparaat, de fout en de bewerkings-telemetrie zijn zowel in de Azure Portal als programmatisch beschikbaar.
  - Met telemetrie per bericht wordt elke push getraceerd van uw eerste aanvraag oproep naar de Notification Hubs-service die de pushes heeft verzonden.
  - Platform Notification System feedback communiceert alle feedback van PNSes om u te helpen bij het opsporen van fouten.
- **Schaalbaarheid**
  - Verzend snelle berichten naar miljoenen apparaten zonder de architectuur of het sharding van het apparaat te herstellen.
- **Beveiliging**
  - Shared Access Secret (SAS) of federatieve verificatie.

## <a name="next-steps"></a>Volgende stappen

U kunt zelf een meldingshub maken en gebruiken door de zelfstudie [Aan de slag met Azure Notification Hubs voor Android-apps en Firebase Cloud Messaging](notification-hubs-android-push-notification-google-fcm-get-started.md) te volgen.

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
