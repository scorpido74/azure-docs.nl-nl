---
title: Gegevens van Amazon S3 naar Azure Storage kopiëren met AzCopy | Microsoft Documenten
description: Gegevens overbrengen met AzCopy- en Amazon S3-buckets
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: a3180593eaf8c01c772fd761d88b5f5b9f7657ee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75941510"
---
# <a name="copy-data-from-amazon-s3-to-azure-storage-by-using-azcopy"></a>Gegevens van Amazon S3 naar Azure Storage kopiëren met AzCopy

AzCopy is een command-line hulpprogramma dat u gebruiken om blobs of bestanden te kopiëren van of naar een opslagaccount. Met dit artikel u objecten, mappen en buckets kopiëren van Amazon Web Services (AWS) S3 naar Azure blob-opslag met AzCopy.

## <a name="choose-how-youll-provide-authorization-credentials"></a>Kiezen hoe u autorisatiereferenties opgeeft

* Als u wilt autoriseren met de Azure Storage, gebruikt u Azure Active Directory (AD) of een SAS-token (Shared Access Signature) om te autoriseren met de Azure Storage.

* Als u wilt autoriseren met AWS S3, gebruikt u een AWS-toegangssleutel en een geheime toegangssleutel.

### <a name="authorize-with-azure-storage"></a>Autoriseren met Azure Storage

Bekijk het artikel [Aan de slag met AzCopy](storage-use-azcopy-v10.md) om AzCopy te downloaden en kies hoe u autorisatiereferenties aan de opslagservice verstrekt.

> [!NOTE]
> De voorbeelden in dit artikel gaan ervan uit dat `AzCopy login` u uw identiteit hebt geverifieerd met behulp van de opdracht. AzCopy gebruikt vervolgens uw Azure AD-account om toegang tot gegevens in Blob-opslag te autoriseren.
>
> Als u liever een SAS-token gebruikt om toegang tot blobgegevens te autoriseren, u dat token toevoegen aan de bron-URL in elke AzCopy-opdracht.
>
> Bijvoorbeeld: `https://mystorageaccount.blob.core.windows.net/mycontainer?<SAS-token>`.

### <a name="authorize-with-aws-s3"></a>Autoriseren met AWS S3

Verzamel uw AWS-toegangssleutel en geheime toegangssleutel en stel de deze omgevingsvariabelen in:

| Besturingssysteem | Opdracht  |
|--------|-----------|
| **Windows** | `set AWS_ACCESS_KEY_ID=<access-key>`<br>`set AWS_SECRET_ACCESS_KEY=<secret-access-key>` |
| **Linux** | `export AWS_ACCESS_KEY_ID=<access-key>`<br>`export AWS_SECRET_ACCESS_KEY=<secret-access-key>` |
| **Macos** | `export AWS_ACCESS_KEY_ID=<access-key>`<br>`export AWS_SECRET_ACCESS_KEY=<secret-access-key>`|

## <a name="copy-objects-directories-and-buckets"></a>Objecten, mappen en buckets kopiëren

AzCopy maakt gebruik van de [Put Block From URL](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) API, zodat gegevens rechtstreeks worden gekopieerd tussen AWS S3 en opslagservers. Deze kopieerbewerkingen maken geen gebruik van de netwerkbandbreedte van uw computer.

> [!IMPORTANT]
> Deze functie is momenteel beschikbaar als preview-product. Als u na een kopiebewerking besluit gegevens uit uw S3-buckets te verwijderen, controleert u of de gegevens correct naar uw opslagaccount zijn gekopieerd voordat u de gegevens verwijdert.

> [!TIP]
> De voorbeelden in deze sectie omsluiten padargumenten met enkele aanhalingstekens (''). Gebruik enkele aanhalingstekens in alle opdrachtshells, behalve de Windows Command Shell (cmd.exe). Als u een Windows Command Shell (cmd.exe) gebruikt, sluit u padargumenten om met dubbele aanhalingstekens ("") in plaats van enkele aanhalingstekens ('').

 Deze voorbeelden werken ook met accounts met een hiërarchische naamruimte. [Met toegang met meerdere protocollen op Data Lake Storage](../blobs/data-lake-storage-multi-protocol-access.md) u dezelfde URL-syntaxis ()`blob.core.windows.net`gebruiken voor die accounts. 

