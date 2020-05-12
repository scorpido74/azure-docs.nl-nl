---
ms.openlocfilehash: 849777ac3ec11915c602861fea0c42a49fb08bcb
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/10/2020
ms.locfileid: "83006414"
---
## <a name="provide-frames-to-the-session"></a>De sessie voorzien van frames

De ruimtelijke-anker sessie werkt door de ruimte rond de gebruiker toe te wijzen. Zo kunt u bepalen waar ankers zich bevinden. Mobiele platformen (iOS & Android) vereisen een systeem eigen aanroep naar de camera feed voor het verkrijgen van frames van de AR-bibliotheek van uw platform. Daarentegen wordt de omgeving voortdurend gescand door HoloLens, dus er is geen specifieke aanroep nodig zoals op mobiele platforms.