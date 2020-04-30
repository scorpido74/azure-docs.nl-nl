---
title: Invoer/uitvoer van weservice
description: Meer informatie over de webservice-modules in Azure Machine Learning Designer (preview)
titleSuffix: Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/13/2020
ms.openlocfilehash: be940e3ba693270707e22ffc7b9377dbea1df5f8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81462085"
---
# <a name="web-service-inputoutput"></a>Invoer/uitvoer van weservice

In dit artikel worden de module voor het **invoeren van webservices** en de module **webservice-uitvoer** beschreven in azure machine learning Designer (preview).

De module voor het **invoeren van webservices** kan alleen verbinding maken met de invoer poort van het type **DataFrameDirectory**. De module voor het **uitvoeren van webservice-Services** kan alleen worden verbonden vanuit een uitvoer poort met het type **DataFrameDirectory**. De twee modules vindt u in de module structuur onder **Web Service** Category. 

De module **Web Service-invoer** wordt gebruikt om aan te geven waar de gebruikers gegevens de pijp lijn en de **webservice-uitvoer** module invoeren, worden gebruikt om aan te geven waar de gebruikers gegevens worden geretourneerd in een real-time pipeline-pijp lijn.

## <a name="how-to-use-web-service-inputoutput"></a>Het gebruik van web service-invoer/-uitvoer

- Wanneer u een real-time pijp lijn van de trainings pijplijn maakt, worden de **Web Service-invoer** en de **webservice-uitvoer** module automatisch toegevoegd om aan te geven waar de gebruikers gegevens de pijp lijn binnenkomen en waar gegevens worden geretourneerd. 

    Meer informatie over [het maken van een real-time pipeline-pijp lijn](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-deploy#create-a-real-time-inference-pipeline).

    > [!NOTE]
    > Het automatisch genereren van een real-time pipeline-pijp lijn is een op regels gebaseerd proces voor Best effort, er is geen garantie voor de juistheid. U kunt hand matig modules voor **Web Service-invoer/uitvoer** toevoegen of verwijderen om te voldoen aan uw vereisten. Zorg ervoor dat er ten minste één **webservice-invoer** module en één **webservice-uitvoer** module in uw realtime-uitstel pijplijn. Als u meerdere **webservice-invoer** of **Web Service-uitvoer** modules hebt, moet u ervoor zorgen dat ze unieke namen hebben, die u de naam in het rechterdeel venster van de module kunt invoeren.

- U kunt ook hand matig een real-time pijp lijn maken door de modules **Web Service input** en **Web Service output** toe te voegen aan uw niet-verzonden pijp lijn.

    > [!NOTE]
    >  Het type pijp lijn wordt bepaald wanneer u het voor het eerst verzendt. Zorg er daarom voor dat u de module **webservice-invoer** en **webservice-uitvoer** toevoegt voordat u voor de eerste keer verzendt als u een real-time pijp lijn wilt maken.

   In het onderstaande voor beeld ziet u hoe u hand matig een real-time-pijp lijn kunt maken op basis van de script module voor het **uitvoeren van python** . 

   ![Voorbeeld](media/module/web-service-input-output-example.png)
   
   Nadat u de pijp lijn hebt verzonden en de uitvoering is voltooid, kunt u het real-time-eind punt implementeren.
   
   > [!NOTE]
   >  In het bovenstaande voor beeld geeft u **gegevens hand matig** op voor het gegevens schema voor de invoer van de webservice. Dit is nodig voor de implementatie van het real-time-eind punt. Over het algemeen moet u altijd een module of gegevensset verbinden met de poort waarmee de invoer van de **webservice** is verbonden om het gegevens schema op te geven.
   
## <a name="next-steps"></a>Volgende stappen
Meer informatie over [het implementeren van het real-time-eind punt](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-deploy#deploy-the-real-time-endpoint).
Bekijk de [set met modules die beschikbaar zijn](module-reference.md) voor Azure machine learning.