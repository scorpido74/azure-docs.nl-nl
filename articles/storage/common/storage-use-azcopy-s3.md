---
title: Gegevens overdragen naar Azure Storage van Amazon S3-buckets met behulp van AzCopy V10 toevoegen | Microsoft Docs
description: Gegevens overdragen met AzCopy en Amazon S3-buckets
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 633d960e430d035a187a402e664c70f27cd23756
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69648737"
---
# <a name="copy-data-from-amazon-s3-buckets-by-using-azcopy"></a>Gegevens van Amazon S3-buckets kopiëren met behulp van AzCopy

AzCopy is een opdracht regel programma dat u kunt gebruiken voor het kopiëren van blobs of bestanden naar of van een opslag account. Dit artikel helpt u bij het kopiëren van objecten, directory's en buckets van Amazon Web Services (AWS) S3 naar Azure Blob Storage met behulp van AzCopy.

## <a name="choose-how-youll-provide-authorization-credentials"></a>Kiezen hoe u autorisatie referenties opgeeft

* Als u de Azure Storage wilt autoriseren, gebruikt u Azure Active Directory (AD) of een Shared Access Signature-token (SAS).

* Als u wilt autoriseren met AWS S3, gebruikt u een AWS-toegangs sleutel en een geheime toegangs sleutel.

### <a name="authorize-with-azure-storage"></a>Autoriseren met Azure Storage

Zie het artikel aan de [slag met AzCopy](storage-use-azcopy-v10.md) om AzCopy te downloaden en kies hoe u autorisatie referenties opgeeft voor de opslag service.

> [!NOTE]
> In de voor beelden in dit artikel wordt ervan uitgegaan dat u uw identiteit `AzCopy login` hebt geverifieerd met behulp van de opdracht. AzCopy gebruikt vervolgens uw Azure AD-account om toegang te verlenen tot gegevens in Blob Storage.
>
> Als u liever een SAS-token gebruikt om toegang te verlenen tot BLOB-gegevens, kunt u dat token toevoegen aan de bron-URL in elke AzCopy-opdracht.
>
> Bijvoorbeeld: `https://mystorageaccount.blob.core.windows.net/mycontainer?<SAS-token>`.

### <a name="authorize-with-aws-s3"></a>Autoriseren met AWS S3

Verzamel uw AWS-toegangs sleutel en geheime toegangs sleutel en stel de volgende omgevings variabelen in:

| Besturingssysteem | Opdracht  |
|--------|-----------|
| **Windows** | `set AWS_ACCESS_KEY_ID=<access-key>`<br>`set AWS_SECRET_ACCESS_KEY=<secret-access-key>` |
| **Linux** | `export AWS_ACCESS_KEY_ID=<access-key>`<br>`export AWS_SECRET_ACCESS_KEY=<secret-access-key>` |
| **MacOS** | `export AWS_ACCESS_KEY_ID=<access-key>`<br>`export AWS_SECRET_ACCESS_KEY=<secret-access-key>`|

## <a name="copy-objects-directories-and-buckets"></a>Objecten, directory's en buckets kopiëren

AzCopy maakt gebruik [van de API put van URL](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) , zodat gegevens rechtstreeks tussen AWS S3 en opslag servers worden gekopieerd. Deze Kopieer bewerkingen gebruiken de netwerk bandbreedte van uw computer niet.

> [!IMPORTANT]
> Deze functie is momenteel beschikbaar als preview-product. Als u besluit om gegevens van uw S3-buckets te verwijderen na een Kopieer bewerking, controleert u of de gegevens correct zijn gekopieerd naar uw opslag account voordat u de gegevens verwijdert.

