---
title: Gegevens overbrengen van of naar Azure Blob-opslag met AzCopy v10 | Microsoft Documenten
description: Dit artikel bevat een verzameling azcopy-voorbeeldopdrachten waarmee u containers maken, bestanden kopiëren en mappen synchroniseren tussen lokale bestandssystemen en containers.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: fbdb447905ae43fe92693dfe45c1add710f76355
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78933579"
---
# <a name="transfer-data-with-azcopy-and-blob-storage"></a>Gegevens overbrengen met AzCopy- en Blob-opslag

AzCopy is een command-line hulpprogramma dat u gebruiken om gegevens te kopiëren naar, van of tussen opslagaccounts. Dit artikel bevat voorbeeldopdrachten die werken met Blob-opslag.

## <a name="get-started"></a>Aan de slag

Bekijk het artikel Aan de [slag met AzCopy](storage-use-azcopy-v10.md) om AzCopy te downloaden en meer te weten te komen over de manieren waarop u autorisatiereferenties verstrekken aan de opslagservice.

> [!NOTE]
> De voorbeelden in dit artikel gaan ervan uit dat `AzCopy login` u uw identiteit hebt geverifieerd met behulp van de opdracht. AzCopy gebruikt vervolgens uw Azure AD-account om toegang tot gegevens in Blob-opslag te autoriseren.
>
> Als u liever een SAS-token gebruikt om toegang tot blobgegevens te autoriseren, u dat token toevoegen aan de bron-URL in elke AzCopy-opdracht.
>
> Bijvoorbeeld: `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`.

## <a name="create-a-container"></a>Een container maken

> [!TIP]
> De voorbeelden in deze sectie omsluiten padargumenten met enkele aanhalingstekens (''). Gebruik enkele aanhalingstekens in alle opdrachtshells, behalve de Windows Command Shell (cmd.exe). Als u een Windows Command Shell (cmd.exe) gebruikt, sluit u padargumenten om met dubbele aanhalingstekens ("") in plaats van enkele aanhalingstekens ('').

U de [opdracht azcopy make](storage-ref-azcopy-make.md) gebruiken om een container te maken. De voorbeelden in deze sectie `mycontainer`maken een container met de naam .

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy make 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>'` |
| **Voorbeeld** | `azcopy make 'https://mystorageaccount.blob.core.windows.net/mycontainer'` |
| **Voorbeeld** (hiërarchische naamruimte) | `azcopy make 'https://mystorageaccount.dfs.core.windows.net/mycontainer'` |

Zie [azcopy maken](storage-ref-azcopy-make.md)voor gedetailleerde referentiedocumenten.

## <a name="upload-files"></a>Bestanden uploaden

U de [azcopy-opdracht](storage-ref-azcopy-copy.md) gebruiken om bestanden en mappen te uploaden vanaf uw lokale computer.

Deze sectie bevat de volgende voorbeelden:

> [!div class="checklist"]
> * Bestand uploaden
> * Een map uploaden
> * De inhoud van een map uploaden 
> * Specifieke bestanden uploaden

Zie [azcopy copy](storage-ref-azcopy-copy.md)voor gedetailleerde referentiedocumenten.

> [!TIP]
> De voorbeelden in deze sectie omsluiten padargumenten met enkele aanhalingstekens (''). Gebruik enkele aanhalingstekens in alle opdrachtshells, behalve de Windows Command Shell (cmd.exe). Als u een Windows Command Shell (cmd.exe) gebruikt, sluit u padargumenten om met dubbele aanhalingstekens ("") in plaats van enkele aanhalingstekens ('').

### <a name="upload-a-file"></a>Bestand uploaden

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy '<local-file-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>'` |
| **Voorbeeld** | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |
| **Voorbeeld** (hiërarchische naamruimte) | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt'` |

U een bestand ook uploaden met een wildcardsymbool (*) overal in het bestandspad of de bestandsnaam. Bijvoorbeeld: `'C:\myDirectory\*.txt'`, `C:\my*\*.txt`of .

> [!NOTE]
> AzCopy uploadt standaard gegevens als blokblobs. Als u bestanden wilt uploaden als Blobs `--blob-type=[BlockBlob|PageBlob|AppendBlob]`of Paginablobs toevoegen, gebruikt u de vlag .
> AzCopy uploadt standaard uw gegevens om de accounttoegangslaag over te nemen. Als u bestanden wilt uploaden naar `--block-blob-tier=[Hot|Cool|Archive]`een specifieke [toegangslaag,](../blobs/storage-blob-storage-tiers.md)gebruikt u de vlag .

