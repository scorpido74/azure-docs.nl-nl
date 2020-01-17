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
ms.openlocfilehash: c8412a01f4a5056b352b1d985f36e5a51a25a649
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/16/2020
ms.locfileid: "76159148"
---
De volgende tabel bevat de quotum gegevens die specifiek zijn voor Azure Service Bus berichten. Voor informatie over prijzen en andere quota's voor Service Bus raadpleegt u [Service Bus prijzen](https://azure.microsoft.com/pricing/details/service-bus/).

| Quotum naam | Scope | Opmerkingen | Waarde |
| --- | --- | --- | --- |
| Maximum aantal basis-of standaard naam ruimten per Azure-abonnement |Naamruimte |Volgende aanvragen voor aanvullende Basic-of Standard-naam ruimten worden geweigerd door de Azure Portal. |100|
| Maximum aantal Premium-naam ruimten per Azure-abonnement |Naamruimte |Volgende aanvragen voor aanvullende Premium-naam ruimten worden geweigerd door de portal. |100 |
| Grootte van de wachtrij of het onderwerp |Entiteit |Gedefinieerd bij het maken van de wachtrij of het onderwerp. <br/><br/> Volgende inkomende berichten worden geweigerd en er wordt een uitzonde ring ontvangen door de aanroepende code. |1, 2, 3, 4 GB of 5 GB.<br /><br />In de Premium-SKU en de standaard-SKU waarvoor [partitionering](/azure/service-bus-messaging/service-bus-partitioning) is ingeschakeld, is de maximum grootte voor de wachtrij of het onderwerp 80 GB. |
| Aantal gelijktijdige verbindingen op een naam ruimte |Naamruimte |Volgende aanvragen voor extra verbindingen worden geweigerd en er wordt een uitzonde ring ontvangen door de aanroepende code. REST-bewerkingen tellen niet mee voor gelijktijdige TCP-verbindingen. |Netmessa ging: 1.000.<br /><br />AMQP: 5.000. |
| Aantal gelijktijdige ontvangen aanvragen voor een entiteit in een wachtrij, onderwerp of abonnement |Entiteit |Volgende ontvangen aanvragen worden afgewezen en er wordt een uitzonde ring ontvangen door de aanroepende code. Dit quotum is van toepassing op het gecombineerde aantal gelijktijdige ontvangst bewerkingen in alle abonnementen op een onderwerp. |5\.000 |
| Aantal onderwerpen of wacht rijen per naam ruimte |Naamruimte |Volgende aanvragen voor het maken van een nieuw onderwerp of nieuwe wachtrij in de naam ruimte worden geweigerd. Als dit het geval is, wordt er een fout bericht gegenereerd als dit wordt geconfigureerd via de [Azure Portal][Azure portal]. Als deze wordt aangeroepen vanuit de beheer-API, wordt er een uitzonde ring ontvangen door de aanroepende code. |10.000 voor de Basic-of Standard-laag. Het totale aantal onderwerpen en wacht rijen in een naam ruimte moet kleiner zijn dan of gelijk zijn aan 10.000. <br/><br/>Voor de Premium-laag, 1.000 per Messa ging-eenheid (MU). De maximum limiet is 4.000. |
| Aantal [gepartitioneerde onderwerpen of wacht rijen](/azure/service-bus-messaging/service-bus-partitioning) per naam ruimte |Naamruimte |Volgende aanvragen voor het maken van een nieuw gepartitioneerd onderwerp of wachtrij in de naam ruimte worden geweigerd. Als dit het geval is, wordt er een fout bericht gegenereerd als dit wordt geconfigureerd via de [Azure Portal][Azure portal]. Indien aangeroepen vanuit de beheer-API, wordt de uitzonde ring **QuotaExceededException** ontvangen door de aanroepende code. |Basis-en standaard lagen: 100.<br/><br/>Gepartitioneerde entiteiten worden niet ondersteund in de [Premium](../articles/service-bus-messaging/service-bus-premium-messaging.md) -laag.<br/><br />Elke gepartitioneerde wachtrij of elk onderwerp telt naar het quotum van 1.000 entiteiten per naam ruimte. |
| Maximale grootte van het pad naar de Messa ging-entiteit: wachtrij of onderwerp |Entiteit |- |260 tekens. |
| Maximale grootte van de naam van de Messa ging-entiteit: naam ruimte, abonnement of abonnements regel |Entiteit |- |50 tekens. |
| Maximale grootte van een [bericht-id](/dotnet/api/microsoft.azure.servicebus.message.messageid) | Entiteit |- | 128 |
| Maximale grootte van een bericht [sessie-id](/dotnet/api/microsoft.azure.servicebus.message.sessionid) | Entiteit |- | 128 |
| Bericht grootte voor een entiteit in een wachtrij, onderwerp of abonnement |Entiteit |Inkomende berichten die groter zijn dan deze quota worden geweigerd en er wordt een uitzonde ring ontvangen door de aanroepende code. |Maximale bericht grootte: 256 KB voor de [Standard-laag](../articles/service-bus-messaging/service-bus-premium-messaging.md), 1 MB voor de Premium- [laag](../articles/service-bus-messaging/service-bus-premium-messaging.md). <br /><br />Als gevolg van systeem overhead is deze limiet kleiner dan deze waarden.<br /><br />Maximale header-grootte: 64 KB.<br /><br />Maximum aantal header-eigenschappen in eigenschappen verzameling: **byte/int. MaxValue**.<br /><br />Maximale grootte van eigenschap in eigenschappen verzameling: geen expliciete limiet. Beperkt door de maximale header-grootte. |
| Grootte van bericht eigenschap voor een entiteit in een wachtrij, onderwerp of abonnement |Entiteit | De uitzonderings **SerializationException** is gegenereerd. |De maximale grootte van de bericht eigenschap voor elke eigenschap is 32.000. De cumulatieve grootte van alle eigenschappen mag niet groter zijn dan 64.000. Deze limiet is van toepassing op de volledige header van de [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage), die zowel gebruikers eigenschappen als systeem eigenschappen heeft, zoals [SequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber), [Label](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.label)en [MessageId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.messageid). |
| Aantal abonnementen per onderwerp |Entiteit |Volgende aanvragen voor het maken van extra abonnementen voor het onderwerp worden geweigerd. Als gevolg hiervan wordt een fout bericht weer gegeven als deze via de portal is geconfigureerd. Als deze wordt aangeroepen vanuit de beheer-API, wordt er een uitzonde ring ontvangen door de aanroepende code. |2\.000 per onderwerp voor de laag standaard. |
| Aantal SQL-filters per onderwerp |Entiteit |Volgende aanvragen voor het maken van extra filters in het onderwerp worden geweigerd en er wordt een uitzonde ring ontvangen door de aanroepende code. |2,000 |
| Aantal correlatie filters per onderwerp |Entiteit |Volgende aanvragen voor het maken van extra filters in het onderwerp worden geweigerd en er wordt een uitzonde ring ontvangen door de aanroepende code. |100.000 |
| Grootte van SQL-filters of-acties |Naamruimte |Volgende aanvragen voor het maken van extra filters worden geweigerd en er wordt een uitzonde ring ontvangen door de aanroepende code. |Maximale lengte van teken reeks voor filter voorwaarde: 1.024 (1 K).<br /><br />Maximale lengte van regel actie teken reeks: 1.024 (1 K).<br /><br />Maximum aantal expressies per regel actie: 32. |
| Aantal [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) -regels per naam ruimte, wachtrij of onderwerp |Entiteit, naam ruimte |Volgende aanvragen voor het maken van aanvullende regels worden afgewezen en er wordt een uitzonde ring ontvangen door de aanroepende code. |Maximum aantal regels: 12. <br /><br /> Regels die zijn geconfigureerd voor een Service Bus naam ruimte, zijn van toepassing op alle wacht rijen en onderwerpen in die naam ruimte. |
| Aantal berichten per trans actie | Transactie | Extra inkomende berichten worden geweigerd en een uitzonde ring met de melding ' kan niet meer dan 100 berichten verzenden in één trans actie ' wordt ontvangen door de aanroepende code. | 100 <br /><br /> Voor bewerkingen voor **verzenden ()** en **SendAsync ()** . |
| Aantal regels voor het virtuele netwerk en IP-filter | Naamruimte | &nbsp; | 128 | 

[Azure portal]: https://portal.azure.com
