---
title: Wat is Azure Machine Learning
description: 'Overzicht van Azure Machine Learning: een geïntegreerde, end-to-end oplossing voor gegevens wetenschap voor professionele gegevens wetenschappers om geavanceerde analyse toepassingen te ontwikkelen, te experimenteren en te implementeren op Cloud schaal.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
author: j-martens
ms.author: jmartens
ms.date: 08/05/2019
ms.custom: seodec18
ms.openlocfilehash: 0a70b1c9d3ac888f0f77cf0f6e9ca37bc6cd4324
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/15/2019
ms.locfileid: "70999940"
---
# <a name="what-is-azure-machine-learning"></a>Wat is Azure Machine Learning?

Azure Machine Learning is een Cloud service die u gebruikt om machine learning modellen te trainen, te implementeren, te automatiseren en te beheren, allemaal op de grote schaal die de Cloud biedt.

## <a name="what-is-machine-learning"></a>Wat is machine learning?

Machine learning is een techniek van gegevenswetenschap die computers in staat stelt om op basis van bestaande gegevens, toekomstig gedrag, resultaten en trends te voorspellen. Met behulp van machine learning kunnen computers leren zonder dat ze expliciet worden geprogrammeerd.

Prognoses of voorspellingen op basis van machine learning kunnen apps en apparaten slimmer maken. Wanneer u bijvoorbeeld online winkelt, helpt machine learning op basis van wat u eerder hebt gekocht, bij het aanraden van andere producten waarvoor u mogelijk belangstelling hebt. Of wanneer uw creditcard wordt gebruikt, vergelijkt machine learning de transactie met een database van transacties om fraude te detecteren. En wanneer uw robotstofzuiger een kamer stofzuigt, helpt machine learning bij het besluiten wanneer de taak klaar is.

## <a name="what-is-azure-machine-learning"></a>Wat is Azure Machine Learning?

Azure Machine Learning biedt een omgeving in de cloud die u kunt gebruiken voor het voorbereiden van gegevens, het trainen, testen, implementeren, beheren en bijhouden van machine learning modellen. Start training op uw lokale machine en schaal vervolgens naar de Cloud. De service biedt volledige ondersteuning voor open-source technologieën zoals PyTorch, tensor flow en scikit-Learn en kan worden gebruikt voor elk soort machine learning, van klassieke ml tot diep gaande lessen, onder Super visie en zonder toezicht.

