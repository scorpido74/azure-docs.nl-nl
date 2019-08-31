---
title: azcopy-taken | Microsoft Docs
description: In dit artikel vindt u Naslag informatie voor de opdracht azcopy Jobs.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: bd5469691f8d859bfcbca7479cdcb2e468a94e26
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195839"
---
# <a name="azcopy-jobs"></a>azcopy-taken

Subopdrachten met betrekking tot het beheren van taken.

## <a name="examples"></a>Voorbeelden

```azcopy
azcopy jobs show [jobID]
```

## <a name="options"></a>Opties

|Optie|Description|
|--|--|
|-h,--Help|Help-inhoud voor de opdracht taken weer geven.|

## <a name="options-inherited-from-parent-commands"></a>Opties overgenomen van bovenliggende opdrachten

|Optie|Description|
|---|---|
|--Cap-Mbps uint32|De overdrachts frequentie in megabits per seconde. Even door Voer kan enigszins afwijken van het kapje. Als deze optie is ingesteld op nul of wordt wegge laten, wordt de door Voer niet afgetopt.|
|--type teken reeks voor uitvoer|De indeling van de uitvoer van de opdracht. De opties zijn onder andere: Text, JSON. De standaard waarde is "text".|

## <a name="see-also"></a>Zie ook

- [azcopy](storage-ref-azcopy.md)
- [azcopy-taken lijst](storage-ref-azcopy-jobs-list.md)
- [azcopy-taken hervatten](storage-ref-azcopy-jobs-resume.md)
- [azcopy-taken weer geven](storage-ref-azcopy-jobs-show.md)
