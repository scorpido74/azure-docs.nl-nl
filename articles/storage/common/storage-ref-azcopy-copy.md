---
title: azcopy kopiëren | Microsoft Docs
description: In dit artikel vindt u Naslag informatie voor de azcopy-Kopieer opdracht.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 78482b5d7013ffa3bbb0a34dd04c8c48626dc77a
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72926652"
---
# <a name="azcopy-copy"></a>azcopy copy

Kopieert de bron gegevens naar een doel locatie.

## <a name="synopsis"></a>Samen vatting

Kopieert de bron gegevens naar een doel locatie. De ondersteunde richtingen zijn:

  - lokale <-> Azure-Blob (SAS of OAuth-verificatie)
  - lokale <-> Azure Files (share/Directory SAS-verificatie)
  - lokale <-> ADLS gen 2 (SAS, OAuth of SharedKey-verificatie)
  - Azure-Blob (SAS of openbaar)-> Azure-Blob (SAS of OAuth-verificatie)
  - Azure-Blob (SAS of openbaar)-> Azure Files (SAS)
  - Azure Files (SAS)-> Azure Files (SAS)
  - Azure Files (SAS)-> Azure-Blob (SAS of OAuth-verificatie)
  - AWS S3 (toegangs sleutel)-> Azure Block BLOB (SAS of OAuth-verificatie)

Raadpleeg de voor beelden voor meer informatie.

## <a name="advanced"></a>Geavanceerd

AzCopy detecteert automatisch het inhouds type van de bestanden bij het uploaden van op de lokale schijf, op basis van de bestands extensie of inhoud (als er geen extensie is opgegeven).

De ingebouwde opzoek tabel is klein, maar op UNIX wordt deze uitgebreid met de MIME. type-bestanden van het lokale systeem, indien beschikbaar onder een of meer van deze namen:

- /etc/mime.types
- /etc/apache2/mime.types
- /etc/apache/mime.types

In Windows worden MIME-typen geëxtraheerd uit het REGI ster. Deze functie kan worden uitgeschakeld met behulp van een vlag. Raadpleeg de sectie met de vlag.

Als u een omgevings variabele instelt met behulp van de opdracht regel, wordt die variabele leesbaar in de opdracht regel geschiedenis. U kunt variabelen met referenties uit de opdracht regel geschiedenis wissen. Als u wilt voor komen dat variabelen in uw geschiedenis worden weer gegeven, kunt u een script gebruiken om de gebruiker om referenties te vragen en de omgevings variabele in te stellen.

```
azcopy copy [source] [destination] [flags]
```

## <a name="examples"></a>Voorbeelden

Upload één bestand met behulp van OAuth-verificatie. Als u zich nog niet hebt aangemeld bij AzCopy, voert u de AzCopy-aanmeld opdracht uit voordat u de volgende opdracht uitvoert.

- azcopy CP "/Path/to/file.txt" "https://[account]. blob. core. Windows. net/[container]/[pad/naar/BLOB]"

Hetzelfde als hierboven, maar deze keer ook de MD5-hash van de bestands inhoud en sla deze op als de eigenschap content-MD5 van de blob:

- azcopy CP "/Path/to/file.txt" "https://[account]. blob. core. Windows. net/[container]/[pad/naar/BLOB]"--put-MD5

Een enkel bestand uploaden met behulp van een SAS-token:

- azcopy CP "/Path/to/file.txt" "https://[account]. blob. core. Windows. net/[container]/[pad/naar/BLOB]? [SAS] "

Eén bestand uploaden met behulp van een SAS-token en pijpleiding (alleen blok-blobs):
  
- kat "/Path/to/file.txt" | azcopy CP "https://[account]. blob. core. Windows. net/[container]/[pad/naar/BLOB]? [SAS] "

Een volledige directory uploaden met behulp van een SAS-token:
  
