---
ms.openlocfilehash: 849777ac3ec11915c602861fea0c42a49fb08bcb
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/25/2020
ms.locfileid: "83006414"
---
## <a name="provide-frames-to-the-session"></a>De sessie voorzien van frames

De ruimtelijke-ankersessie werkt door de ruimte rond de gebruiker toe te wijzen. Zo kunt u bepalen waar de ankers zich moeten bevinden. Mobiele platformen (iOS en Android) vereisen een systeemeigen aanroep naar de camerafeed om frames van de AR-bibliotheek van uw platform te verkrijgen. Daarentegen wordt de omgeving voortdurend gescand door HoloLens, dus er is geen specifieke aanroep nodig, zoals bij mobiele platforms.