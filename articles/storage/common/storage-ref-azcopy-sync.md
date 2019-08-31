---
title: azcopy-synchronisatie | Microsoft Docs
description: In dit artikel vindt u Naslag informatie voor de azcopy-synchronisatie opdracht.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: fb6c3b711a89ae7e4ef403a75927c4c6172523d0
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195761"
---
# <a name="azcopy-sync"></a>azcopy-synchronisatie

De bron locatie wordt gerepliceerd naar de doel locatie.

## <a name="synopsis"></a>Samen vatting

De tijd die het laatst is gewijzigd, wordt gebruikt voor de vergelijking. Het bestand wordt overgeslagen als het tijdstip van de laatste wijziging in de bestemming recenter is.

De ondersteunde paren zijn:

- lokale <-> Azure-Blob (ofwel SAS als OAuth-verificatie kan worden gebruikt)

De synchronisatie opdracht wijkt af van de Kopieer opdracht op verschillende manieren:

  1. De recursieve markering is standaard ingeschakeld.
  2. De bron en het doel mogen geen patronen (zoals * of?) bevatten.
  3. De vlaggen include en exclude kunnen een lijst met patronen zijn die overeenkomen met de bestands namen. Raadpleeg de sectie voor beeld voor afbeelding.
  4. Als er zich bestanden of blobs op de bestemming bevinden die niet aanwezig zijn in de bron, wordt de gebruiker gevraagd deze te verwijderen.

     Deze prompt kan worden versneld met behulp van de bijbehorende vlaggen voor het automatisch beantwoorden van de verwijderings vraag.

### <a name="advanced"></a>Geavanceerd

AzCopy detecteert automatisch het inhouds type van de bestanden bij het uploaden van op de lokale schijf, op basis van de bestands extensie of inhoud (als er geen extensie is opgegeven).

De ingebouwde opzoek tabel is klein, maar op UNIX wordt deze uitgebreid met de MIME. type-bestanden van het lokale systeem, indien beschikbaar onder een of meer van deze namen:

- /etc/mime.types
- /etc/apache2/mime.types
- /etc/apache/mime.types

In Windows worden MIME-typen geëxtraheerd uit het REGI ster.

```azcopy
azcopy sync [flags]
```

## <a name="examples"></a>Voorbeelden

Eén bestand synchroniseren:

```azcopy
azcopy sync "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```

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

> [!NOTE]
> Als er in combi natie/uitsluiten-vlaggen samen worden gebruikt, worden alleen bestanden die overeenkomen met de include-patronen, bekeken, maar deze die overeenkomen met de uitgesloten patronen, worden altijd genegeerd.

## <a name="options"></a>Opties

|Optie|Description|
|--|--|
|--blok-grootte-MB float|Gebruik deze blok grootte (opgegeven in MiB) bij het uploaden naar Azure Storage of het downloaden van Azure Storage. De standaard waarde wordt automatisch berekend op basis van de bestands grootte. Decimale breuken zijn toegestaan (bijvoorbeeld: 0,25).|
|--check-MD5-teken reeks|Hiermee geeft u op hoe strikt MD5-hashes moeten worden gevalideerd bij het downloaden. Deze optie is alleen beschikbaar wanneer u downloadt. Beschik bare waarden zijn: Niet controleren, aanmelden, FailIfDifferent, FailIfDifferentOrMissing. (standaard "FailIfDifferent").|
|--delete-doel teken reeks|Hiermee wordt bepaald of extra bestanden van de doel locatie die niet aanwezig zijn op de bron worden verwijderd. Kan worden ingesteld op True, False of prompt. Als deze instelling is ingesteld op vragen, wordt de gebruiker gevraagd een vraag te stellen voordat bestanden en blobs voor verwijdering worden gepland. (de standaard instelling is False).|
|--teken reeks uitsluiten|Sluit bestanden uit waarvan de naam overeenkomt met de patroon lijst. Bijvoorbeeld: *. jpg;* . PDF; exactenaam.|
|-h,--Help|Help-inhoud voor de synchronisatie opdracht weer geven.|
|--reeks toevoegen|Alleen bestanden opnemen waarvan de naam overeenkomt met de lijst met patronen. Bijvoorbeeld: *. jpg;* . PDF; exactenaam.|
|--teken reeks op logboek niveau|Definieer de uitgebreide logboek registratie voor het logboek bestand, beschik bare niveaus: INFO (alle aanvragen/antwoorden), waarschuwing (trage antwoorden), fout (alleen mislukte aanvragen) en geen (geen uitvoer Logboeken). (standaard INFO).|
|--put-MD5|Maak een MD5-hash van elk bestand en sla de hash op als de eigenschap content-MD5 van de BLOB of het doel bestand. (Standaard wordt de hash niet gemaakt.) Alleen beschikbaar bij het uploaden.|
|--recursief|In de meeste gevallen wordt standaard in submappen gezocht bij het synchroniseren tussen directory's. (standaard instelling waar).|

## <a name="options-inherited-from-parent-commands"></a>Opties overgenomen van bovenliggende opdrachten

|Optie|Description|
|---|---|
|--Cap-Mbps uint32|De overdrachts frequentie in megabits per seconde. Even door Voer kan enigszins afwijken van het kapje. Als deze optie is ingesteld op nul of wordt wegge laten, wordt de door Voer niet afgetopt.|
|--type teken reeks voor uitvoer|De indeling van de uitvoer van de opdracht. De opties zijn onder andere: Text, JSON. De standaard waarde is "text".|

## <a name="see-also"></a>Zie ook

- [azcopy](storage-ref-azcopy.md)
