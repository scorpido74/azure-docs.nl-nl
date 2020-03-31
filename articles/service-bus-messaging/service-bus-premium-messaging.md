---
title: Premium en standaardlagen van Azure Service Bus
description: In dit artikel worden standaard- en premiumlagen van Azure Service Bus beschreven. Hiermee worden deze lagen vergeleken en technische verschillen mogelijk.
services: service-bus-messaging
documentationcenter: .net
author: axisc
manager: timlt
editor: spelluru
ms.assetid: e211774d-821c-4d79-8563-57472d746c58
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/27/2020
ms.author: aschhab
ms.openlocfilehash: ef3cc8d4c7354b43389244e72c2dbc5899b8db25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76774571"
---
# <a name="service-bus-premium-and-standard-messaging-tiers"></a>Prijscategorieën voor Service Bus Premium en Standard Messaging

Service Bus Messaging, dat entiteiten zoals wachtrijen en onderwerpen omvat, combineert functies voor bedrijfsberichten met krachtige semantiek voor publiceren/abonneren in de cloud. Service Bus Messaging wordt gebruikt als de communicatie-backbone voor veel geavanceerde cloudoplossingen.

De *Premium*-laag van de Service Bus Messaging-service zorgt voor de afhandeling van algemene klantaanvragen met betrekking tot de schaal, prestaties en beschikbaarheid van essentiële toepassingen. Voor productiescenario's wordt de laag Premium aanbevolen. Hoewel de functiesets bijna identiek zijn, zijn deze twee lagen van de Service Bus Messaging-service ontworpen voor verschillende gebruiksscenario’s.

In de volgende tabel worden enkele belangrijke verschillen uitgelicht.

| Premium | Standard |
| --- | --- |
| Hoge doorvoersnelheid |Variabele doorvoersnelheid |
| Voorspelbare prestaties |Variabele latentie |
| Vaste prijzen |Variabel omslagstelsel voor betalen per gebruik |
| Mogelijkheid om de workload omhoog en omlaag te schalen |N.v.t. |
| Berichtformaat tot maximaal 1 MB |Berichtformaat tot maximaal 256 kB |

**Service Bus Premium Messaging** biedt isolatie van resources op het niveau van de CPU en het geheugen, zodat elke workload van een klant geïsoleerd wordt uitgevoerd. Deze resourcecontainer wordt een *berichteneenheid*genoemd. Aan elke Premium-naamruimte wordt ten minste één Messaging-eenheid toegewezen. U 1, 2, 4 of 8 berichteneenheden kopen voor elke naamruimte van Service Bus Premium. Eén workload of entiteit kan meerdere berichteneenheden omvatten en het aantal berichten-eenheden kan naar will worden gewijzigd. Dit resulteert in voorspelbare en herhaalbare prestaties voor uw Service Bus-oplossing.

