---
ms.openlocfilehash: 8ebb10f955be8f3004fdbdc595ea0fefc0d2b7ea
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/25/2020
ms.locfileid: "67175748"
---
## <a name="update-properties"></a>Eigenschappen bijwerken

Als u de eigenschappen van een anker wilt bijwerken, gebruikt u methode `UpdateAnchorProperties()`. Als twee of meer apparaten de eigenschappen van hetzelfde anker op hetzelfde moment proberen bij te werken, wordt een model voor optimistische gelijktijdigheid gebruikt. Dit betekent dat de eerste schrijfbewerking wint.  Bij alle andere schrijfbewerkingen wordt een gelijktijdigheidsfout weergeven: vernieuw de eigenschappen voordat u het opnieuw probeert.
