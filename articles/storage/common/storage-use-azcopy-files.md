---
title: Gegevens overdragen van of naar Azure Files met behulp van AzCopy V10 toevoegen | Microsoft Docs
description: Gegevens overdragen met AzCopy en file storage.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 361b16ff074baaf0118ccfe6d3c2a20f0e66c623
ms.sourcegitcommit: f272ba8ecdbc126d22a596863d49e55bc7b22d37
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/11/2019
ms.locfileid: "72273905"
---
# <a name="transfer-data-with-azcopy-and-file-storage"></a>Gegevens overdragen met AzCopy en File Storage 

AzCopy is een opdracht regel programma dat u kunt gebruiken voor het kopiëren van blobs of bestanden naar of van een opslag account. Dit artikel bevat voorbeeld opdrachten die samen werken met Azure Files.

Voordat u begint, raadpleegt u het artikel aan de [slag met AzCopy](storage-use-azcopy-v10.md) om AzCopy te downloaden en vertrouwd te raken met het hulp programma.

## <a name="create-file-shares"></a>Bestands shares maken

U kunt de [azcopy](storage-ref-azcopy-make.md) maken om een bestands share te maken. In het voor beeld in deze sectie wordt een bestands share met de naam `myfileshare` gemaakt.

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy make 'https://<storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>'` |
| **Voorbeeld** | `azcopy make 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'` |

Zie [azcopy maken](storage-ref-azcopy-make.md)voor gedetailleerde naslag documentatie.

## <a name="upload-files"></a>Bestanden uploaden

U kunt de azcopy-opdracht [kopiëren](storage-ref-azcopy-copy.md) gebruiken om bestanden en mappen te uploaden van uw lokale computer.

Deze sectie bevat de volgende voor beelden:

> [!div class="checklist"]
> * Bestand uploaden
> * Een map uploaden
> * De inhoud van een map uploaden
> * Een specifiek bestand uploaden

> [!NOTE]
> De MD5-hash-code van het bestand wordt niet automatisch door AzCopy berekend en opgeslagen. Als u dit wilt doen, voegt u de vlag `--put-md5` toe aan elke Kopieer opdracht. Op die manier wordt, wanneer het bestand wordt gedownload, AzCopy een MD5-hash voor gedownloade gegevens berekend en wordt gecontroleerd of de MD5-hash die is opgeslagen in de eigenschap `Content-md5` van het bestand overeenkomt met de berekende hash.

Zie [azcopy Copy](storage-ref-azcopy-copy.md)voor gedetailleerde naslag documenten.

### <a name="upload-a-file"></a>Bestand uploaden

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy '<local-file-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-name>?<SAS-token>'` |
| **Voorbeeld** | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'` |

U kunt ook een bestand uploaden met behulp van een Joker teken (*) ergens in het bestandspad of de bestands naam. Bijvoorbeeld: `'C:\myDirectory\*.txt'`, of `C:\my*\*.txt`.

### <a name="upload-a-directory"></a>Een map uploaden

In dit voor beeld wordt een map (en alle bestanden in die map) naar een bestands share gekopieerd. Het resultaat is een map in de bestands share met dezelfde naam.

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>' --recursive` |
| **Voorbeeld** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' --recursive` |

Als u wilt kopiëren naar een map in de bestands share, geeft u alleen de naam van die map op in de opdracht teken reeks.

|    |     |
|--------|-----------|
| **Voorbeeld** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' --recursive` |

Als u de naam opgeeft van een map die niet voor komt in de bestands share, maakt AzCopy een nieuwe map met die naam.

### <a name="upload-the-contents-of-a-directory"></a>De inhoud van een map uploaden

U kunt de inhoud van een map uploaden zonder de bovenliggende map zelf te kopiëren met behulp van het Joker teken (*).

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy '<local-directory-path>/*' 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path>?<SAS-token>` |
| **Voorbeeld** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

> [!NOTE]
> Voeg de vlag `--recursive` toe om bestanden te uploaden in alle submappen.

### <a name="upload-specific-files"></a>Specifieke bestanden uploaden

U kunt volledige bestands namen opgeven of gedeeltelijke namen gebruiken met Joker tekens (*).

#### <a name="specify-multiple-complete-file-names"></a>Meerdere volledige bestands namen opgeven

Gebruik de [azcopy Copy](storage-ref-azcopy-copy.md) opdracht met de optie `--include-path`. Scheid afzonderlijke bestands namen met een punt komma (`;`).

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name>' --include-path <semicolon-separated-file-list>` |
| **Voorbeeld** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare' --include-path 'photos;documents\myFile.txt'` |

In dit voor beeld stuurt AzCopy de map `C:\myDirectory\photos` en het bestand `C:\myDirectory\documents\myFile.txt`. U moet de optie `--recursive` toevoegen om alle bestanden in de map `C:\myDirectory\photos` over te dragen.

U kunt ook bestanden uitsluiten met behulp van de optie `--exclude-path`. Zie voor meer informatie [azcopy Copy](storage-ref-azcopy-copy.md) Reference docs.

