---
title: Service Bus onderwerpen worden uitgevoerd vanuit Azure Stream Analytics
description: In dit artikel worden Service Bus onderwerpen beschreven als uitvoer voor Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: ef5802d0c5e35b9c12db1f40782ba5f190ad1883
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90907192"
---
# <a name="service-bus-topics-output-from-azure-stream-analytics"></a>Service Bus onderwerpen worden uitgevoerd vanuit Azure Stream Analytics

Service Bus wachtrijen bieden een een-op-een-communicatie methode van afzender naar ontvanger. [Service Bus onderwerpen](https://msdn.microsoft.com/library/azure/hh367516.aspx) bieden een een-op-veel communicatie vorm.

De volgende tabel bevat de namen van de eigenschappen en de bijbehorende beschrijvingen voor het maken van een Service Bus topic-uitvoer.

| Naam van eigenschap | Description |
| --- | --- |
| Uitvoeralias |Een beschrijvende naam die wordt gebruikt in query's om de uitvoer van de query naar dit Service Bus onderwerp te sturen. |
| Service Bus naam ruimte |Een container voor een set met bericht entiteiten. Wanneer u een nieuwe Event Hub hebt gemaakt, hebt u ook een Service Bus naam ruimte gemaakt. |
| Onderwerpnaam |Onderwerpen zijn bericht entiteiten, vergelijkbaar met Event hubs en wacht rijen. Ze zijn ontworpen voor het verzamelen van gebeurtenis stromen van apparaten en services. Wanneer een onderwerp wordt gemaakt, wordt er ook een specifieke naam gegeven. De berichten die naar een onderwerp worden verzonden, zijn pas beschikbaar als er een abonnement is gemaakt. Zorg er dus voor dat er een of meer abonnementen zijn onder het onderwerp. |
| Naam van onderwerps beleid |Wanneer u een Service Bus onderwerp maakt, kunt u ook beleid voor gedeelde toegang maken op het tabblad **configureren** van het onderwerp. Elk gedeeld toegangs beleid heeft een naam, machtigingen die u instelt en toegangs sleutels. |
| Sleutel van het onderwerpbeleid |De gedeelde toegangs sleutel die wordt gebruikt voor het verifiëren van toegang tot de naam ruimte Service Bus. |
| Serialisatie-indeling voor gebeurtenissen |De serialisatie-indeling voor uitvoer gegevens. JSON, CSV en Avro worden ondersteund. |
| Encoding |Als u de CSV-of JSON-indeling gebruikt, moet u een code ring opgeven. UTF-8 is op dit moment de enige coderings indeling die wordt ondersteund. |
| Scheidingsteken |Alleen van toepassing op CSV-serialisatie. Stream Analytics ondersteunt een aantal gemeen schappelijke scheidings tekens voor het serialiseren van gegevens in CSV-indeling. Ondersteunde waarden zijn komma, punt komma, spatie, tab en verticale streep. |
| Eigenschappen kolommen | Optioneel. Door komma's gescheiden kolommen die moeten worden toegevoegd als gebruikers eigenschappen van het uitgaande bericht in plaats van de payload. Meer informatie over deze functie vindt u in de sectie [aangepaste meta gegevens eigenschappen voor uitvoer](#custom-metadata-properties-for-output). |
| Kolom systeem eigenschappen | Optioneel. Sleutel waarde-paren van systeem eigenschappen en bijbehorende kolom namen die aan het uitgaande bericht moeten worden toegevoegd in plaats van de payload. |

Het aantal partities is [gebaseerd op de service bus SKU en grootte](../service-bus-messaging/service-bus-partitioning.md). De partitie sleutel is een unieke gehele waarde voor elke partitie.

## <a name="partitioning"></a>Partitionering

Partitioneren wordt automatisch gekozen. Het aantal partities is gebaseerd op de [Service Bus SKU en grootte](../service-bus-messaging/service-bus-partitioning.md). De partitie sleutel is een unieke gehele waarde voor elke partitie. Het aantal schrijvers van de uitvoer is hetzelfde als het aantal partities in het uitvoer onderwerp.

## <a name="output-batch-size"></a>Grootte van uitvoer batch

De maximale bericht grootte is 256 KB per bericht voor de Standard-laag en 1 MB voor de Premium-laag. Zie [Service Bus limieten](../service-bus-messaging/service-bus-quotas.md)voor meer informatie. Als u wilt optimaliseren, gebruikt u één gebeurtenis per bericht.

## <a name="custom-metadata-properties-for-output"></a>Aangepaste meta gegevens eigenschappen voor uitvoer

U kunt query kolommen als gebruikers eigenschappen aan uw uitgaande berichten toevoegen. Deze kolommen gaan niet naar de payload. De eigenschappen zijn aanwezig in de vorm van een woorden lijst in het uitvoer bericht. *Sleutel* is de kolom naam en- *waarde* is de kolom waarde in de woorden lijst eigenschappen. Alle Stream Analytics gegevens typen worden ondersteund, behalve record en matrix.

## <a name="system-properties"></a>Systeemeigenschappen

U kunt query kolommen als [systeem eigenschappen](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azure-dotnet#properties&preserve-view=true) koppelen aan uw uitgaande service bus-wachtrij of onderwerp-berichten. Deze kolommen worden niet in de payload gezet, maar de bijbehorende [systeem eigenschap](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azure-dotnet#properties&preserve-view=true) BrokeredMessage wordt gevuld met de query kolom waarden.
Deze systeem eigenschappen worden ondersteund `MessageId, ContentType, Label, PartitionKey, ReplyTo, SessionId, CorrelationId, To, ForcePersistence, TimeToLive, ScheduledEnqueueTimeUtc` .

Teken reeks waarden van deze kolommen worden geparseerd als overeenkomend type eigenschaps waarde van het systeem en eventuele fouten bij het parseren worden behandeld als gegevens fouten.
Dit veld wordt als een JSON-object indeling gegeven. Meer informatie over deze indeling is als volgt:

* Omgeven door accolades {} .
* Geschreven in sleutel/waarde-paren.
* Sleutels en waarden moeten teken reeksen zijn.
* Sleutel is de naam en waarde van de systeem eigenschap is de kolom naam van de query.
* Sleutels en waarden worden gescheiden door een dubbele punt.
* Elke sleutel/waarde-paar wordt gescheiden door een komma.

Hier ziet u hoe u deze eigenschap gebruikt –

* Ophalen `select *, column1, column2 INTO queueOutput FROM iotHubInput`
* Kolom systeem eigenschappen: `{ "MessageId": "column1", "PartitionKey": "column2"}`

Hiermee stelt u de `MessageId` Service Bus-wachtrij berichten in met `column1` de waarden en PartitionKey wordt ingesteld met `column2` de waarden.

## <a name="next-steps"></a>Volgende stappen

* [Snelstart: Een Stream Analytics-taak maken via Azure Portal](stream-analytics-quick-create-portal.md)
* [Quickstart: een Azure Stream Analytics-taak maken via de Azure CLI](quick-create-azure-cli.md)
* [Quickstart: Een Azure Stream Analytics-taak maken via een ARM-sjabloon](quick-create-azure-resource-manager.md)
* [Quickstart: Een Stream Analytics-taak maken met behulp van Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Quickstart: Een Azure Stream Analytics-taak maken met behulp van Visual Studio](stream-analytics-quick-create-vs.md)
* [Snelstartgids: een Azure Stream Analytics-taak maken in Visual Studio code](quick-create-visual-studio-code.md)
