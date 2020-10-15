---
ms.openlocfilehash: 7b9954930c668e338cc289e948a5591c09ec2654
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "76694231"
---
## <a name="provide-feedback-to-the-user"></a>De gebruiker feedback geven

U kunt code schrijven voor het verwerken van de actuele sessiegebeurtenis. Deze gebeurtenis wordt geactiveerd wanneer het begrip van uw omgeving door de sessie worden verbeterd. Hierdoor kunt u het volgende doen:

- Klasse `UserFeedback` gebruiken om feedback te geven aan de gebruiker terwijl het apparaat beweegt en het begrip van de omgeving wordt bijgewerkt. Dit wilt doen
- Bepaal op welk moment er voldoende getraceerde ruimtelijke gegevens zijn om ruimtelijke ankers te maken. U kunt dit vaststellen met `ReadyForCreateProgress` of `RecommendedForCreateProgress`. Als `ReadyForCreateProgress` meer is dan 1, zijn er voldoende gegevens om een ruimtelijk anker in de cloud op te slaan. Het is echter raadzaam te wachten tot `RecommendedForCreateProgress` groter is dan 1.
