---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 05/22/2019
ms.author: alkohli
ms.openlocfilehash: bc156b8c18f46cccf6fc775b82f76383b8c43861
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "66242138"
---
Hier volgt een lijst met de ondersteunde opslag accounts en de opslag typen voor het Data Box apparaat. Zie [typen opslag accounts](/azure/storage/common/storage-account-overview#types-of-storage-accounts)voor een volledige lijst van alle verschillende soorten opslag accounts en de volledige mogelijkheden hiervan.

| **Opslag account/ondersteunde opslag typen** | **Blok-blob** |**Pagina-BLOB*** |**Azure-bestanden** |**Opmerkingen**|
| --- | --- | -- | -- | -- |
| Klassieke standaard | J | J | J |
| Algemene v1-standaard  | J | J | J | Zowel warm als koud worden ondersteund.|
| V1 Premium voor algemeen gebruik  |  | J| | |
| V2-standaard voor algemeen gebruik  | J | J | J | Zowel warm als koud worden ondersteund.|
| V2 Premium voor algemeen gebruik  |  |J | | |
| Blob Storage-standaard |J | | |Zowel warm als koud worden ondersteund. |

\**-Gegevens die zijn geüpload naar pagina-blobs, moeten 512 bytes, zoals vhd's, zijn uitgelijnd.*

>[!NOTE]
> Gen 2-accounts van Azure Data Lake Storage worden niet ondersteund.