- azcopy CP "/Path/to/dir" "https://[account]. blob. core. Windows. net/[container]/[pad/naar/map]? [SAS] "--recursief = True

of

- azcopy CP "/Path/to/dir" "https://[account]. blob. core. Windows. net/[container]/[pad/naar/map]? [SAS]--recursieve = True--put-MD5

Een set bestanden uploaden met behulp van een SAS-token en Joker tekens (*):

- azcopy CP "/Path/*Foo/* bar/*. PDF" "https://[account]. blob. core. Windows. net/[container]/[pad/naar/map]? [SAS] "

Bestanden en mappen uploaden met behulp van een SAS-token en Joker tekens (*):

- azcopy CP "/Path/*Foo/* Bar *" "https://[account]. blob. core. Windows. net/[container]/[pad/naar/map]? [SAS] "--recursief = True

Een enkel bestand downloaden met behulp van OAuth-verificatie. Als u zich nog niet hebt aangemeld bij AzCopy, voert u de AzCopy-aanmeld opdracht uit voordat u de volgende opdracht uitvoert.

- azcopy CP "https://[account]. blob. core. Windows. net/[container]/[pad/naar/BLOB]" "/Path/to/file.txt"

Een enkel bestand downloaden met behulp van een SAS-token:

- azcopy CP "https://[account]. blob. core. Windows. net/[container]/[pad/naar/BLOB]? [SAS] ""/Path/to/file.txt "

Een enkel bestand downloaden met behulp van een SAS-token en vervolgens de uitvoer naar een bestand sluizen (alleen blok-blobs):
  
- azcopy CP "https://[account]. blob. core. Windows. net/[container]/[pad/naar/BLOB]? [SAS] > '/Path/to/file.txt '

Een volledige directory downloaden met behulp van een SAS-token:
  
- azcopy CP "https://[account]. blob. core. Windows. net/[container]/[pad/naar/map]? [SAS] ""/Path/to/dir "--recursieve = True

Een opmerking over het gebruik van een Joker teken (*) in Url's:

Er zijn slechts twee ondersteunde manieren om een Joker teken te gebruiken in een URL. 

- U kunt een van de volgende tekens gebruiken, net na de laatste slash (/) van een URL. Hiermee worden alle bestanden in een map rechtstreeks naar de bestemming gekopieerd zonder dat ze in een submap worden geplaatst.

- U kunt er ook een gebruiken in de naam van een container, zolang de URL alleen verwijst naar een container en niet naar een blob. U kunt deze methode gebruiken om bestanden van een subset van containers te verkrijgen.

De inhoud van een map downloaden zonder de bovenliggende map zelf te kopiëren.

- azcopy CP "https://[srcaccount]. blob. core. Windows. net/[container]/[pad/naar/map]/*? [SAS] ""/Path/to/dir "

Down load een hele opslag account.

- azcopy CP "https://[srcaccount]. blob. core. Windows. net/" "/Path/to/dir"--recursief

Down load een subset van containers in een opslag account met behulp van een Joker teken (*) in de container naam.

- azcopy CP "https://[srcaccount]. blob. core. Windows. net/[container * naam]" "/Path/to/dir"--recursief

Kopieer één BLOB naar een andere blob met behulp van een SAS-token.

- azcopy CP "https://[srcaccount]. blob. core. Windows. net/[container]/[pad/naar/BLOB]? [SAS] "" https://[destaccount]. blob. core. Windows. net/[container]/[pad/naar/BLOB]? [SAS] "

Kopieer één BLOB naar een andere blob met behulp van een SAS-token en een OAuth-token. U moet een SAS-token aan het einde van de URL van het bron account gebruiken, maar het doel account heeft er geen nodig als u zich aanmeldt bij AzCopy met behulp van de AzCopy-aanmeld opdracht. 

- azcopy CP "https://[srcaccount]. blob. core. Windows. net/[container]/[pad/naar/BLOB]? [SAS] "" https://[destaccount]. blob. core. Windows. net/[container]/[pad/naar/BLOB] "

