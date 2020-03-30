---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/11/2020
ms.author: mimart
ms.openlocfilehash: ef08f1adc15475cd5dd38548ed39dc57532c0ef5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78185829"
---
## <a name="guidelines-for-using-javascript"></a>Richtlijnen voor het gebruik van JavaScript

Volg deze richtlijnen wanneer u de interface van uw toepassing aanpast met JavaScript:

- Bind geen klikgebeurtenis aan `<a>` HTML-elementen.
- Neem geen afhankelijkheid van Azure AD B2C-code of opmerkingen.
- Wijzig de volgorde of hiërarchie van Azure AD B2C HTML-elementen niet. Gebruik een Azure AD B2C-beleid om de volgorde van de ui-elementen te beheren.
- U elke RESTful-service bellen met de volgende overwegingen:
    - Mogelijk moet u uw RESTful-service CORS zo instellen dat http-oproepen aan de clientzijde mogelijk zijn.
    - Zorg ervoor dat uw RESTful-service veilig is en alleen het HTTPS-protocol gebruikt.
    - Gebruik JavaScript niet rechtstreeks om Azure AD B2C-eindpunten aan te roepen.
- U uw JavaScript insluiten of u een koppeling maken naar externe JavaScript-bestanden. Wanneer u een extern JavaScript-bestand gebruikt, moet u de absolute URL gebruiken en niet een relatieve URL.
- JavaScript-frameworks:
    - Azure AD B2C gebruikt een specifieke versie van jQuery. Neem geen andere versie van jQuery op. Het gebruik van meer dan één versie op dezelfde pagina veroorzaakt problemen.
    - Het gebruik van RequireJS wordt niet ondersteund.
    - De meeste JavaScript-frameworks worden niet ondersteund door Azure AD B2C.
- Azure AD B2C-instellingen kunnen `window.SETTINGS` `window.CONTENT` worden gelezen door objecten aan te roepen, zoals de huidige gebruikersinterfacetaal. Wijzig de waarde van deze objecten niet.
- Als u het Azure AD B2C-foutbericht wilt aanpassen, gebruikt u lokalisatie in een beleid.
- Als er iets kan worden bereikt met behulp van een beleid, over het algemeen is het de aanbevolen manier.
