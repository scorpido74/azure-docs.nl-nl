---
title: Voeg gegevens sets toe aan een bestaande gegevens share in azure data share preview
description: Voeg gegevens sets toe aan een bestaande gegevens share
author: madams0013
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: jasonh
ms.openlocfilehash: 75ac837491927881fd8b7547b0f49a069c3f2d47
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70257231"
---
# <a name="how-to-add-datasets-to-an-existing-share-in-azure-data-share-preview"></a>Gegevens sets toevoegen aan een bestaande share in azure data share preview

In dit artikel wordt uitgelegd hoe u gegevens sets kunt toevoegen aan een bestaande gegevens share met behulp van de preview-versie van Azure data share. Hierdoor kunt u meer gegevens delen met dezelfde ontvangers zonder dat u een nieuwe share hoeft te maken.

Voor informatie over het toevoegen van gegevens sets wanneer u een share maakt, raadpleegt u de zelf studie [Data delen](share-your-data.md) .

## <a name="navigate-to-a-sent-data-share"></a>Navigeer naar een verzonden gegevens share

In azure data share preview gaat u naar de verzonden share en selecteert u het tabblad **gegevens sets** . Klik op de knop **gegevens sets toevoegen** om meer gegevens sets toe te voegen.

![Gegevens sets toevoegen](./media/how-to/how-to-add-datasets/add-datasets.png)

Selecteer in het deel venster aan de rechter kant het type gegevensset dat u wilt toevoegen en klik vervolgens op **volgende**. Selecteer het abonnement en de resource groep van de gegevens die u wilt toevoegen. Zoek met de vervolg keuzelijst pijlen het selectie vakje naast de gegevens die u wilt toevoegen.

![Gegevens sets toevoegen](./media/how-to/how-to-add-datasets/add-datasets-side.png)

Nadat u op **gegevens sets toevoegen**hebt geklikt, worden de gegevens sets toegevoegd aan uw share. Opmerking: Een moment opname moet worden geactiveerd door uw consumenten zodat ze de nieuwe gegevens sets kunnen zien. Als er moment opname-instellingen zijn geconfigureerd, zien consumenten de nieuwe gegevens sets zodra de volgende geplande moment opname is voltooid. Zonder dat de moment opname-instellingen zijn geconfigureerd, moet de gebruiker hand matig een volledige of incrementele kopie van de gegevens activeren om de updates te ontvangen. Zie [moment opnamen](terminology.md)voor meer informatie over moment opnamen.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over het [toevoegen van ontvangers aan een bestaande gegevens share](how-to-add-recipients.md).