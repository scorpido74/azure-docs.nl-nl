---
title: Geef een versleutelings sleutel op voor een aanvraag voor Blob-opslag
titleSuffix: Azure Storage
description: Clients die aanvragen indienen voor Azure Blob-opslag hebben de mogelijkheid om een versleutelings sleutel op basis van per aanvraag (preview) op te geven. Met inbegrip van de versleutelings sleutel op de aanvraag biedt gedetailleerde controle over de versleutelings instellingen voor Blob Storage-bewerkingen.
services: storage
author: tamram
ms.service: storage
ms.date: 03/12/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: c8a5555c5c33255fdc5902a115e7e9103a4e936f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79410062"
---
# <a name="provide-an-encryption-key-on-a-request-to-blob-storage-preview"></a>Geef een versleutelings sleutel op voor een aanvraag voor Blob-opslag (preview-versie)

Clients die aanvragen indienen voor Azure Blob-opslag hebben de mogelijkheid om een versleutelings sleutel op basis van per aanvraag (preview) op te geven. Met inbegrip van de versleutelings sleutel op de aanvraag biedt gedetailleerde controle over de versleutelings instellingen voor Blob Storage-bewerkingen. Door de klant verschafte sleutels kunnen worden opgeslagen in Azure Key Vault of in een andere sleutel opslag.

## <a name="encrypting-read-and-write-operations"></a>Lees-en schrijf bewerkingen versleutelen

Wanneer een client toepassing een versleutelings sleutel voor de aanvraag levert, Azure Storage de versleuteling en ontsleuteling transparant uitvoeren tijdens het lezen en schrijven van BLOB-gegevens. Azure Storage schrijft een SHA-256-hash van de versleutelings sleutel naast de inhoud van de blob. De hash wordt gebruikt om te controleren of alle volgende bewerkingen voor de BLOB dezelfde versleutelings sleutel gebruiken.

Azure Storage de versleutelings sleutel niet opslaat of beheert die de client met de aanvraag verzendt. De sleutel wordt veilig verwijderd zodra het proces voor versleuteling of ontsleuteling is voltooid.

Wanneer een client een BLOB maakt of bijwerkt met behulp van een door de klant geleverde sleutel op de aanvraag, moeten volgende Lees-en schrijf aanvragen voor die BLOB ook de sleutel bevatten. Als de sleutel niet is opgenomen in een aanvraag voor een blob die al is versleuteld met een door de klant verschafte sleutel, mislukt de aanvraag met fout code 409 (conflict).

Als de client toepassing een versleutelings sleutel op de aanvraag verstuurt en het opslag account ook is versleuteld met een door micro soft beheerde sleutel of een door de klant beheerde sleutel, gebruikt Azure Storage de sleutel die op de aanvraag voor versleuteling en ontsleuteling wordt vermeld.

Als u de versleutelings sleutel wilt verzenden als onderdeel van de aanvraag, moet een client een beveiligde verbinding tot stand brengen met Azure Storage met behulp van HTTPS.

Elke BLOB-moment opname kan een eigen versleutelings sleutel hebben.

## <a name="request-headers-for-specifying-customer-provided-keys"></a>Aanvraag headers voor het opgeven van door de klant opgegeven sleutels

Voor REST-aanroepen kunnen clients de volgende headers gebruiken om informatie over de versleutelings sleutel op een aanvraag voor Blob-opslag veilig door te geven:

|Aanvraagheader | Beschrijving |
|---------------|-------------|
|`x-ms-encryption-key` |Vereist voor zowel schrijf-als Lees aanvragen. Een met base64 gecodeerde AES-256-versleutelings sleutel waarde. |
|`x-ms-encryption-key-sha256`| Vereist voor zowel schrijf-als Lees aanvragen. De met base64 gecodeerde SHA256 van de versleutelings sleutel. |
|`x-ms-encryption-algorithm` | Vereist voor schrijf aanvragen, optioneel voor lees aanvragen. Hiermee geeft u de algoritme op die moet worden gebruikt voor het versleutelen van gegevens met de opgegeven sleutel. Moet AES256 zijn. |

Het opgeven van de versleutelings sleutels voor de aanvraag is optioneel. Als u echter een van de hierboven vermelde kopteksten voor een schrijf bewerking opgeeft, moet u deze allemaal opgeven.

## <a name="blob-storage-operations-supporting-customer-provided-keys"></a>Blob Storage-bewerkingen die door de klant verschafte sleutels worden ondersteund

De volgende bewerkingen voor Blob Storage ondersteunen het verzenden van door de klant verschafte versleutelings sleutels voor een aanvraag:

- [BLOB plaatsen](/rest/api/storageservices/put-blob)
- [Blokkerings lijst plaatsen](/rest/api/storageservices/put-block-list)
- [Blok keren](/rest/api/storageservices/put-block)
- [Blok van URL plaatsen](/rest/api/storageservices/put-block-from-url)
- [Pagina plaatsen](/rest/api/storageservices/put-page)
- [Pagina van URL plaatsen](/rest/api/storageservices/put-page-from-url)
- [Blok toevoegen](/rest/api/storageservices/append-block)
- [BLOB-eigenschappen instellen](/rest/api/storageservices/set-blob-properties)
- [BLOB-meta gegevens instellen](/rest/api/storageservices/set-blob-metadata)
- [BLOB ophalen](/rest/api/storageservices/get-blob)
- [Get Blob Properties](/rest/api/storageservices/get-blob-properties) (Blob-eigenschappen ophalen)
- [BLOB-meta gegevens ophalen](/rest/api/storageservices/get-blob-metadata)
- [Moment opname-BLOB](/rest/api/storageservices/snapshot-blob)

## <a name="rotate-customer-provided-keys"></a>Door de klant verschafte sleutels draaien

Als u een versleutelings sleutel die is gebruikt voor het versleutelen van een BLOB wilt roteren, downloadt u de BLOB en uploadt u deze opnieuw met de nieuwe versleutelings sleutel.

> [!IMPORTANT]
> De Azure Portal kan niet worden gebruikt om te lezen van of te schrijven naar een container of BLOB die is versleuteld met een sleutel die in de aanvraag is gegeven.
>
> Zorg ervoor dat u de versleutelings sleutel beveiligt die u opgeeft voor een aanvraag voor Blob-opslag in een beveiligd sleutel archief zoals Azure Key Vault. Als u een schrijf bewerking probeert uit te voeren op een container of BLOB zonder de versleutelings sleutel, mislukt de bewerking en krijgt u geen toegang meer tot het object.

## <a name="next-steps"></a>Volgende stappen

- [Geef een door de klant opgegeven sleutel op een aanvraag naar Blob-opslag met .NET](../blobs/storage-blob-customer-provided-key.md)
- [Azure Storage versleuteling voor Data-at-rest](storage-service-encryption.md)