### <a name="upload-a-directory"></a>Een map uploaden

In dit voorbeeld wordt een map (en alle bestanden in die map) gekopieerd naar een blobcontainer. Het resultaat is een map in de container met dezelfde naam.

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --recursive` |
| **Voorbeeld** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive` |
| **Voorbeeld** (hiërarchische naamruimte) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --recursive` |

Als u wilt kopiëren naar een map in de container, geeft u de naam van die map op in uw opdrachttekenreeks.

|    |     |
|--------|-----------|
| **Voorbeeld** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' --recursive` |
| **Voorbeeld** (hiërarchische naamruimte) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory' --recursive` |

Als u de naam opgeeft van een map die niet in de container bestaat, maakt AzCopy een nieuwe map met die naam.

### <a name="upload-the-contents-of-a-directory"></a>De inhoud van een map uploaden

U de inhoud van een map uploaden zonder de inhoudsmap zelf te kopiëren met behulp van het wildcardsymbool (*).

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>'` |
| **Voorbeeld** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory'` |
| **Voorbeeld** (hiërarchische naamruimte) | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory'` |

> [!NOTE]
> Voeg de `--recursive` vlag toe om bestanden in alle submappen te uploaden.

### <a name="upload-specific-files"></a>Specifieke bestanden uploaden

U volledige bestandsnamen opgeven of gedeeltelijke namen met jokertekens (*) gebruiken.

#### <a name="specify-multiple-complete-file-names"></a>Meerdere volledige bestandsnamen opgeven

Gebruik de opdracht [azcopy-kopie](storage-ref-azcopy-copy.md) met de `--include-path` optie. Afzonderlijke bestandsnamen scheiden met behulp`;`van een puntkomma ( ).

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-path <semicolon-separated-file-list>` |
| **Voorbeeld** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive` |
| **Voorbeeld** (hiërarchische naamruimte) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive` |

In dit voorbeeld draagt `C:\myDirectory\photos` AzCopy `C:\myDirectory\documents\myFile.txt` de directory en het bestand over. U moet de `--recursive` optie opnemen om `C:\myDirectory\photos` alle bestanden in de map over te zetten.

U bestanden ook `--exclude-path` uitsluiten met behulp van de optie. Zie [naslagdocumenten voor kopiëren](storage-ref-azcopy-copy.md) kopiëren voor meer informatie.

#### <a name="use-wildcard-characters"></a>Jokertekens gebruiken

Gebruik de opdracht [azcopy-kopie](storage-ref-azcopy-copy.md) met de `--include-pattern` optie. Geef gedeeltelijke namen op die de jokertekens bevatten. Aparte namen met behulp`;`van een semicolin ( ). 

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Voorbeeld** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'` |
| **Voorbeeld** (hiërarchische naamruimte) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'` |

U bestanden ook `--exclude-pattern` uitsluiten met behulp van de optie. Zie [naslagdocumenten voor kopiëren](storage-ref-azcopy-copy.md) kopiëren voor meer informatie.

De `--include-pattern` `--exclude-pattern` opties en opties zijn alleen van toepassing op bestandsnamen en niet op het pad.  Als u alle tekstbestanden wilt kopiëren die in een `–recursive` mapstructuur bestaan, gebruikt u de `–include-pattern` optie `*.txt` om de hele mapstructuur op te halen en vervolgens de tekstbestanden en opgeven te gebruiken en op te geven om alle tekstbestanden op te halen.

## <a name="download-files"></a>Bestanden downloaden

U de [opdracht azcopy-kopie](storage-ref-azcopy-copy.md) gebruiken om blobs, mappen en containers naar uw lokale computer te downloaden.

Deze sectie bevat de volgende voorbeelden:

> [!div class="checklist"]
> * Bestand downloaden
> * Een map downloaden
> * De inhoud van een map downloaden
> * Specifieke bestanden downloaden

