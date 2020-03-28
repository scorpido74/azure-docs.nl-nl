---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 02/21/2019
ms.author: rgarcia
ms.openlocfilehash: 63725d55e2b2935ec6a899789249259b096865c3
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "67175730"
---
### <a name="access-tokens"></a>Toegangstokens

Access Tokens zijn een robuustere methode om te verifiëren met Azure Spatial Anchors. Vooral als u uw aanvraag voorbereidt op een productie-implementatie. De samenvatting van deze aanpak is het opzetten van een back-endservice waarmee uw clienttoepassing veilig kan verifiëren. Uw back-endservice interfaces met AAD tijdens runtime en met de Azure Spatial Anchors Secure Token Service om een Access Token aan te vragen. Dit token wordt vervolgens geleverd aan de clienttoepassing en in de SDK gebruikt om te verifiëren met Azure Spatial Anchors.
