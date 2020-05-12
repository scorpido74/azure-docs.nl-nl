---
ms.openlocfilehash: 4df49b4e68769a907423c4edef9f8820df4d20b7
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/10/2020
ms.locfileid: "83006446"
---
## <a name="locate-a-cloud-spatial-anchor"></a>Een ruimtelijke beankering in de Cloud zoeken

Het is een van de belangrijkste redenen voor het gebruik van Azure spatiale ankers om een eerder opgeslagen ruimtelijke bewerkings plaats in de cloud te vinden. Er zijn verschillende manieren waarop u een ruimtelijke ruimte in de cloud kunt vinden. U kunt één strategie per keer gebruiken op een monitor.
- Zoek naar ankers op id.
- Ankers zoeken die zijn verbonden met een eerder gevonden anker. [Hier](/azure/spatial-anchors/concepts/anchor-relationships-way-finding/)vindt u meer informatie over anker relaties.
- Zoek naar een anker met [grof Herlokalisatie](/azure/spatial-anchors/concepts/coarse-reloc/).

> [!NOTE]
> Telkens wanneer u een anker vindt, probeert Azure spatiale ankers de omgevings gegevens te gebruiken die zijn verzameld om de visuele informatie op het anker te verbeteren. Als u problemen ondervindt bij het vinden van een anker, kan het nuttig zijn om een anker te maken en deze vervolgens verschillende keren van verschillende hoeken en belichtings omstandigheden te zoeken.

Als u ruimtelijke beschik bare ruimte in de Cloud zoekt met behulp van id, moet u de id van de in de back-end voor de Cloud beschik bare ruimte opslaan en deze toegankelijk maken voor alle apparaten die op de juiste manier kunnen worden geverifieerd. Zie [zelf studie: ruimtelijke ankers delen tussen apparaten](/azure/spatial-anchors/tutorials/tutorial-share-anchors-across-devices/)voor een voor beeld hiervan.

Maak een `AnchorLocateCriteria` exemplaar van een object, stel de id's in die u zoekt en roep `CreateWatcher` de methode voor de sessie aan door `AnchorLocateCriteria`uw toe te voegen.