#### <a name="use-wildcard-characters"></a>Joker tekens gebruiken

Gebruik de [azcopy Copy](storage-ref-azcopy-copy.md) opdracht met de optie `--include-pattern`. Geef gedeeltelijke namen op die de joker tekens bevatten. Scheid namen met behulp van een semicolin (`;`).

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Voorbeeld** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare' --include-pattern 'myFile*.txt;*.pdf*'` |

U kunt ook bestanden uitsluiten met behulp van de optie `--exclude-pattern`. Zie voor meer informatie [azcopy Copy](storage-ref-azcopy-copy.md) Reference docs.

De opties `--include-pattern` en `--exclude-pattern` zijn alleen van toepassing op bestands namen en niet op het pad.  Als u alle tekst bestanden in een mapstructuur wilt kopiëren, gebruikt u de optie `–recursive` om de volledige mapstructuur op te halen. vervolgens gebruikt u de `–include-pattern` en geeft u `*.txt` op om alle tekst bestanden op te halen.

## <a name="download-files"></a>Bestanden downloaden

U kunt de azcopy-opdracht [kopiëren](storage-ref-azcopy-copy.md) gebruiken om bestanden, mappen en bestands shares te downloaden naar uw lokale computer.

Deze sectie bevat de volgende voor beelden:

> [!div class="checklist"]
> * Bestand downloaden
> * Een directory downloaden
> * De inhoud van een map downloaden
> * Specifieke bestanden downloaden

> [!NOTE]
> Als de waarde van de eigenschap `Content-md5` van een bestand een hash bevat, wordt in AzCopy een MD5-hash voor gedownloade gegevens berekend en wordt gecontroleerd of de MD5-hash die is opgeslagen in de eigenschap `Content-md5` van het bestand overeenkomt met de berekende hash. Als deze waarden niet overeenkomen, mislukt de down load tenzij u dit gedrag overschrijft door `--check-md5=NoCheck` of `--check-md5=LogOnly` toe te voegen aan de Kopieer opdracht.

Zie [azcopy Copy](storage-ref-azcopy-copy.md)voor gedetailleerde naslag documenten.

### <a name="download-a-file"></a>Bestand downloaden

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-path>?<SAS-token>' '<local-file-path>'` |
| **Voorbeeld** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory\myTextFile.txt'` |

### <a name="download-a-directory"></a>Een directory downloaden

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path>?<SAS-token>' '<local-directory-path>' --recursive` |
| **Voorbeeld** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory'  --recursive` |

Dit voor beeld resulteert in een map met de naam `C:\myDirectory\myFileShareDirectory` die alle gedownloade bestanden bevat.

### <a name="download-the-contents-of-a-directory"></a>De inhoud van een map downloaden

