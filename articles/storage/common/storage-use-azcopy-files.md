---
title: Gegevens overdragen van of naar Azure Files met AzCopy v10 | Microsoft Documenten
description: Gegevens overbrengen met AzCopy en bestandsopslag.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 8aa0e5304825b3f016694a40b3fc1e176518237a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77526685"
---
# <a name="transfer-data-with-azcopy-and-file-storage"></a>Gegevens overbrengen met AzCopy en bestandsopslag 

AzCopy is een command-line hulpprogramma dat u gebruiken om blobs of bestanden te kopiëren van of naar een opslagaccount. Dit artikel bevat voorbeeldopdrachten die werken met Azure Files.

Voordat u begint, raadpleegt u het artikel Aan de [slag met AzCopy](storage-use-azcopy-v10.md) om AzCopy te downloaden en vertrouwd te raken met de tool.

## <a name="create-file-shares"></a>Bestandsshares maken

U de [opdracht azcopy make](storage-ref-azcopy-make.md) gebruiken om een bestandsshare te maken. In het voorbeeld in deze `myfileshare`sectie wordt een bestandsshare met de naam .

> [!TIP]
> De voorbeelden in deze sectie omsluiten padargumenten met enkele aanhalingstekens (''). Gebruik enkele aanhalingstekens in alle opdrachtshells, behalve de Windows Command Shell (cmd.exe). Als u een Windows Command Shell (cmd.exe) gebruikt, sluit u padargumenten om met dubbele aanhalingstekens ("") in plaats van enkele aanhalingstekens ('').

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy make 'https://<storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>'` |
| **Voorbeeld** | `azcopy make 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'` |

Zie [azcopy maken](storage-ref-azcopy-make.md)voor gedetailleerde referentiedocumenten.

## <a name="upload-files"></a>Bestanden uploaden

U de [azcopy-opdracht](storage-ref-azcopy-copy.md) gebruiken om bestanden en mappen te uploaden vanaf uw lokale computer.

Deze sectie bevat de volgende voorbeelden:

> [!div class="checklist"]
> * Bestand uploaden
> * Een map uploaden
> * De inhoud van een map uploaden
> * Een specifiek bestand uploaden

> [!NOTE]
> AzCopy berekent en bewaart de md5-hashcode van het bestand niet automatisch. Als u wilt dat AzCopy dat `--put-md5` doet, voeg dan de vlag toe aan elke kopieeropdracht. Op die manier berekent AzCopy, wanneer het bestand wordt gedownload, een MD5-hash voor gedownloade gegevens `Content-md5` en controleert of de MD5-hash die is opgeslagen in de eigenschap van het bestand overeenkomt met de berekende hash.

Zie voor gedetailleerde referentiedocumenten [azcopy.](storage-ref-azcopy-copy.md)

> [!TIP]
> De voorbeelden in deze sectie omsluiten padargumenten met enkele aanhalingstekens (''). Gebruik enkele aanhalingstekens in alle opdrachtshells, behalve de Windows Command Shell (cmd.exe). Als u een Windows Command Shell (cmd.exe) gebruikt, sluit u padargumenten om met dubbele aanhalingstekens ("") in plaats van enkele aanhalingstekens ('').

### <a name="upload-a-file"></a>Bestand uploaden

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy '<local-file-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-name><SAS-token>'` |
| **Voorbeeld** | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'` |

U een bestand ook uploaden met een wildcardsymbool (*) overal in het bestandspad of de bestandsnaam. Bijvoorbeeld: `'C:\myDirectory\*.txt'`, `C:\my*\*.txt`of .

### <a name="upload-a-directory"></a>Een map uploaden

In dit voorbeeld wordt een map (en alle bestanden in die map) gekopieerd naar een bestandsshare. Het resultaat is een map in het bestandsaandeel met dezelfde naam.

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **Voorbeeld** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' --recursive` |

Als u wilt kopiëren naar een map in de bestandsshare, geeft u de naam van die map op in uw opdrachttekenreeks.

|    |     |
|--------|-----------|
| **Voorbeeld** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' --recursive` |

Als u de naam opgeeft van een map die niet in de bestandsshare bestaat, maakt AzCopy een nieuwe map met die naam.

### <a name="upload-the-contents-of-a-directory"></a>De inhoud van een map uploaden

