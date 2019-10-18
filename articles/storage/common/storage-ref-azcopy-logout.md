---
title: azcopy afmelden | Microsoft Docs
description: In dit artikel vindt u Naslag informatie voor de afmeldings opdracht azcopy.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 84b65470b12212eb1038e18bd442ff07511a5c1a
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72513511"
---
# <a name="azcopy-logout"></a>azcopy logout

Meldt de gebruiker af en beëindigt de toegang tot Azure Storage-resources.

## <a name="synopsis"></a>Samen vatting

Met deze opdracht worden alle aanmeldings gegevens in de cache van de huidige gebruiker verwijderd.

```azcopy
azcopy logout [flags]
```

## <a name="options"></a>Opties

|Optie|Beschrijving|
|--|--|
|-h,--Help|Help-inhoud voor de afmeldings opdracht weer geven.|

## <a name="options-inherited-from-parent-commands"></a>Opties overgenomen van bovenliggende opdrachten

|Optie|Beschrijving|
|---|---|
|--Cap-Mbps uint32|De overdrachts frequentie in megabits per seconde. Even door Voer kan enigszins afwijken van het kapje. Als deze optie is ingesteld op nul of wordt wegge laten, wordt de door Voer niet afgetopt.|
|--type teken reeks voor uitvoer|De indeling van de uitvoer van de opdracht. De opties zijn onder andere: Text, JSON. De standaard waarde is "text".|

## <a name="see-also"></a>Zie ook

- [azcopy](storage-ref-azcopy.md)
