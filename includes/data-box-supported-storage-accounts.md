---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 05/22/2019
ms.author: alkohli
ms.openlocfilehash: bc156b8c18f46cccf6fc775b82f76383b8c43861
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "66242138"
---
Hier volgt een lijst met de ondersteunde opslagaccounts en de opslagtypen voor het Data Box-apparaat. Zie [Typen opslagaccounts](/azure/storage/common/storage-account-overview#types-of-storage-accounts)voor een volledige lijst met alle verschillende soorten opslagaccounts en hun volledige mogelijkheden.

| **Opslagaccount / ondersteunde opslagtypen** | **Blok-blob** |**Paginablob*** |**Azure-bestanden** |**Opmerkingen**|
| --- | --- | -- | -- | -- |
| Klassieke standaard | J | J | J |
| Standaard voor algemene doeleinden v1  | J | J | J | Zowel warm als koel worden ondersteund.|
| Algemeen gebruik v1 Premium  |  | J| | |
| Standaard voor algemene doeleinden v2  | J | J | J | Zowel warm als koel worden ondersteund.|
| Algemeen gebruik v2 Premium  |  |J | | |
| Standaard blobopslag |J | | |Zowel warm als koel worden ondersteund. |

\**- Gegevens die naar paginablobs worden geüpload, moeten 512 bytes zijn uitgelijnd, zoals VHD's.*

>[!NOTE]
> Gen 2-accounts van Azure Data Lake Storage worden niet ondersteund.
