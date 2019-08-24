---
ms.openlocfilehash: f478956c3a220ea4435c4f6e8b1096a1e9fdcadf
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/23/2019
ms.locfileid: "70014757"
---
Nadat uw Watcher is gemaakt, wordt `AnchorLocated` de gebeurtenis geactiveerd voor elk aangevraagde anker. Deze gebeurtenis wordt gestart wanneer een anker zich bevindt of als het anker niet kan worden gevonden. Als dit het geval is, wordt de reden aangegeven in de status. Nadat alle ankers voor een Watcher zijn verwerkt, gevonden of niet gevonden, wordt de `LocateAnchorsCompleted` gebeurtenis geactiveerd. Er geldt een limiet van 35 id's per Watcher. 
