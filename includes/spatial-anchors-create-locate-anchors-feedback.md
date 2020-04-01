---
ms.openlocfilehash: 7b9954930c668e338cc289e948a5591c09ec2654
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "76694231"
---
## <a name="provide-feedback-to-the-user"></a>Feedback geven aan de gebruiker

U code schrijven om de sessiebijgewerkte gebeurtenis te verwerken. Deze gebeurtenis vuurt elke keer dat de sessie verbetert haar begrip van uw omgeving. Als u dit doet, u:

- Gebruik `UserFeedback` de klasse om feedback te geven aan de gebruiker terwijl het apparaat wordt verplaatst en de sessie zijn omgevingsinformatie bijwerkt. Dit wilt doen
- Bepaal op welk punt er voldoende bijgehouden ruimtelijke gegevens zijn om ruimtelijke ankers te creëren. U bepaalt dit `ReadyForCreateProgress` `RecommendedForCreateProgress`met een van beide of . Eenmaal `ReadyForCreateProgress` boven de 1 hebben we genoeg gegevens om een ruimtelijk `RecommendedForCreateProgress` anker in de cloud op te slaan, hoewel we u aanraden te wachten tot boven de 1.
