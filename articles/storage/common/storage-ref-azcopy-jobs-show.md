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
ms.openlocfilehash: 7b5f566757dd77a61f252b123d0c9c1b74303fbe
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/27/2020
ms.locfileid: "74034120"
---
# <a name="azcopy-jobs-show"></a>azcopy jobs show

Geeft gedetailleerde informatie weer voor de opgegeven taak-ID.

## <a name="synopsis"></a>Samen vatting

Als alleen de taak-ID wordt opgegeven zonder een vlag, wordt de voortgangs samenvatting van de taak geretourneerd.

Het aantal bytes en het percentage voltooid dat wordt weer gegeven wanneer u deze opdracht uitvoert, geeft alleen bestanden weer die in de taak zijn voltooid. Ze geven niet de gedeeltelijk voltooide bestanden weer.

Als de `with-status` vlag is ingesteld, wordt de lijst met overdrachten in de taak met de opgegeven waarde weer gegeven.

```azcopy
azcopy jobs show [jobID] [flags]
```

## <a name="related-conceptual-articles"></a>Gerelateerde conceptuele artikelen

- [Aan de slag met AzCopy](storage-use-azcopy-v10.md)
- [Gegevens overdragen met AzCopy en Blob Storage](storage-use-azcopy-blobs.md)
- [Gegevens overdragen met AzCopy en File Storage](storage-use-azcopy-files.md)
- [AzCopy configureren, optimaliseren en problemen oplossen](storage-use-azcopy-configure.md)

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

- [azcopy jobs](storage-ref-azcopy-jobs.md)
