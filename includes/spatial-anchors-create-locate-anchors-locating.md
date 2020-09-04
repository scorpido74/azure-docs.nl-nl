---
ms.openlocfilehash: 4df49b4e68769a907423c4edef9f8820df4d20b7
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/25/2020
ms.locfileid: "83006446"
---
## <a name="locate-a-cloud-spatial-anchor"></a>Een ruimtelijk anker in de cloud lokaliseren

Een van de belangrijkste redenen om Azure Spatial Anchors te gebruiken, is dat u een eerder opgeslagen ruimtelijk anker in de cloud kunt lokaliseren. Er zijn verschillende manieren waarop u een ruimtelijk anker in de cloud kunt lokaliseren. Voor een watcher kunt u één strategie per keer gebruiken.
- Lokaliseer ankers op id.
- Lokaliseer ankers die zijn verbonden met een eerder gelokaliseerd anker. U vindt [hier](/azure/spatial-anchors/concepts/anchor-relationships-way-finding/) informatie over ankerrelaties.
- Lokaliseer een anker met [grove herlokalisatie](/azure/spatial-anchors/concepts/coarse-reloc/).

> [!NOTE]
> Telkens wanneer u een anker lokaliseert, probeert Azure Spatial Anchors de omgevingsgegevens te gebruiken die zijn verzameld om de visuele informatie op het anker te verbeteren. Als u problemen ondervindt bij het lokaliseren van een anker, kan het nuttig zijn om een anker te maken en deze vervolgens meerdere keren vanuit verschillende hoeken en lichtomstandigheden te lokaliseren.

Als u ruimtelijke ankers in de cloud lokaliseert op id, moet u deze id in de back-endservice van de toepassing opslaan en toegankelijk maken voor alle apparaten die er op de juiste manier kunnen worden geverifieerd. Voor een voorbeeld hiervan raadpleegt u [Zelfstudie: ruimtelijke ankers met meerdere apparaten delen](/azure/spatial-anchors/tutorials/tutorial-share-anchors-across-devices/).

Instantieer een `AnchorLocateCriteria`-object, stel de id's in die u zoekt en roep de `CreateWatcher`-methode voor de sessie aan door uw `AnchorLocateCriteria` op te geven.