### <a name="copy-an-object"></a>Een object kopiëren

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy "https://s3.amazonaws.com/<bucket-name>/<object-name>" "https://<storage-account-name>.blob.core.windows.net/<container-name>/<blob-name>"` |
| **Voorbeeld** | `azcopy copy "https://s3.amazonaws.com/mybucket/myobject" "https://mystorageaccount.blob.core.windows.net/mycontainer/myblob"` |

> [!NOTE]
> Voor beelden in dit artikel gebruiken Path-Url's voor AWS S3-buckets (bijvoorbeeld: `http://s3.amazonaws.com/<bucket-name>`). 
>
> U kunt ook virtuele Url's voor gehoste stijlen gebruiken (bijvoorbeeld: `http://bucket.s3.amazonaws.com`). 
>
> Zie [virtual host of buckets]] (https://docs.aws.amazon.com/AmazonS3/latest/dev/VirtualHosting.html) voor meer informatie over virtuele hosting van buckets.

### <a name="copy-a-directory"></a>Een map kopiëren

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy "https://s3.amazonaws.com/<bucket-name>/<directory-name>" "https://<storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>" --recursive=true` |
| **Voorbeeld** | `azcopy copy "https://s3.amazonaws.com/mybucket/mydirectory" "https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory" --recursive=true` |

### <a name="copy-a-bucket"></a>Een Bucket kopiëren

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy "https://s3.amazonaws.com/<bucket-name>" "https://<storage-account-name>.blob.core.windows.net/<container-name>" --recursive=true` |
| **Voorbeeld** | `azcopy copy "https://s3.amazonaws.com/mybucket" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive=true` |

### <a name="copy-all-buckets-in-all-regions"></a>Alle buckets in alle regio's kopiëren

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy "https://s3.amazonaws.com/" "https://<storage-account-name>.blob.core.windows.net" --recursive=true` |
| **Voorbeeld** | `azcopy copy "https://s3.amazonaws.com" "https://mystorageaccount.blob.core.windows.net" --recursive=true` |

### <a name="copy-all-buckets-in-a-specific-s3-region"></a>Alle buckets in een specifieke S3-regio kopiëren

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy "https://s3-<region-name>.amazonaws.com/" "https://<storage-account-name>.blob.core.windows.net" --recursive=true` |
| **Voorbeeld** | `azcopy copy "https://s3-rds.eu-north-1.amazonaws.com" "https://mystorageaccount.blob.core.windows.net" --recursive=true` |

## <a name="handle-differences-in-object-naming-rules"></a>Verschillen in regels voor object naamgeving afhandelen

AWS S3 heeft een andere set naam conventies voor Bucket-namen in vergelijking met Azure Blob-containers. Meer informatie hierover vindt u [hier](https://docs.aws.amazon.com/AmazonS3/latest/dev/BucketRestrictions.html#bucketnamingrules). Als u een groep buckets kopieert naar een Azure Storage-account, kan de Kopieer bewerking mislukken vanwege naam verschillen.

AzCopy verwerkt twee van de meest voorkomende problemen die zich kunnen voordoen. buckets die punten en buckets bevatten die opeenvolgende afbreek streepjes bevatten. AWS S3-Bucket namen kunnen punten en opeenvolgende afbreek streepjes bevatten, maar een container in azure kan niet. AzCopy vervangt Peri Oden door afbreek streepjes en opeenvolgende afbreek streepjes met een getal dat het aantal opeenvolgende afbreek streepjes vertegenwoordigt (bijvoorbeeld: een `my----bucket` Bucket met de naam wordt `my-4-bucket`gewijzigd. 

Als AzCopy kopieën over bestanden overneemt, wordt ook gecontroleerd op naam conflicten en wordt geprobeerd deze op te lossen. Als er bijvoorbeeld buckets zijn met de naam `bucket-name` en `bucket.name`, wordt met AzCopy een Bucket `bucket.name` omgezet met de naam First `bucket-name` naar en vervolgens `bucket-name-2`naar.

## <a name="handle-differences-in-object-metadata"></a>Verschillen in meta gegevens van object afhandelen

AWS S3 en Azure staan verschillende sets tekens toe aan de namen van object sleutels. U vindt [hier](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingMetadata.html#object-keys)meer informatie over de tekens die AWS S3 gebruikt. Aan de kant van de Azure-Blob-object sleutels voldoen aan de naamgevings regels voor [ C# id's](https://docs.microsoft.com/dotnet/csharp/language-reference/).

Als onderdeel van een AzCopy `copy` -opdracht kunt u een waarde opgeven voor de `s2s-invalid-metadata-handle` optionele vlag waarmee wordt aangegeven hoe u bestanden wilt afhandelen waarin de meta gegevens van het bestand incompatibele sleutel namen bevatten. De volgende tabel beschrijft de waarde van elke vlag.

| Vlag waarde | Description  |
|--------|-----------|
| **ExcludeIfInvalid** | (Standaard optie) De meta gegevens zijn niet opgenomen in het overgezette object. AzCopy registreert een waarschuwing. |
| **FailIfInvalid** | Objecten worden niet gekopieerd. AzCopy registreert een fout en bevat een fout in het aantal mislukte overzichten dat wordt weer gegeven in het overzicht van de overdracht.  |
| **RenameIfInvalid**  | AzCopy lost de ongeldige meta gegevens sleutel op en kopieert het object naar Azure met behulp van het omgezette meta gegevens sleutel waarde-paar. Zie de sectie [How a object Keys van AzCopy](#rename-logic) reAzCopys voor meer informatie over de stappen die nodig zijn om de naam van object sleutels te wijzigen. Als AzCopy de naam van de sleutel niet kan wijzigen, wordt het object niet gekopieerd. |

<a id="rename-logic" />

### <a name="how-azcopy-renames-object-keys"></a>Het wijzigen van de naam van object sleutels in AzCopy

AzCopy voert de volgende stappen uit:

1. Ongeldige tekens vervangen door _.

2. De teken reeks `rename_` wordt aan het begin van een nieuwe geldige sleutel toegevoegd.

   Deze sleutel wordt gebruikt om de oorspronkelijke meta gegevens **waarde**op te slaan.

3. De teken reeks `rename_key_` wordt aan het begin van een nieuwe geldige sleutel toegevoegd.
   Deze sleutel wordt gebruikt om de oorspronkelijke ongeldige **sleutel**van de meta gegevens op te slaan.
   U kunt deze sleutel gebruiken om de meta gegevens in azure Side te herstellen omdat de meta gegevens sleutel wordt behouden als een waarde in de Blob Storage-service.

## <a name="next-steps"></a>Volgende stappen

Meer voor beelden vindt u in een van deze artikelen:

- [Aan de slag met AzCopy](storage-use-azcopy-v10.md)

- [Gegevens overdragen met AzCopy en Blob Storage](storage-use-azcopy-blobs.md)

- [Gegevens overdragen met AzCopy en File Storage](storage-use-azcopy-files.md)

- [AzCopy configureren, optimaliseren en problemen oplossen](storage-use-azcopy-configure.md)