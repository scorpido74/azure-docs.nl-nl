---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/11/2020
ms.author: mimart
ms.openlocfilehash: ef08f1adc15475cd5dd38548ed39dc57532c0ef5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "78185829"
---
## <a name="guidelines-for-using-javascript"></a>Richt lijnen voor het gebruik van Java script

Volg deze richt lijnen bij het aanpassen van de interface van uw toepassing met behulp van Java script:

- Bind een Click-gebeurtenis op `<a>` HTML-elementen niet.
- Maak geen afhankelijkheid van Azure AD B2C code of opmerkingen.
- Wijzig de volg orde of hiërarchie van Azure AD B2C HTML-elementen niet. Gebruik een Azure AD B2C beleid om de volg orde van de elementen van de gebruikers interface te bepalen.
- U kunt een wille keurige REST-service aanroepen met de volgende overwegingen:
    - Mogelijk moet u de CORS van de REST-service instellen om HTTP-aanroepen aan de client zijde toe te staan.
    - Zorg ervoor dat de REST-service veilig is en alleen het HTTPS-protocol gebruikt.
    - Gebruik Java script niet rechtstreeks om Azure AD B2C-eind punten aan te roepen.
- U kunt uw Java script insluiten of u kunt een koppeling maken naar externe java script-bestanden. Wanneer u een extern java script-bestand gebruikt, moet u ervoor zorgen dat u de absolute URL gebruikt en geen relatieve URL.
- Java script-frameworks:
    - Azure AD B2C maakt gebruik van een specifieke versie van jQuery. Neem geen andere versie van jQuery op. Als u meer dan één versie op dezelfde pagina gebruikt, worden er problemen veroorzaakt.
    - Het gebruik van RequireJS wordt niet ondersteund.
    - De meeste Java script-frameworks worden niet ondersteund door Azure AD B2C.
- Azure AD B2C-instellingen kunnen worden gelezen door `window.SETTINGS` - `window.CONTENT` objecten, zoals de huidige taal van de gebruikers interface, aan te roepen. Wijzig de waarde van deze objecten niet.
- Als u het fout bericht Azure AD B2C wilt aanpassen, gebruikt u lokalisatie in een beleid.
- Als er iets kan worden bereikt met behulp van een beleid, is dit doorgaans de aanbevolen manier.