### <a name="copy-an-object"></a>Een object kopiëren

Gebruik dezelfde URL-syntaxis (`blob.core.windows.net`) voor accounts met een hiërarchische naamruimte.

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy 'https://s3.amazonaws.com/<bucket-name>/<object-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<blob-name>'` |
| **Voorbeeld** | `azcopy copy 'https://s3.amazonaws.com/mybucket/myobject' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myblob'` |
| **Voorbeeld** (hiërarchische naamruimte) | `azcopy copy 'https://s3.amazonaws.com/mybucket/myobject' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myblob'` |

> [!NOTE]
> Voorbeelden in dit artikel gebruiken URL's in padstijl voor `http://s3.amazonaws.com/<bucket-name>`AWS S3-buckets (bijvoorbeeld: ). 
>
> U ook virtuele URL's in gehoste `http://bucket.s3.amazonaws.com`stijl gebruiken (bijvoorbeeld: ). 
>
> Zie [Virtual Hosting of Buckets]voor meer informatie overhttps://docs.aws.amazon.com/AmazonS3/latest/dev/VirtualHosting.html)virtuele hosting van buckets.

### <a name="copy-a-directory"></a>Een map kopiëren

Gebruik dezelfde URL-syntaxis (`blob.core.windows.net`) voor accounts met een hiërarchische naamruimte.

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy 'https://s3.amazonaws.com/<bucket-name>/<directory-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive=true` |
| **Voorbeeld** | `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |
| **Voorbeeld** (hiërarchische naamruimte)| `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

### <a name="copy-a-bucket"></a>Een emmer kopiëren

Gebruik dezelfde URL-syntaxis (`blob.core.windows.net`) voor accounts met een hiërarchische naamruimte.

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy 'https://s3.amazonaws.com/<bucket-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>' --recursive=true` |
| **Voorbeeld** | `azcopy copy 'https://s3.amazonaws.com/mybucket' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive=true` |
| **Voorbeeld** (hiërarchische naamruimte)| `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

### <a name="copy-all-buckets-in-all-regions"></a>Alle buckets in alle regio's kopiëren

Gebruik dezelfde URL-syntaxis (`blob.core.windows.net`) voor accounts met een hiërarchische naamruimte.

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy 'https://s3.amazonaws.com/' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true` |
| **Voorbeeld** | `azcopy copy 'https://s3.amazonaws.com' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |
| **Voorbeeld** (hiërarchische naamruimte)| `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

### <a name="copy-all-buckets-in-a-specific-s3-region"></a>Alle buckets in een specifieke S3-regio kopiëren

Gebruik dezelfde URL-syntaxis (`blob.core.windows.net`) voor accounts met een hiërarchische naamruimte.

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy 'https://s3-<region-name>.amazonaws.com/' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true` |
| **Voorbeeld** | `azcopy copy 'https://s3-rds.eu-north-1.amazonaws.com' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |
| **Voorbeeld** (hiërarchische naamruimte)| `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

## <a name="handle-differences-in-object-naming-rules"></a>Verschillen in regels voor objectnaamgeving verwerken

