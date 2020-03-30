---
title: Schaalbaarheid - Azure Event Hubs | Microsoft Documenten
description: In dit artikel vindt u informatie over het schalen van Azure Event Hubs met behulp van partities en doorvoereenheden.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280948"
---
# <a name="scaling-with-event-hubs"></a>Schalen met gebeurtenishubs

Er zijn twee factoren die van invloed zijn op het schalen met Event Hubs.
*   Doorvoereenheden
*   Partities

## <a name="throughput-units"></a>Doorvoereenheden

De doorvoercapaciteit van Event Hubs wordt aangestuurd door *doorvoereenheden.* Doorvoereenheden zijn vooraf aangeschafte capaciteitseenheden. Met één doorvoerdoorvoer u:

* Ingress: Tot 1 MB per seconde of 1000 gebeurtenissen per seconde (wat het eerst komt).
* Uitgang: tot 2 MB per seconde of 4096 gebeurtenissen per seconde.

Wanneer de capaciteit van de aangekochte doorvoereenheden wordt overschreven, wordt de invoer vertraagd en een [ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) afgegeven. De uitvoer geeft geen vertragingsuitzonderingen af, maar is nog steeds beperkt tot de capaciteit van de aangekochte doorvoereenheden. Als zich uitzonderingen met betrekking tot de publicatiesnelheid voordoen of als u meer uitgaande gegevens verwacht, controleert u hoeveel doorvoereenheden u hebt aangeschaft voor de naamruimte. U kunt doorvoereenheden beheren op de blade **Schaal** van de naamruimten in [Azure Portal](https://portal.azure.com). U doorvoereenheden ook programmatisch beheren met behulp van de [API's voor gebeurtenishubs.](event-hubs-api-overview.md)

Doorvoereenheden worden vooraf gekocht en worden per uur gefactureerd. Nadat u doorvoereenheden hebt aangeschaft, worden deze voor minimaal één uur in rekening gebracht. Er kunnen maximaal 20 doorvoereenheden worden aangeschaft voor een naamruimte voor gebeurtenishubs en worden gedeeld over alle gebeurtenishubs in die naamruimte.

De **functie Automatisch opblazen** van gebeurtenishubs wordt automatisch opschalen door het aantal doorvoereenheden te verhogen om aan de gebruiksbehoeften te voldoen. Het verhogen van doorvoereenheden voorkomt beperkingsscenario's, waarbij:

- Gegevensinvallen overschrijden de ingestelde doorvoereenheden.
- Gegevensuitgangsaanvraagsnelheden overschrijden de ingestelde doorvoereenheden.

De service Gebeurtenishubs verhoogt de doorvoer wanneer de belasting toeneemt boven de minimumdrempel, zonder dat aanvragen mislukken met serverdrukkefouten. 

Zie [Doorvoereenheden automatisch schalen](event-hubs-auto-inflate.md)voor meer informatie over de functie automatisch opblazen.

## <a name="partitions"></a>Partities
[!INCLUDE [event-hubs-partitions](../../includes/event-hubs-partitions.md)]

### <a name="partition-key"></a>Partitiesleutel

U een [partitiesleutel](event-hubs-programming-guide.md#partition-key) gebruiken om binnenkomende gebeurtenisgegevens in specifieke partities toe te passen ten behoeve van gegevensorganisatie. De partitiesleutel is een door de afzender opgegeven waarde die aan een Event Hub wordt doorgegeven. De partitiesleutel wordt verwerkt door een statische hash-functie, die zorgt voor de partitietoewijzing. Als u bij het publiceren van een gebeurtenis geen partitiesleutel opgeeft, wordt er gebruikgemaakt van round robin-toewijzing.

De gebeurtenisuitgever is alleen op de hoogte van de partitiesleutel en niet van de partitie waarop de gebeurtenissen worden gepubliceerd. Deze ontkoppeling van sleutel en partitie schermt de afzender af, zodat deze niet te veel te weten hoeft te komen over de downstreamverwerking. Goede partitiesleutels zijn bijvoorbeeld een apparaatspecifieke of een gebruikersspecifieke identiteit, maar voor het groeperen van gerelateerde gebeurtenissen in dezelfde partitie kunnen ook andere kenmerken, zoals geografie, worden gebruikt.


## <a name="next-steps"></a>Volgende stappen
U kunt meer informatie over Event Hubs vinden via de volgende koppelingen:

- [Doorvoereenheden automatisch schalen](event-hubs-auto-inflate.md)
- [Overzicht van de service event hubs](event-hubs-what-is-event-hubs.md)
