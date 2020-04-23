---
title: azcopy sync | Microsoft Documenten
description: In dit artikel vindt u referentiegegevens voor de opdracht azcopy-synchronisatie.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: d855019be7f357a35a26d14e68ba3d427d984e17
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82086025"
---
# <a name="azcopy-sync"></a>azcopy sync

Hiermee wordt de bronlocatie gerepliceerd naar de bestemmingslocatie.

## <a name="synopsis"></a>Synopsis

De laatste gewijzigde tijden worden gebruikt voor vergelijking. Het bestand wordt overgeslagen als de laatste gewijzigde tijd in de bestemming recenter is.

De ondersteunde paren zijn:

- lokale <-> Azure Blob (SAS- of OAuth-verificatie kan worden gebruikt)
- Azure Blob <-> Azure Blob (Bron moet een SAS bevatten of openbaar toegankelijk zijn; SAS- of OAuth-verificatie kan worden gebruikt voor bestemming)
- Azure File <-> Azure File (Bron moet een SAS bevatten of openbaar toegankelijk is; SAS-verificatie moet worden gebruikt voor bestemming)

De opdracht synchroniseren verschilt op verschillende manieren van de opdracht kopiëren:

1. Standaard is de recursieve vlag true en worden alle submappen gesynchroniseerd. Synchronisatie kopieert alleen de bestanden op het hoogste niveau in een map als de recursieve vlag vals is.
2. Wanneer u synchroniseert tussen virtuele mappen, voegt u een slepende slash toe aan het pad (zie voorbeelden) als er een blob is met dezelfde naam als een van de virtuele mappen.
3. Als de vlag 'deleteDestination' is ingesteld op true of prompt, worden bestanden en blobs op de bestemming verwijderd die niet bij de bron aanwezig zijn.

## <a name="related-conceptual-articles"></a>Gerelateerde conceptuele artikelen

- [Aan de slag met AzCopy](storage-use-azcopy-v10.md)
- [Gegevens overbrengen met AzCopy- en Blob-opslag](storage-use-azcopy-blobs.md)
- [Gegevens overbrengen met AzCopy en bestandsopslag](storage-use-azcopy-files.md)
- [AzCopy configureren, optimaliseren en oplossen](storage-use-azcopy-configure.md)

### <a name="advanced"></a>Geavanceerd

Als u geen bestandsextensie opgeeft, detecteert AzCopy automatisch het inhoudstype van de bestanden bij het uploaden vanaf de lokale schijf, op basis van de bestandsextensie of -inhoud (als er geen extensie is opgegeven).

De ingebouwde opzoektabel is klein, maar op Unix wordt deze aangevuld met het mime.types-bestand(en) van het lokale systeem indien beschikbaar onder een of meer van deze namen:

- /etc/mime.types
- /etc/apache2/mime.types
- /etc/apache/mime.types

Op Windows worden MIME-typen uit het register gehaald.

```azcopy
azcopy sync <source> <destination> [flags]
```

## <a name="examples"></a>Voorbeelden

Synchroniseer één bestand:

```azcopy
azcopy sync "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```

> [!NOTE]
> De doelblob *moet* bestaan. Met `azcopy copy` gebruiken om één bestand te kopiëren dat nog niet bestaat in de bestemming. Anders treedt de volgende `Cannot perform sync due to error: sync must happen between source and destination of the same type, e.g. either file <-> file, or directory/container <-> directory/container`fout op: .

Hetzelfde als hierboven, maar deze keer, ook berekenen MD5 hash van de bestandsinhoud en op te slaan als de blob's Content-MD5 eigenschap:

```azcopy
azcopy sync "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]" --put-md5
```

Synchroniseer een hele map inclusief de submappen (houd er rekening mee dat recursief standaard is ingeschakeld):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]"
```

of

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --put-md5
```

Synchroniseer alleen de bovenste bestanden in een map, maar niet de submappen:

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

Een subset van bestanden in een map synchroniseren (bijvoorbeeld: alleen jpg- en pdf-bestanden, of als de bestandsnaam 'exactName' is):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --include="*.jpg;*.pdf;exactName"
```

Synchroniseer een hele map, maar sluit bepaalde bestanden uit van het bereik (Bijvoorbeeld: elk bestand dat begint met foo of eindigt met de balk):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --exclude="foo*;*bar"
```

Synchroniseer één blob:

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```

Een virtuele map synchroniseren:

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=true
```