Eén virtuele BLOB-map kopiëren naar een andere met behulp van een SAS-token:

- azcopy CP "https://[srcaccount]. blob. core. Windows. net/[container]/[pad/naar/map]? [SAS] "" https://[destaccount]. blob. core. Windows. net/[container]/[pad/naar/map]? [SAS] "--recursief = True

Kopieer alle BLOB-containers, directory's en blobs van het opslag account naar een andere met behulp van een SAS-token:

- azcopy CP "https://[srcaccount]. blob. core. Windows. net? [SAS] "" https://[destaccount]. blob. core. Windows. net? [SAS] "--recursief = True

Kopieer een enkel object naar Blob Storage van Amazon Web Services (AWS) S3 met behulp van een toegangs sleutel en een SAS-token. Stel eerst de omgevings variabele AWS_ACCESS_KEY_ID en AWS_SECRET_ACCESS_KEY in voor AWS S3-bron.
  
- azcopy CP "https://s3.amazonaws.com/ [Bucket]/[object]" "https://[destaccount]. blob. core. Windows. net/[container]/[pad/naar/BLOB]? [SAS] "

Kopieer een volledige directory naar Blob Storage van AWS S3 door gebruik te maken van een toegangs sleutel en een SAS-token. Stel eerst de omgevings variabele AWS_ACCESS_KEY_ID en AWS_SECRET_ACCESS_KEY in voor AWS S3-bron.

- azcopy CP "https://s3.amazonaws.com/ [Bucket]/[map]" "https://[destaccount]. blob. core. Windows. net/[container]/[pad/naar/map]? [SAS] "--recursief = True

Raadpleeg https://docs.aws.amazon.com/AmazonS3/latest/user-guide/using-folders.html voor een beter begrip van de tijdelijke aanduiding voor [map].

Kopieer alle buckets naar Blob Storage vanuit Amazon Web Services (AWS) met behulp van een toegangs sleutel en een SAS-token. Stel eerst de omgevings variabele AWS_ACCESS_KEY_ID en AWS_SECRET_ACCESS_KEY in voor AWS S3-bron.

- azcopy CP "https://s3.amazonaws.com/ " "https://[destaccount]. blob. core. Windows. net? [SAS] "--recursief = True

Kopieer alle buckets naar Blob Storage vanuit een Amazon Web Services (AWS) regio met behulp van een toegangs sleutel en een SAS-token. Stel eerst de omgevings variabele AWS_ACCESS_KEY_ID en AWS_SECRET_ACCESS_KEY in voor AWS S3-bron.

- azcopy CP "https://s3- [regio]. amazonaws. com/" "https://[destaccount]. blob. core. Windows. net? [SAS] "--recursief = True

Kopieer een subset van buckets met behulp van een Joker teken (*) in de Bucket naam. Net als bij de vorige voor beelden hebt u een toegangs sleutel en een SAS-token nodig. Zorg ervoor dat u de omgevings variabele AWS_ACCESS_KEY_ID en AWS_SECRET_ACCESS_KEY instelt voor AWS S3-bron.

- azcopy CP "https://s3.amazonaws.com/ [Bucket * naam]/" "https://[destaccount]. blob. core. Windows. net? [SAS] "--recursief = True

## <a name="options"></a>Opties

**--BLOB-type** teken reeks definieert het type BLOB bij het doel. Dit wordt gebruikt voor het uploaden van blobs en bij het kopiëren tussen accounts (standaard ' detect '). Geldige waarden zijn ' detect ', ' BlockBlob ', ' PageBlob ' en ' AppendBlob '. Bij het kopiëren tussen accounts, wordt met de waarde ' detect ' AzCopy het type van de bron-BLOB gebruikt om het type van de doel-BLOB te bepalen. Bij het uploaden van een bestand wordt door ' detecteren ' bepaald of het bestand een VHD-of VHDX-bestand is op basis van de bestands extensie. Als het bestand eer een VHD-of VHDX-bestand is, wordt het bestand door AzCopy als een pagina-BLOB beschouwd. (standaard ' detecteren ')

