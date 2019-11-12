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
ms.openlocfilehash: 1ee90e0c99234497b072bbee0b92d76129baea48
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/12/2019
ms.locfileid: "73929664"
---
Een pijp lijn wordt uitgevoerd op een compute-doel, een reken resource die aan uw werk ruimte is gekoppeld. Nadat u een compute-doel hebt gemaakt, kunt u het opnieuw gebruiken voor toekomstige uitvoeringen.

1. Selecteer **uitvoeren** boven aan het canvas om de pijp lijn uit te voeren.

1. Wanneer het deel venster **instellingen** wordt weer gegeven, selecteert u **reken doel selecteren**.

    Als u al een beschik bare Compute-doel hebt, kunt u dit selecteren om deze pijp lijn uit te voeren.

    > [!NOTE]
    > De ontwerper kan experimenten alleen uitvoeren op Azure Machine Learning Compute-doelen. Andere reken doelen worden niet weer gegeven.

1. Voer een naam in voor de reken resource.

1. Selecteer **Opslaan**.

    ![Berekenings doel instellen](./media/aml-ui-create-training-compute/set-compute.png)

1. Selecteer **Uitvoeren**.

1. Selecteer in het dialoog venster **pijplijn uitvoering instellen** **+ Nieuw experiment** voor het **experiment**.

    > [!NOTE]
    > Experimenten groeperen gelijksoortige pijp lijnen worden uitgevoerd. Als u een pijp lijn meerdere keren uitvoert, kunt u hetzelfde experiment selecteren voor opeenvolgende uitvoeringen.

    1. Voer een beschrijvende naam in voor de naam van het **experiment**.

    1. Selecteer **Uitvoeren**.
    
    U kunt de uitvoerings status en-gegevens weer geven in de rechter bovenhoek van het canvas.

    > [!NOTE]
    > Het duurt ongeveer vijf minuten om een reken resource te maken. Nadat de resource is gemaakt, kunt u deze opnieuw gebruiken en deze wacht tijd overs laan voor toekomstige uitvoeringen.
    >
    > De reken resource wordt automatisch geschaald naar nul knoop punten wanneer het inactief is om kosten te besparen. Wanneer u het opnieuw gebruikt na een vertraging, kunt u ongeveer vijf minuten wachten terwijl de back-up wordt geschaald.
