---
title: azcopy-taken weer geven | Microsoft Docs
description: In dit artikel vindt u Naslag informatie voor de opdracht show azcopy Jobs.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 42b8bde9adb6980ff2c7004d43b02fc1fdc38363
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72513450"
---
# <a name="azcopy-jobs-show"></a>azcopy jobs show

Geeft gedetailleerde informatie weer voor de opgegeven taak-ID.

## <a name="synopsis"></a>Samen vatting

Als alleen de taak-ID wordt opgegeven zonder een vlag, wordt de voortgangs samenvatting van de taak geretourneerd.

Het aantal bytes en het percentage voltooid dat wordt weer gegeven wanneer u deze opdracht uitvoert, geeft alleen bestanden weer die in de taak zijn voltooid. Ze geven niet de gedeeltelijk voltooide bestanden weer.

Als de vlag `with-status` is ingesteld, wordt de lijst met overdrachten in de taak met de opgegeven waarde weer gegeven.

```azcopy
azcopy jobs show [jobID] [flags]
```

## <a name="options"></a>Opties

|Optie|Beschrijving|
|--|--|
|-h,--Help|Geeft Help-inhoud weer voor de opdracht weer geven.|
|--met-status teken reeks|Alleen lijst met de overdrachten van taken met deze status, beschik bare waarden: gestart, geslaagd, mislukt|

## <a name="options-inherited-from-parent-commands"></a>Opties overgenomen van bovenliggende opdrachten

|Optie|Beschrijving|
|---|---|
|--Cap-Mbps uint32|De overdrachts frequentie in megabits per seconde. Even door Voer kan enigszins afwijken van het kapje. Als deze optie is ingesteld op nul of wordt wegge laten, wordt de door Voer niet afgetopt.|
|--type teken reeks voor uitvoer|De indeling van de uitvoer van de opdracht. De opties zijn onder andere: Text, JSON. De standaard waarde is "text".|

## <a name="see-also"></a>Zie ook

- [azcopy-taken](storage-ref-azcopy-jobs.md)