Verken en bereid gegevens op, Train en test modellen en implementeer ze met uitgebreide hulpprogram ma's zoals:
+ Een [visuele interface](ui-tutorial-automobile-price-train-score.md) waarin u modules kunt slepen en neerzetten om uw experimenten te ontwikkelen en vervolgens modellen te implementeren
+ [Jupyter-notebooks](https://jupyter.org) waarin u de [sdk's](https://docs.microsoft.com/azure/machine-learning) gebruikt om uw eigen code te schrijven, zoals [deze voor beelden van notitie blokken](https://aka.ms/aml-notebooks)
+ [Visual Studio code-extensie](how-to-vscode-tools.md)


> [!VIDEO https://channel9.msdn.com/Events/Connect/Microsoft-Connect--2018/D240/player]

## <a name="what-can-i-do-with-azure-machine-learning"></a>Wat kan ik doen met Azure Machine Learning?

Gebruik de <a href="https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py" target="_blank">Azure machine learning PYTHON SDK</a> met open source Python-pakketten, of gebruik de [visuele interface (preview)](ui-tutorial-automobile-price-train-score.md) om in een Azure machine learning-werkruimte zeer nauw keurige machine learning en diep gaande modellen te bouwen en te trainen.

U kunt kiezen uit veel machine learning onderdelen die beschikbaar zijn in open source Python-pakketten, zoals <a href="https://scikit-learn.org/stable/" target="_blank">Scikit-Learn</a>, <a href="https://www.tensorflow.org" target="_blank">tensor flow</a>, <a href="https://pytorch.org" target="_blank">PyTorch</a>en <a href="https://mxnet.io" target="_blank">MXNet</a>.

Of u code schrijft of de visuele interface gebruikt, u kunt meerdere uitvoeringen bijhouden tijdens het experimenteren om de beste oplossing te vinden en de geïmplementeerde modellen te beheren.

### <a name="code-first-experience"></a>Code-eerste ervaring

Start training op uw lokale machine met behulp van de <a href="https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py" target="_blank">Azure machine learning PYTHON SDK</a> en schaal vervolgens naar de Cloud. Dankzij de vele beschikbare [rekendoelen](how-to-set-up-training-targets.md) zoals Azure Machine Learning Compute en [Azure Databricks](/azure/azure-databricks/what-is-azure-databricks), en [geavanceerde afstemmingsservices van hyperparameters](how-to-tune-hyperparameters.md), kunt u sneller betere modellen bouwen, met de kracht van de cloud.

U kunt [model training en-aanpassing ook automatiseren](tutorial-auto-train-models.md) met de SDK.

### <a name="ui-based-low-code-experience"></a>Op de gebruikers interface gebaseerde, ervaring met weinig code

Voor code-Free training kunt u het volgende proberen:

+ Het maken van [automatische ml-experimenten](tutorial-first-experiment-automated-ml.md) in de gebruiks vriendelijke interface.
+ Het [experimenteren met slepen en neerzetten in de visuele interface](ui-tutorial-automobile-price-train-score.md).
  ![Visuele interface voor Azure Machine Learning](media/overview-what-is-azure-ml/visual-interface.png)



### <a name="operationalization-mlops"></a>Uitoefening (MLOps)

Wanneer u het juiste model hebt, kunt u het eenvoudig gebruiken in een webservice, op een IoT-apparaat of in Power BI. Zie het artikel [Hoe implementeren en waar](how-to-deploy-and-where.md) voor meer informatie.

Vervolgens kunt u uw geïmplementeerde modellen beheren met behulp van de [Azure machine learning SDK voor python](https://aka.ms/aml-sdk), de [Azure Portal](https://portal.azure.com/) of de [landings pagina van uw werk ruimte (preview)](https://ml.azure.com).

Deze modellen kunnen worden gebruikt en voor spellingen in [realtime](how-to-consume-web-service.md) of [asynchroon](how-to-run-batch-predictions.md) in grote hoeveel heden gegevens worden geretourneerd.

En met geavanceerde [machine learning pijp lijnen](concept-ml-pipelines.md)kunt u samen werken aan elke stap van het voorbereiden van gegevens, het model leren en evalueren van een cursus, via implementatie. Met pijp lijnen kunt u het volgende doen:

* Het end-to-end-machine learning proces automatiseren in de Cloud
* Onderdelen opnieuw gebruiken en de stappen alleen opnieuw uitvoeren wanneer dit nodig is
* In elke stap verschillende reken bronnen gebruiken
* Batch Score taken uitvoeren

Zie [volgende stappen](#next-steps)om aan de slag te gaan met Azure machine learning.

## <a name="how-does-azure-machine-learning-differ-from-studio"></a>Hoe wijkt Azure Machine Learning af van Studio?

[Machine learning Studio](../studio/what-is-ml-studio.md) is een samen werkende, visuele werk ruimte met slepen en neerzetten, waar u Machine Learning oplossingen kunt bouwen, testen en implementeren zonder dat u code hoeft te schrijven. Hierbij wordt gebruikgemaakt van vooraf gebouwde en vooraf geconfigureerde machine learning-algoritmen en modules voor het verwerken van gegevens, evenals een eigen reken platform.

Azure Machine Learning biedt zowel Sdk's **als** een visuele interface (preview) om snel gegevens te kunnen prepen, machine learning modellen te trainen en te implementeren. Deze visuele interface (preview) biedt een vergelijk bare functionaliteit voor slepen en neerzetten in Studio. Maar in tegens telling tot het eigen reken platform van Studio gebruikt de Visual Interface uw eigen reken resources en is deze volledig geïntegreerd in Azure Machine Learning.

Hier volgt een snelle vergelijking.

|| Machine Learning Studio | Azure Machine Learning:<br/>Visuele interface|
|---| --- | --- |
|| Algemeen beschikbaar (GA) | In preview|
|Modules voor Interface| Allerlei | Initiële set populaire modules|
|Doelen van de trainings compute| Eigen reken doel, alleen CPU-ondersteuning| Ondersteunt Azure Machine Learning compute, GPU of CPU.<br/>(Andere reken processen die worden ondersteund in SDK)|
|Doelen voor implementatie compute| De indeling van de oorspronkelijke webservice, niet aanpasbaar | Beveiligings opties voor ondernemingen & Azure Kubernetes-service. <br/>([Andere reken](how-to-deploy-and-where.md) processen die worden ondersteund in SDK) |
|Automatische model training en afstemming tuning | Nee | Nog niet in de visuele interface. <br/> (Ondersteund op de lands pagina SDK en Azure Portal/Workspace.) |

Probeer de Visual Interface (preview) uit met [de zelf studie: Prijs voor auto Mobile voor speld met](ui-tutorial-automobile-price-train-score.md)de visuele interface.

> [!NOTE]
> Modellen die zijn gemaakt in Studio, kunnen niet worden geïmplementeerd of beheerd door Azure Machine Learning. Modellen die zijn gemaakt en geïmplementeerd in de service Visual Interface kunnen echter worden beheerd via de werk ruimte Azure Machine Learning.

## <a name="free-trial"></a>Gratis proefversie

Als u nog geen Azure-abonnement hebt, maakt u een gratis account voordat u begint. Probeer vandaag nog de [gratis of betaalde versie van Azure machine learning](https://aka.ms/AMLFree) .

U ontvangt tegoed wat u kunt uitgeven aan Azure-services. Als uw tegoed op is, kunt u het account behouden en de [gratis Azure-services](https://azure.microsoft.com/free/) gebruiken. Er worden nooit kosten in rekening gebracht bij uw creditcard tenzij u de instellingen expliciet wijzigt en aangeeft dat u wilt betalen. U kunt ook [de voordelen voor MSDN-abonnees activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F), waardoor u maandelijks tegoeden krijgt die u kunt gebruiken voor betaalde Azure-services.

## <a name="next-steps"></a>Volgende stappen

- [Maak een machine learning service-werk ruimte](how-to-manage-workspace.md) om aan de slag te gaan.

- Volg de volledige zelfstudies:
  + [Een werk ruimte maken en uw eerste ML model trainen](tutorial-1st-experiment-sdk-setup.md)
  + [Een classificatie model voor een installatie kopie trainen met Azure Machine Learning](tutorial-train-models-with-aml.md)


- Leer meer over [Machine Learning-pijplijnen](/azure/machine-learning/service/concept-ml-pipelines) om uw Machine Learning-scenario’s te bouwen, te optimaliseren en te beheren.

- Lees het artikel uitgebreide [Azure machine learning architecturen en concepten](concept-azure-machine-learning-architecture.md) .

- Raadpleeg [andere machine learning-producten van Microsoft](/azure/architecture/data-guide/technology-choices/data-science-and-machine-learning) voor meer informatie.
