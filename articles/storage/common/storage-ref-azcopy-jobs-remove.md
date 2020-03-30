---
title: azcopy-taken verwijderen | Microsoft Documenten
description: In dit artikel vindt u referentiegegevens voor de opdracht verwijderen van azcopy-taken.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: ba403c8d823b7ead0414521ebd51dc6f6601ccd8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74034161"
---
# <a name="azcopy-jobs-remove"></a>azcopy jobs remove

Verwijder alle bestanden die zijn gekoppeld aan de opgegeven taak-id.

> [!NOTE] 
> U de locatie aanpassen waar logboek- en planbestanden worden opgeslagen. Zie de [azcopy env](storage-ref-azcopy-env.md) opdracht voor meer informatie.

```
azcopy jobs remove [jobID] [flags]
```

## <a name="related-conceptual-articles"></a>Gerelateerde conceptuele artikelen

- [Aan de slag met AzCopy](storage-use-azcopy-v10.md)
- [Gegevens overbrengen met AzCopy- en Blob-opslag](storage-use-azcopy-blobs.md)
- [Gegevens overbrengen met AzCopy en bestandsopslag](storage-use-azcopy-files.md)
- [AzCopy configureren, optimaliseren en oplossen](storage-use-azcopy-configure.md)

## <a name="examples"></a>Voorbeelden

```
  azcopy jobs rm e52247de-0323-b14d-4cc8-76e0be2e2d44
```

## <a name="options"></a>Opties

**-h, --help**                Hulp voor verwijderen.

## <a name="options-inherited-from-parent-commands"></a>Opties die zijn overgenomen van bovenliggende opdrachten

**--cap-mbps uint32**      Caps de overdrachtssnelheid, in megabits per seconde. De doorvoer van moment tot moment kan enigszins afwijken van de dop. Als deze optie is ingesteld op nul of wordt weggelaten, wordt de doorvoer niet afgetopt.

**--uitvoer-type** tekenreeks Notatie van de uitvoer van de opdracht. De keuzes zijn: tekst, json. De standaardwaarde is 'tekst'. (standaard "tekst")

## <a name="see-also"></a>Zie ook

- [azcopy jobs](storage-ref-azcopy-jobs.md)
