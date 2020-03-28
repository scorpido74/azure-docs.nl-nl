---
ms.openlocfilehash: 8ebb10f955be8f3004fdbdc595ea0fefc0d2b7ea
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "67175748"
---
## <a name="update-properties"></a>Eigenschappen bijwerken

Als u de eigenschappen van een `UpdateAnchorProperties()` anker wilt bijwerken, gebruikt u de methode. Als twee of meer apparaten tegelijkertijd proberen eigenschappen voor hetzelfde anker bij te werken, gebruiken we een optimistisch gelijktijdigheidsmodel. Wat betekent dat het eerste schrijven zal winnen.  Alle andere schrijft krijgt een "Gelijktijdigheid" fout: een vernieuwing van de eigenschappen nodig zou zijn voordat u het opnieuw probeert.
