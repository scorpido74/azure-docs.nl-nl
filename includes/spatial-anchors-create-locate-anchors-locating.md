---
ms.openlocfilehash: b5fec8bbc0db78454b080a411702014bd96f7db9
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76887838"
---
## <a name="locate-a-cloud-spatial-anchor"></a>Een ruimtelijke beankering in de Cloud zoeken

Het is een van de belangrijkste redenen voor het gebruik van Azure spatiale ankers om een eerder opgeslagen ruimtelijke bewerkings plaats in de cloud te vinden. Er zijn verschillende manieren waarop u een ruimtelijke ruimte in de cloud kunt vinden. U kunt één strategie per keer gebruiken op een monitor.
- Zoek naar ankers op id.
- Ankers zoeken die zijn verbonden met een eerder gevonden anker. [Hier](/azure/spatial-anchors/concepts/anchor-relationships-way-finding/)vindt u meer informatie over anker relaties.
- Zoek naar een anker met [grof Herlokalisatie](/azure/spatial-anchors/concepts/coarse-reloc/).

Als u ruimtelijke beschik bare ruimte in de Cloud zoekt met behulp van id, moet u de id van de in de back-end voor de Cloud beschik bare ruimte opslaan en deze toegankelijk maken voor alle apparaten die op de juiste manier kunnen worden geverifieerd. Zie [zelf studie: ruimtelijke ankers delen tussen apparaten](/azure/spatial-anchors/tutorials/tutorial-share-anchors-across-devices/)voor een voor beeld hiervan.

Maak een `AnchorLocateCriteria` exemplaar van een object, stel de id's in die u zoekt en roep `CreateWatcher` de methode voor de sessie aan door `AnchorLocateCriteria`uw toe te voegen.