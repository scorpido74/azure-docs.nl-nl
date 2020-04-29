---
ms.openlocfilehash: 8ebb10f955be8f3004fdbdc595ea0fefc0d2b7ea
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "67175748"
---
## <a name="update-properties"></a>Eigenschappen bijwerken

Als u de eigenschappen van een anker wilt bijwerken, gebruikt `UpdateAnchorProperties()` u de-methode. Als twee of meer apparaten de eigenschappen van hetzelfde anker op hetzelfde moment proberen bij te werken, gebruiken we een optimistisch gelijktijdigheids model. Dit betekent dat de eerste schrijf bewerking wordt gewin.  Bij alle andere schrijf bewerkingen wordt een ' gelijktijdigheids ' fout weer geven: u moet de eigenschappen vernieuwen voordat u het opnieuw probeert.
