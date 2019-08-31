---
title: azcopy-taken hervatten | Microsoft Docs
description: In dit artikel vindt u Naslag informatie voor de opdracht azcopy Jobs hervatten.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 8786da42f000a2f13279499159a7af424aa10748
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195787"
---
# <a name="azcopy-jobs-resume"></a>azcopy-taken hervatten

Hiermee wordt de bestaande taak hervat met de opgegeven taak-ID.

## <a name="synopsis"></a>Samen vatting

```azcopy
azcopy jobs resume [jobID] [flags]
```

## <a name="options"></a>Opties

|Optie|Description|
|--|--|
|--doel-SAS-teken reeks|De doel-SAS van het doel voor de opgegeven JobId.|
|--teken reeks uitsluiten|Filterwebonderdelen Deze mislukte overdracht (en) uitsluiten wanneer de taak wordt hervat. Bestanden moeten worden gescheiden door '; '.|
|-h,--Help|Help-inhoud weer geven voor de opdracht hervatten.|
|--reeks toevoegen|Filter: deze mislukte overdracht (en) zijn alleen beschikbaar wanneer de taak wordt hervat. Bestanden moeten worden gescheiden door '; '.|
|--SAS-teken reeks |Bron-SAS van de bron voor de opgegeven JobId.|

## <a name="options-inherited-from-parent-commands"></a>Opties overgenomen van bovenliggende opdrachten

|Optie|Description|
|---|---|
|--Cap-Mbps uint32|De overdrachts frequentie in megabits per seconde. Even door Voer kan enigszins afwijken van het kapje. Als deze optie is ingesteld op nul of wordt wegge laten, wordt de door Voer niet afgetopt.|
|--type teken reeks voor uitvoer|De indeling van de uitvoer van de opdracht. De opties zijn onder andere: Text, JSON. De standaard waarde is "text".|

## <a name="see-also"></a>Zie ook

- [azcopy-taken](storage-ref-azcopy-jobs.md)
