---
ms.openlocfilehash: 6d6886996d926ce778600dfa1d69caaa1c7ac004
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "67175756"
---
## <a name="pause-reset-or-stop-the-session"></a>De sessie onderbreken, opnieuw instellen of stoppen

Als u de sessie tijdelijk wilt stoppen, kunt `Stop()`u aanroepen. Als u dit doet, worden alle volgers en omgevings verwerking gestopt, zelfs als u ProcessFrame () aanroept. U kunt vervolgens aanroepen `Start()` om de verwerking te hervatten. Bij het hervatten worden de omgevings gegevens die al in de sessie zijn vastgelegd, behouden.
