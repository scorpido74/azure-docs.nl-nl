---
title: azcopy kopiëren | Microsoft Docs
description: In dit artikel vindt u Naslag informatie voor de azcopy-Kopieer opdracht.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: c15d188e333bea5e74fa65d2bbdf38ae7fadc246
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195735"
---
# <a name="azcopy-copy"></a>azcopy kopiëren

Kopieert de bron gegevens naar een doel locatie.

## <a name="synopsis"></a>Samen vatting

De ondersteunde richtingen zijn:

- lokale <-> Azure-Blob (SAS of OAuth-verificatie)
- lokale <-> Azure-bestand (share/Directory SAS-verificatie)
- lokale <-> ADLS gen 2 (SAS, OAuth of SharedKey-verificatie)
- Azure Blob (SAS of openbaar) <-> Azure-Blob (SAS of OAuth-verificatie)
- Azure-bestand (SAS)-> Azure Block BLOB (SAS of OAuth-verificatie)
- AWS S3 (toegangs sleutel)-> Azure Block BLOB (SAS of OAuth-verificatie)

Raadpleeg de voor beelden voor meer informatie.

### <a name="advanced"></a>Geavanceerd

AzCopy detecteert automatisch het inhouds type van de bestanden bij het uploaden van op de lokale schijf, op basis van de bestands extensie of inhoud (als er geen extensie is opgegeven).

De ingebouwde opzoek tabel is klein, maar op UNIX wordt deze uitgebreid met de MIME. type-bestanden van het lokale systeem, indien beschikbaar onder een of meer van deze namen:

- /etc/mime.types
- /etc/apache2/mime.types
- /etc/apache/mime.types

In Windows worden MIME-typen geëxtraheerd uit het REGI ster. Deze functie kan worden uitgeschakeld met behulp van een vlag. Raadpleeg de sectie met de vlag.

> [!IMPORTANT]
> Als u een omgevings variabele instelt met behulp van de opdracht regel, wordt die variabele leesbaar in de opdracht regel geschiedenis. U kunt variabelen met referenties uit de opdracht regel geschiedenis wissen. Als u wilt voor komen dat variabelen in uw geschiedenis worden weer gegeven, kunt u een script gebruiken om de gebruiker om referenties te vragen en de omgevings variabele in te stellen.

```azcopy
azcopy copy [source] [destination] [flags]
```

## <a name="examples"></a>Voorbeelden

Upload één bestand met OAuth-verificatie.

Als u zich nog niet hebt aangemeld bij AzCopy, gebruikt `azcopy login` u de opdracht voordat u de volgende opdracht uitvoert.

```azcopy
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```

Hetzelfde als hierboven, maar deze keer ook de MD5-hash van de bestands inhoud en sla deze op als de eigenschap content-MD5 van de blob:

```azcopy
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]" --put-md5
```

Upload één bestand met een SAS:

```azcopy
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Eén bestand met een SAS uploaden met behulp van sluizen (alleen blok-blobs):

```azcopy
cat "/path/to/file.txt" | azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Een volledige directory uploaden met een SAS:

```azcopy
azcopy cp "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

of

```azcopy
azcopy cp "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --put-md5
```

Een set bestanden uploaden met een SAS met behulp van joker tekens:

```azcopy
azcopy cp "/path/*foo/*bar/*.pdf" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]"
```

Bestanden en mappen uploaden met een SAS met behulp van joker tekens:

```azcopy
azcopy cp "/path/*foo/*bar*" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

Down load één bestand met OAuth-verificatie.

Als u zich nog niet hebt aangemeld bij AzCopy, gebruikt `azcopy login` u de opdracht voordat u de volgende opdracht uitvoert.

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]" "/path/to/file.txt"
```

Een enkel bestand met een SAS downloaden:

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "/path/to/file.txt"
```

Een enkel bestand met een SAS downloaden met behulp van sluizen (alleen blok-blobs):

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" > "/path/to/file.txt"
```

Een volledige directory met een SAS downloaden:

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" "/path/to/dir" --recursive=true
```

Een set bestanden downloaden met een SAS met behulp van joker tekens:

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/foo*?[SAS]" "/path/to/dir"
```

Bestanden en mappen downloaden met een SAS met behulp van joker tekens:

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/foo*?[SAS]" "/path/to/dir" --recursive=true
```