**--blok-** teken reeks-BLOB-Block-BLOB uploaden naar Azure Storage met behulp van deze BLOB-laag. (standaard ' geen ')

**--blok-grootte-MB** float gebruik deze blok grootte (opgegeven in MIB) bij het uploaden naar Azure Storage en down loads van Azure Storage. De standaard waarde wordt automatisch berekend op basis van de bestands grootte. Decimale breuken zijn toegestaan (bijvoorbeeld: 0,25).

**--Cache-Control** string Stel de cache-Control-header in. Geretourneerd bij het downloaden.

**--Check-length**                         Controleer de lengte van een bestand op de bestemming na de overdracht. Als de bron en bestemming niet overeenkomen, wordt de overdracht als mislukt gemarkeerd. (standaard instelling waar)

**--Check-MD5** teken reeks geeft aan hoe strikt MD5-hashes moeten worden gevalideerd bij het downloaden. Alleen beschikbaar bij het downloaden. Beschik bare opties: geen controle, aanmelden, FailIfDifferent, FailIfDifferentOrMissing. (standaard "FailIfDifferent")

**--** de teken reeks voor de positie van de inhoud is ingesteld op de content-disposition-header. Geretourneerd bij het downloaden.

**--** de teken reeks voor Content-Encoding: Stel de header content-encoding in. Geretourneerd bij het downloaden.

**--Content-Language-** teken reeks Stel de header content-language in. Geretourneerd bij het downloaden.

**--inhouds type** teken reeks Hiermee geeft u het type inhoud van het bestand op. Impliceert geen-schatting-MIME-type. Geretourneerd bij het downloaden.

**--decomprimeren**                           Bestanden automatisch uitpakken wanneer ze worden gedownload, als hun content-encoding aangeeft dat ze gecomprimeerd zijn. De ondersteunde coderings waarden voor inhoud zijn ' gzip ' en ' deflate '. Bestands extensies van '. gz '/'. gzip ' of '. ZZ ' zijn niet nodig, maar worden indien aanwezig verwijderd.

**--exclude-kenmerken** teken reeks (alleen Windows) bestanden uitsluiten waarvan de kenmerken overeenkomen met de kenmerk lijst. Bijvoorbeeld: A; Z N

**--exclude-BLOB-type** teken reeks geeft optioneel het type BLOB (BlockBlob/PageBlob/AppendBlob) op dat moet worden uitgesloten bij het kopiëren van blobs uit de container of het account. Het gebruik van deze vlag is niet van toepassing op het kopiëren van gegevens van niet-Azure-service naar service. Meer dan één BLOB moet worden gescheiden door '; '.

**--exclude-** teken reeks van het pad deze paden uitsluiten bij het kopiëren. Deze optie biedt geen ondersteuning voor joker tekens (*). Hiermee wordt het relatieve pad voor voegsel gecontroleerd (bijvoorbeeld: myFolder; myFolder/subDirName/file. PDF). Bij gebruik in combi natie met account-traversal bevatten paden geen container naam.

**--exclude-patroon** teken reeks deze bestanden uitsluiten bij het kopiëren. Deze optie ondersteunt joker tekens (*)

**--follow-symlinks**                      Volg symbolische koppelingen bij het uploaden van het lokale bestands systeem.

**--van-tot** teken reeks optioneel de combi natie van bron doel opgeven. Bijvoorbeeld: LocalBlob, BlobLocal, LocalBlobFS.

**-h,--Help** Help voor kopiëren

**--include-kenmerken** teken reeks (alleen Windows) bevatten bestanden waarvan de kenmerken overeenkomen met de kenmerk lijst. Bijvoorbeeld: A; Z N

