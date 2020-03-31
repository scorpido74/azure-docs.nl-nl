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
ms.date: 03/23/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 04/30/2019
ms.openlocfilehash: f5b59d1669d89c73b93199d1f833da149003d399
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80159333"
---
# <a name="what-is-azure-notification-hubs"></a>Wat is Azure Notification Hubs?

Azure Notification Hubs bieden een gebruiksvriendelijke en geschaalde pushengine waarmee u meldingen verzenden naar elk platform (iOS, Android, Windows, enz.) vanaf elke back-end (cloud of on-premises). Notification Hubs is zeer geschikt voor zowel bedrijfs- als consumentenscenario's. Enkele voorbeelden:

- Meldingen van belangrijk nieuws met een lage latentie verzenden naar miljoenen gebruikers.
- Coupons op basis van locatie verzenden naar geïnteresseerde gebruikerssegmenten.
- Meldingen voor evenementen verzenden naar gebruikers of groepen voor bijvoorbeeld media-, sport- en financiële toepassingen.
- Aanbiedingen pushen naar toepassingen om klanten te benaderen en werven.
- Gebruikers op de hoogte stellen van bedrijfsgebeurtenissen, zoals nieuwe berichten en werkitems.
- Codes verzenden voor meervoudige verificatie.

## <a name="what-are-push-notifications"></a>Wat zijn pushmeldingen?

Pushmeldingen zijn een vorm van app-to-user-communicatie waarbij gebruikers van mobiele apps op de hoogte worden gesteld van bepaalde gewenste informatie, meestal in een pop-up of dialoogvenster op een mobiel apparaat. Gebruikers kiezen er over het algemeen voor om het bericht te bekijken of te verwijderen; het kiezen van de eerste opent de mobiele applicatie die de melding heeft gecommuniceerd. Sommige meldingen zijn stil - geleverd achter de schermen voor de app om te verwerken en te beslissen wat te doen.

Pushmeldingen zijn essentieel voor consumenten-apps om de betrokkenheid en het gebruik te vergroten. Voor zakelijke apps zijn ze belangrijk om actuele bedrijfsgegevens te communiceren. Het is de beste app-to-user communicatie omdat het energie-efficiënt is voor mobiele apparaten, flexibel is voor de afzenders van meldingen en beschikbaar wanneer overeenkomstige toepassingen niet actief zijn.

> [!NOTE]
> Azure Notification Hubs biedt officieel geen ondersteuning voor VOIP-pushmeldingen (Voice Over Internet Protocol). In dit artikel wordt echter [beschreven hoe u APNS VOIP-meldingen gebruiken](voip-apns.md) via Azure Notification Hubs.

Zie de volgende onderwerpen voor meer informatie over pushmeldingen voor enkele populaire platforms:

