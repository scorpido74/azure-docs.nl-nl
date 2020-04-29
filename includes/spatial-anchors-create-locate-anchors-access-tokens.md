---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 02/21/2019
ms.author: rgarcia
ms.openlocfilehash: 63725d55e2b2935ec6a899789249259b096865c3
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "67175730"
---
### <a name="access-tokens"></a>Toegangs tokens

Toegangs tokens zijn een robuustere methode voor verificatie met Azure-ruimtelijke ankers. Met name wanneer u uw toepassing voorbereidt voor een productie-implementatie. Het overzicht van deze aanpak is het instellen van een back-end-service waarmee uw client toepassing veilig kan worden geverifieerd. Uw back-end-service interfaces met AAD tijdens runtime en met de Azure spatiale-ankers beveiligde token service om een toegangs token aan te vragen. Dit token wordt vervolgens afgeleverd aan de client toepassing en wordt gebruikt in de SDK om te verifiëren met Azure spatiale ankers.
