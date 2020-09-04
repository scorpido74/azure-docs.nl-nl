---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 02/21/2019
ms.author: rgarcia
ms.openlocfilehash: 63725d55e2b2935ec6a899789249259b096865c3
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/25/2020
ms.locfileid: "67175730"
---
### <a name="access-tokens"></a>Toegangstokens

Toegangstokens zijn een meer robuuste methode om verificatie uit te voeren bij Azure Spatial Anchors. Met name wanneer u de toepassing voorbereidt voor een productie-implementatie. Samengevat: stel een back-endservice in waarmee uw clienttoepassing veilig kan worden geverifieerd. Uw back-endservice communiceert tijdens runtime met AAD en met de beveiligingstokenservice van Azure Spatial Anchors om een toegangstoken aan te vragen. Dit token wordt vervolgens afgeleverd aan de clienttoepassing en wordt in de SDK gebruikt om verificatie bij Azure Spatial Anchors uit te voeren.