Een virtuele map synchroniseren met dezelfde naam als een blob (voeg een slepende slash toe aan het pad om te disambiguateren):

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]/?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]/" --recursive=true
```

Een Azure-bestandsmap synchroniseren (dezelfde syntaxis als Blob):

```azcopy
azcopy sync "https://[account].file.core.windows.net/[share]/[path/to/dir]?[SAS]" "https://[account].file.core.windows.net/[share]/[path/to/dir]" --recursive=true
```

> [!NOTE]
> Als vlaggen worden gebruikt bij het opnemen/uitsluiten worden samen gebruikt, worden alleen bestanden die overeenkomen met de include-patronen bekeken, maar die welke overeenkomen met de uitsluitingspatronen, worden altijd genegeerd.

## <a name="options"></a>Opties

**--block-size-mb** float Gebruik deze blokgrootte (opgegeven in MiB) bij het uploaden naar Azure Storage of het downloaden van Azure Storage. Standaard wordt automatisch berekend op basis van de bestandsgrootte. Decimale breuken zijn toegestaan (bijvoorbeeld: 0,25).

**--check-md5-tekenreeks** Geeft aan hoe strikt MD5 hashes moeten worden gevalideerd bij het downloaden. Deze optie is alleen beschikbaar bij het downloaden. Beschikbare waarden zijn: NoCheck, LogOnly, FailIfDifferent, FailIfDifferentOrMissing. (standaard 'FailIfDifferent'). (standaard "FailIfDifferent")

**--delete-destination** string Definieert of extra bestanden van de bestemming moeten worden verwijderd die niet bij de bron aanwezig zijn. Kan worden ingesteld op waar, onwaar of snel. Als de gebruiker wordt gevraagd, wordt een vraag gesteld voordat hij bestanden en blobs plant voor verwijdering. (standaard 'false'). (standaard "false")

**tekenreeks --exclude-attributeen** (alleen Windows) Bestanden uitsluiten waarvan de kenmerken overeenkomen met de kenmerklijst. Bijvoorbeeld: A; S; R

**--exclude-padtekenreeks** Sluit deze paden uit bij het kopiëren. Deze optie biedt geen ondersteuning voor jokertekens (*). Hiermee controleert u het relatieve padvoorvoegsel(Bijvoorbeeld: myFolder;myFolder/subDirName/file.pdf). Bij gebruik in combinatie met accounttraversal bevatten paden de containernaam niet.

**--exclude-patroonreeks** Bestanden uitsluiten waarbij de naam overeenkomt met de patroonlijst. Bijvoorbeeld: \*.jpg; \*.pdf;exactName

**-h, --help** voor synchronisatie

**--include-attributentekenreeks** (alleen Windows) Voeg alleen bestanden toe waarvan de kenmerken overeenkomen met de lijst met kenmerken. Bijvoorbeeld: A; S; R

**--include-patroontekenreeks** Voeg alleen bestanden toe waar de naam overeenkomt met de patroonlijst. Bijvoorbeeld: \*.jpg; \*.pdf;exactName

**--tekenreeks op logboekniveau** Definieer de logboekverbositeit voor het logboekbestand, beschikbare niveaus: INFO (alle aanvragen en antwoorden), WAARSCHUWING(langzame reacties), FOUT (alleen mislukte aanvragen) en GEEN (geen uitvoerlogboeken). (standaard INFO). (standaard "INFO")

**--put-md5**                     Maak een MD5-hash van elk bestand en sla de hash op als de eigenschap Content-MD5 van de doelblob of -bestand. (Standaard wordt de hash NIET gemaakt.) Alleen beschikbaar bij het uploaden.

**--recursieve**                   True standaard, kijken naar sub-directories recursief bij het synchroniseren tussen mappen. (standaard true). (standaard true)

## <a name="options-inherited-from-parent-commands"></a>Opties die zijn overgenomen van bovenliggende opdrachten

|Optie|Beschrijving|
|---|---|
|--cap-mbps uint32|Caps de overdrachtssnelheid, in megabits per seconde. De doorvoer van moment tot moment kan enigszins afwijken van de dop. Als deze optie is ingesteld op nul of wordt weggelaten, wordt de doorvoer niet afgetopt.|
|--uitvoer-type tekenreeks|Indeling van de uitvoer van de opdracht. De keuzes zijn: tekst, json. De standaardwaarde is "tekst".|

## <a name="see-also"></a>Zie ook

- [azcopy](storage-ref-azcopy.md)
