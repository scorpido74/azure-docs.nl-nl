---
ms.openlocfilehash: b5fec8bbc0db78454b080a411702014bd96f7db9
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "76887838"
---
## <a name="locate-a-cloud-spatial-anchor"></a>Een ruimtelijk anker in de cloud zoeken

Het kunnen vinden van een eerder opgeslagen ruimtelijk anker in de cloud is een van de belangrijkste redenen voor het gebruik van Azure Spatial Anchors. Er zijn verschillende manieren waarop u een ruimtelijk anker in de cloud vinden. U gebruik maken van een strategie op een watcher op een moment.
- Zoek ankers op id.
- Zoek ankers die zijn verbonden met een eerder gelegen anker. U meer informatie over anker relaties [hier](/azure/spatial-anchors/concepts/anchor-relationships-way-finding/).
- Zoek anker met [grove herlokalisatie](/azure/spatial-anchors/concepts/coarse-reloc/).

Als u ruimtelijke ankers in de cloud op id vindt, wilt u de cloud-locatie voor ruimtelijk anker opslaan in de back-endservice van uw toepassing en deze toegankelijk maken voor alle apparaten die deze correct kunnen verifiëren. Zie [Zelfstudie: Ruimtelijke ankers delen op verschillende apparaten](/azure/spatial-anchors/tutorials/tutorial-share-anchors-across-devices/)voor een voorbeeld hiervan.

Instantiate `AnchorLocateCriteria` een object, stel de id's die `CreateWatcher` u zoekt, en `AnchorLocateCriteria`beroep de methode op de sessie door het verstrekken van uw .