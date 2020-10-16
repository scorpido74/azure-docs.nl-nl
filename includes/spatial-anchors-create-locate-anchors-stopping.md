---
ms.openlocfilehash: a165061b2f927d6302504819ef27c846d76d0f30
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "83006413"
---
## <a name="pause-reset-or-stop-the-session"></a>De sessie onderbreken, opnieuw instellen of stoppen

Als u de sessie tijdelijk wilt stoppen, kunt u `Stop()` aanroepen. Als u dit doet, worden alle watchers en de omgevingsverwerking gestopt, zelfs als u `ProcessFrame()` aanroept. Vervolgens kunt u `Start()` aanroepen om de verwerking te hervatten. Bij het hervatten blijven de omgevingsgegevens die al in de sessie zijn vastgelegd, behouden.
