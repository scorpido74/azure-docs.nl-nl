---
title: azcopy verwijderen | Microsoft Docs
description: In dit artikel vindt u Naslag informatie voor de opdracht azcopy Remove.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 0cc366ab2cdad9c7258dca905d8f4a06472119fe
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195904"
---
# <a name="azcopy-remove"></a>azcopy verwijderen

Hiermee verwijdert u entiteiten uit Azure Storage Blob, bestand en Azure Data Lake Storage Gen2.

## <a name="synopsis"></a>Samen vatting

```azcopy
azcopy remove [resourceURL] [flags]
```

## <a name="examples"></a>Voorbeelden

Eén BLOB verwijderen met SAS:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Een volledige virtuele map met een SAS verwijderen:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

Alleen de top-blobs in een virtuele map, maar niet de onderliggende mappen, verwijderen:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

Verwijder een subset blobs in een virtuele map (bijvoorbeeld: alleen jpg-en PDF-bestanden, of als de blobnaam ' exacte naam ' is):

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --include="*.jpg;*.pdf;exactName"
```

Verwijder een volledige virtuele map, maar sluit bepaalde blobs uit van het bereik (bijvoorbeeld: elke blob die begint met foo of eindigt op de balk):

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --exclude="foo*;*bar"
```

Eén bestand verwijderen uit Data Lake Storage Gen2 (include en exclude niet ondersteund):

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/file]?[SAS]"
```

Eén map verwijderen uit Data Lake Storage Gen2 (include en exclude niet ondersteund):

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/directory]?[SAS]"
```

## <a name="options"></a>Opties

|Optie|Description|
|--|--|
|--teken reeks uitsluiten|Sluit bestanden uit waarvan de naam overeenkomt met de patroon lijst. Bijvoorbeeld: *. jpg;* . PDF; exactenaam|
|-h,--Help|Help-inhoud voor de Remove-opdracht weer geven.|
|--reeks toevoegen|Alleen bestanden opnemen waarvan de naam overeenkomt met de patroon lijst. Bijvoorbeeld: *. jpg;* . PDF; exactenaam|
|--teken reeks op logboek niveau|Definieer de uitgebreide logboek registratie voor het logboek bestand. Beschik bare niveaus zijn: INFO (alle aanvragen/antwoorden), waarschuwing (trage antwoorden), fout (alleen mislukte aanvragen) en geen (geen uitvoer Logboeken). (standaard INFO)|
|--recursief|In submappen recursief bekijken bij het synchroniseren tussen directory's.|

## <a name="options-inherited-from-parent-commands"></a>Opties overgenomen van bovenliggende opdrachten

|Optie|Description|
|---|---|
|--Cap-Mbps uint32|De overdrachts frequentie in megabits per seconde. Even door Voer kan enigszins afwijken van het kapje. Als deze optie is ingesteld op nul of wordt wegge laten, wordt de door Voer niet afgetopt.|
|--type teken reeks voor uitvoer|De indeling van de uitvoer van de opdracht. De opties zijn onder andere: Text, JSON. De standaard waarde is "text".|

## <a name="see-also"></a>Zie ook

- [azcopy](storage-ref-azcopy.md)
