---
title: Invoer/uitvoer van webservice
description: Meer informatie over de webservicemodules in Azure Machine Learning designer (preview)
titleSuffix: Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/13/2020
ms.openlocfilehash: be940e3ba693270707e22ffc7b9377dbea1df5f8
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81462085"
---
# <a name="web-service-inputoutput"></a>Invoer/uitvoer van webservice

In dit artikel worden **de webserviceinvoermodule** en **de webserviceuitvoermodule** in Azure Machine Learning-ontwerper (voorbeeld) beschreven.

**WebService-invoermodule** kan alleen verbinding maken met de invoerpoort met het type **DataFrameDirectory**. En **de webserviceuitvoermodule** kan alleen worden verbonden vanuit de uitvoerpoort met het type **DataFrameDirectory**. De twee modules zijn te vinden in de modulestructuur, onder de categorie **WebService.** 

**Web Service Input** module wordt gebruikt om aan te geven waar gebruikersgegevens de pijplijn binnenkomen en **Web Service Output** module wordt gebruikt om aan te geven waar gebruikersgegevens worden geretourneerd in een Real-time gevolgtrekking Pijplijn.

## <a name="how-to-use-web-service-inputoutput"></a>Input/uitvoer van webservice gebruiken

- Wanneer u een real-time gevolgtrekkingspijplijn maakt vanuit uw trainingspijplijn, worden de **webserviceinvoer-** en **webserviceuitvoermodule** automatisch toegevoegd om aan te geven waar gebruikersgegevens de pijplijn binnenkomen en waar gegevens worden geretourneerd. 

    Meer informatie over [het maken van een realtime gevolgtrekkingspijplijn](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-deploy#create-a-real-time-inference-pipeline).

    > [!NOTE]
    > Automatisch genereren van real-time gevolgtrekking pijplijn is een regel-gebaseerde best-effort proces, is er geen garantie voor de juistheid. U handmatig **webserviceinvoer-/uitvoermodules** toevoegen of verwijderen om aan uw vereisten te voldoen. Zorg ervoor dat er ten minste één **Web Service Input-module** en één **Web Service Output-module** in uw realtime gevolgtrekkingspijplijn staan. Als u meerdere **webserviceinvoer-** of **webserviceuitvoermodules** hebt, moet u ervoor zorgen dat deze unieke namen hebben, waarmee u de naam in het rechterdeelvenster van de module invoeren.

- U ook handmatig een pijplijn voor realtime gevolgtrekkingen maken door **modules voor webserviceinvoer** en **webserviceuitvoer** toe te voegen aan uw niet-ingediende pijplijn.

    > [!NOTE]
    >  Het type pijplijn wordt bepaald bij de eerste keer dat u het indient. Zorg er dus voor dat u de module **WebService-invoer** en **Webservice uitvoer** toevoegt voordat u voor de eerste keer een realtime inferencepijplijn wilt maken.

   Hieronder voorbeeld laat zien hoe u handmatig real-time gevolgtrekkingpijplijn maakt vanuit **de Python Script-module uitvoeren.** 

   ![Voorbeeld](media/module/web-service-input-output-example.png)
   
   Nadat u de pijplijn hebt verzonden en de run is voltooid, u het realtime eindpunt implementeren.
   
   > [!NOTE]
   >  In het bovenstaande voorbeeld biedt **Gegevens handmatig** het gegevensschema voor webserviceinvoer en is het noodzakelijk voor het implementeren van het realtime eindpunt. Over het algemeen moet u altijd een module of gegevensset verbinden met de poort die **webserviceinvoer** is verbonden om het gegevensschema op te geven.
   
## <a name="next-steps"></a>Volgende stappen
Meer informatie over [het implementeren van het realtime eindpunt](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-deploy#deploy-the-real-time-endpoint).
Bekijk de [set modules die beschikbaar zijn](module-reference.md) voor Azure Machine Learning.