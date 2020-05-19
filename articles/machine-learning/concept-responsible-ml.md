---
title: Verantwoordelijke Machine Learning (ML)
titleSuffix: Azure Machine Learning
description: Meer informatie over de belaste ML en hoe u deze kunt gebruiken in Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: luquinta
author: luisquintanilla
ms.date: 05/08/2020
ms.openlocfilehash: 3cef3c2179019f6d84de5596e61abaf8d7d3182c
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83597658"
---
# <a name="responsible-machine-learning-ml"></a>Verantwoordelijke Machine Learning (ML)

In dit artikel vindt u informatie over de verantwoordelijke MILLILITERs en manieren waarop u deze in de praktijk kunt plaatsen met Azure Machine Learning.

Tijdens de ontwikkeling en het gebruik van AI-systemen moet de vertrouwens relatie de kern zijn. Vertrouwen in het platform, het proces en de modellen. Bij micro soft omvatten de verantwoordelijke MILLILITERs de volgende waarden en principes:

- machine learning modellen begrijpen
  - Model gedrag interpreteren en uitleggen
  - Model oneerlijkheid beoordelen en beperken
- Personen en hun gegevens beveiligen
  - Voor komen dat gegevens bloot vallen met differentiële privacy  
- Het end-to-end-machine learning proces beheren
  - De levens cyclus van machine learning documenteren met gegevens bladen

:::image type="content" source="media/concept-responsible-ml/responsible-ml-pillars.png" alt-text="Verantwoordelijke pijlers":::

Aangezien kunst matige intelligentie en autonome systemen meer kunnen worden geïntegreerd in de infra structuur van de samenleving, is het belang rijk om proactief een inspanning te maken en de onbedoelde gevolgen van deze technologieën te verhelpen.

## <a name="interpret-and-explain-model-behavior"></a>Model gedrag interpreteren en uitleggen

Het is moeilijk om uitleg of Black-Box systemen te geven, omdat het moeilijk is voor belanghebbenden zoals systeem ontwikkelaars, regelgevende instanties, gebruikers en besluit vormers om te begrijpen waarom systemen bepaalde beslissingen nemen. Sommige AI-systemen zijn beter verklaren dan andere en er is soms een afweging tussen een systeem met nauw keurigheid en een meer uitleg.

Gebruik [InterpretML](https://github.com/interpretml/interpret), een open-source pakket dat is gebouwd door micro soft, voor het bouwen van interpretatieve AI-systemen. [InterpretML kan worden gebruikt in azure machine learning](how-to-machine-learning-interpretability.md) voor [het interpreteren en uitleggen van uw machine learning modellen](how-to-machine-learning-interpretability-aml.md), inclusief [geautomatiseerde machine learning modellen](how-to-machine-learning-interpretability-automl.md).

## <a name="assess-and-mitigate-model-unfairness"></a>Model oneerlijkheid beoordelen en beperken

Naarmate AI-systemen meer betrokken zijn bij de alledaagse besluit vorming van de samenleving, is het uiterst belang rijk dat deze systemen goed werken voor het leveren van eerlijke resultaten voor iedereen.

Oneerlijkheid in AI-systemen kan de volgende onbedoelde gevolgen hebben:

- Het inhouden van verkoop kansen, resources of informatie van personen.
- Versterking van BIASS en stereo typen.

Veel aspecten van verdeling kunnen niet worden vastgelegd of vertegenwoordigd door metrische gegevens. Er zijn hulpprogram ma's en procedures waarmee verdeling in het ontwerp en de ontwikkeling van AI-systemen kunnen worden verbeterd.

Er zijn twee belang rijke stappen om de oneerlijkheid in AI-systemen te verminderen. We raden [FairLearn](https://github.com/fairlearn/fairlearn)aan, een open-source pakket waarmee de potentiële oneerlijkheid van AI-systemen kan worden beoordeeld en verholpen. Zie het [artikel verdeling in ml](./concept-fairness-ml.md)voor meer informatie over verdeling en het FairLearn-pakket.

## <a name="prevent-data-exposure-with-differential-privacy"></a>Voor komen dat gegevens bloot vallen met differentiële privacy

Wanneer gegevens worden gebruikt voor analyse, is het belang rijk dat de gegevens persoonlijk en vertrouwelijk blijven in het gebruik. Differentiële privacy is een verzameling systemen en procedures die u helpen de gegevens van personen veilig en privé te houden.

In traditionele scenario's worden onbewerkte gegevens opgeslagen in bestanden en data bases. Wanneer gebruikers gegevens analyseren, gebruiken ze doorgaans de onbewerkte gegevens. Dit is een probleem omdat dit kan leiden tot inbreuk op de privacy van een persoon. Differentiële privacy probeert dit probleem te verhelpen door "Noise" of wille keurigheid toe te voegen aan de gegevens, zodat gebruikers geen afzonderlijke gegevens punten kunnen identificeren.

Het implementeren van differentiatie van een differentieel particulier systeem is lastig. [WhiteNoise](https://github.com/opendifferentialprivacy/whitenoise-core) is een open-source project dat verschillende onderdelen bevat voor het bouwen van wereld wijde differentiatie systemen. Voor meer informatie over de differentiële privacy en het WhiteNoise-project raadpleegt u het artikel privacy [beschermen met behulp van differentiële privacy en WhiteNoise](./concept-differential-privacy.md) .

## <a name="document-the-machine-learning-lifecycle-with-datasheets"></a>De levens cyclus van machine learning documenteren met gegevens bladen

Het documenteren van de juiste informatie in het machine learning proces is essentieel voor het maken van verantwoordelijke beslissingen in elke fase. Gegevens bladen zijn een manier om machine learning activa te documenteren die worden gebruikt en gemaakt als onderdeel van de levens cyclus van machine learning.

Modellen worden vaak beschouwd als zwarte vakjes en er is slechts weinig informatie over. Omdat machine learning systemen steeds betrouwbaarder worden en worden gebruikt voor het nemen van beslissingen, is het gebruik van gegevens bladen een stap voor het ontwikkelen van meer verantwoordelijke machine learning systemen.

Sommige model gegevens die u mogelijk wilt documenteren als onderdeel van een gegevens blad:

- Beoogd gebruik
- Model architectuur
- Gebruikte trainings gegevens
- Gebruikte evaluatie gegevens
- Metrische prestatie gegevens van trainings model
- Verdeling-gegevens.

Raadpleeg het volgende voor beeld om te leren hoe u de Azure Machine Learning SDK kunt gebruiken om [gegevens bladen voor modellen](https://github.com/microsoft/MLOps/blob/master/pytorch_with_datasheet/model_with_datasheet.ipynb)te implementeren.

## <a name="additional-resources"></a>Aanvullende bronnen

- Meer informatie over de set met richt lijnen [over milliliters](https://www.partnershiponai.org/about-ml/) voor machine learning systeem documentatie.