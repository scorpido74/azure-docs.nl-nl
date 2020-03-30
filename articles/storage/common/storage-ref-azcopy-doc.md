---
title: azcopy doc | Microsoft Documenten
description: In dit artikel vindt u referentiegegevens voor de opdracht azcopy doc.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: d071517377053e8f4f22ad00966e2be688b0d486
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74029861"
---
# <a name="azcopy-doc"></a>azcopy doc

Hiermee genereert u documentatie voor het hulpprogramma in de Markdown-indeling.

## <a name="synopsis"></a>Synopsis

Hiermee genereert u documentatie voor het hulpprogramma in de Markdown-indeling en worden deze opgeslagen op de aangewezen locatie.

Standaard worden de bestanden opgeslagen in een map met de naam 'doc' in de huidige map.

```azcopy
azcopy doc [flags]
```

## <a name="related-conceptual-articles"></a>Gerelateerde conceptuele artikelen

- [Aan de slag met AzCopy](storage-use-azcopy-v10.md)
- [Gegevens overbrengen met AzCopy- en Blob-opslag](storage-use-azcopy-blobs.md)
- [Gegevens overbrengen met AzCopy en bestandsopslag](storage-use-azcopy-files.md)
- [AzCopy configureren, optimaliseren en oplossen](storage-use-azcopy-configure.md)

## <a name="options"></a>Opties

|Optie|Beschrijving|
|--|--|
|-h, --help|Hiermee worden help-inhoud voor de opdracht document weergegeven.|

## <a name="options-inherited-from-parent-commands"></a>Opties die zijn overgenomen van bovenliggende opdrachten

|Optie|Beschrijving|
|---|---|
|--cap-mbps uint32|Caps de overdrachtssnelheid, in megabits per seconde. De doorvoer van moment tot moment kan enigszins afwijken van de dop. Als deze optie is ingesteld op nul of wordt weggelaten, wordt de doorvoer niet afgetopt.|
|--uitvoer-type tekenreeks|Indeling van de uitvoer van de opdracht. De keuzes zijn: tekst, json. De standaardwaarde is "tekst".|

## <a name="see-also"></a>Zie ook

- [azcopie](storage-ref-azcopy.md)
