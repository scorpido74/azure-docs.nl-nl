---
title: azcopy-lijst | Microsoft Docs
description: In dit artikel vindt u Naslag informatie voor de opdracht azcopy list.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 8c40241c49917c6db6663f346aed0ec5d3b96be7
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195943"
---
# <a name="azcopy-list"></a>lijst met azcopy

Hiermee worden de entiteiten in een bepaalde resource weer gegeven.

## <a name="synopsis"></a>Samen vatting

Alleen BLOB-containers worden ondersteund in de huidige release.

```azcopy
azcopy list [containerURL] [flags]
```

## <a name="examples"></a>Voorbeelden

```azcopy
azcopy list [containerURL]
```

## <a name="options"></a>Opties

|Optie|Description|
|--|--|
|-h,--Help|Help-inhoud voor de lijst opdracht weer geven.|
|--computer-leesbaar|Geeft een lijst van bestands grootten in bytes.|
|--Mega eenheden|Eenheden weer geven in orders van 1000, niet 1024.|
|--wordt uitgevoerd|Telt het totale aantal bestanden en hun grootte.|

## <a name="options-inherited-from-parent-commands"></a>Opties overgenomen van bovenliggende opdrachten

|Optie|Description|
|---|---|
|--Cap-Mbps uint32|De overdrachts frequentie in megabits per seconde. Even door Voer kan enigszins afwijken van het kapje. Als deze optie is ingesteld op nul of wordt wegge laten, wordt de door Voer niet afgetopt.|
|--type teken reeks voor uitvoer|De indeling van de uitvoer van de opdracht. De opties zijn onder andere: Text, JSON. De standaard waarde is "text".|

## <a name="see-also"></a>Zie ook

- [azcopy](storage-ref-azcopy.md)