U de inhoud van een map uploaden zonder de inhoudsmap zelf te kopiëren met behulp van het wildcardsymbool (*).

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy '<local-directory-path>/*' 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path><SAS-token>` |
| **Voorbeeld** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

> [!NOTE]
> Voeg de `--recursive` vlag toe om bestanden in alle submappen te uploaden.

### <a name="upload-specific-files"></a>Specifieke bestanden uploaden

U volledige bestandsnamen opgeven of gedeeltelijke namen met jokertekens (*) gebruiken.

#### <a name="specify-multiple-complete-file-names"></a>Meerdere volledige bestandsnamen opgeven

Gebruik de opdracht [azcopy-kopie](storage-ref-azcopy-copy.md) met de `--include-path` optie. Afzonderlijke bestandsnamen scheiden met behulp`;`van een puntkomma ( ).

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' --include-path <semicolon-separated-file-list>` |
| **Voorbeeld** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --include-path 'photos;documents\myFile.txt'` |

In dit voorbeeld draagt `C:\myDirectory\photos` AzCopy `C:\myDirectory\documents\myFile.txt` de directory en het bestand over. U moet de `--recursive` optie opnemen om `C:\myDirectory\photos` alle bestanden in de map over te zetten.

U bestanden ook `--exclude-path` uitsluiten met behulp van de optie. Zie [naslagdocumenten voor kopiëren](storage-ref-azcopy-copy.md) kopiëren voor meer informatie.

#### <a name="use-wildcard-characters"></a>Jokertekens gebruiken

Gebruik de opdracht [azcopy-kopie](storage-ref-azcopy-copy.md) met de `--include-pattern` optie. Geef gedeeltelijke namen op die de jokertekens bevatten. Aparte namen met behulp`;`van een semicolin ( ).

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Voorbeeld** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --include-pattern 'myFile*.txt;*.pdf*'` |

U bestanden ook `--exclude-pattern` uitsluiten met behulp van de optie. Zie [naslagdocumenten voor kopiëren](storage-ref-azcopy-copy.md) kopiëren voor meer informatie.

De `--include-pattern` `--exclude-pattern` opties en opties zijn alleen van toepassing op bestandsnamen en niet op het pad.  Als u alle tekstbestanden wilt kopiëren die in een `–recursive` mapstructuur bestaan, gebruikt u de `–include-pattern` optie `*.txt` om de hele mapstructuur op te halen en vervolgens de tekstbestanden en opgeven te gebruiken en op te geven om alle tekstbestanden op te halen.

## <a name="download-files"></a>Bestanden downloaden

U de [azcopy-opdracht](storage-ref-azcopy-copy.md) gebruiken om bestanden, mappen en bestandsshares te downloaden naar uw lokale computer.

Deze sectie bevat de volgende voorbeelden:

> [!div class="checklist"]
> * Bestand downloaden
> * Een map downloaden
> * De inhoud van een map downloaden
> * Specifieke bestanden downloaden

> [!NOTE]
> Als `Content-md5` de eigenschapswaarde van een bestand een hash bevat, berekent AzCopy een MD5-hash voor gedownloade `Content-md5` gegevens en controleert of de MD5-hash die is opgeslagen in de eigenschap van het bestand overeenkomt met de berekende hash. Als deze waarden niet overeenkomen, mislukt de download, tenzij `--check-md5=NoCheck` `--check-md5=LogOnly` u dit gedrag overschrijft door een append of de kopieeropdracht toe te passen.

Zie voor gedetailleerde referentiedocumenten [azcopy.](storage-ref-azcopy-copy.md)

> [!TIP]
> De voorbeelden in deze sectie omsluiten padargumenten met enkele aanhalingstekens (''). Gebruik enkele aanhalingstekens in alle opdrachtshells, behalve de Windows Command Shell (cmd.exe). Als u een Windows Command Shell (cmd.exe) gebruikt, sluit u padargumenten om met dubbele aanhalingstekens ("") in plaats van enkele aanhalingstekens ('').

### <a name="download-a-file"></a>Bestand downloaden

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-path><SAS-token>' '<local-file-path>'` |
| **Voorbeeld** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory\myTextFile.txt'` |

