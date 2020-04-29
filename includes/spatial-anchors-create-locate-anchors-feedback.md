---
ms.openlocfilehash: 7b9954930c668e338cc289e948a5591c09ec2654
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76694231"
---
## <a name="provide-feedback-to-the-user"></a>Feedback geven aan de gebruiker

U kunt code schrijven voor het verwerken van de gebeurtenis bijgewerkt met de sessie. Deze gebeurtenis wordt geactiveerd wanneer de sessie de inzichten van uw omgeving verg root. Als u dit doet, kunt u het volgende doen:

- Gebruik de `UserFeedback` -klasse om feedback te geven aan de gebruiker wanneer het apparaat wordt verplaatst en de sessie wordt bijgewerkt. Dit wilt doen
- Bepaal op welk punt voldoende getraceerde ruimtelijke gegevens zich bevinden om ruimtelijke ankers te maken. U kunt dit vaststellen met `ReadyForCreateProgress` ofwel `RecommendedForCreateProgress`of. Nadat `ReadyForCreateProgress` de 1 is afgelopen, hebben we voldoende gegevens om een ruimtelijke ruimte in de Cloud op te slaan, maar `RecommendedForCreateProgress` we raden u aan te wachten tot de bovenstaande 1 is.
