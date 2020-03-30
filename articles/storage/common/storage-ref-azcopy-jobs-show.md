---
title: azcopy jobs show | Microsoft Documenten
description: In dit artikel vindt u referentiegegevens voor de opdracht azcopy-taken.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 7b5f566757dd77a61f252b123d0c9c1b74303fbe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74034120"
---
# <a name="azcopy-jobs-show"></a>azcopy jobs show

Toont gedetailleerde informatie voor de opgegeven taak-ID.

## <a name="synopsis"></a>Synopsis

Als alleen de taak-ID zonder vlag wordt geleverd, wordt de voortgangssamenvatting van de taak geretourneerd.

De bytetellingen en het percentage voltooid die worden weergegeven wanneer u deze opdracht uitvoert, weerspiegelen alleen bestanden die in de taak zijn voltooid. Ze weerspiegelen geen gedeeltelijk voltooide bestanden.

Als `with-status` de vlag is ingesteld, wordt de lijst met overdrachten in de taak met de opgegeven waarde weergegeven.

```azcopy
azcopy jobs show [jobID] [flags]
```

## <a name="related-conceptual-articles"></a>Gerelateerde conceptuele artikelen

- [Aan de slag met AzCopy](storage-use-azcopy-v10.md)
- [Gegevens overbrengen met AzCopy- en Blob-opslag](storage-use-azcopy-blobs.md)
- [Gegevens overbrengen met AzCopy en bestandsopslag](storage-use-azcopy-files.md)
- [AzCopy configureren, optimaliseren en oplossen](storage-use-azcopy-configure.md)

## <a name="options"></a>Opties

|Optie|Beschrijving|
|--|--|
|-h, --help|Hiermee worden help-inhoud voor de opdracht Weergeven weergegeven.|
|--met-statustekenreeks|Alleen een lijst van de overdrachten van taak met deze status, beschikbare waarden: Gestart, Succes, Mislukt|

## <a name="options-inherited-from-parent-commands"></a>Opties die zijn overgenomen van bovenliggende opdrachten

|Optie|Beschrijving|
|---|---|
|--cap-mbps uint32|Caps de overdrachtssnelheid, in megabits per seconde. De doorvoer van moment tot moment kan enigszins afwijken van de dop. Als deze optie is ingesteld op nul of wordt weggelaten, wordt de doorvoer niet afgetopt.|
|--uitvoer-type tekenreeks|Indeling van de uitvoer van de opdracht. De keuzes zijn: tekst, json. De standaardwaarde is "tekst".|

## <a name="see-also"></a>Zie ook

- [azcopy jobs](storage-ref-azcopy-jobs.md)
