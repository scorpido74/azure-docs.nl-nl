---
ms.openlocfilehash: 6d6886996d926ce778600dfa1d69caaa1c7ac004
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "67175756"
---
## <a name="pause-reset-or-stop-the-session"></a>De sessie onderbreken, opnieuw instellen of stoppen

Als u de sessie tijdelijk `Stop()`wilt stoppen, u een beroep doen op. Als u dit doet, worden alle watchers en de verwerking van de omgeving gestopt, zelfs als u ProcessFrame() aanroept. U `Start()` vervolgens een beroep doen op de verwerking. Bij hervatting worden omgevingsgegevens die al in de sessie zijn vastgelegd, bijgehouden.
