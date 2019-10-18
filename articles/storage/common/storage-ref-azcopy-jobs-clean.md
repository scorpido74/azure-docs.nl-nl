---
title: azcopy-taken opschonen | Microsoft Docs
description: In dit artikel vindt u Naslag informatie voor de opdracht azcopy-taken opschonen.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 31529155ee44b2bcfad90e8634053403dfe8fc8c
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72518510"
---
# <a name="azcopy-jobs-clean"></a>azcopy-taken opschonen

Alle logboek bestanden en abonnementen voor alle taken verwijderen

```
azcopy jobs clean [flags]
```

## <a name="examples"></a>Voorbeelden

```
  azcopy jobs clean --with-status=completed
```

## <a name="options"></a>Opties

**-h,--Help**                Help voor schonen.

**--met-status** teken reeks verwijdert alleen de taken met deze status, beschik bare waarden: geannuleerd, voltooid, mislukt, InProgress, alle (standaard ' all ')

## <a name="options-inherited-from-parent-commands"></a>Opties overgenomen van bovenliggende opdrachten

**--Cap-Mbps uint32**      De overdrachts frequentie in megabits per seconde. Even door Voer kan enigszins afwijken van het kapje. Als deze optie is ingesteld op nul of wordt wegge laten, wordt de door Voer niet afgetopt.

**--** de teken reeks indeling van het uitvoer type van de uitvoer van de opdracht. De opties zijn onder andere: Text, JSON. De standaard waarde is ' text '. (standaard tekst)

## <a name="see-also"></a>Zie ook

- [azcopy-taken](storage-ref-azcopy-jobs.md)