**--include-** teken reeks bevat alleen deze paden bij het kopiëren. Deze optie biedt geen ondersteuning voor joker tekens (*). Hiermee wordt het relatieve pad voor voegsel gecontroleerd (bijvoorbeeld: myFolder; myFolder/subDirName/file. PDF).

**--include-patroon** teken reeks bevatten alleen deze bestanden bij het kopiëren. Deze optie biedt ondersteuning voor joker tekens (*). Scheid bestanden met behulp van een '; '.

**--** teken reeks op logboek niveau definiëren de logboek uitgebreidheids voor het logboek bestand, beschik bare niveaus: info (alle aanvragen/antwoorden), waarschuwing (trage antwoorden), fout (alleen mislukte aanvragen) en geen (geen uitvoer Logboeken). (standaard INFO)

**--** de teken reeks voor het uploaden van meta gegevens Azure Storage met deze sleutel-waardeparen als meta gegevens.

**--no-schatting-MIME-type**                   Hiermee voor komt u dat AzCopy het inhouds type detecteert op basis van de extensie of inhoud van het bestand.

**--** de teken reeks overschrijven de conflicterende bestanden en blobs op de bestemming overschrijven als deze vlag is ingesteld op True. Mogelijke waarden zijn ' True ', ' false ' en ' prompt '. (standaard ingesteld op ' True ')

**--pagina-BLOB-** teken reeks voor het uploaden van de pagina-blob naar Azure Storage met behulp van deze BLOB-laag. (standaard ' geen ')

**--behoud-laatste-gewijzigd-tijd**          Alleen beschikbaar wanneer het doel bestands systeem is.

**--put-MD5**                             Maak een MD5-hash van elk bestand en sla de hash op als de eigenschap content-MD5 van de BLOB of het doel bestand. (Standaard wordt de hash niet gemaakt.) Alleen beschikbaar bij het uploaden.

**--recursief**                            Zoek naar submappen recursief bij het uploaden van het lokale bestands systeem.

**--S2S-detecteren-bron-gewijzigd**           Controleer of de bron is gewijzigd na het inventariseren.

**--S2S-handle-ongeldig-meta gegevens** teken reeks geeft aan hoe ongeldige sleutels voor meta gegevens worden verwerkt. Beschik bare opties: ExcludeIfInvalid, FailIfInvalid, RenameIfInvalid. (standaard "ExcludeIfInvalid")

**--S2S-pres Erve-Access-tier**             De Access-laag behouden tijdens service to service copy. Raadpleeg [Azure Blob-opslag: dynamische, koude en archief toegangs lagen](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers) om ervoor te zorgen dat het opslag account van de bestemming ondersteuning biedt voor het instellen van de toegangs laag. In het geval dat het instellen van de toegangs laag niet wordt ondersteund, gebruikt u s2sPreserveAccessTier = False om het kopiëren van de toegangs lagen over te slaan. (standaard instelling waar)

**--pres Erve-eigenschappen**              Volledige eigenschappen behouden tijdens service to service copy. Voor AWS S3 en Azure file-bron niet-enkelvoudige bestands bronnen retourneert de lijst bewerking geen volledige eigenschappen van objecten en bestanden. Voor het behouden van de volledige eigenschappen moet AzCopy één extra aanvraag per object of bestand verzenden. (standaard instelling waar)

## <a name="options-inherited-from-parent-commands"></a>Opties overgenomen van bovenliggende opdrachten

**--Cap-Mbps uint32**      De overdrachts frequentie in megabits per seconde. Even door Voer kan enigszins afwijken van het kapje. Als deze optie is ingesteld op nul of wordt wegge laten, wordt de door Voer niet afgetopt.

**--** de teken reeks indeling van het uitvoer type van de uitvoer van de opdracht. De opties zijn onder andere: Text, JSON. De standaard waarde is ' text '. (standaard tekst)

## <a name="see-also"></a>Zie ook

- [azcopy](storage-ref-azcopy.md)
