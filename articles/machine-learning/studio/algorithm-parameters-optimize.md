---
title: Algoritmen optimaliseren
titleSuffix: ML Studio (classic) - Azure
description: Hierin wordt uitgelegd hoe u de optimale parameterset kiest voor een algoritme in Azure Machine Learning Studio (klassiek).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 11/29/2017
ms.openlocfilehash: ac3aa0dc619ec05dcd79a4f8740026b1eabc19aa
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75427703"
---
# <a name="choose-parameters-to-optimize-your-algorithms-in-azure-machine-learning-studio-classic"></a>Para meters kiezen voor het optimaliseren van uw algoritmen in Azure Machine Learning Studio (klassiek)

In dit onderwerp wordt beschreven hoe u de juiste afstemming kunt instellen voor een algoritme in Azure Machine Learning Studio (klassiek). De meeste machine learning-algoritmen hebben parameters om in te stellen. Wanneer u een model te trainen, moet u waarden opgeven voor deze parameters. De effectiviteit van het getrainde model, is afhankelijk van de Modelparameters die u kiest. Het proces uit het vinden van de optimale set met parameters wordt ook wel *model selectie*.



Er zijn verschillende manieren om Modelleer selectie. In machine learning is Kruis validatie een van de meest gebruikte methoden voor het selecteren van modellen en is het standaard model selectie mechanisme in Azure Machine Learning Studio (klassiek). Omdat Azure Machine Learning Studio (klassiek) zowel R als python ondersteunt, kunt u hun eigen model selectie mechanismen altijd implementeren met behulp van R of python.

Er zijn vier stappen van de beste parameterset zoeken:

1. **Definieer de parameter-ruimte**: voor het algoritme, besluit u eerst de exacte parameterwaarden die u wilt gebruiken.
2. **Definieer de instellingen van de kruisvalidatie**: bepalen hoe u kruisvalidatie vouwen opgeven voor de gegevensset kiezen.
3. **De metrische gegevens definiëren**: Bepaal welke metrische gegevens te gebruiken voor het bepalen van de beste set parameters, zoals nauwkeurigheid, root mean squared fout, precisie, intrekken of f-score.
4. **Trainen, evalueren en vergelijken**: voor elke unieke combinatie van de parameterwaarden kruisvalidatie is uitgevoerd door en op basis van de fout meetwaarde definiëren. Na de evaluatie en vergelijking, kunt u het best presterende model kiezen.

In de volgende afbeelding ziet u hoe dit kan worden bereikt in Azure Machine Learning Studio (klassiek).

![De beste parameterset zoeken](./media/algorithm-parameters-optimize/fig1.png)

## <a name="define-the-parameter-space"></a>De parameter-ruimte definiëren
U kunt de parameter is ingesteld op de stap van de initialisatie van model definiëren. Het deelvenster parameter van alle machine learning-algoritmen beschikt over twee modi voor trainer: *één Parameter* en *Parameter bereik*. Kies de Parameter-bereik-modus. U kunt meerdere waarden voor elke parameter invoeren in de modus van de Parameter-bereik. U kunt met door komma's gescheiden waarden invoeren in het tekstvak in.

![Twee-class boosted-beslisboom, één parameter](./media/algorithm-parameters-optimize/fig2.png)

 U kunt ook kunt u de maximale en minimale punten van het raster en het totale aantal punten moet worden gegenereerd met **gebruik bereik Builder**. Standaard worden de parameterwaarden die zijn gegenereerd op een lineaire schaal. Maar als **logaritmische schaal** is ingeschakeld, wordt de waarden worden gegenereerd in de logaritmische schaal (dat wil zeggen, de verhouding van de aangrenzende punten constant is in plaats van hun verschil). Voor de parameters van geheel getal zijn, kunt u een bereik definiëren met behulp van een afbreekstreepje. Bijvoorbeeld, "1-10" betekent dat alle gehele getallen tussen 1 en 10 (zowel inclusieve) de parameterset vormen. Een gemengde modus wordt ook ondersteund. Bijvoorbeeld, de parameter ingesteld ' 1-10, 20, 50 "zijn gehele getallen van 1-10, 20, en 50.