Eén blob met SAS naar een andere blob kopiëren met SAS:

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Kopieer één blob met SAS naar een andere blob met OAuth-token.

Als u zich nog niet hebt aangemeld bij AzCopy, gebruikt `azcopy login` u de opdracht voordat u de volgende opdracht uitvoert. Het OAuth-token wordt gebruikt om toegang te krijgen tot het doel-opslag account.

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]"
```

Een volledige directory kopiëren van een virtuele BLOB-map met SAS naar een andere virtuele BLOB-map met SAS:

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

Kopieer een volledige account gegevens van een BLOB-account met SAS naar een ander BLOB-account met SAS:

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net?[SAS]" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive=true
```

Een enkel object kopiëren van S3 met een toegangs sleutel naar een blob met SAS:

Stel de omgevings variabele AWS_ACCESS_KEY_ID en AWS_SECRET_ACCESS_KEY in voor de S3-bron.

```azcopy
azcopy cp "https://s3.amazonaws.com/[bucket]/[object]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Kopieer een volledige directory van S3 met toegangs sleutel naar virtuele BLOB-map met SAS:

```azcopy
azcopy cp "https://s3.amazonaws.com/[bucket]/[folder]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

Zie https://docs.aws.amazon.com/AmazonS3/latest/user-guide/using-folders.html voor meer informatie over wat [map] betekent voor S3. Stel de omgevings variabele AWS_ACCESS_KEY_ID en AWS_SECRET_ACCESS_KEY in voor de S3-bron.

Kopieer alle buckets in de S3-service met toegangs sleutel naar BLOB-account met SAS:

Stel de omgevings variabele AWS_ACCESS_KEY_ID en AWS_SECRET_ACCESS_KEY in voor de S3-bron.

```azcopy
azcopy cp "https://s3.amazonaws.com/" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive=true
```

Kopieer alle buckets in een S3-regio met toegangs sleutel naar BLOB-account met SAS:

```azcopy
azcopy cp "https://s3-[region].amazonaws.com/" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive=true
```

Stel de omgevings variabele AWS_ACCESS_KEY_ID en AWS_SECRET_ACCESS_KEY in voor de S3-bron.

## <a name="options"></a>Opties

