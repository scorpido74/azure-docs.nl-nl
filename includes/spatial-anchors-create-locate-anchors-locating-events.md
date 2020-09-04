---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 08/23/2019
ms.author: rgarcia
ms.openlocfilehash: 9a02dfbf3f2976489c10ccfeb935915a65d8dc84
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/25/2020
ms.locfileid: "71180044"
---
Nadat de watcher is gemaakt, wordt de `AnchorLocated`-gebeurtenis voor elk aangevraagd anker geactiveerd. Deze gebeurtenis wordt geactiveerd wanneer er een anker wordt gelokaliseerd of als het anker niet kan worden gelokaliseerd. Als deze situatie zich voordoet, wordt de reden aangegeven in de status. Nadat alle ankers voor een watcher zijn verwerkt, gevonden of niet gevonden, wordt de `LocateAnchorsCompleted`-gebeurtenis geactiveerd. Er geldt een limiet van 35 id's per watcher. 