- [Android](https://developer.android.com/guide/topics/ui/notifiers/notifications.html)
- [iOS](https://developer.apple.com/notifications/)
- [Windows](https://msdn.microsoft.com/library/windows/apps/hh779725.aspx)

## <a name="how-do-push-notifications-work"></a>Hoe werken pushmeldingen?

Pushmeldingen worden bezorgd via platformspecifieke infrastructuren, *PNS (Platform Notification System)* genoemd. Ze bieden basispushfunctionaliteiten om een bericht te leveren aan een apparaat met een meegeleverde handgreep en hebben geen gemeenschappelijke interface. Als u een melding wilt verzenden naar alle klanten in de Android-, iOS- en Windows-versies van een app, moet de ontwikkelaar afzonderlijk werken met Apple Push Notification Service (APNS), Firebase Cloud Messaging (FCM) en Windows Notification Service (WNS).

Dit zijn de algemene stappen voor het verzenden van pushberichten:

1. Een toepassing wil een melding ontvangen, zodat deze contact opneemt met de PNS voor het doelplatform waarop de app wordt uitgevoerd en een unieke en tijdelijke pushhandle aanvraagt. Het type handgreep is afhankelijk van het systeem (WNS gebruikt bijvoorbeeld URI's terwijl API's tokens gebruiken).
2. De client-app slaat deze handgreep op in de backend of provider van de app.
3. Als u een pushmelding wilt verzenden, neemt de backend van de app contact op met de PNS met de handgreep om een specifieke client-app te targeten.
4. De PNS stuurt de melding door naar het apparaat dat is opgegeven door de ingang.

![Werkstroom voor pushmeldingen](./media/notification-hubs-overview/registration-diagram.png)

## <a name="the-challenges-of-push-notifications"></a>Aandachtspunten voor pushmeldingen

Platform Notification System zijn krachtig. App-ontwikkelaars moeten echter zelfs voor meer algemene scenario’s voor pushmeldingen nog heel wat werk verrichten om deze te implementeren, bijvoorbeeld voor het broadcasten van pushmeldingen naar gesegmenteerde gebruikers.

Het verzenden van pushmeldingen vereist complexe infrastructuur die niets te maken heeft met de belangrijkste bedrijfslogica van de toepassing. Enkele van de uitdagingen op het gebied van infrastructuur zijn:

- **Afhankelijk van het platform**
  - De backend vereist complexe en moeilijk te onderhouden platformafhankelijke logica om meldingen naar apparaten op verschillende platforms te verzenden, omdat PNSes niet zijn verenigd.
- **Schaal**
  - Volgens PNS-richtlijnen moeten apparaattokens worden vernieuwd bij elke app-lancering. De backend gaat over een grote hoeveelheid verkeer en database toegang alleen maar om de tokens up-to-date te houden. Wanneer het aantal apparaten groeit tot honderden, duizenden of miljoenen, de kosten van het creëren en onderhouden van deze infrastructuur is enorm.
  - De meeste Platform Notification Systems bieden geen ondersteuning voor broadcasten naar meerdere apparaten. Om één bericht te broadcasten naar een miljoen apparaten, zijn een miljoen aanroepen naar de Platform Notification Systems nodig. Het is zeer lastig om een dergelijke hoeveelheid verkeer met een minimale latentie te schalen.
- **Routering**
  - Hoewel PNSes een manier bieden om berichten naar apparaten te verzenden, zijn de meeste app-meldingen gericht op gebruikers of belangengroepen. De backend moet een register bijhouden om apparaten te koppelen aan belangengroepen, gebruikers, eigenschappen, enz. Deze overhead draagt bij aan de time-to-market en onderhoudskosten van een app.

## <a name="why-use-azure-notification-hubs"></a>Waarom kiezen voor Azure Notification Hubs?

Notification Hubs elimineert alle complexiteiten die gepaard gaan met het verzenden van pushmeldingen alleen vanaf je app back-end. De uitgeschaalde infrastructuur ondersteunt meerdere platforms, waardoor er minder push-gerelateerde code nodig is en de back-end kan worden vereenvoudigd. Met Notification Hubs hoeven apparaten alleen maar hun PNS-ingangen te registreren bij een hub, terwijl de back-end berichten verstuurt naar gebruikers of belangengroepen, zoals wordt weergegeven in de volgende afbeelding:

![Schema van Notification Hubs](./media/notification-hubs-overview/notification-hub-diagram.png)

Notification Hubs is uw kant-en-klare pushengine met de volgende voordelen:

- **Platformoverschrijdend**
  - Ondersteuning voor alle grote pushplatforms.
  - Een algemene interface om te pushen naar alle platforms in platformspecifieke of platformonafhankelijk indelingen zonder platformspecifieke taken.
  - Beheer van apparaatingang op één plaats.
- **Ondersteuning voor verschillende back-ends**
  - Cloud of on-premises.
  - .NET, Node.js, Java, Python, etc.
- **Rijke set leveringspatronen**
  - Uitzenden naar een of meer platforms: u direct uitzenden naar miljoenen apparaten op verschillende platforms met één API-aanroep.
  - Pushen naar apparaat: u kunt meldingen op afzonderlijke apparaten richten.
  - Push naar gebruiker: tags en sjablonen helpen u alle cross-platform apparaten voor een gebruiker te bereiken.
  - Push naar segment met dynamische tags: de tagsfunctie helpt u apparaten te segmenteren en ernaar te pushen op basis van uw behoeften, of u nu naar één segment of een expressie van segmenten verzendt (Bijvoorbeeld actief EN woont in Seattle NIET nieuwe gebruiker). In plaats van te worden beperkt tot het publiceren van een abonnement, u apparaattags altijd en overal bijwerken.
  - Gelokaliseerde push: de sjablonenfunctie helpt bij het bereiken van lokalisatie zonder dat dit gevolgen heeft voor backendcode.
  - Pushen op de achtergrond: u kunt het push-naar-pull-patroon inschakelen door op de achtergrond meldingen te verzenden naar apparaten en deze zo opdracht te geven om bepaalde pulls of acties te voltooien.
  - Geplande push: U plannen dat meldingen op elk gewenst moment worden verzonden.
  - Direct pushen: u kunt het registreren van apparaten bij de Notification Hubs-service overslaan en rechtstreeks batchgewijs pushen naar een lijst met apparaatingangen.
  - Gepersonaliseerde push: Met pushvariabelen voor apparaten u apparaatspecifieke gepersonaliseerde pushmeldingen verzenden met aangepaste sleutelwaardeparen.
- **Uitgebreide telemetrie**
  - Algemene push-, apparaat-, fout- en bewerkingstelemetrie zijn zowel beschikbaar in de Azure-portal als programmatisch.
  - Telemetrie per bericht volgt elke push van uw eerste verzoekoproep naar de Service Meldinghubs en verzendt de pushs.
  - Feedback van het Platform Notification System communiceert alle feedback van PNSes om te helpen bij het opsporen van fouten.
- **Schaalbaarheid**
  - Stuur snelle berichten naar miljoenen apparaten zonder opnieuw te ontwerpen of apparaatsharding.
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
