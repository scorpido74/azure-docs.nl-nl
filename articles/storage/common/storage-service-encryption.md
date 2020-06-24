---
title: Azure Storage-versleuteling voor inactieve gegevens
description: Azure Storage beveiligt uw gegevens door deze automatisch te versleutelen voordat deze in de cloud worden bewaard. U kunt gebruikmaken van door micro soft beheerde sleutels voor het versleutelen van de gegevens in uw opslag account, of u kunt versleuteling beheren met uw eigen sleutels.
services: storage
author: tamram
ms.service: storage
ms.date: 06/17/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 8b4236e40e8dfbe6ce67bca007be0b6737a6e0c8
ms.sourcegitcommit: 51977b63624dfd3b4f22fb9fe68761d26eed6824
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/17/2020
ms.locfileid: "84945576"
---
# <a name="azure-storage-encryption-for-data-at-rest"></a>Azure Storage-versleuteling voor inactieve gegevens

Azure Storage worden uw gegevens automatisch versleuteld wanneer deze persistent worden gemaakt in de Cloud. Azure Storage versleuteling beveiligt uw gegevens en helpt u om te voldoen aan de beveiligings-en nalevings verplichtingen van uw organisatie.

## <a name="about-azure-storage-encryption"></a>Over Azure Storage versleuteling

Gegevens in Azure Storage worden transparant versleuteld en ontsleuteld met 256-bits [AES-versleuteling](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), een van de krach tigste blok cijfers die beschikbaar zijn en is compatibel met FIPS 140-2. Azure Storage versleuteling lijkt op BitLocker-versleuteling in Windows.

Azure Storage versleuteling is ingeschakeld voor alle opslag accounts, met inbegrip van Resource Manager en klassieke opslag accounts. Azure Storage versleuteling kan niet worden uitgeschakeld. Omdat uw gegevens standaard worden beveiligd, hoeft u uw code of toepassingen niet te wijzigen om Azure Storage versleuteling te benutten.

Gegevens in een opslag account worden versleuteld, ongeacht de standaardlaag (Standard of Premium), de Access-laag (Hot of cool) of het implementatie model (Azure Resource Manager of klassiek). Alle blobs in de archief laag worden ook versleuteld. Alle Azure Storage redundantie opties ondersteunen versleuteling en alle gegevens in de primaire en secundaire regio's worden versleuteld wanneer geo-replicatie is ingeschakeld. Alle Azure Storage bronnen worden versleuteld, waaronder blobs, schijven, bestanden, wacht rijen en tabellen. Alle meta gegevens van objecten worden ook versleuteld. Er zijn geen extra kosten verbonden aan Azure Storage versleuteling.

Elke blok-blob, een toevoeg-BLOB of een pagina-blob die na 20 oktober 2017 is geschreven naar Azure Storage, is versleuteld. De blobs die vóór deze datum zijn gemaakt, worden versleuteld met een achtergrond proces. Als u de versleuteling wilt afdwingen van een blob die is gemaakt vóór 20 oktober 2017, kunt u de BLOB herschrijven. Zie [de versleutelings status van een BLOB controleren](../blobs/storage-blob-encryption-status.md)voor meer informatie over het controleren van de versleutelings status van een blob.

Zie [crypto GRAFIE API: Next Generation](https://docs.microsoft.com/windows/desktop/seccng/cng-portal)(Engelstalig) voor meer informatie over de onderliggende cryptografische modules Azure Storage versleuteling.

## <a name="about-encryption-key-management"></a>Over het beheer van versleutelings sleutels

Gegevens in een nieuw opslag account worden versleuteld met door micro soft beheerde sleutels. U kunt gebruikmaken van door micro soft beheerde sleutels voor het versleutelen van uw gegevens, maar u kunt ook versleuteling beheren met uw eigen sleutels. Als u ervoor kiest om versleuteling te beheren met uw eigen sleutels, hebt u twee opties:

- U kunt een door de *klant beheerde sleutel* opgeven met Azure Key Vault die moet worden gebruikt voor het versleutelen en ontsleutelen van gegevens in Blob Storage en in azure files. <sup>1, 2</sup> als u meer informatie wilt over door de klant beheerde sleutels, raadpleegt u [door de klant beheerde sleutels gebruiken met Azure Key Vault voor het beheren van Azure Storage versleuteling](encryption-customer-managed-keys.md).
- U kunt een door de *klant opgegeven sleutel* voor Blob-opslag bewerkingen opgeven. Een client die een lees-of schrijf aanvraag uitvoert voor Blob Storage, kan een versleutelings sleutel bevatten op de aanvraag voor gedetailleerde controle over hoe BLOB-gegevens worden versleuteld en ontsleuteld. Zie voor meer informatie over door de klant geleverde sleutels [een versleutelings sleutel opgeven voor een aanvraag voor Blob-opslag (preview)](encryption-customer-provided-keys.md).

De volgende tabel vergelijkt de opties voor sleutel beheer voor Azure Storage versleuteling.

|                                        |    Door micro soft beheerde sleutels                             |    Door klant beheerde sleutels                                                                                                                        |    Door de klant verschafte sleutels                                                          |
|----------------------------------------|-------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------|
|    Bewerkingen voor versleuteling/ontsleuteling    |    Azure                                              |    Azure                                                                                                                                        |    Azure                                                                         |
|    Azure Storage services ondersteund    |    Alles                                                |    Blob-opslag, Azure Files<sup>1, 2</sup>                                                                                                               |    Blob Storage                                                                  |
|    Sleutel opslag                         |    Micro soft-sleutel archief    |    Azure Key Vault                                                                                                                              |    Eigen sleutel archief van de klant                                                                 |
|    Verantwoordelijkheid voor sleutel rotatie         |    Microsoft                                          |    Klant                                                                                                                                     |    Klant                                                                      |
|    Sleutel besturings element                          |    Microsoft                                     |    Klant                                                                                                                    |    Klant                                                                 |

<sup>1</sup> Zie [een account maken dat door de klant beheerde sleutels voor wacht rijen ondersteunt](account-encryption-key-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)voor meer informatie over het maken van een account dat gebruikmaakt van door de klant beheerde sleutels met behulp van een wachtrij opslag.<br />
<sup>2</sup> Zie voor meer informatie over het maken van een account dat gebruikmaakt van door de klant beheerde sleutels met table-opslag [een account maken dat door de klant beheerde sleutels voor tabellen ondersteunt](account-encryption-key-create.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json).

Voor informatie over versleuteling en sleutel beheer voor Azure Managed disks, Zie [Server versleuteling van Azure Managed disks](../../virtual-machines/windows/disk-encryption.md) voor Windows-vm's of [versleuteling aan de server zijde van Azure Managed disks](../../virtual-machines/linux/disk-encryption.md) voor Linux-vm's.

## <a name="next-steps"></a>Volgende stappen

- [Wat is Azure Key Vault?](../../key-vault/general/overview.md)
- [Door de klant beheerde sleutels configureren voor Azure Storage-versleuteling via de Azure-portal](storage-encryption-keys-portal.md)
- [Door de klant beheerde sleutels configureren voor Azure Storage-versleuteling met behulp van PowerShell](storage-encryption-keys-powershell.md)
- [Door de klant beheerde sleutels configureren voor Azure Storage-versleuteling met behulp van Azure CLI](storage-encryption-keys-cli.md)