### <a name="download-a-directory"></a>Een map downloaden

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path><SAS-token>' '<local-directory-path>' --recursive` |
| **Voorbeeld** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory'  --recursive` |

Dit voorbeeld resulteert in `C:\myDirectory\myFileShareDirectory` een map met de naam met de naam van alle gedownloade bestanden.

### <a name="download-the-contents-of-a-directory"></a>De inhoud van een map downloaden

U de inhoud van een map downloaden zonder de inhoudvan de map zelf te kopiëren met behulp van het wildcardsymbool (*).

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/*<SAS-token>' '<local-directory-path>/'` |
| **Voorbeeld** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory/*?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory'` |

> [!NOTE]
> Sluit de `--recursive` vlag toe om bestanden in alle submappen te downloaden.

### <a name="download-specific-files"></a>Specifieke bestanden downloaden

U volledige bestandsnamen opgeven of gedeeltelijke namen met jokertekens (*) gebruiken.

#### <a name="specify-multiple-complete-file-names"></a>Meerdere volledige bestandsnamen opgeven

Gebruik de opdracht [azcopy-kopie](storage-ref-azcopy-copy.md) met de `--include-path` optie. Afzonderlijke bestandsnamen scheiden met behulp`;`van een semicolin ( ).

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' '<local-directory-path>'  --include-path <semicolon-separated-file-list>` |
| **Voorbeeld** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt' --recursive` |

In dit voorbeeld draagt `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/photos` AzCopy `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/documents/myFile.txt` de directory en het bestand over. U moet de `--recursive` optie opnemen om `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/photos` alle bestanden in de map over te zetten.

U bestanden ook `--exclude-path` uitsluiten met behulp van de optie. Zie [naslagdocumenten voor kopiëren](storage-ref-azcopy-copy.md) kopiëren voor meer informatie.

#### <a name="use-wildcard-characters"></a>Jokertekens gebruiken

Gebruik de opdracht [azcopy-kopie](storage-ref-azcopy-copy.md) met de `--include-pattern` optie. Geef gedeeltelijke namen op die de jokertekens bevatten. Aparte namen met behulp`;`van een semicolin ( ).

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name><SAS-token>' '<local-directory-path>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Voorbeeld** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |

U bestanden ook `--exclude-pattern` uitsluiten met behulp van de optie. Zie [naslagdocumenten voor kopiëren](storage-ref-azcopy-copy.md) kopiëren voor meer informatie.

De `--include-pattern` `--exclude-pattern` opties en opties zijn alleen van toepassing op bestandsnamen en niet op het pad.  Als u alle tekstbestanden wilt kopiëren die in een `–recursive` mapstructuur bestaan, gebruikt u de `–include-pattern` optie `*.txt` om de hele mapstructuur op te halen en vervolgens de tekstbestanden en opgeven te gebruiken en op te geven om alle tekstbestanden op te halen.

## <a name="copy-files-between-storage-accounts"></a>Bestanden kopiëren tussen opslagaccounts

U AzCopy gebruiken om bestanden naar andere opslagaccounts te kopiëren. De kopieerbewerking is synchroon, dus wanneer de opdracht wordt geretourneerd, geeft dit aan dat alle bestanden zijn gekopieerd.

AzCopy maakt gebruik van [server-naar-server](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) [API's,](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url)zodat gegevens rechtstreeks tussen opslagservers worden gekopieerd. Deze kopieerbewerkingen maken geen gebruik van de netwerkbandbreedte van uw computer. U de doorvoer van deze bewerkingen `AZCOPY_CONCURRENCY_VALUE` verhogen door de waarde van de omgevingsvariabele in te stellen. Zie [Doorvoer optimaliseren](storage-use-azcopy-configure.md#optimize-throughput)voor meer informatie.

Deze sectie bevat de volgende voorbeelden:

> [!div class="checklist"]
> * Een bestand naar een ander opslagaccount kopiëren
> * Een map naar een ander opslagaccount kopiëren
> * Een bestandsshare naar een ander opslagaccount kopiëren
> * Alle bestandsshares, mappen en bestanden naar een ander opslagaccount kopiëren

Zie [azcopy copy](storage-ref-azcopy-copy.md)voor gedetailleerde referentiedocumenten.

> [!TIP]
> De voorbeelden in deze sectie omsluiten padargumenten met enkele aanhalingstekens (''). Gebruik enkele aanhalingstekens in alle opdrachtshells, behalve de Windows Command Shell (cmd.exe). Als u een Windows Command Shell (cmd.exe) gebruikt, sluit u padargumenten om met dubbele aanhalingstekens ("") in plaats van enkele aanhalingstekens ('').

### <a name="copy-a-file-to-another-storage-account"></a>Een bestand naar een ander opslagaccount kopiëren

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<file-path>?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>/<file-path><SAS-token>'` |
| **Voorbeeld** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D'` |

### <a name="copy-a-directory-to-another-storage-account"></a>Een map naar een ander opslagaccount kopiëren

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path>?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **Voorbeeld** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

### <a name="copy-a-file-share-to-another-storage-account"></a>Een bestandsshare naar een ander opslagaccount kopiëren

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **Voorbeeld** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

### <a name="copy-all-file-shares-directories-and-files-to-another-storage-account"></a>Alle bestandsshares, mappen en bestanden naar een ander opslagaccount kopiëren

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<SAS-token>' --recursive'` |
| **Voorbeeld** | `azcopy copy 'https://mysourceaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

## <a name="synchronize-files"></a>Bestanden synchroniseren

U de inhoud van een bestandsshare synchroniseren met een andere bestandsshare. U de inhoud van een map in een bestandsshare ook synchroniseren met de inhoud van een map die zich in een andere bestandsshare bevindt. Synchronisatie is eenrichtingsverkeer. Met andere woorden, u kiest welke van deze twee eindpunten de bron is en welke de bestemming is. Synchronisatie maakt ook gebruik van server naar server API's.

> [!NOTE]
> Momenteel wordt dit scenario alleen ondersteund voor accounts die geen hiërarchische naamruimte hebben. De huidige versie van AzCopy synchroniseert niet tussen Azure Files en Blob Storage.

Met [de opdracht Synchronisatie](storage-ref-azcopy-sync.md) worden bestandsnamen en laatst gewijzigde tijdstempels vergeleken. Stel `--delete-destination` de optionele vlag `true` in `prompt` op een waarde van of om bestanden in de doelmap te verwijderen als deze bestanden niet meer bestaan in de bronmap.

Als u `--delete-destination` de `true` vlag instelt op AzCopy, worden bestanden verwijderd zonder een prompt te geven. Als u een prompt wilt weergeven voordat AzCopy `--delete-destination` een `prompt`bestand verwijdert, stelt u de vlag in op .

Zie [azcopy-synchronisatie](storage-ref-azcopy-sync.md)voor gedetailleerde referentiedocumenten .

> [!TIP]
> De voorbeelden in deze sectie omsluiten padargumenten met enkele aanhalingstekens (''). Gebruik enkele aanhalingstekens in alle opdrachtshells, behalve de Windows Command Shell (cmd.exe). Als u een Windows Command Shell (cmd.exe) gebruikt, sluit u padargumenten om met dubbele aanhalingstekens ("") in plaats van enkele aanhalingstekens ('').

### <a name="update-a-file-share-with-changes-to-another-file-share"></a>Een bestandsshare bijwerken met wijzigingen in een ander bestandsshare

De eerste bestandsshare die in deze opdracht wordt weergegeven, is de bron. De tweede is de bestemming.

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **Voorbeeld** | `azcopy sync 'https://mysourceaccount.file.core.windows.net/myfileShare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

### <a name="update-a-directory-with-changes-to-a-directory-in-another-file-share"></a>Een map bijwerken met wijzigingen in een map in een ander bestandsaandeel

De eerste map die in deze opdracht wordt weergegeven, is de bron. De tweede is de bestemming.

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-name>?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-name><SAS-token>' --recursive` |
| **Voorbeeld** | `azcopy sync 'https://mysourceaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

## <a name="next-steps"></a>Volgende stappen

Meer voorbeelden vindt u in een van deze artikelen:

- [Aan de slag met AzCopy](storage-use-azcopy-v10.md)

- [Gegevens overbrengen met AzCopy- en blob-opslag](storage-use-azcopy-blobs.md)

- [Gegevens overbrengen met AzCopy- en Amazon S3-buckets](storage-use-azcopy-s3.md)

- [AzCopy configureren, optimaliseren en oplossen](storage-use-azcopy-configure.md)
