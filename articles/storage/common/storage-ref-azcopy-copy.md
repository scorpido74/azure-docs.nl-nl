---
title: azcopy| Microsoft Documenten
description: Dit artikel bevat referentie-informatie voor de opdracht azcopy-kopie.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 431372b930269c3dfa6bdc6e8b2fe4d291a8162e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78933783"
---
# <a name="azcopy-copy"></a>azcopy copy

Kopieert brongegevens naar een bestemmingslocatie.

## <a name="synopsis"></a>Synopsis

Kopieert brongegevens naar een bestemmingslocatie. De ondersteunde aanwijzingen zijn:

  - lokale <-> Azure Blob (SAS- of OAuth-verificatie)
  - lokale <-> Azure Files (SAS-verificatie voor delen/directory)
  - lokale <-> ADLS Gen 2 (SAS, OAuth of SharedKey-verificatie)
  - Azure Blob (SAS of openbaar) -> Azure Blob (SAS- of OAuth-verificatie)
  - Azure Blob (SAS of openbaar) -> Azure Files (SAS)
  - Azure Files (SAS) -> Azure Files (SAS)
  - Azure Files (SAS) -> Azure Blob (SAS- of OAuth-verificatie)
  - AWS S3 (Access Key) -> Azure Block Blob (SAS- of OAuth-verificatie)

Zie de voorbeelden voor meer informatie.

## <a name="related-conceptual-articles"></a>Gerelateerde conceptuele artikelen

- [Aan de slag met AzCopy](storage-use-azcopy-v10.md)
- [Gegevens overbrengen met AzCopy- en Blob-opslag](storage-use-azcopy-blobs.md)
- [Gegevens overbrengen met AzCopy en bestandsopslag](storage-use-azcopy-files.md)
- [AzCopy configureren, optimaliseren en oplossen](storage-use-azcopy-configure.md)

## <a name="advanced"></a>Geavanceerd

AzCopy detecteert automatisch het inhoudstype van de bestanden bij het uploaden vanaf de lokale schijf, op basis van de bestandsextensie of -inhoud (als er geen extensie is opgegeven).

De ingebouwde opzoektabel is klein, maar op Unix wordt deze aangevuld met het mime.types-bestand(en) van het lokale systeem indien beschikbaar onder een of meer van deze namen:

- /etc/mime.types
- /etc/apache2/mime.types
- /etc/apache/mime.types

Op Windows worden MIME-typen uit het register gehaald. Deze functie kan worden uitgeschakeld met behulp van een vlag. Raadpleeg de vlagsectie.

Als u een omgevingsvariabele instelt met behulp van de opdrachtregel, is die variabele leesbaar in de geschiedenis van de opdrachtregel. Overweeg variabelen te wissen die referenties bevatten uit de geschiedenis van de opdrachtregel. Om te voorkomen dat variabelen in uw geschiedenis worden weergegeven, u een script gebruiken om de gebruiker om hun referenties te vragen en de omgevingsvariabele in te stellen.

```
azcopy copy [source] [destination] [flags]
```

## <a name="examples"></a>Voorbeelden

Upload één bestand met OAuth-verificatie. Als u zich nog niet hebt aangemeld bij AzCopy, voert u de azcopy-aanmeldingsopdracht uit voordat u de volgende opdracht uitvoert.

- azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[pad/naar/blob]"

Hetzelfde als hierboven, maar deze keer ook md5 hash van de bestandsinhoud berekenen en opslaan als de eigenschap Content-MD5 van de blob:

- azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[pad/naar/blob]" --put-md5

Upload één bestand met een SAS-token:

- azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[pad/naar/blob]? [SAS]"

Upload één bestand met behulp van een SAS-token en leidingen (alleen blobs blokkeren):
  
- kat "/pad/naar/file.txt" | azcopy cp "https://[account].blob.core.windows.net/[container]/[pad/naar/blob]? [SAS]"

Upload een hele map met een SAS-token:
  
- azcopy cp "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[pad/naar/directory]? [SAS]" --recursief=waar

of

- azcopy cp "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[pad/naar/directory]? [SAS]" --recursief=waar --put-md5

Upload een set bestanden met een SAS-token en wildcard (*) tekens:

- azcopy cp "/path/*foo/* bar/*.pdf" "https://[account].blob.core.windows.net/[container]/[pad/naar/directory]? [SAS]"

Upload bestanden en mappen met behulp van een SAS-token en wildcard (*) tekens:

- azcopy cp "/path/*foo/* bar*" "https://[account].blob.core.windows.net/[container]/[pad/naar/directory]? [SAS]" --recursief=waar