Niet alleen zijn de prestaties beter voorspelbaar en beschikbaar, ze zijn ook sneller. Service Bus Premium Messaging bouwt voort op de opslagengine die is geïntroduceerd in [Azure Event Hubs.](https://azure.microsoft.com/services/event-hubs/) Met de Premium-laag zijn de piekprestaties veel sneller dan met de Standard-laag.

## <a name="premium-messaging-technical-differences"></a>Technische verschillen Premium Messaging

In de volgende secties wordt een aantal verschillen besproken tussen Premium en Standard Messaging.

### <a name="partitioned-queues-and-topics"></a>Gepartitioneerde wachtrijen en onderwerpen

Gepartitioneerde wachtrijen en onderwerpen worden niet ondersteund in Premium Messaging. Zie [Gepartitioneerde wachtrijen en onderwerpen](service-bus-partitioning.md) voor meer informatie over partitioneren.

### <a name="express-entities"></a>Express-entiteiten

Omdat Premium Messaging wordt uitgevoerd in een volledig geïsoleerde runtime-omgeving, worden Express-entiteiten niet ondersteund in Premium-naamruimten. Zie de eigenschap [QueueDescription.EnableExpress](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enableexpress#Microsoft_ServiceBus_Messaging_QueueDescription_EnableExpress) voor meer informatie over de Express-functie.

Als u code uitvoert onder Standard Messaging en deze wilt overzetten naar de Premium-prijscategorie, moet de eigenschap [EnableExpress](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enableexpress#Microsoft_ServiceBus_Messaging_QueueDescription_EnableExpress) zijn ingesteld op **false** (de standaardwaarde).

## <a name="premium-messaging-resource-usage"></a>Premium Messaging-brongebruik
Over het algemeen kan elke bewerking op een entiteit cpu- en geheugengebruik veroorzaken. Hier zijn enkele van deze bewerkingen: 

- Beheerbewerkingen zoals CRUD-bewerkingen (Maken, Ophalen, Bijwerken en Verwijderen) voor wachtrijen, onderwerpen en abonnementen.
- Runtime-bewerkingen (berichten verzenden en ontvangen)
- Toezicht op operaties en signaleringen

De extra CPU en het geheugen gebruik is niet extra geprijsd wel. Voor de premiumberichtenlaag is er één prijs voor de berichteenheid.

Het CPU- en geheugengebruik wordt om de volgende redenen bijgehouden en aan u weergegeven: 

- Zorgen voor transparantie in de interne systeem
- Inzicht in de capaciteit van de gekochte resources.
- Capaciteitsplanning die u helpt om omhoog/omlaag te schalen.

## <a name="messaging-unit---how-many-are-needed"></a>Berichten-eenheid - Hoeveel zijn er nodig?

Bij het inrichten van een Azure Service Bus Premium-naamruimte moet het aantal toegewezen berichteneenheden worden opgegeven. Deze berichten-eenheden zijn speciale bronnen die zijn toegewezen aan de naamruimte.

Het aantal berichteneenheden dat is toegewezen aan de naamruimte servicebuspremium kan dynamisch worden **aangepast** aan de wijziging (toename of afname) van de werkbelasting.

Er zijn een aantal factoren waarmee rekening moet worden gehouden bij het bepalen van het aantal berichten-eenheden voor uw architectuur:

- Begin met ***1 of 2 berichteneenheden*** toegewezen aan uw naamruimte.
- Bestudeer de CPU-gebruiksstatistieken in de [resourcegebruiksstatistieken](service-bus-metrics-azure-monitor.md#resource-usage-metrics) voor uw naamruimte.
    - Als het CPU-gebruik lager is ***dan 20%,*** u mogelijk het aantal berichteneenheden dat aan uw naamruimte is ***toegewezen, schalen.***
    - Als het CPU-gebruik hoger is ***dan 70%,*** profiteert uw toepassing van ***het opschalen*** van het aantal berichteneenheden dat aan uw naamruimte is toegewezen.

Het proces van het schalen van de resources die zijn toegewezen aan naamruimten van een servicebus, kan worden geautomatiseerd met [Azure Automation Runbooks.](../automation/automation-quickstart-create-runbook.md)

> [!NOTE]
> **Schalen** van de resources die aan de naamruimte zijn toegewezen, kan preventief of reactief zijn.
>
>  * **Preventief**: Als er extra werkbelasting wordt verwacht (vanwege seizoensgebondenheid of trends), u meer berichteneenheden toewijzen aan de naamruimte voordat de workloads worden getroffen.
>
>  * **Reactief:** Als extra workloads worden geïdentificeerd door de statistieken voor resourcegebruik te bestuderen, kunnen extra resources worden toegewezen aan de naamruimte om de toenemende vraag op te nemen.
>
> De facturatiemeters voor servicebus zijn per uur. In het geval van opschaling betaalt u alleen voor de extra resources voor de uren die deze zijn gebruikt.
>

## <a name="get-started-with-premium-messaging"></a>Aan de slag met Premium Messaging

U kunt snel aan de slag met Premium Messaging. Het proces is vergelijkbaar met dat van Standard Messaging. [Maak eerst een naamruimte](service-bus-create-namespace-portal.md) in [Azure Portal](https://portal.azure.com). Zorg ervoor dat bij **Prijscategorie** de optie **Premium** is geselecteerd. Klik op **Volledige prijsgegevens weergeven** voor meer informatie over de verschillende lagen.

![maken-premium-naamruimte][create-premium-namespace]

U kunt ook [Premium-naamruimtes maken met behulp van Azure Resource Manager-sjablonen](https://azure.microsoft.com/resources/templates/101-servicebus-pn-ar/).

## <a name="next-steps"></a>Volgende stappen

Zie de volgende koppelingen voor meer informatie over de Service Bus Messaging-service:

* [Introductie van Azure Service Bus Premium Messaging (blogbericht)](https://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
* [Introducing Azure Service Bus Premium messaging (Channel9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)
* [Overzicht van Service Bus Messaging](service-bus-messaging-overview.md)
* [Aan de slag met Service Bus-wachtrijen](service-bus-dotnet-get-started-with-queues.md)

<!--Image references-->

[create-premium-namespace]: ./media/service-bus-premium-messaging/select-premium-tier.png
