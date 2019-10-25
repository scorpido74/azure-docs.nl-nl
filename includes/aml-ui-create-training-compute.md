---
title: bestand opnemen
description: bestand opnemen
services: machine-learning
author: peterclu
ms.service: machine-learning
ms.author: peterlu
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 10/09/2019
ms.openlocfilehash: 3f969fb346ce341e694458dcc38bf193f012226b
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792634"
---
Een pijp lijn wordt uitgevoerd op een compute-doel dat een reken resource is die aan uw werk ruimte is gekoppeld. Wanneer u een compute-doel hebt gemaakt, kunt u het opnieuw gebruiken voor toekomstige uitvoeringen.

1. Selecteer **uitvoeren** boven aan het canvas om de pijp lijn uit te voeren.

1. Wanneer het deel venster **instellingen** wordt weer gegeven, selecteert u **reken doel selecteren**.

    Als u al een beschik bare Compute-doel hebt, kunt u dit selecteren om deze pijp lijn uit te voeren.

    > [!NOTE]
    > Met de visuele interface kunnen alleen experimenten op Machine Learning Compute doelen worden uitgevoerd. Andere compute-doelen worden niet weer gegeven.

1. Geef een naam op voor de reken resource.

1. Selecteer **Opslaan**.

    ![Berekenings doel instellen](./media/aml-ui-create-training-compute/set-compute.png)

1. Selecteer **Uitvoeren**.

1. Selecteer in het dialoog venster **pijplijn uitvoering instellen** **+ Nieuw experiment** voor het **experiment**

    > [!NOTE]
    > Experimenten groeperen gelijksoortige pijp lijnen worden uitgevoerd. Als u een pijp lijn meerdere keren uitvoert, kunt u hetzelfde experiment selecteren voor opeenvolgende uitvoeringen.

    * Voer een beschrijvende **naam** voor het experiment in

    * Selecteer **uitvoeren**
    
    U kunt de uitvoerings status en-gegevens weer geven in de rechter bovenhoek van het canvas.

    > [!NOTE]
    > Het duurt ongeveer vijf minuten om een reken resource te maken. Nadat de resource is gemaakt, kunt u deze opnieuw gebruiken en deze wacht tijd overs laan voor toekomstige uitvoeringen.
    >
    > De reken resource schaalt automatisch naar 0 knoop punten wanneer het inactief is om kosten te besparen.  Wanneer u het opnieuw gebruikt na een vertraging, is het mogelijk dat u nog steeds ongeveer 5 minuten wacht tijd hebt terwijl de back-up wordt geschaald.
