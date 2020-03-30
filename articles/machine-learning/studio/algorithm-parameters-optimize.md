---
title: Algoritmen optimaliseren
titleSuffix: ML Studio (classic) - Azure
description: Hier wordt uitgelegd hoe u de optimale parameterset kiest voor een algoritme in Azure Machine Learning Studio (klassiek).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 11/29/2017
ms.openlocfilehash: 04148b482cb07665f43df5bd86a77175cbbaf08b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218273"
---
# <a name="choose-parameters-to-optimize-your-algorithms-in-azure-machine-learning-studio-classic"></a>Parameters kiezen om uw algoritmen te optimaliseren in Azure Machine Learning Studio (klassiek)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

In dit onderwerp wordt beschreven hoe u de juiste hyperparameterset kiest voor een algoritme in Azure Machine Learning Studio (klassiek). De meeste machine learning-algoritmen hebben parameters om in te stellen. Wanneer u een model traint, moet u waarden opgeven voor deze parameters. De werkzaamheid van het getrainde model is afhankelijk van de modelparameters die u kiest. Het proces van het vinden van de optimale set parameters staat bekend als *modelselectie.*



Er zijn verschillende manieren om modelselectie te doen. Bij machine learning is cross-validatie een van de meest gebruikte methoden voor modelselectie en het standaardmodelselectiemechanisme in Azure Machine Learning Studio (klassiek). Omdat Azure Machine Learning Studio (klassiek) zowel R als Python ondersteunt, u altijd hun eigen modelselectiemechanismen implementeren met Behulp van R of Python.

Er zijn vier stappen in het proces van het vinden van de beste parameter set:

1. **De parameterruimte definiëren:** Bepaal voor het algoritme eerst de exacte parameterwaarden die u wilt overwegen.
2. **De instellingen voor crossvalidatie definiëren:** bepaal hoe u kruisvalidatievouwen voor de gegevensset kiest.
3. **De statistiek definiëren:** bepaal welke statistiek u wilt gebruiken voor het bepalen van de beste set parameters, zoals nauwkeurigheid, hoofdgemiddelde kwadraatfout, precisie, terugroep of f-score.
4. **Trainen, evalueren en vergelijken:** Voor elke unieke combinatie van de parameterwaarden wordt cross-validatie uitgevoerd door en op basis van de foutstatistiek die u definieert. Na evaluatie en vergelijking u het best presterende model kiezen.

De volgende afbeelding laat zien hoe dit kan worden bereikt in Azure Machine Learning Studio (klassiek).

![De beste parameterset zoeken](./media/algorithm-parameters-optimize/fig1.png)

## <a name="define-the-parameter-space"></a>De parameterruimte definiëren
U de parameterset definiëren bij de modelinitialisatiestap. Het parametervenster van alle machine learning-algoritmen heeft twee trainermodi: *Single Parameter* en *Parameter Range*. Kies parameterbereikmodus. In de parameterbereikmodus u meerdere waarden voor elke parameter invoeren. U door komma's gescheiden waarden invoeren in het tekstvak.

![Tweeklassen versterkte beslissingsstructuur, enkele parameter](./media/algorithm-parameters-optimize/fig2.png)

 U afwisselend de maximale en minimale punten van het raster definiëren en het totale aantal punten dat moet worden gegenereerd met **De Opbouw van het bereik gebruiken.** Standaard worden de parameterwaarden gegenereerd op een lineaire schaal. Maar als **logboekschaal** wordt gecontroleerd, worden de waarden gegenereerd in de logboekschaal (dat wil zeggen dat de verhouding van de aangrenzende punten constant is in plaats van hun verschil). Voor gehele parameters u een bereik definiëren met behulp van een koppelteken. '1-10' betekent bijvoorbeeld dat alle gehele getallen tussen 1 en 10 (beide inclusief) de parameterset vormen. Een gemengde modus wordt ook ondersteund. De parameterset '1-10, 20, 50' bevat bijvoorbeeld gehele getallen 1-10, 20 en 50.

![Tweeklassen versterkte beslissingsstructuur, parameterbereik](./media/algorithm-parameters-optimize/fig3.png)

## <a name="define-cross-validation-folds"></a>Kruisvalidatievouwen definiëren
De [partitie- en voorbeeldmodule][partition-and-sample] kan worden gebruikt om willekeurig plooien aan de gegevens toe te wijzen. In de volgende voorbeeldconfiguratie voor de module definiëren we vijf vouwen en wijzen we willekeurig een vouwnummer toe aan de voorbeeldinstanties.

![Partitie en voorbeeld](./media/algorithm-parameters-optimize/fig4.png)

## <a name="define-the-metric"></a>De statistiek definiëren
De [module Hyperparameters van tunemodel][tune-model-hyperparameters] biedt ondersteuning voor het empirisch kiezen van de beste set parameters voor een bepaald algoritme en gegevensset. Naast andere informatie over het trainen van het model, bevat het deelvenster **Eigenschappen** van deze module de statistiek voor het bepalen van de beste parameterset. Het heeft twee verschillende vervolgkeuzelijsten voor classificatie- en regressiealgoritmen. Als het onderzochte algoritme een classificatiealgoritme is, wordt de regressiestatistiek genegeerd en vice versa. In dit specifieke voorbeeld is de statistiek **Nauwkeurigheid**.   

![Parameters vegen](./media/algorithm-parameters-optimize/fig5.png)

## <a name="train-evaluate-and-compare"></a>Trainen, evalueren en vergelijken
Dezelfde [Tune Model Hyperparameters][tune-model-hyperparameters] module traint alle modellen die overeenkomen met de parameterset, evalueert verschillende statistieken en maakt vervolgens het best getrainde model op basis van de statistiek die u kiest. Deze module heeft twee verplichte ingangen:

* De ongetrainde leerling
* De gegevensset

De module heeft ook een optionele gegevenssetinvoer. Verbind de gegevensset met vouwgegevens met de verplichte gegevenssetinvoer. Als de gegevensset geen vouwgegevens wordt toegewezen, wordt een 10-voudige kruisvalidatie standaard automatisch uitgevoerd. Als de vouwtoewijzing niet wordt uitgevoerd en er een validatiegegevensset wordt geleverd op de optionele gegevenssetpoort, wordt een treintestmodus gekozen en wordt de eerste gegevensset gebruikt om het model voor elke parametercombinatie te trainen.

![Verhoogde beslissing boom classifier](./media/algorithm-parameters-optimize/fig6a.png)

Het model wordt vervolgens geëvalueerd op de validatiegegevensset. De linkeruitvoerpoort van de module toont verschillende statistieken als functies van parameterwaarden. De juiste uitvoerpoort geeft het getrainde model dat overeenkomt met het best presterende model volgens de gekozen statistiek **(Nauwkeurigheid** in dit geval).  

![Gegevensset Voor validatie](./media/algorithm-parameters-optimize/fig6b.png)

U de exacte parameters zien die zijn gekozen door de juiste uitvoerpoort te visualiseren. Dit model kan worden gebruikt bij het scoren van een testset of in een geoperationaliseerde webservice na het opslaan als een getraind model.

<!-- Module References -->
[partition-and-sample]: https://msdn.microsoft.com/library/azure/a8726e34-1b3e-4515-b59a-3e4a475654b8/
[tune-model-hyperparameters]: https://msdn.microsoft.com/library/azure/038d91b6-c2f2-42a1-9215-1f2c20ed1b40/
