---
title: bestand opnemen
description: bestand opnemen
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 05/06/2019
ms.openlocfilehash: eb84dc1b5bf3f756e484ef27aaa998ab6b94cc51
ms.sourcegitcommit: a3a40ad60b8ecd8dbaf7f756091a419b1fe3208e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/21/2019
ms.locfileid: "69891654"
---
Een experiment wordt uitgevoerd op een reken doel, een reken resource die aan uw werk ruimte is gekoppeld.  Wanneer u een compute-doel hebt gemaakt, kunt u het opnieuw gebruiken voor toekomstige uitvoeringen.

1. Selecteer onder **uitvoeren** om het experiment uit te voeren.

1. Wanneer het dialoog venster instellen van COMPUTE- **doelen** wordt weer gegeven, kunt u dit nu selecteren als uw werk ruimte al een reken resource heeft.  Anders selecteert u **nieuwe maken**.

    > [!NOTE]
    > Met de visuele interface kunnen alleen experimenten op Machine Learning Compute doelen worden uitgevoerd. Andere compute-doelen worden niet weer gegeven.

1. Geef een naam op voor de reken resource.

1. Selecteer **Uitvoeren**.

    ![Berekenings doel instellen](./media/aml-ui-create-training-compute/set-compute.png)

    De reken resource wordt nu gemaakt. Bekijk de status in de rechter bovenhoek van het experiment. 

    > [!NOTE]
    > Het duurt ongeveer vijf minuten om een reken resource te maken. Nadat de resource is gemaakt, kunt u deze opnieuw gebruiken en deze wacht tijd overs laan voor toekomstige uitvoeringen.
    >
    > De reken resource schaalt automatisch naar 0 knoop punten wanneer het inactief is om kosten te besparen.  Wanneer u het opnieuw gebruikt na een vertraging, is het mogelijk dat u nog steeds ongeveer 5 minuten wacht tijd hebt terwijl de back-up wordt geschaald.
