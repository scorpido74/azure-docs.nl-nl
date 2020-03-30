---
title: Azure Storage-versleuteling voor gegevens in rust
description: Azure Storage beschermt uw gegevens door deze automatisch te versleutelen voordat deze naar de cloud worden gebracht. U vertrouwen op door Microsoft beheerde sleutels voor de versleuteling van de gegevens in uw opslagaccount, of u versleuteling beheren met uw eigen sleutels.
services: storage
author: tamram
ms.service: storage
ms.date: 03/12/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: f8f6f40f8ce8297b3cbfe6b3afcbf10df4db6572
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409827"
---
# <a name="azure-storage-encryption-for-data-at-rest"></a>Azure Storage-versleuteling voor gegevens in rust

Azure Storage versleutelt uw gegevens automatisch wanneer deze worden gehandhaafd in de cloud. Azure Storage-versleuteling beschermt uw gegevens en helpt u om te voldoen aan uw verplichtingen op het gebied van organisatiebeveiliging en naleving.

## <a name="about-azure-storage-encryption"></a>Over Azure Storage-versleuteling

Gegevens in Azure Storage worden versleuteld en transparant gedecodeerd met behulp van 256-bits [AES-versleuteling](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), een van de sterkste blokcijfers die beschikbaar zijn en is FIPS 140-2-compatibel. Azure Storage-versleuteling is vergelijkbaar met BitLocker-versleuteling op Windows.

Azure Storage-versleuteling is ingeschakeld voor alle opslagaccounts, inclusief zowel Resource Manager- als klassieke opslagaccounts. Azure Storage-versleuteling kan niet worden uitgeschakeld. Omdat uw gegevens standaard zijn beveiligd, hoeft u uw code of toepassingen niet te wijzigen om gebruik te maken van Azure Storage-versleuteling.

Gegevens in een opslagaccount worden versleuteld, ongeacht de prestatielaag (standaard of premium), toegangslaag (hot of cool) of implementatiemodel (Azure Resource Manager of klassiek). Alle blobs in de archieflaag zijn ook versleuteld. Alle redundantieopties voor Azure Storage ondersteunen versleuteling en alle gegevens in zowel de primaire als secundaire regio's worden versleuteld wanneer georeplicatie is ingeschakeld. Alle Azure Storage-bronnen zijn versleuteld, inclusief blobs, schijven, bestanden, wachtrijen en tabellen. Alle objectmetadata is ook versleuteld. Er zijn geen extra kosten verbonden aan Azure Storage-versleuteling.

Elke blokblob, toevoegenblob of paginablob die na 20 oktober 2017 naar Azure Storage is geschreven, is versleuteld. Blobs die vóór deze datum zijn gemaakt, worden nog steeds versleuteld door een achtergrondproces. Als u de versleuteling wilt forceren van een blob die vóór 20 oktober 2017 is gemaakt, u de blob opnieuw schrijven. Zie [De versleutelingsstatus van een blob controleren](../blobs/storage-blob-encryption-status.md)voor meer informatie over het controleren van de versleutelingsstatus van een blob.

Zie [Cryptografie API: Next Generation](https://docs.microsoft.com/windows/desktop/seccng/cng-portal)voor meer informatie over de cryptografische modules die ten grondslag liggen aan Azure Storage-versleuteling.

## <a name="about-encryption-key-management"></a>Over beheer van versleutelingssleutels

Standaard worden gegevens in een opslagaccount versleuteld met door Microsoft beheerde sleutels. U vertrouwen op door Microsoft beheerde sleutels voor de versleuteling van uw gegevens, of u versleuteling beheren met uw eigen sleutels. Als u ervoor kiest om versleuteling met uw eigen sleutels te beheren, hebt u twee opties:

- U een door de klant beheerde sleutel opgeven met Azure Key Vault die u wilt gebruiken voor het versleutelen en decoderen van gegevens in *Blob-opslag* en in Azure-bestanden. <sup>1,2</sup> Zie [Sleutels met beheerde sleutels van klanten gebruiken met Azure Key Vault om Azure Storage-versleuteling te beheren](encryption-customer-managed-keys.md)voor meer informatie over door klanten beheerde sleutels.
- U een *door de klant geleverde sleutel* opgeven voor Blob-opslagbewerkingen. Een client die een lees- of schrijfverzoek indient tegen Blob-opslag, kan een versleutelingssleutel bevatten op het verzoek om gedetailleerde controle over hoe blobgegevens worden versleuteld en gedecodeerd. Zie [Een versleutelingssleutel geven op een verzoek om blobopslag (preview) voor](encryption-customer-provided-keys.md)meer informatie over door de klant verstrekte sleutels.

In de volgende tabel worden sleutelbeheeropties voor Azure Storage-versleuteling vergeleken.

|                                        |    Door Microsoft beheerde sleutels                             |    Door klant beheerde sleutels                                                                                                                        |    Door de klant geleverde sleutels                                                          |
|----------------------------------------|-------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------|
|    Versleutelings-/decryptiebewerkingen    |    Azure                                              |    Azure                                                                                                                                        |    Azure                                                                         |
|    Ondersteuning voor Azure Storage-services    |    Alle                                                |    Blob-opslag, Azure Files<sup>1,2</sup>                                                                                                               |    Blob Storage                                                                  |
|    Sleutelopslag                         |    Microsoft-sleutelarchief    |    Azure Key Vault                                                                                                                              |    Azure Key Vault of een andere sleutelwinkel                                                                 |
|    Belangrijkste rotatieverantwoordelijkheid         |    Microsoft                                          |    Klant                                                                                                                                     |    Klant                                                                      |
|    Sleutelgebruik                           |    Microsoft                                          |    Azure-portal, REST API voor Storage Resource Provider, Azure Storage Management-bibliotheken, PowerShell, CLI        |    Azure Storage REST API (Blob-opslag), Azure Storage-clientbibliotheken    |
|    Toegang tot sleutels                          |    Alleen Microsoft                                     |    Microsoft, Klant                                                                                                                    |    Alleen voor de klant                                                                 |

<sup>1</sup> Zie Een account maken dat door klanten beheerde sleutels ondersteunt voor informatie over het maken van een account dat door klanten [beheerde sleutels voor wachtrijen ondersteunt.](account-encryption-key-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)<br />
<sup>2</sup> Zie Een account maken dat door klanten beheerde sleutels ondersteunt voor informatie over het maken van een account dat door klanten [beheerde sleutels voor tabellen ondersteunt.](account-encryption-key-create.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json)

## <a name="next-steps"></a>Volgende stappen

- [Wat is Azure Key Vault?](../../key-vault/key-vault-overview.md)
- [Door de klant beheerde sleutels configureren voor Azure Storage-versleuteling via de Azure-portal](storage-encryption-keys-portal.md)
- [Door de klant beheerde sleutels configureren voor Azure Storage-versleuteling met behulp van PowerShell](storage-encryption-keys-powershell.md)
- [Door de klant beheerde sleutels configureren voor Azure Storage-versleuteling met behulp van Azure CLI](storage-encryption-keys-cli.md)
