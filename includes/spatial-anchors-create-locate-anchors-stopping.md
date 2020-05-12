---
ms.openlocfilehash: a165061b2f927d6302504819ef27c846d76d0f30
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/10/2020
ms.locfileid: "83006413"
---
## <a name="pause-reset-or-stop-the-session"></a>De sessie onderbreken, opnieuw instellen of stoppen

Als u de sessie tijdelijk wilt stoppen, kunt `Stop()`u aanroepen. Als u dit doet, worden alle weers omstandigheden en omgevings verwerking `ProcessFrame()`gestopt, zelfs als u aanroept. U kunt vervolgens aanroepen `Start()` om de verwerking te hervatten. Bij het hervatten worden de omgevings gegevens die al in de sessie zijn vastgelegd, behouden.
