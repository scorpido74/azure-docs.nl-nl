---
title: azcopy-synchronisatie | Microsoft Docs
description: In dit artikel vindt u Naslag informatie voor de azcopy-synchronisatie opdracht.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: d4b43b590b147335a70877a7c3c0b07f8b818e3c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84221062"
---
# <a name="azcopy-sync"></a>azcopy sync

De bron locatie wordt gerepliceerd naar de doel locatie.

## <a name="synopsis"></a>Samen vatting

De tijd die het laatst is gewijzigd, wordt gebruikt voor de vergelijking. Het bestand wordt overgeslagen als het tijdstip van de laatste wijziging in de bestemming recenter is.

De ondersteunde paren zijn:

- lokale <-> Azure-Blob (ofwel SAS als OAuth-verificatie kan worden gebruikt)
- Azure Blob <-> Azure-Blob (de bron moet een SAS bevatten of openbaar toegankelijk zijn; SAS of OAuth-verificatie kan worden gebruikt voor de bestemming)
- Azure file <-> Azure-bestand (de bron moet een SAS bevatten of openbaar toegankelijk zijn; SAS-verificatie moet worden gebruikt voor de bestemming)

De synchronisatie opdracht wijkt af van de Kopieer opdracht op verschillende manieren:

1. Standaard is de recursieve vlag ingesteld op True en worden alle submappen gekopieerd met Sync. Met Sync worden alleen de bestanden op het hoogste niveau in een Directory gekopieerd als de recursieve vlag is ingesteld op false.
2. Wanneer u synchroniseert tussen virtuele mappen, voegt u een slash toe aan het pad (Zie voor beelden) als er een blob is met dezelfde naam als een van de virtuele mappen.
3. Als de vlag ' deleteDestination ' is ingesteld op ' True ' of ' prompt ', worden de bestanden en blobs die niet aanwezig zijn in de bron, door synchronisatie verwijderd.

## <a name="related-conceptual-articles"></a>Gerelateerde conceptuele artikelen

- [Aan de slag met AzCopy](storage-use-azcopy-v10.md)
- [Gegevens overdragen met AzCopy en Blob Storage](storage-use-azcopy-blobs.md)
- [Gegevens overdragen met AzCopy en File Storage](storage-use-azcopy-files.md)
- [AzCopy configureren, optimaliseren en problemen oplossen](storage-use-azcopy-configure.md)

### <a name="advanced"></a>Geavanceerd

Als u geen bestands extensie opgeeft, detecteert AzCopy automatisch het inhouds type van de bestanden bij het uploaden van vanaf de lokale schijf, op basis van de bestands extensie of inhoud (als er geen extensie is opgegeven).

De ingebouwde opzoek tabel is klein, maar op UNIX wordt deze uitgebreid met de MIME. type-bestanden van het lokale systeem, indien beschikbaar onder een of meer van deze namen:

- /etc/mime.types
- /etc/apache2/mime.types
- /etc/apache/mime.types

In Windows worden MIME-typen geëxtraheerd uit het REGI ster.

```azcopy
azcopy sync <source> <destination> [flags]
```

## <a name="examples"></a>Voorbeelden

Eén bestand synchroniseren:

```azcopy
azcopy sync "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```

> [!NOTE]
> De doel-BLOB *moet* bestaan. Gebruiken `azcopy copy` om één bestand te kopiëren dat nog niet in het doel bestaat. Anders wordt de volgende fout weer gegeven: `Cannot perform sync due to error: sync must happen between source and destination of the same type, e.g. either file <-> file, or directory/container <-> directory/container` .

Hetzelfde als hierboven, maar deze keer ook de MD5-hash van de bestands inhoud en sla deze op als de eigenschap content-MD5 van de blob:

```azcopy
azcopy sync "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]" --put-md5
```

Een volledige directory met inbegrip van de submappen synchroniseren (Houd er rekening mee dat recursieve standaard is ingeschakeld):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]"
```

of

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --put-md5
```

Alleen de bovenste bestanden in een map, maar niet de submappen, synchroniseren:

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

Synchroniseer een subset van bestanden in een map (bijvoorbeeld: alleen jpg-en PDF-bestanden, of als de bestands naam "exacte") is:

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --include="*.jpg;*.pdf;exactName"
```

Een volledige Directory synchroniseren, maar bepaalde bestanden uitsluiten van het bereik (bijvoorbeeld: elk bestand dat begint met foo of eindigt op de balk):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --exclude="foo*;*bar"
```

Eén BLOB synchroniseren:

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```

Een virtuele map synchroniseren:

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=true
```

