---
title: bestand opnemen
description: bestand opnemen
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 12/13/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: b19dc7a85fafa1a4d875c84db9bbefabb3cd5a7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77651551"
---
In de volgende tabel worden quota-informatie weergegeven die specifiek is voor Azure Service Bus-berichten. Zie [ServiceBus-prijzen](https://azure.microsoft.com/pricing/details/service-bus/)voor informatie over prijzen en andere quota voor servicebus.

| Naam quotum | Bereik | Opmerkingen | Waarde |
| --- | --- | --- | --- |
| Maximum aantal basis- of standaardnaamruimten per Azure-abonnement |Naamruimte |Volgende aanvragen voor aanvullende basis- of standaardnaamruimten worden geweigerd door de Azure-portal. |100|
| Maximaal aantal Premium-naamruimten per Azure-abonnement |Naamruimte |Latere aanvragen voor extra Premium-naamruimten worden door de portal afgewezen. |100 |
| Wachtrij of onderwerpgrootte |Entiteit |Gedefinieerd bij het maken van de wachtrij of het onderwerp. <br/><br/> Volgende binnenkomende berichten worden afgewezen en een uitzondering wordt ontvangen door de belcode. |1, 2, 3, 4 GB of 5 GB.<br /><br />In de Premium SKU en de Standaard SKU met [partitionering](/azure/service-bus-messaging/service-bus-partitioning) ingeschakeld, is de maximale wachtrij- of onderwerpgrootte 80 GB. |
| Aantal gelijktijdige verbindingen op een naamruimte |Naamruimte |Latere aanvragen voor aanvullende verbindingen worden afgewezen en een uitzondering wordt ontvangen door de belcode. REST-bewerkingen tellen niet mee voor gelijktijdige TCP-verbindingen. |NetMessaging: 1.000.<br /><br />AMQP: 5.000. |
| Aantal gelijktijdige ontvangen aanvragen voor een wachtrij, onderwerp of abonnementsentiteit |Entiteit |Latere ontvangstaanvragen worden afgewezen en er wordt een uitzondering ontvangen door de belcode. Dit quotum is van toepassing op het gecombineerde aantal gelijktijdige ontvangen bewerkingen voor alle abonnementen op een onderwerp. |5.000 |
| Aantal onderwerpen of wachtrijen per naamruimte |Naamruimte |Latere aanvragen voor het maken van een nieuw onderwerp of wachtrij op de naamruimte worden afgewezen. Als u daarom via de [Azure-portal][Azure portal]is geconfigureerd, wordt een foutbericht gegenereerd. Als de beheer-API wordt aangeroepen, wordt een uitzondering ontvangen door de aanroepcode. |10.000 voor de laag Basis of Standaard. Het totale aantal onderwerpen en wachtrijen in een naamruimte moet lager zijn dan of gelijk zijn aan 10.000. <br/><br/>Voor de Premium-laag 1.000 per berichteneenheid (MU). De maximumlimiet is 4.000. |
| Aantal [verdeelde onderwerpen of wachtrijen](/azure/service-bus-messaging/service-bus-partitioning) per naamruimte |Naamruimte |Latere aanvragen voor het maken van een nieuw verdeeld onderwerp of wachtrij op de naamruimte worden afgewezen. Als u daarom via de [Azure-portal][Azure portal]is geconfigureerd, wordt een foutbericht gegenereerd. Als de beheer-API wordt aangeroepen, wordt de uitzondering **QuotaExceededException** ontvangen door de aanroepcode. |Basis- en standaardniveaus: 100.<br/><br/>Partitieentiteiten worden niet ondersteund in [de](../articles/service-bus-messaging/service-bus-premium-messaging.md) premiumlaag.<br/><br />Elke partitiewachtrij of onderwerp telt mee voor het quotum van 1.000 entiteiten per naamruimte. |
| Maximale grootte van een pad van een berichtenentiteit: wachtrij of onderwerp |Entiteit |- |260 tekens. |
| Maximale grootte van de naam van een berichtenentiteit: naamruimte, abonnement of abonnementsregel |Entiteit |- |50 tekens. |
| Maximale grootte van een [bericht-id](/dotnet/api/microsoft.azure.servicebus.message.messageid) | Entiteit |- | 128 |
| Maximale grootte van een [berichtsessie-id](/dotnet/api/microsoft.azure.servicebus.message.sessionid) | Entiteit |- | 128 |
| Berichtgrootte voor een wachtrij, onderwerp of abonnementsentiteit |Entiteit |Binnenkomende berichten die deze quota overschrijden, worden afgewezen en er wordt een uitzondering ontvangen door de aanroepcode. |Maximale berichtgrootte: 256 KB voor [standaardlaag,](../articles/service-bus-messaging/service-bus-premium-messaging.md)1 MB voor [Premium-laag](../articles/service-bus-messaging/service-bus-premium-messaging.md). <br /><br />Door de overhead van het systeem is deze limiet lager dan deze waarden.<br /><br />Maximale kopgrootte: 64 KB.<br /><br />Maximaal aantal header-eigenschappen in de eigenschappenzak: **byte/int. MaxValue**.<br /><br />Maximale grootte van de woning in de tas: Geen expliciete limiet. Beperkt door maximale kopgrootte. |
| Grootte van de eigenschappen van een bericht voor een wachtrij, onderwerp of abonnementsentiteit |Entiteit | De uitzondering **SerialisatieUitzondering** wordt gegenereerd. |De maximale grootte van de berichteigenschap voor elke eigenschap is 32.000. De cumulatieve grootte van alle eigenschappen mag niet hoger zijn dan 64.000. Deze limiet is van toepassing op de volledige koptekst van [brokeredmessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage), die zowel gebruikerseigenschappen als systeemeigenschappen heeft, zoals [SequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber), [Label](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.label)en [MessageId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.messageid). |
| Aantal abonnementen per onderwerp |Entiteit |Latere aanvragen voor het maken van aanvullende abonnementen voor het onderwerp worden afgewezen. Als u daarom via de portal wordt geconfigureerd, wordt een foutbericht weergegeven. Als de beheer-API wordt aangeroepen, wordt een uitzondering ontvangen door de aanroepcode. |2.000 per onderwerp voor de standaardlaag. |
| Aantal SQL-filters per onderwerp |Entiteit |Latere aanvragen voor het maken van extra filters over het onderwerp worden afgewezen en een uitzondering wordt ontvangen door de aanroepcode. |2.000 |
| Aantal correlatiefilters per onderwerp |Entiteit |Latere aanvragen voor het maken van extra filters over het onderwerp worden afgewezen en een uitzondering wordt ontvangen door de aanroepcode. |100.000 |
| Grootte van SQL-filters of -acties |Naamruimte |Latere aanvragen voor het maken van extra filters worden afgewezen en een uitzondering wordt ontvangen door de belcode. |Maximale lengte van de filterconditietekenreeks: 1.024 (1 K).<br /><br />Maximale lengte van regelactietekenreeks: 1.024 (1 K).<br /><br />Maximum aantal expressies per regelactie: 32. |
| Aantal Regels voor [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) per naamruimte, wachtrij of onderwerp |Entiteit, naamruimte |Latere verzoeken om het maken van aanvullende regels worden afgewezen en een uitzondering wordt ontvangen door de belcode. |Maximumaantal regels per type entiteit: 12. <br /><br /> Regels die zijn geconfigureerd op een naamruimte van een servicebus zijn van toepassing op alle typen: wachtrijen, onderwerpen. |
| Aantal berichten per transactie | Transactie | Extra binnenkomende berichten worden geweigerd en een uitzondering met de vermelding 'Mag niet meer dan 100 berichten verzenden in één transactie' wordt ontvangen door de belcode. | 100 <br /><br /> Voor zowel **Send() als** **SendAsync()** bewerkingen. |
| Aantal regels voor virtuele netwerken en IP-filters | Naamruimte | &nbsp; | 128 | 

[Azure portal]: https://portal.azure.com