Download één bestand met OAuth-verificatie. Als u zich nog niet hebt aangemeld bij AzCopy, voert u de azcopy-aanmeldingsopdracht uit voordat u de volgende opdracht uitvoert.

- azcopy cp "https://[account].blob.core.windows.net/[container]/[pad/naar/blob]" "/pad/naar/file.txt"

Download één bestand met een SAS-token:

- azcopy cp "https://[account].blob.core.windows.net/[container]/[pad/naar/blob]? [SAS]" "/pad/naar/bestand.txt"

Download één bestand met een SAS-token en breng vervolgens de uitvoer naar een bestand (alleen blobs blokkeren):
  
- azcopy cp "https://[account].blob.core.windows.net/[container]/[pad/naar/blob]? [SAS]" > "/pad/naar/bestand.txt"

Download een hele map met een SAS-token:
  
- azcopy cp "https://[account].blob.core.windows.net/[container]/[pad/naar/directory]? [SAS]" "/pad/naar/dir" --recursief=waar

Een opmerking over het gebruik van een jokerteken (*) in URL's:

Er zijn slechts twee ondersteunde manieren om een jokerteken in een URL te gebruiken. 

- U er een gebruiken net na de laatste slash (/) van een URL. Hiermee worden alle bestanden in een map rechtstreeks naar de bestemming gekopieerd zonder ze in een submap te plaatsen.

- U er ook een gebruiken in de naam van een container, zolang de URL alleen naar een container verwijst en niet naar een blob. U deze aanpak gebruiken om bestanden te verkrijgen uit een subset van containers.

Download de inhoud van een map zonder de inhoudvan de map zelf te kopiëren.

- azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[pad/naar/map]/*? [SAS]" "/pad/naar/dir"

Download een volledig opslagaccount.

- azcopy cp "https://[srcaccount].blob.core.windows.net/" "/path/to/dir" --recursief

Download een subset van containers binnen een opslagaccount met een wildcardsymbool (*) in de containernaam.

- azcopy cp "https://[srcaccount].blob.core.windows.net/[container*name]" "/path/to/dir" --recursief

Kopieer een enkele blob naar een andere blob met behulp van een SAS-token.

- azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[pad/naar/blob]? [SAS]" "https://[destaccount].blob.core.windows.net/[container]/[pad/naar/blob]? [SAS]"

Kopieer een enkele blob naar een andere blob met behulp van een SAS-token en een OAuth-token. Je moet een SAS-token gebruiken aan het einde van de URL van het bronaccount, maar het doelaccount heeft er geen nodig als je je aanmeldt bij AzCopy met behulp van de azcopy-aanmeldingsopdracht. 

- azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[pad/naar/blob]? [SAS]" "https://[destaccount].blob.core.windows.net/[container]/[pad/naar/blob]"

Kopieer de virtuele map van de blob naar de andere met behulp van een SAS-token:

- azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[pad/naar/directory]? [SAS]" "https://[destaccount].blob.core.windows.net/[container]/[pad/naar/directory]? [SAS]" --recursief=waar

Kopieer alle blobcontainers, mappen en blobs van het opslagaccount naar een ander account met behulp van een SAS-token:

- azcopy cp "https://[srcaccount].blob.core.windows.net? [SAS]" "https://[destaccount].blob.core.windows.net? [SAS]" --recursief=waar

Kopieer één object naar Blob Storage van Amazon Web Services (AWS) S3 met behulp van een toegangssleutel en een SAS-token. Stel eerst de omgevingsvariabele AWS_ACCESS_KEY_ID en AWS_SECRET_ACCESS_KEY in voor AWS S3-bron.
  
- azcopy cphttps://s3.amazonaws.com/" [bucket]/[object]" "https://[destaccount].blob.core.windows.net/[container]/[pad/naar/blob]? [SAS]"

Kopieer een hele map naar Blob Storage van AWS S3 met behulp van een toegangssleutel en een SAS-token. Stel eerst de omgevingsvariabele AWS_ACCESS_KEY_ID en AWS_SECRET_ACCESS_KEY in voor AWS S3-bron.

- azcopy cphttps://s3.amazonaws.com/" [bucket]/[map]" "https://[destaccount].blob.core.windows.net/[container]/[pad/naar/directory]? [SAS]" --recursief=waar

Raadpleeg om https://docs.aws.amazon.com/AmazonS3/latest/user-guide/using-folders.html de tijdelijke aanduiding [map] beter te begrijpen.

Kopieer alle buckets naar Blob Storage van Amazon Web Services (AWS) met behulp van een toegangssleutel en een SAS-token. Stel eerst de omgevingsvariabele AWS_ACCESS_KEY_ID en AWS_SECRET_ACCESS_KEY in voor AWS S3-bron.

- azcopy cphttps://s3.amazonaws.com/" " "https://[destaccount].blob.core.windows.net? [SAS]" --recursief=waar

Kopieer alle buckets naar Blob Storage vanuit een AWS-regio (Amazon Web Services) met behulp van een toegangssleutel en een SAS-token. Stel eerst de omgevingsvariabele AWS_ACCESS_KEY_ID en AWS_SECRET_ACCESS_KEY in voor AWS S3-bron.

- azcopy cphttps://s3-" [regio].amazonaws.com/" "https://[destaccount].blob.core.windows.net? [SAS]" --recursief=waar

Kopieer een subset van buckets met een wildcardsymbool (*) in de bucketnaam. Net als in de vorige voorbeelden hebt u een toegangssleutel en een SAS-token nodig. Zorg ervoor dat u de omgevingsvariabele AWS_ACCESS_KEY_ID en AWS_SECRET_ACCESS_KEY instelt voor AWS S3-bron.

- azcopy cphttps://s3.amazonaws.com/" [bucket*name]/" "https://[destaccount].blob.core.windows.net? [SAS]" --recursief=waar

## <a name="options"></a>Opties

**--blob-type** tekenreeks Definieert het type blob op de bestemming. Dit wordt gebruikt voor het uploaden van blobs en bij het kopiëren tussen accounts (standaard 'Detecteren'). Geldige waarden zijn 'Detect', 'BlockBlob', 'PageBlob' en 'AppendBlob'. Bij het kopiëren tussen accounts zorgt een waarde van 'Detecteren' ervoor dat AzCopy het type bronblob gebruikt om het type doelblob te bepalen. Bij het uploaden van een bestand bepaalt 'Detecteren' of het bestand een VHD- of een VHDX-bestand is op basis van de bestandsextensie. Als het bestand ether is van een VHD- of VHDX-bestand, behandelt AzCopy het bestand als een paginablob. (standaard 'Detecteren')

**--block-blob-tier** string Upload block blobs rechtstreeks naar de [toegangslaag](../blobs/storage-blob-storage-tiers.md) van uw keuze. (standaard 'Geen'). Geldige waarden zijn 'None', 'Hot', 'Cool' en 'Archive'. Als 'Geen' of geen laag wordt doorgegeven, neemt de blob de laag van het opslagaccount over.

**--block-size-mb** float Gebruik deze blokgrootte (opgegeven in MiB) bij het uploaden naar Azure Storage en het downloaden van Azure Storage. De standaardwaarde wordt automatisch berekend op basis van de bestandsgrootte. Decimale breuken zijn toegestaan (bijvoorbeeld: 0,25).

**--cache-control** string Stel de cache-control header in. Terug bij download.

**--controlelengte**                         Controleer de lengte van een bestand op de bestemming na de overdracht. Als er een mismatch is tussen bron en bestemming, wordt de overdracht gemarkeerd als mislukt. (standaard true)

**--check-md5-tekenreeks** Geeft aan hoe strikt MD5 hashes moeten worden gevalideerd bij het downloaden. Alleen beschikbaar bij het downloaden. Beschikbare opties: NoCheck, LogOnly, FailIfdifferent, FailIfdifferentormissing. (standaard "FailIfDifferent")

**--content-disposition** string Stel de inhoudsdispositieheader in. Terug bij download.

**--content-encoding** string Stel de koptekst voor inhoudscodering in. Terug bij download.

**--inhoudstaaltekenreeks** De koptekst voor inhoudstaal instellen. Terug bij download.

**--inhoudstekenreeks** Geeft het inhoudstype van het bestand op. Impliceert no-guess-mime-type. Terug bij download.

**--decomprimeren**                           Automatisch bestanden decomprimeren tijdens het downloaden, als de inhoudscodering aangeeft dat ze worden gecomprimeerd. De ondersteunde content-encoding waarden zijn 'gzip' en 'leeglopen'. Bestandsextensies van '.gz'/'.gzip' of '.zz' zijn niet nodig, maar worden indien aanwezig verwijderd.

**tekenreeks --exclude-attributeen** (alleen Windows) Bestanden uitsluiten waarvan de kenmerken overeenkomen met de kenmerklijst. Bijvoorbeeld: A; S; R

**--exclude-blob-type** tekenreeks Geeft optioneel het type blob (BlockBlob/ PageBlob/ AppendBlob) op dat moet worden uitgesloten bij het kopiëren van blobs uit de container of het account. Het gebruik van deze vlag is niet van toepassing voor het kopiëren van gegevens van niet-azure-service naar service. Meer dan één blob moet worden gescheiden door ';'.

**--exclude-padtekenreeks** Sluit deze paden uit bij het kopiëren. Deze optie biedt geen ondersteuning voor jokertekens (*). Hiermee controleert u het relatieve padvoorvoegsel(Bijvoorbeeld: myFolder;myFolder/subDirName/file.pdf). Bij gebruik in combinatie met accounttraversal bevatten paden de containernaam niet.

**--exclude-patroontekenreeks** Sluit deze bestanden uit bij het kopiëren. Deze optie ondersteunt jokertekens (*)

**--follow-symlinks**                      Volg symbolische koppelingen bij het uploaden vanuit het lokale bestandssysteem.

**--van-naar-tekenreeks** Optioneel geeft u de combinatie van bronbestemming op. Bijvoorbeeld: localblob, bloblocal, localBlobfs.

**-h, --help** voor kopiëren

**--include-attributentekenreeks** (alleen Windows) Voeg bestanden toe waarvan de kenmerken overeenkomen met de lijst met kenmerken. Bijvoorbeeld: A; S; R

**--include-padtekenreeks** Voeg alleen deze paden toe bij het kopiëren. Deze optie biedt geen ondersteuning voor jokertekens (*). Hiermee controleert u het relatieve padvoorvoegsel (bijvoorbeeld: myFolder;myFolder/subDirName/file.pdf).

**--include-patroontekenreeks** Voeg alleen deze bestanden toe bij het kopiëren. Deze optie ondersteunt jokertekens (*). Bestanden scheiden met een ';'.

**--tekenreeks op logboekniveau** Definieer de logboekverbositeit voor het logboekbestand, beschikbare niveaus: INFO (alle aanvragen/antwoorden), WAARSCHUWING(langzame reacties), FOUT (alleen mislukte aanvragen) en GEEN (geen uitvoerlogboeken). (standaard "INFO")

**--metagegevenstekenreeks** Uploaden naar Azure Storage met deze sleutelwaardenparen als metagegevens.

**--no-guess-mime-type**                   Hiermee voorkomt u dat AzCopy het inhoudstype detecteert op basis van de extensie of inhoud van het bestand.

**--overschrijf** tekenreeks Overschrijf de conflicterende bestanden en blobs op de bestemming als deze vlag is ingesteld op true. Mogelijke waarden zijn 'true', 'false', 'ifSourceNewer' en 'prompt'. (standaard 'waar')

**--pagina-blob-tier** string Upload pagina blob naar Azure Storage met behulp van deze blob tier. (standaard 'Geen')

**--preserve-last-modified-time**          Alleen beschikbaar wanneer de bestemming is bestandssysteem.

**--put-md5**                             Maak een MD5-hash van elk bestand en sla de hash op als de eigenschap Content-MD5 van de doelblob of -bestand. (Standaard wordt de hash NIET gemaakt.) Alleen beschikbaar bij het uploaden.

**--recursieve**                            Kijk in sub-directories recursief bij het uploaden van lokale bestandssysteem.

**--s2s-detect-source-changed**           Controleer of de bron is gewijzigd na het opsommen.

**--s2s-handle-invalid-metadata** string Geeft op hoe ongeldige metagegevenssleutels worden verwerkt. Beschikbare opties: ExcludeIfInvalid, FailIfInvalid, RenameIfInvalid. (standaard "ExcludeIfInvalid")

**--s2s-preserve-access-tier**             De toegangslaag behouden tijdens de service-kopie. Raadpleeg [Azure Blob-opslag: hot-, cool- en archieftoegangslagen](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers) om ervoor te zorgen dat het doelopslagaccount de toegangslaag ondersteunt. In de gevallen dat de toegangslaag niet wordt ondersteund, gebruikt u s2sPreserveAccessTier=false om de kopieertoegangslaag te omzeilen. (standaard true)

**--s2s-preserve-eigenschappen**              Behoudt volledige eigenschappen tijdens de service tot servicekopie. Voor niet-afzonderlijke bestandsbronnen van AWS S3 en Azure File retourneert de lijstbewerking niet alle eigenschappen van objecten en bestanden. Om volledige eigenschappen te behouden, moet AzCopy één extra aanvraag per object of bestand verzenden. (standaard true)

## <a name="options-inherited-from-parent-commands"></a>Opties die zijn overgenomen van bovenliggende opdrachten

**--cap-mbps uint32**      Caps de overdrachtssnelheid, in megabits per seconde. De doorvoer van moment tot moment kan enigszins afwijken van de dop. Als deze optie is ingesteld op nul of wordt weggelaten, wordt de doorvoer niet afgetopt.

**--uitvoer-type** tekenreeks Notatie van de uitvoer van de opdracht. De keuzes zijn: tekst, json. De standaardwaarde is 'tekst'. (standaard "tekst")

## <a name="see-also"></a>Zie ook

- [azcopie](storage-ref-azcopy.md)