U kunt de inhoud van een map downloaden zonder de bovenliggende map zelf te kopiëren met behulp van het Joker teken (*).

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/*?<SAS-token>' '<local-directory-path>/'` |
| **Voorbeeld** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory/*?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory'` |

> [!NOTE]
> Voeg de vlag `--recursive` toe om bestanden in alle submappen te downloaden.

### <a name="download-specific-files"></a>Specifieke bestanden downloaden

U kunt volledige bestands namen opgeven of gedeeltelijke namen gebruiken met Joker tekens (*).

#### <a name="specify-multiple-complete-file-names"></a>Meerdere volledige bestands namen opgeven

Gebruik de [azcopy Copy](storage-ref-azcopy-copy.md) opdracht met de optie `--include-path`. Scheid afzonderlijke bestands namen met behulp van een semicolin (`;`).

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name>' '<local-directory-path>'  --include-path <semicolon-separated-file-list>` |
| **Voorbeeld** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt' --recursive` |

In dit voor beeld stuurt AzCopy de map `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/photos` en het bestand `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/documents/myFile.txt`. U moet de optie `--recursive` toevoegen om alle bestanden in de map `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/photos` over te dragen.

U kunt ook bestanden uitsluiten met behulp van de optie `--exclude-path`. Zie voor meer informatie [azcopy Copy](storage-ref-azcopy-copy.md) Reference docs.

#### <a name="use-wildcard-characters"></a>Joker tekens gebruiken

Gebruik de [azcopy Copy](storage-ref-azcopy-copy.md) opdracht met de optie `--include-pattern`. Geef gedeeltelijke namen op die de joker tekens bevatten. Scheid namen met behulp van een semicolin (`;`).

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Voorbeeld** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |

U kunt ook bestanden uitsluiten met behulp van de optie `--exclude-pattern`. Zie voor meer informatie [azcopy Copy](storage-ref-azcopy-copy.md) Reference docs.

De opties `--include-pattern` en `--exclude-pattern` zijn alleen van toepassing op bestands namen en niet op het pad.  Als u alle tekst bestanden in een mapstructuur wilt kopiëren, gebruikt u de optie `–recursive` om de volledige mapstructuur op te halen. vervolgens gebruikt u de `–include-pattern` en geeft u `*.txt` op om alle tekst bestanden op te halen.

## <a name="copy-files-between-storage-accounts"></a>Bestanden kopiëren tussen opslag accounts

U kunt AzCopy gebruiken om bestanden naar andere opslag accounts te kopiëren. De Kopieer bewerking is synchroon, dus wanneer de opdracht wordt geretourneerd. Dit geeft aan dat alle bestanden zijn gekopieerd.

AzCopy maakt gebruik van [server-naar-server-](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) [api's](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url), zodat gegevens rechtstreeks tussen opslag servers worden gekopieerd. Deze Kopieer bewerkingen gebruiken de netwerk bandbreedte van uw computer niet. U kunt de door Voer van deze bewerkingen verhogen door de waarde van de omgevings variabele `AZCOPY_CONCURRENCY_VALUE` in te stellen. Zie de [door Voer optimaliseren](storage-use-azcopy-configure.md#optimize-throughput)voor meer informatie.

Deze sectie bevat de volgende voor beelden:

> [!div class="checklist"]
> * Een bestand kopiëren naar een ander opslag account
> * Een map kopiëren naar een ander opslag account
> * Een bestands share kopiëren naar een ander opslag account
> * Alle bestands shares, directory's en bestanden kopiëren naar een ander opslag account

Zie [azcopy Copy](storage-ref-azcopy-copy.md)voor gedetailleerde naslag documentatie.

### <a name="copy-a-file-to-another-storage-account"></a>Een bestand kopiëren naar een ander opslag account

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<file-path>?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>/<file-path>'` |
| **Voorbeeld** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer/myTextFile.txt'` |

### <a name="copy-a-directory-to-another-storage-account"></a>Een map kopiëren naar een ander opslag account

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path>?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>' --recursive` |
| **Voorbeeld** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer' --recursive` |

### <a name="copy-a-file-share-to-another-storage-account"></a>Een bestands share kopiëren naar een ander opslag account

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>' --recursive` |
| **Voorbeeld** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer' --recursive` |

### <a name="copy-all-file-shares-directories-and-files-to-another-storage-account"></a>Alle bestands shares, directory's en bestanden kopiëren naar een ander opslag account

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/' --recursive'` |
| **Voorbeeld** | `azcopy copy 'https://mysourceaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net' --recursive` |

## <a name="synchronize-files"></a>Bestanden synchroniseren

U kunt de inhoud van een lokaal bestands systeem synchroniseren met een bestands share. Synchronisatie is in één richting. Met andere woorden, u kunt kiezen welke van deze twee eind punten de bron is en welke het doel is. Synchronisatie gebruikt ook server-naar-server-Api's.

> [!NOTE]
> Dit scenario wordt momenteel alleen ondersteund voor accounts die geen hiërarchische naam ruimte hebben. De huidige release van AzCopy synchroniseert niet tussen Azure Files en Blob Storage.

Met de opdracht [Sync](storage-ref-azcopy-sync.md) worden bestands namen en tijds tempels met de laatste wijziging vergeleken. Stel de vlag `--delete-destination` optioneel in op een waarde van `true` of `prompt` om bestanden in de doelmap te verwijderen als deze bestanden niet meer in de bron directory bestaan.

Als u de vlag `--delete-destination` instelt op `true` AzCopy, worden bestanden verwijderd zonder dat u een prompt hoeft te geven. Als u wilt dat er een prompt wordt weer gegeven voordat AzCopy een bestand verwijdert, stelt u de vlag `--delete-destination` in op `prompt`.

Zie [azcopy Sync](storage-ref-azcopy-sync.md)(Engelstalig) voor gedetailleerde naslag documentatie.

### <a name="update-a-file-share-with-changes-to-a-local-file-system"></a>Een bestands share bijwerken met wijzigingen in een lokaal bestands systeem

In dit geval is de bestands share het doel en is het lokale bestands systeem de bron.

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy sync '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-name>' --recursive` |
| **Voorbeeld** | `azcopy sync 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/mycontainer' --recursive` |

### <a name="update-a-local-file-system-with-changes-to-a-file-share"></a>Een lokaal bestands systeem bijwerken met wijzigingen in een bestands share

In dit geval is het lokale bestands systeem het doel en de bestands share de bron.

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy sync 'https://<storage-account-name>.file.core.windows.net/<file-share-name>' 'C:\myDirectory' --recursive` |
| **Voorbeeld** | `azcopy sync 'https://mystorageaccount.file.core.windows.net/mycontainer' 'C:\myDirectory' --recursive` |

## <a name="next-steps"></a>Volgende stappen

Meer voor beelden vindt u in een van deze artikelen:

- [Aan de slag met AzCopy](storage-use-azcopy-v10.md)

- [Gegevens overdragen met AzCopy en Blob Storage](storage-use-azcopy-blobs.md)

- [Gegevens overdragen met AzCopy en Amazon S3-buckets](storage-use-azcopy-s3.md)

- [AzCopy configureren, optimaliseren en problemen oplossen](storage-use-azcopy-configure.md)