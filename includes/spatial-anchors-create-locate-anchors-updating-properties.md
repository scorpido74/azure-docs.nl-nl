---
ms.openlocfilehash: 8ebb10f955be8f3004fdbdc595ea0fefc0d2b7ea
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "67175748"
---
## <a name="update-properties"></a>Eigenschappen bijwerken

Als u de eigenschappen van een anker wilt bijwerken, gebruikt u methode `UpdateAnchorProperties()`. Als twee of meer apparaten de eigenschappen van hetzelfde anker op hetzelfde moment proberen bij te werken, wordt een model voor optimistische gelijktijdigheid gebruikt. Dit betekent dat de eerste schrijfbewerking wint.  Bij alle andere schrijfbewerkingen wordt een gelijktijdigheidsfout weergeven: vernieuw de eigenschappen voordat u het opnieuw probeert.
