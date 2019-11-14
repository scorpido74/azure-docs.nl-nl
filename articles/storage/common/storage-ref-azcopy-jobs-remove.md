---
title: azcopy-taken verwijderen | Microsoft Docs
description: In dit artikel vindt u Naslag informatie voor de opdracht azcopy-taken verwijderen.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: ba403c8d823b7ead0414521ebd51dc6f6601ccd8
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74034161"
---
# <a name="azcopy-jobs-remove"></a>azcopy jobs remove

Alle bestanden verwijderen die zijn gekoppeld aan de opgegeven taak-ID.

> [!NOTE] 
> U kunt de locatie aanpassen waarin logboek-en plan bestanden worden opgeslagen. Zie de [azcopy env](storage-ref-azcopy-env.md) -opdracht voor meer informatie.

```
azcopy jobs remove [jobID] [flags]
```

## <a name="related-conceptual-articles"></a>Gerelateerde conceptuele artikelen

- [Aan de slag met AzCopy](storage-use-azcopy-v10.md)
- [Gegevens overdragen met AzCopy en Blob Storage](storage-use-azcopy-blobs.md)
- [Gegevens overdragen met AzCopy en File Storage](storage-use-azcopy-files.md)
- [AzCopy configureren, optimaliseren en problemen oplossen](storage-use-azcopy-configure.md)

## <a name="examples"></a>Voorbeelden

```
  azcopy jobs rm e52247de-0323-b14d-4cc8-76e0be2e2d44
```

## <a name="options"></a>Opties

**-h,--Help**                Help voor verwijderen.

## <a name="options-inherited-from-parent-commands"></a>Opties overgenomen van bovenliggende opdrachten

**--Cap-Mbps uint32**      De overdrachts frequentie in megabits per seconde. Even door Voer kan enigszins afwijken van het kapje. Als deze optie is ingesteld op nul of wordt wegge laten, wordt de door Voer niet afgetopt.

**--** de teken reeks indeling van het uitvoer type van de uitvoer van de opdracht. De opties zijn onder andere: Text, JSON. De standaard waarde is ' text '. (standaard tekst)

## <a name="see-also"></a>Zie ook

- [azcopy-taken](storage-ref-azcopy-jobs.md)
