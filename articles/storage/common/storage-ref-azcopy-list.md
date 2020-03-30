---
title: azcopy-lijst | Microsoft Documenten
description: In dit artikel vindt u referentiegegevens voor de opdracht azcopy-lijst.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: f02c1afadf18a7d3170eb178696487464e4a0bd3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74034096"
---
# <a name="azcopy-list"></a>azcopy list

Hiermee worden de entiteiten in een bepaalde resource weergegeven.

## <a name="synopsis"></a>Synopsis

Alleen Blob-containers worden ondersteund in de huidige release.

```azcopy
azcopy list [containerURL] [flags]
```

## <a name="related-conceptual-articles"></a>Gerelateerde conceptuele artikelen

- [Aan de slag met AzCopy](storage-use-azcopy-v10.md)
- [Gegevens overbrengen met AzCopy- en Blob-opslag](storage-use-azcopy-blobs.md)
- [Gegevens overbrengen met AzCopy en bestandsopslag](storage-use-azcopy-files.md)
- [AzCopy configureren, optimaliseren en oplossen](storage-use-azcopy-configure.md)

## <a name="examples"></a>Voorbeelden

```azcopy
azcopy list [containerURL]
```

## <a name="options"></a>Opties

|Optie|Beschrijving|
|--|--|
|-h, --help|Help-inhoud weergeven voor de opdracht lijst.|
|--machineleesbaar|Hiermee worden bestandsformaten weergegeven in bytes.|
|--mega-eenheden|Hiermee worden eenheden weergegeven in orders van 1000, niet 1024.|
|--running-tally|Telt het totale aantal bestanden en hun grootte.|

## <a name="options-inherited-from-parent-commands"></a>Opties die zijn overgenomen van bovenliggende opdrachten

|Optie|Beschrijving|
|---|---|
|--cap-mbps uint32|Caps de overdrachtssnelheid, in megabits per seconde. De doorvoer van moment tot moment kan enigszins afwijken van de dop. Als deze optie is ingesteld op nul of wordt weggelaten, wordt de doorvoer niet afgetopt.|
|--uitvoer-type tekenreeks|Indeling van de uitvoer van de opdracht. De keuzes zijn: tekst, json. De standaardwaarde is "tekst".|

## <a name="see-also"></a>Zie ook

- [azcopie](storage-ref-azcopy.md)
