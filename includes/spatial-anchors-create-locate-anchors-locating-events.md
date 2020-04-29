---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 08/23/2019
ms.author: rgarcia
ms.openlocfilehash: 9a02dfbf3f2976489c10ccfeb935915a65d8dc84
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "71180044"
---
Nadat uw Watcher is gemaakt, wordt `AnchorLocated` de gebeurtenis geactiveerd voor elk aangevraagde anker. Deze gebeurtenis wordt gestart wanneer een anker zich bevindt of als het anker niet kan worden gevonden. Als dit het geval is, wordt de reden aangegeven in de status. Nadat alle ankers voor een Watcher zijn verwerkt, gevonden of niet gevonden, wordt de `LocateAnchorsCompleted` gebeurtenis geactiveerd. Er geldt een limiet van 35 id's per Watcher. 
