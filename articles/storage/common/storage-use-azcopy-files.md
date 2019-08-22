---
title: Gegevens overdragen van of naar Azure Files met behulp van AzCopy V10 toevoegen | Microsoft Docs
description: Gegevens overdragen met AzCopy en file storage.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 0c350776323c1b2949285a7ebe6a7c2778ae4dc4
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69648764"
---
# <a name="transfer-data-with-azcopy-and-file-storage"></a>Gegevens overdragen met AzCopy en File Storage 

AzCopy is een opdracht regel programma dat u kunt gebruiken voor het kopiëren van blobs of bestanden naar of van een opslag account. Dit artikel bevat voorbeeld opdrachten die samen werken met Azure Files.

Voordat u begint, raadpleegt u het artikel aan de [slag met AzCopy](storage-use-azcopy-v10.md) om AzCopy te downloaden en vertrouwd te raken met het hulp programma.

## <a name="create-file-shares"></a>Bestands shares maken

U kunt de opdracht AzCopy `make` gebruiken om een bestands share te maken. In het voor beeld in deze sectie wordt een bestands `myfileshare`share met de naam gemaakt.

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy make "https://<storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>"` |
| **Voorbeeld** | `azcopy make "https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

## <a name="upload-files"></a>Bestanden uploaden

U kunt de opdracht AzCopy `copy` gebruiken om bestanden en mappen te uploaden vanaf uw lokale computer.

Deze sectie bevat de volgende voorbeelden:

> [!div class="checklist"]
> * Een bestand uploaden
> * Een map uploaden
> * Bestanden uploaden met behulp van joker tekens

> [!NOTE]
> De MD5-hash-code van het bestand wordt niet automatisch door AzCopy berekend en opgeslagen. Als u dit wilt doen, voegt u de `--put-md5` markering toe aan elke Kopieer opdracht. Op die manier wordt, wanneer het bestand wordt gedownload, AzCopy een MD5-hash voor gedownloade gegevens berekend en wordt gecontroleerd of de MD5- `Content-md5` hash die is opgeslagen in de eigenschap van het bestand overeenkomt met de berekende hash.

### <a name="upload-a-file"></a>Een bestand uploaden

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy "<local-file-path>" "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-name>?<SAS-token>"` |
| **Voorbeeld** | `azcopy copy "C:\myDirectory\myTextFile.txt" "https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

### <a name="upload-a-directory"></a>Een map uploaden

In dit voor beeld wordt een map (en alle bestanden in die map) naar een bestands share gekopieerd. Het resultaat is een map in de bestands share met dezelfde naam.

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy "<local-directory-path>" "https://<storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>" --recursive` |
| **Voorbeeld** | `azcopy copy "C:\myDirectory" "https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" --recursive` |

Als u wilt kopiëren naar een map in de bestands share, geeft u alleen de naam van die map op in de opdracht teken reeks.

|    |     |
|--------|-----------|
| **Voorbeeld** | `azcopy copy "C:\myDirectory" "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" --recursive` |

Als u de naam opgeeft van een map die niet voor komt in de bestands share, maakt AzCopy een nieuwe map met die naam.

### <a name="upload-the-contents-of-a-directory"></a>De inhoud van een map uploaden

U kunt de inhoud van een map uploaden zonder de bovenliggende map zelf te kopiëren met behulp van het Joker teken (*).

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy "<local-directory-path>/*" "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path>?<SAS-token>` |
| **Voorbeeld** | `azcopy copy "C:\myDirectory\*" "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

> [!NOTE]
> Voeg de `--recursive` vlag toe om bestanden te uploaden in alle submappen.

## <a name="download-files"></a>Bestanden downloaden

U kunt de opdracht AzCopy `copy` gebruiken om bestanden, mappen en bestands shares te downloaden naar uw lokale computer.

Deze sectie bevat de volgende voorbeelden:

> [!div class="checklist"]
> * Een bestand downloaden
> * Een directory downloaden
> * Bestanden downloaden met behulp van joker tekens

> [!NOTE]
> Als de `Content-md5` eigenschaps waarde van een bestand een hash bevat, wordt in AzCopy een MD5-hash voor gedownloade gegevens berekend en wordt gecontroleerd of de MD5 `Content-md5` -hash die is opgeslagen in de eigenschap van het bestand overeenkomt met de berekende hash. Als deze waarden niet overeenkomen, mislukt de down load tenzij u dit gedrag overschrijft `--check-md5=NoCheck` door `--check-md5=LogOnly` toe te voegen of aan de Kopieer opdracht.

### <a name="download-a-file"></a>Een bestand downloaden

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-path>?<SAS-token>" "<local-file-path>"` |
| **Voorbeeld** | `azcopy copy "https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" "C:\myDirectory\myTextFile.txt"` |

### <a name="download-a-directory"></a>Een directory downloaden

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path>?<SAS-token>" "<local-directory-path>" --recursive` |
| **Voorbeeld** | `azcopy copy "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" "C:\myDirectory"  --recursive` |

Dit voor beeld resulteert in een map `C:\myDirectory\myFileShareDirectory` met de naam die alle gedownloade bestanden bevat.

### <a name="download-the-contents-of-a-directory"></a>De inhoud van een map downloaden

U kunt de inhoud van een map downloaden zonder de bovenliggende map zelf te kopiëren met behulp van het Joker teken (*).

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy "https://<storage-account-name>.file.core.windows.net/<file-share-name>/*?<SAS-token>" "<local-directory-path>/"` |
| **Voorbeeld** | `azcopy copy "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory/*?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" "C:\myDirectory"` |

> [!NOTE]
> Voeg de `--recursive` vlag toe om bestanden in alle submappen te downloaden.

## <a name="next-steps"></a>Volgende stappen

Meer voor beelden vindt u in een van deze artikelen:

- [Aan de slag met AzCopy](storage-use-azcopy-v10.md)

- [Gegevens overdragen met AzCopy en Blob Storage](storage-use-azcopy-blobs.md)

- [Gegevens overdragen met AzCopy en Amazon S3-buckets](storage-use-azcopy-s3.md)

- [AzCopy configureren, optimaliseren en problemen oplossen](storage-use-azcopy-configure.md)