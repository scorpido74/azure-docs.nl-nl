---
title: azcopy-taken weer geven | Microsoft Docs
description: In dit artikel vindt u Naslag informatie voor de opdracht show azcopy Jobs.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: d4369bd0c986ee20a0796436fea47509a711de4f
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195956"
---
# <a name="azcopy-jobs-show"></a>azcopy-taken weer geven

Geeft gedetailleerde informatie weer voor de opgegeven taak-ID.

## <a name="synopsis"></a>Samen vatting

Als alleen de taak-ID wordt opgegeven zonder een vlag, wordt de voortgangs samenvatting van de taak geretourneerd.

Als de `with-status` vlag is ingesteld, wordt de lijst met overdrachten in de taak met de opgegeven waarde weer gegeven.

```azcopy
azcopy jobs show [jobID] [flags]
```

## <a name="options"></a>Opties

|Optie|Description|
|--|--|
|-h,--Help|Geeft Help-inhoud weer voor de opdracht weer geven.|
|--met-status teken reeks|Alleen lijst met de overdracht van een taak met deze status, beschik bare waarden: Gestart, geslaagd, mislukt|

## <a name="options-inherited-from-parent-commands"></a>Opties overgenomen van bovenliggende opdrachten

|Optie|Description|
|---|---|
|--Cap-Mbps uint32|De overdrachts frequentie in megabits per seconde. Even door Voer kan enigszins afwijken van het kapje. Als deze optie is ingesteld op nul of wordt wegge laten, wordt de door Voer niet afgetopt.|
|--type teken reeks voor uitvoer|De indeling van de uitvoer van de opdracht. De opties zijn onder andere: Text, JSON. De standaard waarde is "text".|

## <a name="see-also"></a>Zie ook

- [azcopy-taken](storage-ref-azcopy-jobs.md)
