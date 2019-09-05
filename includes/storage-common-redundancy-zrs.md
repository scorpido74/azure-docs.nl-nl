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
ms.openlocfilehash: b7fddce8f682bc341b361a47f8e083cc281e90aa
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70309571"
---
Zone-redundante opslag (ZRS) repliceert uw gegevens synchroon over drie opslag clusters in één regio. Elk opslag cluster is fysiek gescheiden van de andere en bevindt zich in een eigen beschikbaarheids zone (AZ). Elke beschikbaarheids zone&mdash;en het ZRS-cluster in&mdash;it zijn autonoom en bevatten afzonderlijke hulpprogram ma's en netwerk functies. Een schrijf aanvraag naar een ZRS-opslag account retourneert pas nadat de gegevens zijn geschreven naar alle replica's in de drie clusters.

Wanneer u uw gegevens opslaat in een opslag account met behulp van ZRS-replicatie, kunt u uw gegevens blijven benaderen en beheren als een beschikbaarheids zone niet beschikbaar is. ZRS biedt uitstekende prestaties en lage latentie. ZRS biedt dezelfde [schaalbaarheids doelen](../articles/storage/common/storage-scalability-targets.md) als [lokaal redundante opslag (LRS)](../articles/storage/common/storage-redundancy-lrs.md).

Overweeg ZRS voor scenario's die consistentie, duurzaamheid en hoge Beschik baarheid vereisen. Zelfs als een storing of een natuur ramp een beschikbaarheids zone weer geven die niet beschikbaar is, biedt ZRS duurzaamheid voor opslag objecten van ten minste 99,9999999999% (12 9) gedurende een bepaald jaar.

Geo-zone-redundante opslag (GZRS) (preview) repliceert uw gegevens synchroon over drie Azure-beschikbaarheids zones in de primaire regio. vervolgens worden de gegevens asynchroon gerepliceerd naar de secundaire regio. GZRS biedt hoge Beschik baarheid samen met maximale duurzaamheid. GZRS is ontworpen om ten minste 99.99999999999999% (16 9) duurzaamheid van objecten in een bepaald jaar te bieden. Voor lees toegang tot gegevens in de secundaire regio schakelt u geo-zone-redundante opslag met lees toegang (RA-GZRS) in. Zie [geo-zone-redundante opslag voor hoge Beschik baarheid en maximale duurzaamheid (preview)](../articles/storage/common/storage-redundancy-gzrs.md)voor meer informatie over GZRS.

Zie [Beschikbaarheidszones Overview](https://docs.microsoft.com/azure/availability-zones/az-overview)voor meer informatie over beschikbaarheids zones.
