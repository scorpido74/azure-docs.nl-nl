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
ms.openlocfilehash: 28478f38df6ba6ea356626cc36c23ad498fc1f47
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/21/2019
ms.locfileid: "72692259"
---
Een pijp lijn wordt uitgevoerd op een compute-doel, een reken resource die aan uw werk ruimte is gekoppeld.  Wanneer u een compute-doel hebt gemaakt, kunt u het opnieuw gebruiken voor toekomstige uitvoeringen.

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

    * Voer een beschrijvende **naam** voor het experiment in

    * Selecteer **uitvoeren**
    
    U kunt de uitvoerings status en-gegevens weer geven in de rechter bovenhoek van het canvas.

    > [!NOTE]
    > Het duurt ongeveer vijf minuten om een reken resource te maken. Nadat de resource is gemaakt, kunt u deze opnieuw gebruiken en deze wacht tijd overs laan voor toekomstige uitvoeringen.
    >
    > De reken resource schaalt automatisch naar 0 knoop punten wanneer het inactief is om kosten te besparen.  Wanneer u het opnieuw gebruikt na een vertraging, is het mogelijk dat u nog steeds ongeveer 5 minuten wacht tijd hebt terwijl de back-up wordt geschaald.
