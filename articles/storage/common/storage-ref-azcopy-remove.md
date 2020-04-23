---
title: azcopy verwijderen | Microsoft Docs
description: In dit artikel vindt u Naslag informatie voor de opdracht azcopy Remove.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: abce1acb88e920c0de7bbb6447ec9d838f10486c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74033986"
---
# <a name="azcopy-remove"></a>azcopy remove

Blobs of bestanden verwijderen uit een Azure-opslag account.

## <a name="synopsis"></a>Samen vatting

```azcopy
azcopy remove [resourceURL] [flags]
```

## <a name="related-conceptual-articles"></a>Gerelateerde conceptuele artikelen

- [Aan de slag met AzCopy](storage-use-azcopy-v10.md)
- [Gegevens overdragen met AzCopy en Blob Storage](storage-use-azcopy-blobs.md)
- [Gegevens overdragen met AzCopy en File Storage](storage-use-azcopy-files.md)
- [AzCopy configureren, optimaliseren en problemen oplossen](storage-use-azcopy-configure.md)

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

Verwijder specifieke blobs en virtuele mappen door hun relatieve paden (niet met URL-code ring) in een bestand te plaatsen:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/parent/dir]" --recursive=true --list-of-files=/usr/bar/list.txt
file content:
  dir1/dir2
  blob1
  blob2

```

Een enkel bestand verwijderen uit een Blob Storage account met een hiërarchische naam ruimte (opnemen/uitsluiten niet ondersteund).

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/file]?[SAS]"
```

Eén map verwijderen een Blob Storage account met een hiërarchische naam ruimte (opnemen/uitsluiten niet ondersteund):

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/directory]?[SAS]"
```

## <a name="options"></a>Opties

**--exclude-teken reeks**      Deze paden uitsluiten bij het verwijderen. Deze optie biedt geen ondersteuning voor joker tekens (*). Hiermee wordt het relatieve pad-voor voegsel gecontroleerd. Bijvoorbeeld: myFolder; myFolder/subDirName/file. PDF.

**--exclude-patroon** teken reeks bestanden uitsluiten waarbij de naam overeenkomt met de patroon lijst. Bijvoorbeeld: *. jpg;*. PDF; exactenaam

**-h,--Help** Help voor verwijderen

**--include-** teken reeks bevat alleen deze paden bij het verwijderen. Deze optie biedt geen ondersteuning voor joker tekens (*). Hiermee wordt het relatieve pad-voor voegsel gecontroleerd. Bijvoorbeeld: myFolder; myFolder/subDirName/file. PDF

**--include-patroon** teken reeks alleen bestanden opnemen waarvan de naam overeenkomt met de patroon lijst. Bijvoorbeeld: *. jpg;*. PDF; exactenaam

**--lijst-of-files** teken reeks definieert de locatie van een bestand dat de lijst met bestanden en mappen bevat die moeten worden verwijderd. De relatieve paden moeten worden gescheiden door regel einden en de paden mogen geen URL-gecodeerd zijn.

**--** teken reeks op logboek niveau Definieer het logboek bestand uitgebreider voor het logbestand. Beschik bare niveaus zijn: INFO (alle aanvragen/antwoorden), waarschuwing (trage antwoorden), fout (alleen mislukte aanvragen) en geen (geen uitvoer Logboeken). (standaard INFO) (standaard INFO)

**--recursief**                In submappen recursief bekijken bij het synchroniseren tussen directory's.

## <a name="options-inherited-from-parent-commands"></a>Opties overgenomen van bovenliggende opdrachten

|Optie|Beschrijving|
|---|---|
|--Cap-Mbps uint32|De overdrachts frequentie in megabits per seconde. Even door Voer kan enigszins afwijken van het kapje. Als deze optie is ingesteld op nul of wordt wegge laten, wordt de door Voer niet afgetopt.|
|--type teken reeks voor uitvoer|De indeling van de uitvoer van de opdracht. De opties zijn onder andere: Text, JSON. De standaard waarde is "text".|

## <a name="see-also"></a>Zie ook

- [azcopy](storage-ref-azcopy.md)
