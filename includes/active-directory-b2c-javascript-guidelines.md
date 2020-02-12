---
author: mmacy
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/11/2020
ms.author: marsma
ms.openlocfilehash: 687853720e8f963d5a58093b824bb36e8063bcf7
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77149066"
---
## <a name="guidelines-for-using-javascript"></a>Richtlijnen voor het gebruik van JavaScript

Volg deze richtlijnen wanneer u de interface van uw toepassing met behulp van JavaScript aanpast:

- BIND geen gebeurtenis Click aan `<a>` HTML-elementen.
- Geen duren voordat een afhankelijkheid van Azure AD B2C-code of opmerkingen hebt.
- De volgorde of de hiërarchie van Azure AD B2C-HTML-elementen worden niet gewijzigd. Azure AD B2C-beleid gebruiken voor het beheren van de volgorde van de UI-elementen.
- U kunt een RESTful-service met deze overwegingen aanroepen:
    - Mogelijk moet u uw RESTful-service CORS waarmee HTTP-aanroepen aan clientzijde instellen.
    - Zorg ervoor dat uw RESTful-service is beveiligd en alleen het HTTPS-protocol wordt gebruikt.
    - Gebruik geen JavaScript rechtstreeks naar Azure AD B2C-eindpunten worden aangeroepen.
- U kunt uw JavaScript insluiten of u kunt koppelen aan externe JavaScript-bestanden. Wanneer u een bestand met externe JavaScript, zorg ervoor dat u de absolute URL zijn en niet een relatieve URL.
- JavaScript-frameworks:
    - Azure AD B2C maakt gebruik van een specifieke versie van jQuery. Een andere versie van jQuery zijn niet opgenomen. Met behulp van meer dan één versie op dezelfde pagina zorgt ervoor dat de problemen.
    - Met behulp van RequireJS wordt niet ondersteund.
    - De meeste JavaScript-frameworks worden niet ondersteund door Azure AD B2C.
- Azure AD B2C-instellingen kunnen worden gelezen door het aanroepen van `window.SETTINGS`, `window.CONTENT` objecten, zoals de huidige taal van de gebruikers interface. De waarde van deze objecten niet te wijzigen.
- Voor het aanpassen van het foutbericht van de Azure AD B2C, lokalisatie in een beleid te gebruiken.
- Als alles kan worden bereikt met behulp van een beleid, in het algemeen is de aanbevolen manier.
