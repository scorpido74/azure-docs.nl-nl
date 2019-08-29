---
title: Overzicht van gegevens stroom voor Azure Data Factory toewijzing
description: Een overzicht van de toewijzing van gegevens stromen in Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: 6f4c124c59584c8538d85ac61650661ae559a77b
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70123947"
---
# <a name="what-are-mapping-data-flows"></a>Wat zijn gegevens stromen toewijzen?

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Het toewijzen van gegevens stromen is visueel ontworpen gegevens transformatie in Azure Data Factory. Met gegevens stromen kunnen data engineers grafische gegevens transformatie logica ontwikkelen zonder code te schrijven. De resulterende gegevens stromen worden uitgevoerd als activiteiten binnen Azure Data Factory pijp lijnen met behulp van uitgeschaalde Azure Databricks clusters.

De bedoeling van Azure Data Factory gegevens stroom is een volledig visuele ervaring te bieden zonder code ring vereist. Uw gegevens stromen worden uitgevoerd op uw eigen uitvoerings cluster voor uitgeschaalde gegevens verwerking. Azure Data Factory verwerkt alle code omzetting, optimalisatie van paden en de uitvoering van uw gegevens stroom taken.

Begin met het maken van gegevens stromen in de foutopsporingsmodus zodat u uw transformatie logica interactief kunt valideren. Voeg vervolgens een gegevensstroom activiteit aan uw pijp lijn toe om de gegevens stroom in de fout opsporing voor de pijp lijn uit te voeren en te testen, of gebruik nu activeren in de pijp lijn om uw gegevens stroom te testen vanuit een pijplijn activiteit.

U kunt vervolgens uw activiteiten voor gegevens stromen plannen en bewaken met behulp van Azure Data Factory pijp lijnen die de activiteit van de gegevens stroom uitvoeren.

Met de wissel knop voor de foutopsporingsmodus in het ontwerp oppervlak voor de gegevens stroom kunt u op interactieve wijze gegevens transformaties maken. De foutopsporingsmodus biedt een omgeving voor gegevens voorbereiding en gegevens voorbeeld voor de constructie van gegevens stromen.

## <a name="begin-building-your-data-flow-logical-graph"></a>Beginnen met het bouwen van de logische grafiek van uw gegevens stroom

Begin met het bouwen van gegevens stromen met behulp van het plus teken onder Factory resources om een nieuwe gegevens stroom te maken.

![nieuwe gegevens stroom](media/data-flow/newdataflow2.png "nieuwe gegevens stroom")

Begin met het configureren van de bron transformatie en voeg vervolgens gegevens transformatie toe aan elke volgende stap met behulp van het plus teken. Terwijl u uw logische grafiek bouwt, kunt u scha kelen tussen de grafiek-en configuratie modus met behulp van de knop diagram weer geven en de grafiek verbergen.

![Grafiek weer geven](media/data-flow/showg.png "Grafiek weer geven")

## <a name="configure-transformation-logic"></a>Transformatie logica configureren

![Grafiek verbergen](media/data-flow/hideg.png "Grafiek verbergen")

Door uw grafiek te verbergen, kunt u later door uw transformatie knooppunten navigeren.

![Navigeren](media/data-flow/showhide.png "navigeren")

## <a name="next-steps"></a>Volgende stappen

* [Beginnen met een bron transformatie](data-flow-source.md)
