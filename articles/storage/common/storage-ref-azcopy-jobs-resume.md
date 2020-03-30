---
title: azcopy-taken hervatten | Microsoft Documenten
description: In dit artikel vindt u referentiegegevens voor de opdracht cv-taken van azcopy.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 3ee7879475801660b5200dddca88a0a81b2b6b9b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74034146"
---
# <a name="azcopy-jobs-resume"></a>azcopy jobs resume

Hiermee wordt de bestaande taak hervat met de opgegeven taak-id.

## <a name="synopsis"></a>Synopsis

```azcopy
azcopy jobs resume [jobID] [flags]
```

## <a name="related-conceptual-articles"></a>Gerelateerde conceptuele artikelen

- [Aan de slag met AzCopy](storage-use-azcopy-v10.md)
- [Gegevens overbrengen met AzCopy- en Blob-opslag](storage-use-azcopy-blobs.md)
- [Gegevens overbrengen met AzCopy en bestandsopslag](storage-use-azcopy-files.md)
- [AzCopy configureren, optimaliseren en oplossen](storage-use-azcopy-configure.md)

## <a name="options"></a>Opties

|Optie|Beschrijving|
|--|--|
|--destination-sas-tekenreeks|Bestemming SAS van de bestemming voor bepaalde JobId.|
|--tekenreeks uitsluiten|Filter: Sluit deze mislukte overdracht(en) uit bij het hervatten van de taak. Bestanden moeten worden gescheiden door ';'.|
|-h, --help|Help-inhoud weergeven voor de opdracht Hervatten.|
|--snaar opnemen|Filter: neem deze mislukte overdracht(en) alleen op bij het hervatten van de taak. Bestanden moeten worden gescheiden door ';'.|
|--bron-sas-tekenreeks |bron SAS van de bron voor bepaalde JobId.|

## <a name="options-inherited-from-parent-commands"></a>Opties die zijn overgenomen van bovenliggende opdrachten

|Optie|Beschrijving|
|---|---|
|--cap-mbps uint32|Caps de overdrachtssnelheid, in megabits per seconde. De doorvoer van moment tot moment kan enigszins afwijken van de dop. Als deze optie is ingesteld op nul of wordt weggelaten, wordt de doorvoer niet afgetopt.|
|--uitvoer-type tekenreeks|Indeling van de uitvoer van de opdracht. De keuzes zijn: tekst, json. De standaardwaarde is "tekst".|

## <a name="see-also"></a>Zie ook

- [azcopy jobs](storage-ref-azcopy-jobs.md)
