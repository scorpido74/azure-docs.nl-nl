---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 08/23/2019
ms.author: rgarcia
ms.openlocfilehash: 9a02dfbf3f2976489c10ccfeb935915a65d8dc84
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "71180044"
---
Nadat je watcher is `AnchorLocated` gemaakt, wordt het evenement voor elk aangevraagd anker gemaakt. Deze gebeurtenis wordt geactiveerd wanneer een anker zich bevindt of als het anker zich niet kan bevinden. Als deze situatie zich voordoet, wordt de reden vermeld in de status. Nadat alle ankers voor een watcher zijn verwerkt, `LocateAnchorsCompleted` gevonden of niet gevonden, dan zal het evenement vuren. Er is een limiet van 35 id's per watcher. 