|Optie|Description|
|--|--|
|--teken reeks voor BLOB-type|Hiermee wordt het type Blob op het doel gedefinieerd. Dit wordt gebruikt voor het uploaden van blobs en bij het kopiëren van een account (standaard ' geen ').|
|--blok-teken reeks voor BLOB-laag|Upload de blok-BLOB naar Azure Storage met behulp van deze BLOB-laag. (standaard ' geen ').|
|--blok-grootte-MB float |Gebruik deze blok grootte (opgegeven in MiB) bij het uploaden naar Azure Storage en down loads van Azure Storage. De standaard waarde wordt automatisch berekend op basis van de bestands grootte. Decimale breuken zijn toegestaan (bijvoorbeeld: 0,25).|
|--Cache-Control-teken reeks|Stel de cache-Control-header in. Geretourneerd bij het downloaden.|
|--check-MD5-teken reeks|Hiermee geeft u op hoe strikt MD5-hashes moeten worden gevalideerd bij het downloaden. Alleen beschikbaar bij het downloaden. Beschikbare opties: Niet controleren, aanmelden, FailIfDifferent, FailIfDifferentOrMissing. (standaard "FailIfDifferent")|
|--de teken reeks voor de toestand van inhoud|Stel de content-disposition-header in. Geretourneerd bij het downloaden.|
|--teken reeks voor inhoud-encoding|Stel de Content-Encoding-header in. Geretourneerd bij het downloaden.|
|--teken reeks voor inhoud-taal|De header content-language instellen. Geretourneerd bij het downloaden.|
|--type teken reeks voor inhoud |Hiermee geeft u het type inhoud van het bestand. Impliceert geen-schatting-MIME-type. Geretourneerd bij het downloaden.|
|--teken reeks uitsluiten|Deze bestanden uitsluiten bij het kopiëren. Ondersteuning voor het gebruik van *.|
|--exclude-teken reeks voor BLOB-type|Hiermee geeft u optioneel het type BLOB (BlockBlob/PageBlob/AppendBlob) op dat moet worden uitgesloten bij het kopiëren van blobs uit de container of het account. Het gebruik van deze vlag is niet van toepassing op het kopiëren van gegevens van niet-Azure-service naar service. Meer dan één BLOB moet worden gescheiden door '; '.|
|--follow-symlinks|Volg symbolische koppelingen bij het uploaden van het lokale bestands systeem.|
|--van-tot teken reeks|Hier kunt u desgewenst de combi natie van bron doel opgeven. Bijvoorbeeld: LocalBlob, BlobLocal, LocalBlobFS.|
|-h,--Help|Hiermee wordt Help-inhoud voor de Kopieer opdracht weer gegeven. |
|--teken reeks op logboek niveau|Definieer de uitgebreide logboek registratie voor het logboek bestand, beschik bare niveaus: INFO (alle aanvragen/antwoorden), waarschuwing (trage antwoorden), fout (alleen mislukte aanvragen) en geen (geen uitvoer Logboeken). (standaard INFO).|
|--teken reeks voor meta gegevens|Upload naar Azure Storage met deze sleutel-waardeparen als meta gegevens.|
|--No-schatting-MIME-type|Hiermee voor komt u dat AzCopy het inhouds type detecteert op basis van de extensie of inhoud van het bestand.|
|--overschrijven|Overschrijf de conflicterende bestanden/blobs op het doel als deze vlag is ingesteld op True. (standaard instelling waar).|
|--pagina-teken reeks voor BLOB-laag |Upload de pagina-BLOB naar Azure Storage met behulp van deze BLOB-laag. (standaard ' geen ').|
|--behoud-laatste-gewijzigd-tijd|Alleen beschikbaar wanneer het doel bestands systeem is.|
|--put-MD5|Maak een MD5-hash van elk bestand en sla de hash op als de eigenschap content-MD5 van de BLOB of het doel bestand. (Standaard wordt de hash niet gemaakt.) Alleen beschikbaar bij het uploaden.|
|--recursief|Zoek naar submappen recursief bij het uploaden van het lokale bestands systeem.|
|--S2S-detecteren-bron-gewijzigd|Controleer of de bron is gewijzigd na het inventariseren. Voor een S2S-kopie is de bron een externe bron, zodat u kunt controleren of de bron is gewijzigd bijkomende aanvraag kosten nodig heeft.|
|--S2S-handle-ongeldig-meta teken reeks |Hiermee geeft u op hoe ongeldige sleutels voor meta gegevens worden verwerkt. Beschikbare opties: ExcludeIfInvalid, FailIfInvalid, RenameIfInvalid. (standaard "ExcludeIfInvalid").|
|--S2S-pres Erve-Access-tier|De Access-laag behouden tijdens service to service copy. Raadpleeg [Azure Blob-opslag: dynamische, koude en archief toegangs lagen](../blobs/storage-blob-storage-tiers.md) om ervoor te zorgen dat het opslag account van de bestemming ondersteuning biedt voor het instellen van de toegangs laag. In het geval dat het instellen van de toegangs laag niet wordt ondersteund, gebruikt u s2sPreserveAccessTier = False om het kopiëren van de toegangs lagen over te slaan.  (standaard instelling waar).|
|--Pres Erve-eigenschappen|Volledige eigenschappen behouden tijdens service to service copy. Voor S3-en Azure file-bron bestanden, omdat de lijst bewerking niet de volledige eigenschappen van objecten en bestanden retourneert, voor het behouden van de volledige eigenschappen AzCopy moet één extra aanvraag per object en bestand verzenden. (standaard instelling waar).|

## <a name="options-inherited-from-parent-commands"></a>Opties overgenomen van bovenliggende opdrachten

|Optie|Description|
|---|---|
|--Cap-Mbps uint32|De overdrachts frequentie in megabits per seconde. Even door Voer kan enigszins afwijken van het kapje. Als deze optie is ingesteld op nul of wordt wegge laten, wordt de door Voer niet afgetopt.|
|--type teken reeks voor uitvoer|De indeling van de uitvoer van de opdracht. De opties zijn onder andere: Text, JSON. De standaard waarde is "text".|

## <a name="see-also"></a>Zie ook

- [azcopy](storage-ref-azcopy.md)
