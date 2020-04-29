---
title: Schaal baarheid-Azure Event Hubs | Microsoft Docs
description: Dit artikel bevat informatie over het schalen van Azure Event Hubs met behulp van partities en doorvoer eenheden.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 06/18/2019
ms.author: shvija
ms.openlocfilehash: 2b36faef8c39a8e9b02a056576ae7f5a77b1f6bf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79280948"
---
# <a name="scaling-with-event-hubs"></a>Schalen met Event Hubs

Er zijn twee factoren die van invloed zijn op schalen met Event Hubs.
*   Doorvoereenheden
*   Partities

## <a name="throughput-units"></a>Doorvoereenheden

De doorvoer capaciteit van Event Hubs wordt bepaald door *doorvoer eenheden*. Doorvoereenheden zijn vooraf aangeschafte capaciteitseenheden. Met één door Voer kunt u:

* Binnenkomend: Maxi maal 1 MB per seconde of 1000 gebeurtenissen per seconde (afhankelijk van wat het eerste komt).
* Uitgang: Maxi maal 2 MB per seconde of 4096 gebeurtenissen per seconde.

Wanneer de capaciteit van de aangekochte doorvoereenheden wordt overschreven, wordt de invoer vertraagd en een [ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) afgegeven. De uitvoer geeft geen vertragingsuitzonderingen af, maar is nog steeds beperkt tot de capaciteit van de aangekochte doorvoereenheden. Als zich uitzonderingen met betrekking tot de publicatiesnelheid voordoen of als u meer uitgaande gegevens verwacht, controleert u hoeveel doorvoereenheden u hebt aangeschaft voor de naamruimte. U kunt doorvoereenheden beheren op de blade **Schaal** van de naamruimten in [Azure Portal](https://portal.azure.com). U kunt doorvoer eenheden ook programmatisch beheren met behulp van de [Event hubs-api's](event-hubs-api-overview.md).

Doorvoer eenheden zijn vooraf aangeschaft en worden per uur gefactureerd. Nadat u doorvoereenheden hebt aangeschaft, worden deze voor minimaal één uur in rekening gebracht. Er kunnen Maxi maal 20 doorvoer eenheden worden aangeschaft voor een Event Hubs naam ruimte en worden gedeeld door alle Event hubs in die naam ruimte.

De functie **automatisch verg Roten** van Event hubs wordt automatisch geschaald door het aantal doorvoer eenheden te verhogen om te voldoen aan de behoeften van het gebruik. Het verhogen van doorvoer eenheden voor komt het beperken van scenario's, waarbij:

- Tarieven voor gegevens ingang overschrijden de ingestelde doorvoer eenheden.
- De tarieven voor gegevens uitwissel aanvragen overschrijden de ingestelde doorvoer eenheden.

De Event Hubs-service verhoogt de door Voer wanneer de belasting groter wordt dan de minimale drempel waarde, zonder dat er aanvragen worden uitgevoerd met ServerBusy-fouten. 

Zie [automatisch schalen door Voer eenheden](event-hubs-auto-inflate.md)voor meer informatie over de functie voor automatisch verg Roten.

## <a name="partitions"></a>Partities
[!INCLUDE [event-hubs-partitions](../../includes/event-hubs-partitions.md)]

### <a name="partition-key"></a>Partitiesleutel

U kunt een [partitie sleutel](event-hubs-programming-guide.md#partition-key) gebruiken om inkomende gebeurtenis gegevens toe te wijzen aan specifieke partities voor het doel van de gegevens organisatie. De partitiesleutel is een door de afzender opgegeven waarde die aan een Event Hub wordt doorgegeven. De partitiesleutel wordt verwerkt door een statische hash-functie, die zorgt voor de partitietoewijzing. Als u bij het publiceren van een gebeurtenis geen partitiesleutel opgeeft, wordt er gebruikgemaakt van round robin-toewijzing.

De gebeurtenisuitgever is alleen op de hoogte van de partitiesleutel en niet van de partitie waarop de gebeurtenissen worden gepubliceerd. Deze ontkoppeling van sleutel en partitie schermt de afzender af, zodat deze niet te veel te weten hoeft te komen over de downstreamverwerking. Goede partitiesleutels zijn bijvoorbeeld een apparaatspecifieke of een gebruikersspecifieke identiteit, maar voor het groeperen van gerelateerde gebeurtenissen in dezelfde partitie kunnen ook andere kenmerken, zoals geografie, worden gebruikt.


## <a name="next-steps"></a>Volgende stappen
U kunt meer informatie over Event Hubs vinden via de volgende koppelingen:

- [Doorvoereenheden automatisch schalen](event-hubs-auto-inflate.md)
- [Overzicht van Event Hubs-service](event-hubs-what-is-event-hubs.md)
