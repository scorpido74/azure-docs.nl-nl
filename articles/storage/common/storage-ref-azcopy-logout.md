---
title: azcopy afmelden | Microsoft Docs
description: In dit artikel vindt u Naslag informatie voor de afmeldings opdracht azcopy.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: f42e2517f8c40855e56bd062fe8bc9b22634d4dc
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195917"
---
# <a name="azcopy-logout"></a>azcopy afmelden

Meldt de gebruiker af en beëindigt de toegang tot Azure Storage-resources.

## <a name="synopsis"></a>Samen vatting

Met deze opdracht worden alle aanmeldings gegevens in de cache van de huidige gebruiker verwijderd.

```azcopy
azcopy logout [flags]
```

## <a name="options"></a>Opties

|Optie|Description|
|--|--|
|-h,--Help|Help-inhoud voor de afmeldings opdracht weer geven.|

## <a name="options-inherited-from-parent-commands"></a>Opties overgenomen van bovenliggende opdrachten

|Optie|Description|
|---|---|
|--Cap-Mbps uint32|De overdrachts frequentie in megabits per seconde. Even door Voer kan enigszins afwijken van het kapje. Als deze optie is ingesteld op nul of wordt wegge laten, wordt de door Voer niet afgetopt.|
|--type teken reeks voor uitvoer|De indeling van de uitvoer van de opdracht. De opties zijn onder andere: Text, JSON. De standaard waarde is "text".|

## <a name="see-also"></a>Zie ook

- [azcopy](storage-ref-azcopy.md)