![Twee-class boosted-beslisboom, parameter-bereik](./media/algorithm-parameters-optimize/fig3.png)

## <a name="define-cross-validation-folds"></a>Kruisvalidatie vouwen definiëren
De [partitie en de voorbeeld][partition-and-sample] module kunnen worden gebruikt om een wille keurige vouw toe te wijzen aan de gegevens. In de volgende voorbeeldconfiguratie voor de module, we definiëren vijf vouwen en willekeurig een aantal vouwen toewijzen aan de voorbeeld-exemplaren.

![Partitie en steekproef](./media/algorithm-parameters-optimize/fig4.png)

## <a name="define-the-metric"></a>De metrische gegevens definiëren
De Hyper parameters-module voor het [afstemmen van modellen][tune-model-hyperparameters] biedt ondersteuning voor het empirisch kiezen van de beste set para meters voor een bepaalde algoritme en gegevensset. Naast andere informatie met betrekking tot het model te trainen de **eigenschappen** deelvenster van deze module bevat de metrische gegevens voor het bepalen van de beste functieset voor de parameter. Twee verschillende vervolgkeuzelijst vakken voor de classificatie- en regressiemodellen algoritmen, heeft respectievelijk. Als de onderzochte-algoritme een classificatiealgoritme is, de metriek regressie wordt genegeerd en vice versa. In dit specifieke voorbeeld de metriek is **nauwkeurigheid**.   

![Parameters zwaaihoek](./media/algorithm-parameters-optimize/fig5.png)

## <a name="train-evaluate-and-compare"></a>Trainen, evalueren en vergelijken
De Hyper parameters-module van hetzelfde [Tune-model][tune-model-hyperparameters] traint alle modellen die overeenkomen met de parameterset, evalueert diverse metrische gegevens en maakt vervolgens het beste getrainde model op basis van de metrische gegevens die u kiest. Deze module heeft twee verplichte invoeren:

* De ongetrainde cursist
* De gegevensset

De module heeft ook een optionele gegevensset de invoer. Verbinding maken met de gegevensset met informatie op de invoer van de verplichte gegevensset vouwen. Als de gegevensset geen gegevens delen niet is toegewezen, klikt u vervolgens een 10-fold kruisvalidatie automatisch standaard uitgevoerd. Als de toewijzing vouwen is niet uitgevoerd en een validatie-gegevensset u aan de poort optioneel gegevensset vindt, een train testmodus is gekozen en de eerste gegevensset wordt gebruikt om te trainen het model voor elke combinatie van parameters.

![Boosted decision tree classificatie](./media/algorithm-parameters-optimize/fig6a.png)

Het model wordt vervolgens geëvalueerd voor de validatie-gegevensset. De uitvoerpoort links van de module ziet u verschillende metrische gegevens als functies van parameterwaarden. De juiste uitvoerpoort biedt het getrainde model dat overeenkomt met de best presterende model op basis van de gekozen metrische gegevens (**nauwkeurigheid** in dit geval).  

![Validatie van gegevensset](./media/algorithm-parameters-optimize/fig6b.png)

Hier ziet u de exacte parameters door het visualiseren van de juiste uitvoerpoort gekozen. Dit model kan worden gebruikt in een testset scoren of in een geoperationaliseerde webservice nadat ze zijn opgeslagen als een getraind model.

<!-- Module References -->
[partition-and-sample]: https://msdn.microsoft.com/library/azure/a8726e34-1b3e-4515-b59a-3e4a475654b8/
[tune-model-hyperparameters]: https://msdn.microsoft.com/library/azure/038d91b6-c2f2-42a1-9215-1f2c20ed1b40/
