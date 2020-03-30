---
title: azcopy banen schoon | Microsoft Documenten
description: In dit artikel vindt u referentiegegevens voor de opdracht azcopy-taken.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 7ae14c3606dfe6bffa8481682843f3f2e85c2131
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74033725"
---
# <a name="azcopy-jobs-clean"></a>azcopy jobs clean

Alle logboeken verwijderen en bestanden plannen voor alle taken

```
azcopy jobs clean [flags]
```

## <a name="related-conceptual-articles"></a>Gerelateerde conceptuele artikelen

- [Aan de slag met AzCopy](storage-use-azcopy-v10.md)
- [Gegevens overbrengen met AzCopy- en Blob-opslag](storage-use-azcopy-blobs.md)
- [Gegevens overbrengen met AzCopy en bestandsopslag](storage-use-azcopy-files.md)
- [AzCopy configureren, optimaliseren en oplossen](storage-use-azcopy-configure.md)

## <a name="examples"></a>Voorbeelden

```
  azcopy jobs clean --with-status=completed
```

## <a name="options"></a>Opties

**-h, --help**                Hulp voor schoon.

**--met-statustekenreeks** Alleen de taken met deze status verwijderen, beschikbare waarden: Geannuleerd, voltooid, mislukt, InProgress, Alles (standaard 'Alles')

## <a name="options-inherited-from-parent-commands"></a>Opties die zijn overgenomen van bovenliggende opdrachten

**--cap-mbps uint32**      Caps de overdrachtssnelheid, in megabits per seconde. De doorvoer van moment tot moment kan enigszins afwijken van de dop. Als deze optie is ingesteld op nul of wordt weggelaten, wordt de doorvoer niet afgetopt.

**--uitvoer-type** tekenreeks Notatie van de uitvoer van de opdracht. De keuzes zijn: tekst, json. De standaardwaarde is 'tekst'. (standaard "tekst")

## <a name="see-also"></a>Zie ook

- [azcopy jobs](storage-ref-azcopy-jobs.md)