> [!NOTE]
> Als `Content-md5` de eigenschapswaarde van een blob een hash bevat, berekent AzCopy een MD5-hash voor gedownloade `Content-md5` gegevens en controleert of de MD5-hash die is opgeslagen in de eigenschap van de blob overeenkomt met de berekende hash. Als deze waarden niet overeenkomen, mislukt de download, tenzij `--check-md5=NoCheck` `--check-md5=LogOnly` u dit gedrag overschrijft door een append of de kopieeropdracht toe te passen.

Zie [azcopy copy](storage-ref-azcopy-copy.md)voor gedetailleerde referentiedocumenten.

> [!TIP]
> De voorbeelden in deze sectie omsluiten padargumenten met enkele aanhalingstekens (''). Gebruik enkele aanhalingstekens in alle opdrachtshells, behalve de Windows Command Shell (cmd.exe). Als u een Windows Command Shell (cmd.exe) gebruikt, sluit u padargumenten om met dubbele aanhalingstekens ("") in plaats van enkele aanhalingstekens ('').

### <a name="download-a-file"></a>Bestand downloaden

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>' '<local-file-path>'` |
| **Voorbeeld** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt'` |
| **Voorbeeld** (hiërarchische naamruimte) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt'` |

### <a name="download-a-directory"></a>Een map downloaden

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>' '<local-directory-path>' --recursive` |
| **Voorbeeld** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' 'C:\myDirectory'  --recursive` |
| **Voorbeeld** (hiërarchische naamruimte) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory' 'C:\myDirectory'  --recursive` |

Dit voorbeeld resulteert in `C:\myDirectory\myBlobDirectory` een map met de naam met de naam van alle gedownloade bestanden.

### <a name="download-the-contents-of-a-directory"></a>De inhoud van een map downloaden

U de inhoud van een map downloaden zonder de inhoudvan de map zelf te kopiëren met behulp van het wildcardsymbool (*).

> [!NOTE]
> Momenteel wordt dit scenario alleen ondersteund voor accounts die geen hiërarchische naamruimte hebben.

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy 'https://<storage-account-name>.blob.core.windows.net/<container-name>/*' '<local-directory-path>/'` |
| **Voorbeeld** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory/*' 'C:\myDirectory'` |

> [!NOTE]
> Sluit de `--recursive` vlag toe om bestanden in alle submappen te downloaden.

### <a name="download-specific-files"></a>Specifieke bestanden downloaden

U volledige bestandsnamen opgeven of gedeeltelijke namen met jokertekens (*) gebruiken.

#### <a name="specify-multiple-complete-file-names"></a>Meerdere volledige bestandsnamen opgeven

Gebruik de opdracht [azcopy-kopie](storage-ref-azcopy-copy.md) met de `--include-path` optie. Afzonderlijke bestandsnamen scheiden met behulp`;`van een semicolin ( ).

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>'  --include-path <semicolon-separated-file-list>` |
| **Voorbeeld** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt' --recursive` |
| **Voorbeeld** (hiërarchische naamruimte) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt'--recursive` |

In dit voorbeeld draagt `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos` AzCopy `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/documents/myFile.txt` de directory en het bestand over. U moet de `--recursive` optie opnemen om `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos` alle bestanden in de map over te zetten.

U bestanden ook `--exclude-path` uitsluiten met behulp van de optie. Zie [naslagdocumenten voor kopiëren](storage-ref-azcopy-copy.md) kopiëren voor meer informatie.

#### <a name="use-wildcard-characters"></a>Jokertekens gebruiken

Gebruik de opdracht [azcopy-kopie](storage-ref-azcopy-copy.md) met de `--include-pattern` optie. Geef gedeeltelijke namen op die de jokertekens bevatten. Aparte namen met behulp`;`van een semicolin ( ).

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Voorbeeld** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |
| **Voorbeeld** (hiërarchische naamruimte) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |

U bestanden ook `--exclude-pattern` uitsluiten met behulp van de optie. Zie [naslagdocumenten voor kopiëren](storage-ref-azcopy-copy.md) kopiëren voor meer informatie.

De `--include-pattern` `--exclude-pattern` opties en opties zijn alleen van toepassing op bestandsnamen en niet op het pad.  Als u alle tekstbestanden wilt kopiëren die in een `–recursive` mapstructuur bestaan, gebruikt u de `–include-pattern` optie `*.txt` om de hele mapstructuur op te halen en vervolgens de tekstbestanden en opgeven te gebruiken en op te geven om alle tekstbestanden op te halen.

