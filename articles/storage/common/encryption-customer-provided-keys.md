---
title: Een versleutelingssleutel op een verzoek aan Blob-opslag verstrekken
titleSuffix: Azure Storage
description: Clients die aanvragen indienen tegen Azure Blob-opslag hebben de mogelijkheid om per aanvraag een versleutelingssleutel op te bieden (voorbeeld). Het opnemen van de versleutelingssleutel op de aanvraag biedt gedetailleerde controle over versleutelingsinstellingen voor Blob-opslagbewerkingen.
services: storage
author: tamram
ms.service: storage
ms.date: 03/12/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: c8a5555c5c33255fdc5902a115e7e9103a4e936f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79410062"
---
# <a name="provide-an-encryption-key-on-a-request-to-blob-storage-preview"></a>Een versleutelingssleutel op een verzoek aan Blob-opslag (voorbeeld)

Clients die aanvragen indienen tegen Azure Blob-opslag hebben de mogelijkheid om per aanvraag een versleutelingssleutel op te bieden (voorbeeld). Het opnemen van de versleutelingssleutel op de aanvraag biedt gedetailleerde controle over versleutelingsinstellingen voor Blob-opslagbewerkingen. Door de klant geleverde sleutels kunnen worden opgeslagen in Azure Key Vault of in een andere sleutelwinkel.

## <a name="encrypting-read-and-write-operations"></a>Lees- en schrijfbewerkingen versleutelen

Wanneer een clienttoepassing een versleutelingssleutel op de aanvraag biedt, voert Azure Storage versleuteling en decryptie transparant uit tijdens het lezen en schrijven van blobgegevens. Azure Storage schrijft een SHA-256 hash van de encryptiesleutel naast de inhoud van de blob. De hash wordt gebruikt om te controleren of alle volgende bewerkingen tegen de blob dezelfde versleutelingssleutel gebruiken.

Azure Storage slaat de versleutelingssleutel die de client met de aanvraag verzendt niet op of beheert deze niet. De sleutel wordt veilig verwijderd zodra het versleutelings- of decryptieproces is voltooid.

Wanneer een client een blob maakt of bijwerkt met behulp van een door de klant geleverde sleutel op de aanvraag, moeten vervolgens volgende lees- en schrijfverzoeken voor die blob ook de sleutel bieden. Als de sleutel niet wordt geleverd op een verzoek voor een blob die al is versleuteld met een door de klant geleverde sleutel, mislukt het verzoek met foutcode 409 (Conflict).

Als de clienttoepassing een versleutelingssleutel op de aanvraag verzendt en het opslagaccount ook is versleuteld met een door Microsoft beheerde sleutel of een door de klant beheerde sleutel, gebruikt Azure Storage de sleutel die is opgegeven in het verzoek voor versleuteling en decryptie.

Als u de versleutelingssleutel als onderdeel van de aanvraag wilt verzenden, moet een client een beveiligde verbinding met Azure Storage tot stand brengen met HTTPS.

Elke blob-momentopname kan zijn eigen versleutelingssleutel hebben.

## <a name="request-headers-for-specifying-customer-provided-keys"></a>Kopteksten aanvragen voor het opgeven van door de klant verstrekte sleutels

Voor REST-gesprekken kunnen clients de volgende kopteksten gebruiken om versleutelingssleutelgegevens veilig door te geven op een verzoek aan Blob-opslag:

|Aanvraagheader | Beschrijving |
|---------------|-------------|
|`x-ms-encryption-key` |Vereist voor zowel schrijf- als leesverzoeken. Een Door Base64 gecodeerde AES-256-versleutelingssleutelwaarde. |
|`x-ms-encryption-key-sha256`| Vereist voor zowel schrijf- als leesverzoeken. De Base64-gecodeerde SHA256 van de encryptiesleutel. |
|`x-ms-encryption-algorithm` | Vereist voor schrijfverzoeken, optioneel voor leesverzoeken. Hiermee geeft u het algoritme op dat moet worden gebruikt bij het versleutelen van gegevens met behulp van de opgegeven sleutel. Moet AES256 zijn. |

Het opgeven van versleutelingssleutels op de aanvraag is optioneel. Als u echter een van de hierboven vermelde kopteksten opgeeft voor een schrijfbewerking, moet u ze allemaal opgeven.

## <a name="blob-storage-operations-supporting-customer-provided-keys"></a>Blob-opslagbewerkingen die door de klant geleverde sleutels ondersteunen

De volgende Blob-opslagbewerkingen ondersteunen het verzenden van door de klant verstrekte versleutelingssleutels op een verzoek:

- [Blob plaatsen](/rest/api/storageservices/put-blob)
- [Lijst met blokkeringplaatsen plaatsen](/rest/api/storageservices/put-block-list)
- [Zet blok](/rest/api/storageservices/put-block)
- [Blok van URL plaatsen](/rest/api/storageservices/put-block-from-url)
- [Pagina plaatsen](/rest/api/storageservices/put-page)
- [Pagina van URL plaatsen](/rest/api/storageservices/put-page-from-url)
- [Blok toevoegen](/rest/api/storageservices/append-block)
- [Blob-eigenschappen instellen](/rest/api/storageservices/set-blob-properties)
- [Blob-metagegevens instellen](/rest/api/storageservices/set-blob-metadata)
- [Get Blob](/rest/api/storageservices/get-blob)
- [Get Blob Properties](/rest/api/storageservices/get-blob-properties) (Blob-eigenschappen ophalen)
- [Blob-metagegevens oppakken](/rest/api/storageservices/get-blob-metadata)
- [Momentopnameblob](/rest/api/storageservices/snapshot-blob)

## <a name="rotate-customer-provided-keys"></a>Door de klant geleverde sleutels roteren

Als u een versleutelingssleutel wilt roteren die is gebruikt om een blob te versleutelen, downloadt u de blob en uploadt u deze vervolgens opnieuw met de nieuwe versleutelingssleutel.

> [!IMPORTANT]
> De Azure-portal kan niet worden gebruikt om te lezen van of te schrijven naar een container of blob die is versleuteld met een sleutel die op het verzoek is opgegeven.
>
> Zorg ervoor dat u de versleutelingssleutel beschermt die u op een verzoek aan Blob-opslag opgeeft in een beveiligde sleutelopslag zoals Azure Key Vault. Als u een schrijfbewerking op een container of blob probeert zonder de versleutelingssleutel, mislukt de bewerking en verliest u de toegang tot het object.

## <a name="next-steps"></a>Volgende stappen

- [Een door de klant verstrekte sleutel opgeven op een verzoek om Blob-opslag met .NET](../blobs/storage-blob-customer-provided-key.md)
- [Azure Storage-versleuteling voor gegevens in rust](storage-service-encryption.md)
