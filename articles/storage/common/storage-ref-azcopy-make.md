---
title: azcopy maken | Microsoft Docs
description: In dit artikel vindt u Naslag informatie voor de opdracht azcopy.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: ebed5c8dbe5001e9beab17bdbff41610277143b2
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72514689"
---
# <a name="azcopy-make"></a>azcopy make

Hiermee maakt u een container of bestands share.

## <a name="synopsis"></a>Samen vatting

Maak een container of bestands share die wordt vertegenwoordigd door de opgegeven bron-URL.

```azcopy
azcopy make [resourceURL] [flags]
```

## <a name="examples"></a>Voorbeelden

```azcopy
azcopy make "https://[account-name].[blob,file,dfs].core.windows.net/[top-level-resource-name]"
```

## <a name="options"></a>Opties

|Optie|Beschrijving|
|--|--|
|-h,--Help|Help-inhoud voor de opdracht maken weer geven. |
|--quota-GB uint32|Hiermee geeft u de maximale grootte van de share in gigabytes (GiB), 0 betekent dat u het standaard quotum van de bestands service accepteert.|

## <a name="options-inherited-from-parent-commands"></a>Opties overgenomen van bovenliggende opdrachten

|Optie|Beschrijving|
|---|---|
|--Cap-Mbps uint32|De overdrachts frequentie in megabits per seconde. Even door Voer kan enigszins afwijken van het kapje. Als deze optie is ingesteld op nul of wordt wegge laten, wordt de door Voer niet afgetopt.|
|--type teken reeks voor uitvoer|De indeling van de uitvoer van de opdracht. De opties zijn onder andere: Text, JSON. De standaard waarde is "text".|

## <a name="see-also"></a>Zie ook

- [azcopy](storage-ref-azcopy.md)
