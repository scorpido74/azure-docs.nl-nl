---
title: azcopy verwijderen | Microsoft Documenten
description: In dit artikel vindt u referentiegegevens voor de opdracht azcopy verwijderen.
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

Blobs of bestanden verwijderen uit een Azure-opslagaccount.

## <a name="synopsis"></a>Synopsis

```azcopy
azcopy remove [resourceURL] [flags]
```

## <a name="related-conceptual-articles"></a>Gerelateerde conceptuele artikelen

- [Aan de slag met AzCopy](storage-use-azcopy-v10.md)
- [Gegevens overbrengen met AzCopy- en Blob-opslag](storage-use-azcopy-blobs.md)
- [Gegevens overbrengen met AzCopy en bestandsopslag](storage-use-azcopy-files.md)
- [AzCopy configureren, optimaliseren en oplossen](storage-use-azcopy-configure.md)

## <a name="examples"></a>Voorbeelden

Verwijder één blob met SAS:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Verwijder een volledige virtuele map met een SAS:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

Verwijder alleen de bovenste blobs in een virtuele map, maar niet de submappen:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

Een subset van blobs in een virtuele map verwijderen (bijvoorbeeld: alleen jpg- en pdf-bestanden of als de blobnaam 'exactName' is):

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --include="*.jpg;*.pdf;exactName"
```

Verwijder een volledige virtuele map, maar sluit bepaalde blobs uit van het bereik (Bijvoorbeeld: elke blob die begint met foo of eindigt met balk):

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --exclude="foo*;*bar"
```

Verwijder specifieke blobs en virtuele mappen door hun relatieve paden (NIET URL-gecodeerd) in een bestand te plaatsen:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/parent/dir]" --recursive=true --list-of-files=/usr/bar/list.txt
file content:
  dir1/dir2
  blob1
  blob2

```

Verwijder één bestand uit een Blob Storage-account met een hiërarchische naamruimte (opnemen/uitsluiten niet ondersteund).

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/file]?[SAS]"
```

Verwijder één map een Blob Storage-account met een hiërarchische naamruimte (niet ondersteund opnemen/uitsluiten):

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/directory]?[SAS]"
```

## <a name="options"></a>Opties

**--exclude-padtekenreeks**      Sluit deze paden uit bij het verwijderen. Deze optie biedt geen ondersteuning voor jokertekens (*). Hiermee controleert u het relatieve padvoorvoegsel. Bijvoorbeeld: myFolder;myFolder/subDirName/file.pdf.

**--exclude-patroonreeks** Bestanden uitsluiten waarbij de naam overeenkomt met de patroonlijst. Bijvoorbeeld: *.jpg;*. pdf;exactNaam

**-h, --help** voor verwijderen

**--include-padtekenreeks** Voeg alleen deze paden toe bij het verwijderen. Deze optie biedt geen ondersteuning voor jokertekens (*). Hiermee controleert u het relatieve padvoorvoegsel. Bijvoorbeeld: myFolder;myFolder/subDirName/file.pdf

**--include-patroontekenreeks** Voeg alleen bestanden toe waar de naam overeenkomt met de patroonlijst. Bijvoorbeeld: *.jpg;*. pdf;exactNaam

**--lijst van bestanden** tekenreeks Definieert de locatie van een bestand dat de lijst met bestanden en mappen bevat die moeten worden verwijderd. De relatieve paden moeten worden afgebakend door regeleinden en de paden mogen NIET worden gecodeerd met URL.The relative paths should be delimit by line breaks, and the paths should NOT be URL-encoded.

**--tekenreeks op logboekniveau** Definieer de logboekverbositeit voor het logboekbestand. Beschikbare niveaus zijn: INFO(alle aanvragen/antwoorden), WAARSCHUWING(langzame reacties), FOUT (alleen mislukte aanvragen) en GEEN (geen uitvoerlogboeken). (standaard 'INFO') (standaard "INFO")

**--recursieve**                Kijk in sub-directories recursief bij het synchroniseren tussen mappen.

## <a name="options-inherited-from-parent-commands"></a>Opties die zijn overgenomen van bovenliggende opdrachten

|Optie|Beschrijving|
|---|---|
|--cap-mbps uint32|Caps de overdrachtssnelheid, in megabits per seconde. De doorvoer van moment tot moment kan enigszins afwijken van de dop. Als deze optie is ingesteld op nul of wordt weggelaten, wordt de doorvoer niet afgetopt.|
|--uitvoer-type tekenreeks|Indeling van de uitvoer van de opdracht. De keuzes zijn: tekst, json. De standaardwaarde is "tekst".|

## <a name="see-also"></a>Zie ook

- [azcopie](storage-ref-azcopy.md)
