---
title: bestand opnemen
description: bestand opnemen
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 06/28/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: ac3e87d7f921da2c1089eb6f2c7e61fc2c432f9f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75463933"
---
Zone-redundante opslag (ZRS) repliceert uw gegevens synchroon over drie opslag clusters in één regio. Elk opslag cluster is fysiek gescheiden van de andere en bevindt zich in een eigen beschikbaarheids zone (AZ). Elke&mdash;voor de beschikbaarheids zone en het ZRS-cluster in IT&mdash;autonoom en bevat afzonderlijke hulpprogram ma's en netwerk functies. Een schrijf aanvraag naar een ZRS-opslag account retourneert pas nadat de gegevens zijn geschreven naar alle replica's in de drie clusters.

Wanneer u uw gegevens opslaat in een opslag account met behulp van ZRS-replicatie, kunt u uw gegevens blijven benaderen en beheren als een beschikbaarheids zone niet beschikbaar is. ZRS biedt uitstekende prestaties en lage latentie. ZRS biedt dezelfde schaalbaarheids doelen als [lokaal redundante opslag (LRS)](../articles/storage/common/storage-redundancy-lrs.md). Zie [schaalbaarheids doelen voor standaard opslag accounts](../articles/storage/common/scalability-targets-standard-account.md)voor meer informatie over de schaalbaarheids doelen voor standaard opslag accounts.

Overweeg ZRS voor scenario's die consistentie, duurzaamheid en hoge Beschik baarheid vereisen. Zelfs als een storing of een natuur ramp een beschikbaarheids zone weer geven die niet beschikbaar is, biedt ZRS duurzaamheid voor opslag objecten van ten minste 99,9999999999% (12 9) gedurende een bepaald jaar.

Geo-zone-redundante opslag (GZRS) (preview) repliceert uw gegevens synchroon over drie Azure-beschikbaarheids zones in de primaire regio. vervolgens worden de gegevens asynchroon gerepliceerd naar de secundaire regio. GZRS biedt hoge Beschik baarheid samen met maximale duurzaamheid. GZRS is ontworpen om ten minste 99.99999999999999% (16 9) duurzaamheid van objecten in een bepaald jaar te bieden. Voor lees toegang tot gegevens in de secundaire regio schakelt u geo-zone-redundante opslag met lees toegang (RA-GZRS) in. Zie [geo-zone-redundante opslag voor hoge Beschik baarheid en maximale duurzaamheid (preview)](../articles/storage/common/storage-redundancy-gzrs.md)voor meer informatie over GZRS.

Zie [Beschikbaarheidszones Overview](https://docs.microsoft.com/azure/availability-zones/az-overview)voor meer informatie over beschikbaarheids zones.
