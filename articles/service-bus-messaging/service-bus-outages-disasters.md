---
title: Azure Service Bus-toepassingen isoleren tegen storingen en rampen
description: In deze artikelen worden technieken gevonden om toepassingen te beschermen tegen een mogelijke azure servicebus-storing.
services: service-bus-messaging
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 01/27/2020
ms.author: aschhab
ms.openlocfilehash: 07b071b0e8efc5d664dada133a214d778c6531d0
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984943"
---
# <a name="best-practices-for-insulating-applications-against-service-bus-outages-and-disasters"></a>Best practices voor de isolatie van toepassingen tegen Service Bus-uitval en -noodgevallen

Bedrijfskritieke toepassingen moeten continu werken, zelfs in aanwezigheid van ongeplande uitval of rampen. In dit artikel worden technieken beschreven die u gebruiken om Service Bus-toepassingen te beschermen tegen een mogelijke servicestoring of ramp.

Een storing wordt gedefinieerd als de tijdelijke onbeschikbaarheid van Azure Service Bus. De storing kan gevolgen hebben voor sommige onderdelen van Service Bus, zoals een berichtenwinkel of zelfs het hele datacenter. Nadat het probleem is opgelost, is Service Bus weer beschikbaar. Een storing leidt meestal niet tot verlies van berichten of andere gegevens. Een voorbeeld van een componentfout is de onbeschikbaarheid van een bepaalde berichtenwinkel. Een voorbeeld van een datacenter-brede storing is een stroomstoring van het datacenter of een defecte datacenternetwerkswitch. Een storing kan enkele minuten tot enkele dagen duren.

Een ramp wordt gedefinieerd als het permanente verlies van een servicebusschaaleenheid of datacenter. Het datacenter kan wel of niet weer beschikbaar komen. Meestal veroorzaakt een ramp verlies van sommige of alle berichten of andere gegevens. Voorbeelden van rampen zijn brand, overstromingof aardbevingen.

## <a name="protecting-against-outages-and-disasters---service-bus-premium"></a>Bescherming tegen storingen en rampen - Service Bus Premium
Concepten met hoge beschikbaarheid en disaster recovery zijn direct ingebouwd in de Azure Service Bus Premium-laag, zowel binnen dezelfde regio (via beschikbaarheidszones) als in verschillende regio's (via Geo-Disaster Recovery).

### <a name="geo-disaster-recovery"></a>Herstel na georamp

Service Bus Premium ondersteunt Geo-disaster recovery, op naamruimteniveau. Zie [Azure Service Bus Geo-disaster recovery](service-bus-geo-dr.md)voor meer informatie. De functie voor noodherstel, alleen beschikbaar voor de [Premium SKU,](service-bus-premium-messaging.md) implementeert metagegevens disaster recovery en is afhankelijk van primaire en secundaire naamruimten voor noodherstel.

### <a name="availability-zones"></a>Beschikbaarheidszones

De Service Bus Premium SKU ondersteunt [beschikbaarheidszones](../availability-zones/az-overview.md)en biedt op foutgeïsoleerde locaties binnen dezelfde Azure-regio. Service Bus beheert drie exemplaren van berichtenwinkel (1 primaire en 2 secundaire). Service Bus houdt alle drie de kopieën gesynchroniseerd voor gegevens en beheerbewerkingen. Als de primaire kopie mislukt, wordt een van de secundaire kopieën gepromoot naar primaire zonder waargenomen downtime. Als de toepassingen de tijdelijke verbinding met Service Bus zien, wordt de logica voor opnieuw proberen in de SDK automatisch opnieuw verbinding gemaakt met Service Bus. 