AWS S3 heeft een andere set naamgevingsconventies voor bucketnamen in vergelijking met Azure blob-containers. U lezen over hen [hier](https://docs.aws.amazon.com/AmazonS3/latest/dev/BucketRestrictions.html#bucketnamingrules). Als u ervoor kiest een groep buckets naar een Azure-opslagaccount te kopiëren, kan de kopieerbewerking mislukken vanwege naamgevingsverschillen.

AzCopy behandelt twee van de meest voorkomende problemen die zich kunnen voordoen; buckets die perioden en buckets bevatten die opeenvolgende koppeltekens bevatten. AWS S3-bucketnamen kunnen perioden en opeenvolgende koppeltekens bevatten, maar een container in Azure niet. AzCopy vervangt perioden door koppeltekens en opeenvolgende koppeltekens door een getal dat het aantal `my----bucket` `my-4-bucket`opeenvolgende koppeltekens vertegenwoordigt (Bijvoorbeeld: een emmer met de naam wordt . 

Als AzCopy kopieën over bestanden maakt, wordt ook gecontroleerd op het benoemen van botsingen en pogingen om deze op te lossen. Als er bijvoorbeeld buckets met `bucket-name` de `bucket.name`naam zijn en AzCopy `bucket-name` een bucket `bucket-name-2`oplost die eerst naar en vervolgens wordt genoemd `bucket.name` naar .

## <a name="handle-differences-in-object-metadata"></a>Verschillen in objectmetagegevens verwerken

AWS S3 en Azure staan verschillende sets tekens toe in de namen van objectsleutels. U lezen over de tekens die AWS S3 gebruikt [hier](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingMetadata.html#object-keys). Aan de Azure-zijde houden blobobjectsleutels zich aan de [naamgevingsregels voor C#-id's](https://docs.microsoft.com/dotnet/csharp/language-reference/).

Als onderdeel van `copy` een AzCopy-opdracht u `s2s-invalid-metadata-handle` een waarde voor optioneel de vlag geven die aangeeft hoe u bestanden wilt verwerken waarin de metagegevens van het bestand onverenigbare sleutelnamen bevatten. In de volgende tabel wordt elke vlagwaarde beschreven.

| Vlagwaarde | Beschrijving  |
|--------|-----------|
| **UitgeslotenAlsOngeldig** | (Standaardoptie) De metagegevens zijn niet opgenomen in het overgedragen object. AzCopy registreert een waarschuwing. |
| **FailIfOngeldig** | Objecten worden niet gekopieerd. AzCopy registreert een fout en bevat die fout in het aantal mislukte gegevens dat wordt weergegeven in het overdrachtsoverzicht.  |
| **Naam wijzigen Als ongeldig**  | AzCopy lost de ongeldige metagegevenssleutel op en kopieert het object naar Azure met behulp van het waardesleutelsleutelsleutelpaar opgelost metagegevens. Zie de sectie Hoe AzCopy de [naam van objectsleutels wijzigt,](#rename-logic) om precies te weten welke stappen AzCopy neemt om precies de naam van objectsleutels te wijzigen. Als AzCopy de naam van de sleutel niet kan wijzigen, wordt het object niet gekopieerd. |

<a id="rename-logic" />

### <a name="how-azcopy-renames-object-keys"></a>Hoe AzCopy de namen van objectsleutels hernoemt

AzCopy voert de volgende stappen uit:

1. Hiermee worden ongeldige tekens vervangen door '_'.

2. Hiermee voegt `rename_` u de tekenreeks toe aan het begin van een nieuwe geldige sleutel.

   Deze sleutel wordt gebruikt om **value**de oorspronkelijke metagegevenswaarde op te slaan.

3. Hiermee voegt `rename_key_` u de tekenreeks toe aan het begin van een nieuwe geldige sleutel.
   Deze sleutel wordt gebruikt om de ongeldige **sleutel**van originele metagegevens op te slaan.
   U deze sleutel gebruiken om te proberen de metagegevens in Azure-zijde te herstellen, omdat de metagegevenssleutel wordt behouden als een waarde op de Blob-opslagservice.

## <a name="next-steps"></a>Volgende stappen

Meer voorbeelden vindt u in een van deze artikelen:

- [Aan de slag met AzCopy](storage-use-azcopy-v10.md)

- [Gegevens overbrengen met AzCopy- en blob-opslag](storage-use-azcopy-blobs.md)

- [Gegevens overbrengen met AzCopy en bestandsopslag](storage-use-azcopy-files.md)

- [AzCopy configureren, optimaliseren en oplossen](storage-use-azcopy-configure.md)