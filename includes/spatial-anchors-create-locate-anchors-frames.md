---
ms.openlocfilehash: 849777ac3ec11915c602861fea0c42a49fb08bcb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "83006414"
---
## <a name="provide-frames-to-the-session"></a>De sessie voorzien van frames

De ruimtelijke-ankersessie werkt door de ruimte rond de gebruiker toe te wijzen. Zo kunt u bepalen waar de ankers zich moeten bevinden. Mobiele platformen (iOS en Android) vereisen een systeemeigen aanroep naar de camerafeed om frames van de AR-bibliotheek van uw platform te verkrijgen. Daarentegen wordt de omgeving voortdurend gescand door HoloLens, dus er is geen specifieke aanroep nodig, zoals bij mobiele platforms.