> [!NOTE]
> De ondersteuning voor beschikbaarheidszones voor Azure Service Bus Premium is alleen beschikbaar in [Azure-regio's](../availability-zones/az-overview.md#services-support-by-region) waar beschikbaarheidszones aanwezig zijn.

U beschikbaarheidszones alleen inschakelen op nieuwe naamruimten met behulp van de Azure-portal. Service Bus ondersteunt geen migratie van bestaande naamruimten. U zoneredundantie niet uitschakelen nadat u deze hebt ingeschakeld op uw naamruimte.

![1][]


## <a name="protecting-against-outages-and-disasters---service-bus-standard"></a>Bescherming tegen storingen en rampen - Service Bus Standard
Service Bus ondersteunt twee benaderingen *om* veerkracht te bieden tegen uitval van *passive* datacenters bij het gebruik van de standaardprijscategorie voor berichten. Voor elke benadering u deze in beide naamruimten maken als een bepaalde wachtrij of onderwerp toegankelijk moet blijven in aanwezigheid van een datacenterstoring. Beide entiteiten kunnen dezelfde naam hebben. Een primaire wachtrij kan bijvoorbeeld worden bereikt onder **contosoPrimary.servicebus.windows.net/myQueue**, terwijl de secundaire tegenhanger kan worden bereikt onder **contosoSecondary.servicebus.windows.net/myQueue**.

>[!NOTE]
> De instelling **Actieve replicatie** en **passieve replicatie** zijn oplossingen voor algemene doeleinden en geen specifieke functies van Service Bus. De replicatielogica (verzenden naar 2 verschillende naamruimten) leeft op de afzendertoepassingen en de ontvanger moet aangepaste logica hebben voor dubbele detectie.

Als de toepassing geen permanente communicatie tussen afzender en ontvanger vereist, kan de toepassing een duurzame client-side queue implementeren om berichtverlies te voorkomen en om de afzender te beschermen tegen tijdelijke servicebusfouten.

### <a name="active-replication"></a>Actieve replicatie
Actieve replicatie gebruikt entiteiten in beide naamruimten voor elke bewerking. Elke client die een bericht verzendt, stuurt twee kopieën van hetzelfde bericht. Het eerste exemplaar wordt verzonden naar de primaire entiteit (bijvoorbeeld **contosoPrimary.servicebus.windows.net/sales)** en het tweede exemplaar van het bericht wordt naar de secundaire entiteit verzonden (bijvoorbeeld **contosoSecondary.servicebus.windows.net/sales).**

Een client ontvangt berichten van beide wachtrijen. De ontvanger verwerkt het eerste exemplaar van een bericht en het tweede exemplaar wordt onderdrukt. Om dubbele berichten te onderdrukken, moet de afzender elk bericht taggen met een unieke id. Beide kopieën van het bericht moeten worden getagd met dezelfde id. U de eigenschappen [BrokeredMessage.MessageId][BrokeredMessage.MessageId] of [BrokeredMessage.Label][BrokeredMessage.Label] of een aangepaste eigenschap gebruiken om het bericht te taggen. De ontvanger moet een lijst bijhouden met berichten die hij al heeft ontvangen.

In het voorbeeld [Geo-replicatie met Service Bus Standard Tier][Geo-replication with Service Bus Standard Tier] wordt actieve replicatie van berichtenentiteiten getoond.

> [!NOTE]
> De actieve replicatiebenadering verdubbelt het aantal bewerkingen, waardoor deze aanpak kan leiden tot hogere kosten.
> 
> 

### <a name="passive-replication"></a>Passieve replicatie
In de foutvrije case gebruikt passieve replicatie slechts één van de twee berichtenentiteiten. Een client stuurt het bericht naar de actieve entiteit. Als de bewerking op de actieve entiteit mislukt met een foutcode die aangeeft dat het datacenter dat de actieve entiteit host mogelijk niet beschikbaar is, stuurt de client een kopie van het bericht naar de back-upentiteit. Op dat moment wisselen de actieve en de back-upentiteiten van rol: de verzendende client beschouwt de oude actieve entiteit als de nieuwe back-upentiteit en de oude back-upentiteit is de nieuwe actieve entiteit. Als beide verzendbewerkingen mislukken, blijven de rollen van de twee entiteiten ongewijzigd en wordt een fout geretourneerd.

Een client ontvangt berichten van beide wachtrijen. Omdat er een kans bestaat dat de ontvanger twee kopieën van hetzelfde bericht ontvangt, moet de ontvanger dubbele berichten onderdrukken. U duplicaten op dezelfde manier onderdrukken als beschreven voor actieve replicatie.

In het algemeen is passieve replicatie voordeliger dan actieve replicatie, omdat in de meeste gevallen slechts één bewerking wordt uitgevoerd. Latentie, doorvoer en monetaire kosten zijn identiek aan het niet-gerepliceerde scenario.

Bij het gebruik van passieve replicatie kunnen berichten in de volgende scenario's twee keer verloren gaan of ontvangen:

* **Berichtvertraging of -verlies**: Ga ervan uit dat de afzender een bericht m1 naar de primaire wachtrij heeft verzonden en dat de wachtrij niet beschikbaar is voordat de ontvanger m1 ontvangt. De afzender stuurt een volgend bericht m2 naar de secundaire wachtrij. Als de primaire wachtrij tijdelijk niet beschikbaar is, ontvangt de ontvanger m1 nadat de wachtrij weer beschikbaar is. In geval van een ramp kan de ontvanger nooit m1 ontvangen.
* **Dubbele ontvangst:** ga ervan uit dat de afzender een bericht m naar de primaire wachtrij stuurt. Service Bus verwerkt m met succes, maar stuurt geen antwoord. Nadat de verzendbewerking is uitgeschakeld, stuurt de afzender een identieke kopie van m naar de secundaire wachtrij. Als de ontvanger het eerste exemplaar van m kan ontvangen voordat de primaire wachtrij niet beschikbaar is, ontvangt de ontvanger beide exemplaren van m op ongeveer hetzelfde moment. Als de ontvanger het eerste exemplaar van m niet kan ontvangen voordat de primaire wachtrij niet beschikbaar is, ontvangt de ontvanger in eerste instantie alleen het tweede exemplaar van m, maar ontvangt vervolgens een tweede exemplaar van m wanneer de primaire wachtrij beschikbaar komt.

Het voorbeeld [Geo-replicatie met Service Bus Standard Tier][Geo-replication with Service Bus Standard Tier] toont passieve replicatie van berichtenentiteiten.

## <a name="protecting-relay-endpoints-against-datacenter-outages-or-disasters"></a>Relay-eindpunten beschermen tegen uitval van datacenters of rampen
Geo-replicatie van [Azure Relay-eindpunten](../service-bus-relay/relay-what-is-it.md) maakt het mogelijk een service die een relay-eindpunt blootstelt, bereikbaar te zijn in aanwezigheid van servicebus-onderbrekingen. Om geo-replicatie te bereiken, moet de service twee relay-eindpunten maken in verschillende naamruimten. De naamruimten moeten zich in verschillende datacenters bevinden en de twee eindpunten moeten verschillende namen hebben. Een primair eindpunt kan bijvoorbeeld worden bereikt onder **contosoPrimary.servicebus.windows.net/myPrimaryService**, terwijl de secundaire tegenhanger onder **contosoSecondary.servicebus.windows.net/mySecondaryService**kan worden bereikt .

De service luistert vervolgens op beide eindpunten en een klant kan de service aanroepen via beide eindpunten. Een clienttoepassing kiest willekeurig een van de relays als het primaire eindpunt en stuurt de aanvraag naar het actieve eindpunt. Als de bewerking mislukt met een foutcode, geeft deze fout aan dat het relay-eindpunt niet beschikbaar is. De toepassing opent een kanaal naar het back-upeindpunt en geeft de aanvraag opnieuw uit. Op dat moment wisselen de actieve en de back-upeindpunten van rol: de clienttoepassing beschouwt het oude actieve eindpunt als het nieuwe back-upeindpunt en het oude back-upeindpunt als het nieuwe actieve eindpunt. Als beide verzendbewerkingen mislukken, blijven de rollen van de twee entiteiten ongewijzigd en wordt een fout geretourneerd.

## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen voor meer informatie over herstel na noodgevallen:

* [Azure Service Bus Geo-disaster recovery](service-bus-geo-dr.md)
* [Azure SQL Database Bedrijfscontinuïteit][Azure SQL Database Business Continuity]
* [Robuuste toepassingen ontwerpen voor Azure][Azure resiliency technical guidance]

[Service Bus Authentication]: service-bus-authentication-and-authorization.md
[Partitioned messaging entities]: service-bus-partitioning.md
[Asynchronous messaging patterns and high availability]: service-bus-async-messaging.md#failure-of-service-bus-within-an-azure-datacenter
[BrokeredMessage.MessageId]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[BrokeredMessage.Label]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[Geo-replication with Service Bus Standard Tier]: https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoReplication
[Azure SQL Database Business Continuity]: ../sql-database/sql-database-business-continuity.md
[Azure resiliency technical guidance]: /azure/architecture/resiliency

[1]: ./media/service-bus-outages-disasters/az.png
