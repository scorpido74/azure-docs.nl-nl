---
title: Azure Storage voor beelden met behulp van python | Microsoft Docs
description: Voorbeeld code en toepassingen voor Azure Storage weer geven, downloaden en uitvoeren. Ontdek aan de slag met voor beelden voor blobs, wacht rijen, tabellen en bestanden, met behulp van de python Storage-client bibliotheken.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 02/13/2020
ms.service: storage
ms.subservice: common
ms.topic: sample
ms.openlocfilehash: fc3079d316a252bc5a658017cb6b04fe6ef20c2f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "77485825"
---
# <a name="azure-storage-samples-using-v12-python-client-libraries"></a>Azure Storage voor beelden met behulp van V12 python-client bibliotheken

In de volgende tabellen vindt u een overzicht van onze voor beelden van opslag plaatsen en de scenario's die in elk voor beeld worden behandeld. Klik op de koppelingen om de bijbehorende voorbeeld code in GitHub weer te geven.

> [!NOTE]
> In deze voor beelden wordt de nieuwste Azure Storage .NET V12-bibliotheek gebruikt. Zie voor de verouderde v 2.1-code [Azure Storage: aan de slag met Azure Storage in python](https://github.com/Azure-Samples/storage-blob-python-getting-started) in de GitHub-opslag plaats.

## <a name="blob-samples"></a>BLOB-voor beelden

### <a name="authentication"></a>Verificatie

:::row:::
   :::column span="":::
      [Een BLOB-serviceclient maken met behulp van een connection string](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_authentication.py#L45)
   :::column-end:::
   :::column span="":::
      [Een container-client maken met behulp van een connection string](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_authentication.py#L50)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Een BLOB-client maken met behulp van een connection string](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_authentication.py#L56)
   :::column-end:::
   :::column span="":::
      [Een BLOB-serviceclient maken met behulp van een gedeelde toegangs sleutel](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_authentication.py#L66)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [BLOB-client maken op basis van URL](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_authentication.py#L75)
   :::column-end:::
   :::column span="":::
      [SAS-URL van BLOB-client maken](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_authentication.py#L80)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Een BLOB-serviceclient maken met behulp van ClientSecretCredential](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_authentication.py#L88)
   :::column-end:::
   :::column span="":::
      [SAS-token maken](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_authentication.py#L110)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Een BLOB-serviceclient maken met behulp van Azure-identiteit](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_authentication.py#L125)
   :::column-end:::
   :::column span="":::
      [BLOB-moment opname maken](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_common.py#L56)
   :::column-end:::
:::row-end:::

### <a name="blob-service"></a>Blob-service

:::row:::
   :::column span="":::
      [Gegevens van BLOB service-account ophalen](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_service.py#L30)
   :::column-end:::
   :::column span="":::
      [Eigenschappen van BLOB-service instellen](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_service.py#L41)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Eigenschappen van BLOB-service ophalen](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_service.py#L61)
   :::column-end:::
   :::column span="":::
      [Statistieken van BLOB-service ophalen](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_service.py#L71)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Container maken met Service-client](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_service.py#L82)
   :::column-end:::
   :::column span="":::
      [Containers weergeven](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_service.py#L90)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Container verwijderen met Service-client](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_service.py#L103)
   :::column-end:::
   :::column span="":::
      [Container-client ophalen](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_service.py#L117)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [BLOB-client ophalen](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_service.py#L130)
   :::column-end:::
:::row-end:::

### <a name="container"></a>Container

:::row:::
   :::column span="":::
      [Container-client maken vanuit service](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_containers.py#L35)
   :::column-end:::
   :::column span="":::
      [Container-client maken met SAS-URL](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_containers.py#L44)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Container maken met container client](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_containers.py#L52)
   :::column-end:::
   :::column span="":::
      [Container eigenschappen ophalen](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_containers.py#L56)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Container verwijderen met container client](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_containers.py#L61)
   :::column-end:::
   :::column span="":::
      [Lease verkrijgen bij container](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_containers.py#L80)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Meta gegevens van container instellen](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_containers.py#L101)
   :::column-end:::
   :::column span="":::
      [Toegangs beleid voor container instellen](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_containers.py#L128)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Toegangs beleid voor container ophalen](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_containers.py#L141)
   :::column-end:::
   :::column span="":::
      [SAS-token genereren](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_containers.py#L145)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Container-client maken met SAS-token](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_containers.py#L158)
   :::column-end:::
   :::column span="":::
      [BLOB uploaden naar container](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_containers.py#L182)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Blobs in container weer geven](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_containers.py#L189)
   :::column-end:::
   :::column span="":::
      [BLOB-client ophalen](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_containers.py#L213)
   :::column-end:::
:::row-end:::

### <a name="blob"></a>Blob

:::row:::
   :::column span="":::
      [Een blob uploaden](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_hello_world.py#L68)
   :::column-end:::
   :::column span="":::
      [Een blob downloaden](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_hello_world.py#L74)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [BLOB verwijderen](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_hello_world.py#L80)
   :::column-end:::
   :::column span="":::
      [BLOB verwijderen](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_common.py#L100)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [BLOB-eigenschappen ophalen](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_common.py#L105)
   :::column-end:::
   :::column span="":::
      [Meerdere blobs verwijderen](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_common.py#L133)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [BLOB kopiëren van URL](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_common.py#L193)
   :::column-end:::
   :::column span="":::
      [Kopiëren van BLOB van URL afbreken](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_common.py#L205)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Lease verkrijgen op BLOB](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_common.py#L167)
   :::column-end:::
:::row-end:::

## <a name="data-lake-storage-gen2-samples"></a>Data Lake Storage Gen2-voor beelden

### <a name="data-lake-service"></a>Data Lake-service

:::row:::
   :::column span="2":::
      [Data Lake-serviceclient maken](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control.py#L64)
   :::column-end:::
:::row-end:::

### <a name="file-system"></a>Bestandssysteem

:::row:::
   :::column span="":::
      [Een File System-client maken](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control.py#L74)
   :::column-end:::
   :::column span="":::
      [Bestands systeem verwijderen](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control.py#L81)
   :::column-end:::
:::row-end:::

### <a name="directory"></a>Directory

:::row:::
   :::column span="":::
      [Directory-client maken](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control.py#L14)
   :::column-end:::
   :::column span="":::
      [Mapmachtigingen ophalen](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control.py#L19)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Mapmachtigingen instellen](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control.py#L23)
   :::column-end:::
   :::column span="":::
      [Naam van map wijzigen](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_directory.py#L19)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Mapeigenschappen ophalen](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_directory.py#L25)
   :::column-end:::
   :::column span="":::
      [Map verwijderen](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_directory.py#L29)
   :::column-end:::
:::row-end:::

### <a name="file"></a>File

:::row:::
   :::column span="":::
      [Bestands-client maken](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_upload_download.py#L13)
   :::column-end:::
   :::column span="":::
      [Bestand maken](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control.py#L45)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Bestands machtigingen ophalen](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control.py#L35)
   :::column-end:::
   :::column span="":::
      [Bestands machtigingen instellen](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control.py#L38)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Gegevens toevoegen aan bestand](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_upload_download.py#L18)
   :::column-end:::
   :::column span="":::
      [Gegevens uit bestand lezen](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_upload_download.py#L29)
   :::column-end:::
:::row-end:::

## <a name="azure-files-samples"></a>Azure Files-voor beelden

### <a name="authentication"></a>Verificatie

:::row:::
   :::column span="":::
      [Share service-client maken op basis van connection string](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-share/samples/file_samples_authentication.py#L42)
   :::column-end:::
   :::column span="":::
      [Share service-client maken op basis van account en toegangs sleutel](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-share/samples/file_samples_authentication.py#L49)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [SAS-token genereren](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-share/samples/file_samples_authentication.py#L59)
   :::column-end:::
:::row-end:::

### <a name="file-service"></a>Bestands service

:::row:::
   :::column span="":::
      [Service-eigenschappen instellen](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-share/samples/file_samples_service.py#L39)
   :::column-end:::
   :::column span="":::
      [Service-eigenschappen ophalen](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-share/samples/file_samples_service.py#L68)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Shares maken met de File Service-client](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-share/samples/file_samples_service.py#L77)
   :::column-end:::
   :::column span="":::
      [Shares weer geven met behulp van de File Service-client](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-share/samples/file_samples_service.py#L81)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Shares verwijderen met de File Service-client](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-share/samples/file_samples_service.py#L91)
   :::column-end:::
:::row-end:::

### <a name="file-share"></a>Bestandsshare

:::row:::
   :::column span="":::
      [Share-client maken op basis van connection string](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-share/samples/file_samples_share.py#L52)
   :::column-end:::
   :::column span="":::
      [Share-client ophalen](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-share/samples/file_samples_service.py#L96)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Share maken met behulp van de bestands share-client](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-share/samples/file_samples_share.py#L39)
   :::column-end:::
   :::column span="":::
      [Moment opname van de share maken](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-share/samples/file_samples_share.py#L43)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Share verwijderen met file share-client](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-share/samples/file_samples_share.py#L47)
   :::column-end:::
   :::column span="":::
      [Share quotum instellen](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-share/samples/file_samples_share.py#L61)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Meta gegevens van share instellen](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-share/samples/file_samples_share.py#L66)
   :::column-end:::
   :::column span="":::
      [Eigenschappen van share ophalen](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-share/samples/file_samples_hello_world.py#L47)
   :::column-end:::
:::row-end:::

### <a name="directory"></a>Directory

:::row:::
   :::column span="":::
      [Map maken](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-share/samples/file_samples_directory.py#L45)
   :::column-end:::
   :::column span="":::
      [Bestand uploaden naar map](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-share/samples/file_samples_directory.py#L49)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Bestand uit map verwijderen](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-share/samples/file_samples_directory.py#L55)
   :::column-end:::
   :::column span="":::
      [Map verwijderen](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-share/samples/file_samples_directory.py#L60)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Submap maken](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-share/samples/file_samples_directory.py#L80)
   :::column-end:::
   :::column span="":::
      [Mappen en bestanden weer geven](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-share/samples/file_samples_directory.py#L96)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Submap verwijderen](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-share/samples/file_samples_directory.py#L104)
   :::column-end:::
   :::column span="":::
      [Subdirectory-client ophalen](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-share/samples/file_samples_directory.py#L121)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Bestanden in de map weer geven](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-share/samples/file_samples_share.py#L87)
   :::column-end:::
:::row-end:::

### <a name="file"></a>File

:::row:::
   :::column span="":::
      [Bestands-client maken](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-share/samples/file_samples_hello_world.py#L65)
   :::column-end:::
   :::column span="":::
      [Bestand maken](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-share/samples/file_samples_client.py#L48)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Bestand uploaden](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-share/samples/file_samples_client.py#L54)
   :::column-end:::
   :::column span="":::
      [Bestand downloaden](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-share/samples/file_samples_client.py#L60)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Bestand verwijderen](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-share/samples/file_samples_client.py#L67)
   :::column-end:::
   :::column span="":::
      [Bestand kopiëren van URL](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-share/samples/file_samples_client.py#L101)
   :::column-end:::
:::row-end:::

## <a name="queue-samples"></a>Wachtrij voorbeelden

### <a name="authentication"></a>Verificatie

:::row:::
   :::column span="":::
      [Verifiëren met behulp van connection string](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-queue/samples/queue_samples_authentication.py#L50)
   :::column-end:::
   :::column span="":::
      [Client token voor wachtrij service maken](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-queue/samples/queue_samples_authentication.py#L69)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Een wachtrij client maken op basis van connection string](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-queue/samples/queue_samples_message.py#L35)
   :::column-end:::
   :::column span="":::
      [SAS-token van de wachtrij-client genereren](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-queue/samples/queue_samples_message.py#L61)
   :::column-end:::
:::row-end:::

### <a name="queue-service"></a>Queue-service

:::row:::
   :::column span="":::
      [Wachtrij service-client maken](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-queue/samples/queue_samples_authentication.py#L60)
   :::column-end:::
   :::column span="":::
      [Eigenschappen van wachtrij service instellen](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-queue/samples/queue_samples_service.py#L35)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Eigenschappen van wachtrij service ophalen](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-queue/samples/queue_samples_service.py#L67)
   :::column-end:::
   :::column span="":::
      [Wachtrij maken met Service-client](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-queue/samples/queue_samples_service.py#L76)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Wachtrij verwijderen met Service-client](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-queue/samples/queue_samples_service.py#L94)
   :::column-end:::
:::row-end:::

### <a name="queue"></a>Wachtrij

:::row:::
   :::column span="":::
      [Wachtrij-client maken](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-queue/samples/queue_samples_message.py#L72)
   :::column-end:::
   :::column span="":::
      [Meta gegevens van de wachtrij instellen](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-queue/samples/queue_samples_message.py#L95)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Wachtrij-eigenschappen ophalen](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-queue/samples/queue_samples_message.py#L100)
   :::column-end:::
   :::column span="":::
      [Wachtrij maken met Queue-client](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-queue/samples/queue_samples_hello_world.py#L45)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Wachtrij verwijderen met Queue-client](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-queue/samples/queue_samples_hello_world.py#L62)
   :::column-end:::
   :::column span="":::
      [Wacht rijen weer geven](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-queue/samples/queue_samples_service.py#L81)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Wachtrij-client ophalen](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-queue/samples/queue_samples_service.py#L103)
   :::column-end:::
:::row-end:::

### <a name="message"></a>Bericht

:::row:::
   :::column span="":::
      [Berichten verzenden](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-queue/samples/queue_samples_message.py#L117)
   :::column-end:::
   :::column span="":::
      [Berichten ontvangen](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-queue/samples/queue_samples_message.py#L125)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Bericht bekijken](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-queue/samples/queue_samples_message.py#L197)
   :::column-end:::
   :::column span="":::
      [Updatebericht](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-queue/samples/queue_samples_message.py#L222)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Bericht verwijderen](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-queue/samples/queue_samples_message.py#L165)
   :::column-end:::
   :::column span="":::
      [Berichten wissen](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-queue/samples/queue_samples_message.py#L173)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Toegangs beleid voor berichten instellen](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-queue/samples/queue_samples_message.py#L47)
   :::column-end:::
:::row-end:::


## <a name="table-samples-sdk-v21"></a>Tabel voorbeelden (SDK v 2.1)

:::row:::
   :::column span="":::
      [Tabel maken](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_basic_samples.py#L46)
   :::column-end:::
   :::column span="":::
      [Entiteit/tabel verwijderen](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_basic_samples.py#L79)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Entiteit invoegen/samen voegen/vervangen](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_basic_samples.py#L57)
   :::column-end:::
   :::column span="":::
      [Entiteiten opvragen](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_basic_samples.py#L62)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Querytabellen](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_basic_samples.py)
   :::column-end:::
   :::column span="":::
      [Tabel-ACL/eigenschappen](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_advanced_samples.py#L138)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Entiteit bijwerken](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_basic_samples.py#L68)
   :::column-end:::
:::row-end:::

## <a name="azure-code-sample-libraries"></a>Voorbeeld bibliotheken van Azure-code

Als u de complete python-voorbeeld bibliotheken wilt bekijken, gaat u naar:

* [Voor beelden van Azure Blob-code](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-blob/samples)
* [Voor beelden van Azure Data Lake code](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples)
* [Voor beelden van Azure Files code](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-share/samples)
* [Voor beelden van Azure-wachtrij code](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-queue/samples)

U kunt de GitHub-opslag plaats voor elke tape wisselaar door bladeren en klonen.

## <a name="getting-started-guides"></a>Aan de slag-hand leidingen

Bekijk de volgende hand leidingen als u op zoek bent naar instructies voor het installeren en aan de slag met de Azure Storage-client bibliotheken.

* [Aan de slag met Azure Blob service in python](../blobs/storage-quickstart-blobs-python.md)
* [Aan de slag met Azure Queue service in python](../queues/storage-quickstart-queues-python.md)
* [Aan de slag met Azure Table service in python](../../cosmos-db/table-storage-how-to-use-python.md)
* [Aan de slag met Azure File Service in python](../files/storage-python-how-to-use-file-storage.md)

## <a name="next-steps"></a>Volgende stappen

Voor informatie over voor beelden voor andere talen:

* .NET: voor [beelden Azure Storage met .net](storage-samples-dotnet.md)
* Java: [Azure Storage voor beelden met behulp van Java](storage-samples-java.md)
* Java script/node. js: [Azure Storage voor beelden met behulp van Java script](storage-samples-javascript.md)
* Alle andere talen: [Azure Storage](storage-samples.md) -voor beelden
