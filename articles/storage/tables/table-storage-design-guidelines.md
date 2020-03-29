---
title: Richtlijnen voor azure-opslagtabelontwerp | Microsoft Documenten
description: Ontwerp uw Azure-tabelservice om leesbewerkingen efficiënt te ondersteunen.
services: storage
author: SnehaGunda
ms.service: storage
ms.topic: article
ms.date: 04/23/2018
ms.author: sngun
ms.subservice: tables
ms.openlocfilehash: d056d29469ad9a60fceeee307aca3c0e1319283c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "61269830"
---
# <a name="guidelines-for-table-design"></a>Richtlijnen voor tabelontwerp

Het ontwerpen van tabellen voor gebruik met de Azure-opslagtabelservice verschilt sterk van ontwerpoverwegingen voor een relationele database. In dit artikel worden richtlijnen beschreven voor het ontwerpen van uw Table-serviceoplossing om efficiënt te lezen en efficiënt te schrijven.

## <a name="design-your-table-service-solution-to-be-read-efficient"></a>Ontwerp uw Table-serviceoplossing om leesefficiënt te zijn

* ***Ontwerp voor query's in leeszware toepassingen.*** Wanneer u uw tabellen ontwerpt, moet u nadenken over de query's (met name de latentiegevoelige) die u uitvoert voordat u nadenkt over hoe u uw entiteiten bijwerkt. Dit resulteert meestal in een efficiënte en performante oplossing.  
* ***Geef zowel PartitionKey als RowKey op in uw query's.*** *Puntquery's* zoals deze zijn de meest efficiënte tabelservicequery's.  
* ***Overweeg dubbele kopieën van entiteiten op te slaan.*** Tabelopslag is goedkoop, dus overweeg om dezelfde entiteit meerdere keren (met verschillende sleutels) op te slaan om efficiëntere query's mogelijk te maken.  
* ***Overweeg uw gegevens te denormaliseren.*** Tabelopslag is goedkoop, dus overweeg uw gegevens te denormaliseren. Sla bijvoorbeeld overzichtsentiteiten op, zodat query's voor geaggregeerde gegevens slechts toegang hoeven te krijgen tot één entiteit.  
* ***Gebruik samengestelde sleutelwaarden.*** De enige toetsen die u hebt zijn **PartitionKey** en **RowKey.** Gebruik bijvoorbeeld samengestelde sleutelwaarden om alternatieve hoofdtoegangspaden naar entiteiten in te schakelen.  
* ***Queryprojectie gebruiken.*** U de hoeveelheid gegevens die u via het netwerk overdraagt, verminderen door query's te gebruiken die alleen de velden selecteren die u nodig hebt.  

## <a name="design-your-table-service-solution-to-be-write-efficient"></a>Ontwerp uw Table-serviceoplossing om schrijfefficiënt te zijn  

* ***Maak geen hete partities.*** Kies toetsen waarmee u uw aanvragen op elk gewenst moment over meerdere partities spreiden.  
* ***Vermijd pieken in het verkeer.*** Glad het verkeer over een redelijke periode van tijd en vermijd pieken in het verkeer.
* ***Maak niet noodzakelijkerwijs een aparte tabel voor elk type entiteit.*** Wanneer u atoomtransacties voor entiteitstypen nodig hebt, u deze meerdere entiteitstypen opslaan in dezelfde partitie in dezelfde tabel.
* ***Houd rekening met de maximale doorvoer die u moet bereiken.*** U moet zich bewust zijn van de schaalbaarheidsdoelstellingen voor de tabelservice en ervoor zorgen dat uw ontwerp ervoor zorgt dat u deze niet overschrijdt.  

Als u deze gids leest, ziet u voorbeelden die al deze beginselen in de praktijk brengen. 

## <a name="next-steps"></a>Volgende stappen

- [Tabelontwerppatronen](table-storage-design-patterns.md)
- [Ontwerp voor query's](table-storage-design-for-query.md)
- [Tabelgegevens versleutelen](table-storage-design-encrypt-data.md)
- [Ontwerp voor gegevenswijziging](table-storage-design-for-modification.md)