## <a name="copy-blobs-between-storage-accounts"></a>Blobs kopiëren tussen opslagaccounts

U AzCopy gebruiken om blobs naar andere opslagaccounts te kopiëren. De kopieerbewerking is synchroon, dus wanneer de opdracht wordt geretourneerd, geeft dit aan dat alle bestanden zijn gekopieerd. 

AzCopy maakt gebruik van [server-naar-server](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) [API's,](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url)zodat gegevens rechtstreeks tussen opslagservers worden gekopieerd. Deze kopieerbewerkingen maken geen gebruik van de netwerkbandbreedte van uw computer. U de doorvoer van deze bewerkingen `AZCOPY_CONCURRENCY_VALUE` verhogen door de waarde van de omgevingsvariabele in te stellen. Zie [Doorvoer optimaliseren](storage-use-azcopy-configure.md#optimize-throughput)voor meer informatie.

> [!NOTE]
> Dit scenario heeft de volgende beperkingen in de huidige release.
>
> - U moet een SAS-token toevoegen aan elke bron-URL. Als u autorisatiereferenties opgeeft met Behulp van Azure Active Directory (AD), u het SAS-token alleen weglaten op de bestemmings-URL.
>-  Premium block blob-opslagaccounts bieden geen ondersteuning voor toegangsniveaus. De toegangslaag van een blob weglaten van `s2s-preserve-access-tier` `false` de kopieerbewerking `--s2s-preserve-access-tier=false`door de to in te stellen (Bijvoorbeeld: ).

Deze sectie bevat de volgende voorbeelden:

> [!div class="checklist"]
> * Een blob naar een ander opslagaccount kopiëren
> * Een map naar een ander opslagaccount kopiëren
> * Een container naar een ander opslagaccount kopiëren
> * Alle containers, mappen en bestanden naar een ander opslagaccount kopiëren

Zie [azcopy copy](storage-ref-azcopy-copy.md)voor gedetailleerde referentiedocumenten.

> [!TIP]
> De voorbeelden in deze sectie omsluiten padargumenten met enkele aanhalingstekens (''). Gebruik enkele aanhalingstekens in alle opdrachtshells, behalve de Windows Command Shell (cmd.exe). Als u een Windows Command Shell (cmd.exe) gebruikt, sluit u padargumenten om met dubbele aanhalingstekens ("") in plaats van enkele aanhalingstekens ('').

 Deze voorbeelden werken ook met accounts met een hiërarchische naamruimte. [Met toegang met meerdere protocollen op Data Lake Storage](../blobs/data-lake-storage-multi-protocol-access.md) u dezelfde URL-syntaxis ()`blob.core.windows.net`gebruiken voor die accounts. 

### <a name="copy-a-blob-to-another-storage-account"></a>Een blob naar een ander opslagaccount kopiëren

Gebruik dezelfde URL-syntaxis (`blob.core.windows.net`) voor accounts met een hiërarchische naamruimte.

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>'` |
| **Voorbeeld** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |
| **Voorbeeld** (hiërarchische naamruimte) | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |

### <a name="copy-a-directory-to-another-storage-account"></a>Een map naar een ander opslagaccount kopiëren

Gebruik dezelfde URL-syntaxis (`blob.core.windows.net`) voor accounts met een hiërarchische naamruimte.

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Voorbeeld** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |
| **Voorbeeld** (hiërarchische naamruimte)| `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="copy-a-container-to-another-storage-account"></a>Een container naar een ander opslagaccount kopiëren

Gebruik dezelfde URL-syntaxis (`blob.core.windows.net`) voor accounts met een hiërarchische naamruimte.

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Voorbeeld** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |
| **Voorbeeld** (hiërarchische naamruimte)| `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="copy-all-containers-directories-and-blobs-to-another-storage-account"></a>Alle containers, mappen en blobs naar een ander opslagaccount kopiëren

Gebruik dezelfde URL-syntaxis (`blob.core.windows.net`) voor accounts met een hiërarchische naamruimte.

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/' --recursive` |
| **Voorbeeld** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive` |
| **Voorbeeld** (hiërarchische naamruimte)| `azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive` |

## <a name="synchronize-files"></a>Bestanden synchroniseren

U de inhoud van een lokaal bestandssysteem synchroniseren met een blobcontainer. U ook containers en virtuele mappen met elkaar synchroniseren. Synchronisatie is eenrichtingsverkeer. Met andere woorden, u kiest welke van deze twee eindpunten de bron is en welke de bestemming is. Synchronisatie maakt ook gebruik van server naar server API's. De voorbeelden in deze sectie werken ook met accounts met een hiërarchische naamruimte. 

> [!NOTE]
> De huidige release van AzCopy synchroniseert niet tussen andere bronnen en bestemmingen (bijvoorbeeld: Bestandsopslag of Amazon Web Services (AWS) S3-buckets).

Met [de opdracht Synchronisatie](storage-ref-azcopy-sync.md) worden bestandsnamen en laatst gewijzigde tijdstempels vergeleken. Stel `--delete-destination` de optionele vlag `true` in `prompt` op een waarde van of om bestanden in de doelmap te verwijderen als deze bestanden niet meer bestaan in de bronmap.

Als u `--delete-destination` de `true` vlag instelt op AzCopy, worden bestanden verwijderd zonder een prompt te geven. Als u een prompt wilt weergeven voordat AzCopy `--delete-destination` een `prompt`bestand verwijdert, stelt u de vlag in op .

> [!NOTE]
> Als u onbedoelde verwijderingen wilt voorkomen, moet u `--delete-destination=prompt|true` de functie soft [delete](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) inschakelen voordat u de vlag gebruikt.

Zie [azcopy-synchronisatie](storage-ref-azcopy-sync.md)voor gedetailleerde referentiedocumenten .

> [!TIP]
> De voorbeelden in deze sectie omsluiten padargumenten met enkele aanhalingstekens (''). Gebruik enkele aanhalingstekens in alle opdrachtshells, behalve de Windows Command Shell (cmd.exe). Als u een Windows Command Shell (cmd.exe) gebruikt, sluit u padargumenten om met dubbele aanhalingstekens ("") in plaats van enkele aanhalingstekens ('').

### <a name="update-a-container-with-changes-to-a-local-file-system"></a>Een container bijwerken met wijzigingen in een lokaal bestandssysteem

In dit geval is de container de bestemming en is het lokale bestandssysteem de bron. 

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy sync '<local-directory-path>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Voorbeeld** | `azcopy sync 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="update-a-local-file-system-with-changes-to-a-container"></a>Een lokaal bestandssysteem bijwerken met wijzigingen in een container

In dit geval is het lokale bestandssysteem de bestemming en is de container de bron.

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy sync 'https://<storage-account-name>.blob.core.windows.net/<container-name>' 'C:\myDirectory' --recursive` |
| **Voorbeeld** | `azcopy sync 'https://mystorageaccount.blob.core.windows.net/mycontainer' 'C:\myDirectory' --recursive` |

### <a name="update-a-container-with-changes-in-another-container"></a>Een container bijwerken met wijzigingen in een andere container

De eerste container die in deze opdracht wordt weergegeven, is de bron. De tweede is de bestemming.

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Voorbeeld** | `azcopy sync 'https://mysourceaccount.blob.core.windows.net/mycontainer' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="update-a-directory-with-changes-to-a-directory-in-another-file-share"></a>Een map bijwerken met wijzigingen in een map in een ander bestandsaandeel

De eerste map die in deze opdracht wordt weergegeven, is de bron. De tweede is de bestemming.

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive` |
| **Voorbeeld** | `azcopy sync 'https://mysourceaccount.blob.core.windows.net/<container-name>/myDirectory' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myDirectory' --recursive` |

## <a name="next-steps"></a>Volgende stappen

Meer voorbeelden vindt u in een van deze artikelen:

- [Aan de slag met AzCopy](storage-use-azcopy-v10.md)

- [Zelfstudie: on-premises gegevens migreren naar cloudopslag met behulp van AzCopy](storage-use-azcopy-migrate-on-premises-data.md)

- [Gegevens overbrengen met AzCopy en bestandsopslag](storage-use-azcopy-files.md)

- [Gegevens overbrengen met AzCopy- en Amazon S3-buckets](storage-use-azcopy-s3.md)

- [AzCopy configureren, optimaliseren en oplossen](storage-use-azcopy-configure.md)
