---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 06/08/2020
ms.author: alkohli
ms.openlocfilehash: da36e2bbf358a1c61d2b9b3f7ede592ac88fd427
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/09/2020
ms.locfileid: "86200329"
---
Hier volgt een lijst met de ondersteunde opslag accounts en de opslag typen voor het Data Box apparaat. Zie [typen opslag accounts](/azure/storage/common/storage-account-overview#types-of-storage-accounts)voor een volledige lijst van alle verschillende soorten opslag accounts en de volledige mogelijkheden hiervan.

De volgende tabel bevat de ondersteunde opslag accounts voor import orders.

| **Opslag account/ondersteunde opslag typen** | **Blok-blob** |**Pagina-BLOB*** |**Azure-bestanden** |**Opmerkingen**|
| --- | --- | -- | -- | -- |
| Klassieke standaard | J | J | J |
| Algemene v1-standaard  | J | J | J | Zowel warm als koud worden ondersteund.|
| V1 Premium voor algemeen gebruik  |  | Y| | |
| V2-standaard voor algemeen gebruik  | J | J | J | Zowel warm als koud worden ondersteund.|
| V2 Premium voor algemeen gebruik  |  |Y | | |
| Blob Storage-standaard |Y | | |Zowel warm als koud worden ondersteund. |

\**-Gegevens die zijn geüpload naar pagina-blobs, moeten 512 bytes, zoals vhd's, zijn uitgelijnd.*

De volgende tabel bevat de ondersteunde opslag accounts voor export orders.

| **Opslag account/ondersteunde opslag typen** | **Blok-blob** |**Pagina-BLOB*** |**Azure-bestanden** |**Ondersteunde toegangs lagen**|
| --- | --- | -- | -- | -- |
| Klassieke standaard | J | J | J | |
| Algemene v1-standaard  | J | J | J | Warm, cool|
| V1 Premium voor algemeen gebruik  |  | Y| | |
| V2-standaard voor algemeen gebruik  | J | J | J | Warm, cool|
| V2 Premium voor algemeen gebruik  |  |Y | | |
| Blob Storage-standaard |Y | | |Warm, cool |
| De Premium-opslag voor BLOB blok keren |Y | | |Warm, cool |
| Pagina Blob Storage Premium | |Y | | |

> [!IMPORTANT]
> - Voor accounts voor algemeen gebruik biedt Data Box geen ondersteuning voor wachtrij-, tabel-, schijf-en Azure Data Lake gen 2-opslag typen.
> - Data Box biedt geen ondersteuning voor toevoeg-blobs voor Blob Storage en Blob Storage-accounts blok keren.
> - Data Box biedt geen ondersteuning voor Premium File Storage-accounts.
> - Gegevens die zijn geüpload naar pagina-blobs, moeten 512 bytes, zoals Vhd's, zijn uitgelijnd.
> - Maxi maal 80 TB kan worden geëxporteerd.
> - Bestands geschiedenis en BLOB-moment opnamen worden niet geëxporteerd.


