---
ms.openlocfilehash: 7b9954930c668e338cc289e948a5591c09ec2654
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76694231"
---
## <a name="provide-feedback-to-the-user"></a>Feedback geven aan de gebruiker

U kunt code schrijven voor het verwerken van de gebeurtenis bijgewerkt met de sessie. Deze gebeurtenis wordt geactiveerd wanneer de sessie de inzichten van uw omgeving verg root. Als u dit doet, kunt u het volgende doen:

- Gebruik de klasse `UserFeedback` om feedback te geven aan de gebruiker wanneer het apparaat wordt verplaatst en de sessie van de omgeving wordt bijgewerkt. Dit wilt doen
- Bepaal op welk punt voldoende getraceerde ruimtelijke gegevens zich bevinden om ruimtelijke ankers te maken. U kunt dit bepalen op basis van `ReadyForCreateProgress` of `RecommendedForCreateProgress`. Als `ReadyForCreateProgress` hoger is dan 1, hebben we voldoende gegevens om een ruimtelijke ruimte in de Cloud op te slaan, maar we raden u aan te wachten tot `RecommendedForCreateProgress` groter is dan 1.