Synchroniseer een virtuele map met dezelfde naam als een BLOB (Voeg een afsluitende slash toe aan het pad om te dubbel zinnigheid):

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]/?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]/" --recursive=true
```

Een Azure-Bestands Directory synchroniseren (dezelfde syntaxis als blob):

```azcopy
azcopy sync "https://[account].file.core.windows.net/[share]/[path/to/dir]?[SAS]" "https://[account].file.core.windows.net/[share]/[path/to/dir]" --recursive=true
```

> [!NOTE]
> Als er in combi natie/uitsluiten-vlaggen samen worden gebruikt, worden alleen bestanden die overeenkomen met de include-patronen, bekeken, maar deze die overeenkomen met de uitgesloten patronen, worden altijd genegeerd.

## <a name="options"></a>Opties

**--block-size-MB** float gebruik deze blok grootte (opgegeven in MIB) bij het uploaden naar Azure Storage of het downloaden van Azure Storage. De standaard waarde wordt automatisch berekend op basis van de bestands grootte. Decimale breuken zijn toegestaan (bijvoorbeeld: 0,25).

**--Check-MD5** teken reeks geeft aan hoe strikt MD5-hashes moeten worden gevalideerd bij het downloaden. Deze optie is alleen beschikbaar wanneer u downloadt. Beschik bare waarden zijn: nacontroleren, aanmelden, FailIfDifferent, FailIfDifferentOrMissing. (standaard ' FailIfDifferent '). (standaard "FailIfDifferent")

**--Delete-doel** teken reeks bepaalt of extra bestanden van de doel locatie die niet aanwezig zijn op de bron worden verwijderd. Kan worden ingesteld op True, False of prompt. Als deze instelling is ingesteld op vragen, wordt de gebruiker gevraagd een vraag te stellen voordat bestanden en blobs voor verwijdering worden gepland. (standaard ' false '). (standaard ingesteld op ' false ')

**--exclude-kenmerken** teken reeks (alleen Windows) bestanden uitsluiten waarvan de kenmerken overeenkomen met de kenmerk lijst. Bijvoorbeeld: A; Z N

**--exclude-** teken reeks van het pad deze paden uitsluiten bij het kopiëren. Deze optie biedt geen ondersteuning voor joker tekens (*). Hiermee wordt het relatieve pad voor voegsel gecontroleerd (bijvoorbeeld: myFolder; myFolder/subDirName/file.pdf). Bij gebruik in combi natie met account-traversal bevatten paden geen container naam.

**--exclude-patroon** teken reeks bestanden uitsluiten waarbij de naam overeenkomt met de patroon lijst. Bijvoorbeeld: \* . jpg; \* . PDF; exactenaam

**-h,--Help** Help voor synchronisatie

**--include-** kenmerk reeks (alleen Windows) bevatten alleen bestanden waarvan de kenmerken overeenkomen met de kenmerk lijst. Bijvoorbeeld: A; Z N

**--include-patroon** teken reeks alleen bestanden opnemen waarvan de naam overeenkomt met de patroon lijst. Bijvoorbeeld: \* . jpg; \* . PDF; exactenaam

**--** teken reeks op logboek niveau definiëren de logboek uitgebreidheids voor het logboek bestand, beschik bare niveaus: info (alle aanvragen en antwoorden), waarschuwing (trage reacties), fout (alleen mislukte aanvragen) en geen (geen uitvoer Logboeken). (standaard INFO). (standaard INFO)

**--put-MD5**                     Maak een MD5-hash van elk bestand en sla de hash op als de eigenschap content-MD5 van de BLOB of het doel bestand. (Standaard wordt de hash niet gemaakt.) Alleen beschikbaar bij het uploaden.

**--recursief**                   In de meeste gevallen wordt standaard in submappen gezocht bij het synchroniseren tussen directory's. (standaard instelling waar). (standaard instelling waar)

## <a name="options-inherited-from-parent-commands"></a>Opties overgenomen van bovenliggende opdrachten

|Optie|Description|
|---|---|
|--Cap-Mbps uint32|De overdrachts frequentie in megabits per seconde. Even door Voer kan enigszins afwijken van het kapje. Als deze optie is ingesteld op nul of wordt wegge laten, wordt de door Voer niet afgetopt.|
|--type teken reeks voor uitvoer|De indeling van de uitvoer van de opdracht. De opties zijn onder andere: Text, JSON. De standaard waarde is "text".|
|--vertrouwd-micro soft-achtervoegsels teken reeks   |Hiermee geeft u aanvullende domein achtervoegsels op waar Azure Active Directory aanmeldings tokens kunnen worden verzonden.  De standaard waarde is *. core.Windows.net;*. core.chinacloudapi.cn; *. core.cloudapi.de;*. core.usgovcloudapi.net '. Alle hier vermelde waarden worden toegevoegd aan de standaard instelling. Voor beveiliging moet u Microsoft Azure domeinen hier alleen plaatsen. Scheid meerdere vermeldingen met een punt komma.|

## <a name="see-also"></a>Zie tevens

- [azcopy](storage-ref-azcopy.md)
