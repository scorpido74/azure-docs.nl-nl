---
ms.openlocfilehash: b5fec8bbc0db78454b080a411702014bd96f7db9
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2020
ms.locfileid: "76887838"
---
## <a name="locate-a-cloud-spatial-anchor"></a>Een ruimtelijke beankering in de Cloud zoeken

Het is een van de belangrijkste redenen voor het gebruik van Azure spatiale ankers om een eerder opgeslagen ruimtelijke bewerkings plaats in de cloud te vinden. Er zijn verschillende manieren waarop u een ruimtelijke ruimte in de cloud kunt vinden. U kunt één strategie per keer gebruiken op een monitor.
- Zoek naar ankers op id.
- Ankers zoeken die zijn verbonden met een eerder gevonden anker. [Hier](/azure/spatial-anchors/concepts/anchor-relationships-way-finding/)vindt u meer informatie over anker relaties.
- Zoek naar een anker met [grof Herlokalisatie](/azure/spatial-anchors/concepts/coarse-reloc/).

Als u ruimtelijke beschik bare ruimte in de Cloud zoekt met behulp van id, moet u de id van de in de back-end voor de Cloud beschik bare ruimte opslaan en deze toegankelijk maken voor alle apparaten die op de juiste manier kunnen worden geverifieerd. Zie [zelf studie: ruimtelijke ankers delen tussen apparaten](/azure/spatial-anchors/tutorials/tutorial-share-anchors-across-devices/)voor een voor beeld hiervan.

Maak een exemplaar van een `AnchorLocateCriteria`-object, stel de id's in die u zoekt en roep de `CreateWatcher`-methode aan voor de sessie door uw `AnchorLocateCriteria`op te geven.