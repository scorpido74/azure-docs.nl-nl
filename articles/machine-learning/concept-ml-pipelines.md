---
title: Wat zijn ML-pijplijnen
titleSuffix: Azure Machine Learning
description: Leer in dit artikel de voordelen van de ml-pijplijnen (machine learning) die u bouwen met de Azure Machine Learning SDK voor Python. Machine learning-pijplijnen worden door gegevenswetenschappers gebruikt om hun machine learning-workflows te bouwen, optimaliseren en beheren.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: laobri
author: lobrien
ms.date: 11/06/2019
ms.openlocfilehash: da45c0db027dffc89bd058b70331a4bd6d093b08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336962"
---
# <a name="what-are-azure-machine-learning-pipelines"></a>Wat zijn Azure Machine Learning-pijplijnen?

Met Azure Machine Learning-pijplijnen u werkstromen maken in uw machine learning-projecten. Deze workflows hebben een aantal voordelen: 

+ Eenvoud
+ Snelheid
+ Herhaalbaarheid
+ Flexibiliteit
+ Versiebeheer en tracking
+ Modulariteit 
+ Kwaliteitscontrole
+ Kostenbeheersing

Deze voordelen worden aanzienlijk zodra uw machine learning-project verder gaat dan pure exploratie en in iteratie. Zelfs eenvoudige pijpleidingen in één stap kunnen waardevol zijn. Machine learning-projecten bevinden zich vaak in een complexe toestand en het kan een opluchting zijn om van de precieze verwezenlijking van één workflow een triviaal proces te maken.

Meer informatie over het [maken van uw eerste pijplijn.](how-to-create-your-first-pipeline.md)

![Machine learning-pijplijnen in Azure Machine Learning](./media/concept-ml-pipelines/pipeline-flow.png)

<a name="compare"></a>
### <a name="which-azure-pipeline-technology-should-i-use"></a>Welke Azure-pijplijntechnologie moet ik gebruiken?

De Azure-cloud biedt verschillende andere pijplijnen, elk met een ander doel. In de volgende tabel worden de verschillende pijplijnen weergegeven en waarvoor ze worden gebruikt:

| Scenario | Primaire persona | Azure-aanbod | OSS-aanbod | Canonieke pijp | Sterke punten | 
| -------- | --------------- | -------------- | ------------ | -------------- | --------- | 
| Modelorkestratie (Machine learning) | Gegevenswetenschapper | Azure Machine Learning-pijplijnen | Kubeflow-pijpleidingen | Gegevens -> Model | Distributie, caching, code-first, hergebruik | 
| Gegevensorkestratie (Data prep) | Data engineer | [Azure Data Factory-pijplijnen](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities) | Apache Luchtstroom | Gegevens -> Gegevens | Sterk getypte beweging. Datagerichte activiteiten. |
| Code &-app orchestration (CI/CD) | App-ontwikkelaar / Ops | [Azure DevOps-pijplijnen](https://azure.microsoft.com/services/devops/pipelines/) | Jenkins | Code + Model -> App/Service | Meest open en flexibele activiteitsondersteuning, goedkeuringswachtrijen, fasen met gating | 


## <a name="what-can-azure-ml-pipelines-do"></a>Wat kunnen Azure ML-pijplijnen doen?

Een Azure Machine Learning-pijplijn is een onafhankelijk uitvoerbare werkstroom van een volledige machine learning-taak. Subtaken worden ingekapseld als een reeks stappen in de pijplijn. Een Azure Machine Learning-pijplijn kan zo eenvoudig zijn als een pijplijn die een Python-script aanroept, dus _kan_ zo ongeveer alles doen. Pijplijnen _moeten_ zich richten op machine learning-taken zoals:

+ Gegevensvoorbereiding, waaronder importeren, valideren en opschonen, munging en transformatie, normalisatie en fasering
+ Trainingsconfiguratie met inbegrip van parameteriseren van argumenten, bestandspaden en logboek-/rapportageconfiguratie
+ Efficiënt en herhaaldelijk trainen en valideren. Efficiëntie kan het ontstaan van het specificeren van specifieke gegevenssubsets, verschillende hardwarecompute resources, gedistribueerde verwerking en voortgangsbewaking
+ Implementatie, inclusief versiebeheer, schalen, inrichten en toegangsbeheer 

Met onafhankelijke stappen kunnen meerdere gegevenswetenschappers tegelijkertijd aan dezelfde pijplijn werken zonder rekenresources te overbelasten. Afzonderlijke stappen maken het ook gemakkelijk om verschillende rekentypen/-formaten voor elke stap te gebruiken.

Nadat de pijpleiding is ontworpen, is er vaak meer fine-tuning rond de trainingslus van de pijpleiding. Wanneer u een pijplijn opnieuw uitvoert, springt de run naar de stappen die opnieuw moeten worden uitgevoerd, zoals een bijgewerkt trainingsscript. Stappen die niet opnieuw hoeven te worden uitgevoerd, worden overgeslagen. Dezelfde analyse is van toepassing op ongewijzigde scripts die worden gebruikt voor de uitvoering van de stap. Deze functionaliteit voor hergebruik helpt om te voorkomen dat kostbare en tijdintensieve stappen worden uitgevoerd, zoals het innemen van gegevens en transformatie als de onderliggende gegevens niet zijn gewijzigd.

Met Azure Machine Learning u verschillende toolkits en frameworks, zoals PyTorch of TensorFlow, gebruiken voor elke stap in uw pijplijn. Azure coördineert de verschillende [compute targets](concept-azure-machine-learning-architecture.md) die u gebruikt, zodat uw tussenliggende gegevens kunnen worden gedeeld met de downstream compute targets.

U [de statistieken voor uw pijplijnexperimenten](https://docs.microsoft.com/azure/machine-learning/how-to-track-experiments) rechtstreeks bijhouden in de Azure-portal of uw [bestemmingspagina voor werkruimten (voorbeeld).](https://ml.azure.com) Nadat een pijplijn is gepubliceerd, u een REST-eindpunt configureren, waarmee u de pijplijn vanaf elk platform of stack opnieuw uitvoeren.

Kortom, alle complexe taken van de machine learning levenscyclus kunnen worden geholpen met pijplijnen. Andere Azure-pijplijntechnologieën hebben hun eigen sterke punten, zoals [Azure Data Factory-pijplijnen](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities) voor het werken met gegevens en [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) voor continue integratie en implementatie. Maar als uw focus machine learning is, zijn Azure Machine Learning-pijplijnen waarschijnlijk de beste keuze voor uw werkstroombehoeften. 

## <a name="what-are-azure-ml-pipelines"></a>Wat zijn Azure ML-pijplijnen?

Een Azure ML-pijplijn voert een volledige logische werkstroom uit met een geordende reeks stappen. Elke stap is een discrete verwerkingsactie. Pijplijnen worden uitgevoerd in de context van een Azure Machine Learning [Experiment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py).

In de vroege stadia van een ML-project is het prima om één Jupyter-notitieblok of Python-script te hebben dat al het werk van Azure-werkruimte- en resourceconfiguratie, gegevensvoorbereiding, runconfiguratie, training en validatie doet. Maar net zoals functies en klassen al snel de voorkeur krijgen boven één enkel dwingend codeblok, krijgen ML-workflows al snel de voorkeur boven een monolithisch notitieblok of script. 

Door ml-taken te modulariseren, ondersteunen pijpleidingen de noodzaak van Computer Science dat een component "(slechts) één ding goed moet doen." Modulariteit is duidelijk essentieel voor het project succes bij het programmeren in teams, maar zelfs bij het werken alleen, zelfs een klein ML-project gaat om afzonderlijke taken, elk met een goede hoeveelheid complexiteit. Taken zijn onder andere: configuratie van werkruimtes en toegang tot gegevens, gegevensvoorbereiding, modeldefinitie en -configuratie en implementatie. Terwijl de uitgangen van een of meer taken vormen de ingangen naar een andere, de exacte implementatie details van een taak zijn, op zijn best, irrelevant afleiding in de volgende. In het slechtste geval kan de computationele toestand van de ene taak een bug in een andere veroorzaken. 

### <a name="analyzing-dependencies"></a>Afhankelijkheden analyseren

Veel programmeerecosystemen hebben tools die afhankelijkheden van bronnen, bibliotheken of compilaties orkestreren. Over het algemeen gebruiken deze hulpprogramma's bestandstijdstempels om afhankelijkheden te berekenen. Wanneer een bestand wordt gewijzigd, worden alleen het bestand en de afhankelijken bijgewerkt (gedownload, opnieuw gecompileerd of verpakt). Azure ML-pijplijnen breiden dit concept drastisch uit. Net als traditionele buildtools berekenen pijplijnen afhankelijkheden tussen stappen en voeren ze alleen de noodzakelijke herberekeningen uit. 

De afhankelijkheidsanalyse in Azure ML-pijplijnen is echter geavanceerder dan eenvoudige tijdstempels. Elke stap kan worden uitgevoerd in een andere hardware- en softwareomgeving. Gegevensvoorbereiding kan een tijdrovend proces zijn, maar hoeft niet te worden uitgevoerd op hardware met krachtige GPU's, bepaalde stappen vereisen mogelijk OS-specifieke software, u wilt misschien gedistribueerde training gebruiken, enzovoort. Hoewel de kostenbesparingen voor het optimaliseren van resources aanzienlijk kunnen zijn, kan het overweldigend zijn om handmatig te jongleren met alle verschillende variaties in hardware- en softwarebronnen. Het is nog moeilijker om dat alles te doen zonder ooit een fout te maken in de gegevens die u tussen stappen doorbrengt. 

Pijpleidingen lossen dit probleem op. Azure Machine Learning orkestreert automatisch alle afhankelijkheden tussen pijplijnstappen. Deze orkestratie kan bestaan uit het op en neer draaien van Docker-afbeeldingen, het koppelen en loskoppelen van compute resources en het op een consistente en automatische manier verplaatsen van gegevens tussen de stappen.

### <a name="reusing-results"></a>Resultaten opnieuw gebruiken

Bovendien kan de uitvoer van een stap, indien u dat wilt, opnieuw worden gebruikt. Als u hergebruik als mogelijkheid opgeeft en er geen upstream-afhankelijkheden zijn die een herberekening veroorzaken, gebruikt de pijplijnservice een in de cache opgeslagen versie van de resultaten van de stap. Dergelijk hergebruik kan de ontwikkelingstijd drastisch verkorten. Als u een complexe taak voor het voorbereiden van gegevens hebt, voert u deze waarschijnlijk vaker opnieuw uit dan strikt noodzakelijk is. Pijpleidingen verlichten u van die zorgen: indien nodig, zal de stap worden uitgevoerd, zo niet, zal het niet.

Al deze afhankelijkheidsanalyse, orkestratie en activering worden afgehandeld door Azure Machine Learning wanneer u `Experiment`een [pijplijnobject](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline(class)?view=azure-ml-py) instantiate, doorgeeft aan een , en aanroepen `submit()`. 

### <a name="coordinating-the-steps-involved"></a>Coördinatie van de betrokken stappen

Wanneer u een `Pipeline` object maakt en uitvoert, vinden de volgende stappen op hoog niveau plaats:

+ Voor elke stap berekent de service vereisten voor:
    + Compute resources voor hardware
    + BE-bronnen (Docker-afbeelding(en))
    + Software resources (Conda / virtualenv afhankelijkheden)
    + Gegevensinvoer 
+ De service bepaalt de afhankelijkheden tussen stappen, wat resulteert in een dynamische uitvoeringsgrafiek
+ Wanneer elk knooppunt in de uitvoeringsgrafiek wordt uitgevoerd:
    + De service configureert de benodigde hardware- en softwareomgeving (misschien het hergebruik van bestaande bronnen)
    + De stap wordt uitgevoerd, het verstrekken `Experiment` van logging en monitoring informatie aan haar bevattende object
    + Wanneer de stap is voltooid, worden de uitgangen voorbereid als ingangen naar de volgende stap en/of naar de opslag
    + Resources die niet meer nodig zijn, worden afgerond en

![Pijplijnstappen](./media/concept-ml-pipelines/run_an_experiment_as_a_pipeline.png)

## <a name="building-pipelines-with-the-python-sdk"></a>Pijpleidingen bouwen met de Python SDK

In de [Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)is een `azureml.pipeline.core` pijplijn een Python-object dat in de module is gedefinieerd. Een [pijplijnobject](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline%28class%29?view=azure-ml-py) bevat een geordende reeks van een of meer [PipelineStep-objecten.](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.builder.pipelinestep?view=azure-ml-py) De `PipelineStep` klasse is abstract en de werkelijke stappen zijn van subklassen zoals [EstimatorStep,](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimatorstep?view=azure-ml-py) [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.pythonscriptstep?view=azure-ml-py)of [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py). De klasse [ModuleStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.modulestep?view=azure-ml-py) bevat een herbruikbare reeks stappen die kunnen worden gedeeld tussen pijplijnen. Een `Pipeline` loopt als `Experiment`onderdeel van een .

Een Azure ML-pijplijn is gekoppeld aan een Azure Machine Learning-werkruimte en een pijplijnstap is gekoppeld aan een rekendoel dat beschikbaar is in die werkruimte. Zie [Azure Machine Learning-werkruimten maken en beheren in de Azure-portal](https://docs.microsoft.com/azure/machine-learning/how-to-manage-workspace) of [Wat zijn rekendoelen in Azure Machine Learning voor](https://docs.microsoft.com/azure/machine-learning/concept-compute-target)meer informatie.

In Azure Machine Learning is een compute target de omgeving waarin een ML-fase plaatsvindt. De softwareomgeving kan een Externe VM, Azure Machine Learning Compute, Azure Databricks, Azure Batch, enzovoort zijn. De hardwareomgeving kan ook sterk variëren, afhankelijk van GPU-ondersteuning, geheugen, opslag, enzovoort. U het rekendoel voor elke stap opgeven, waardoor u een fijnmazige controle over de kosten krijgt. U min of meer krachtige resources gebruiken voor de specifieke actie, het gegevensvolume en de prestatiebehoeften van uw project. 

## <a name="building-pipelines-with-the-designer"></a>Pijpleidingen bouwen met de ontwerper

Ontwikkelaars die de voorkeur geven aan een visueel ontwerpoppervlak, kunnen de Azure Machine Learning-ontwerper gebruiken om pijplijnen te maken. U deze tool openen via de **selectie van Designer** op de startpagina van uw werkruimte.  Met de ontwerper u stappen op het ontwerpoppervlak slepen en neerzetten. Voor een snelle ontwikkeling u bestaande modules gebruiken in het hele spectrum van ML-taken; bestaande modules bestrijken alles, van gegevenstransformatie tot algoritmeselectie tot training tot implementatie. Of u een volledig aangepaste pijplijn maken door uw eigen stappen te combineren die zijn gedefinieerd in Python-scripts.

Wanneer u pijplijnen visueel ontwerpt, worden de ingangen en uitgangen van een stap zichtbaar weergegeven. U gegevensverbindingen slepen en neerzetten, zodat u de gegevensstroom van uw pijplijn snel begrijpen en wijzigen.
 
![Voorbeeld van Azure Machine Learning-ontwerper](./media/concept-designer/designer-drag-and-drop.gif)

### <a name="understanding-the-execution-graph"></a>Inzicht in de uitvoeringsgrafiek

De stappen binnen een pijplijn kunnen afhankelijk zijn van andere stappen. De Azure ML-pijplijnservice doet het werk van het analyseren en orkestreren van deze afhankelijkheden. De knooppunten in de resulterende "uitvoeringsgrafiek" zijn verwerkingsstappen. Elke stap kan het creëren of hergebruiken van een bepaalde combinatie van hardware en software, het hergebruik van resultaten in de cache, enzovoort. De orkestratie en optimalisatie van deze uitvoeringsgrafiek van de service kan een ML-fase aanzienlijk versnellen en de kosten verlagen. 

Omdat stappen onafhankelijk worden uitgevoerd, moeten objecten die de invoer- en uitvoergegevens bevatten die tussen stappen stromen, extern worden gedefinieerd. Dit is de rol van [DataReference,](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py) [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)en bijbehorende klassen. Deze gegevensobjecten zijn gekoppeld aan een [Datastore-object](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore%28class%29?view=azure-ml-py) dat hun opslagconfiguratie inkapselt. De `PipelineStep` basisklasse wordt altijd `name` gemaakt met `inputs`een tekenreeks, `outputs`een lijst van , en een lijst van . Meestal heeft het ook `arguments` een lijst van en `resource_inputs`vaak zal het een lijst van . Subklassen hebben over het algemeen ook extra `PythonScriptStep` argumenten (vereist bijvoorbeeld de bestandsnaam en het pad van het script om uit te voeren). 

De uitvoeringsgrafiek is acyclisch, maar pijplijnen kunnen worden uitgevoerd volgens een terugkerend schema en kunnen Python-scripts uitvoeren die statusinformatie naar het bestandssysteem kunnen schrijven, waardoor het mogelijk is om complexe profielen te maken. Als u uw pijplijn zo ontwerpt dat bepaalde stappen parallel of asynchroon kunnen worden uitgevoerd, verwerkt Azure Machine Learning op transparante wijze de afhankelijkheidsanalyse en coördinatie van fan-out en fan-in. U hoeft zich over het algemeen geen zorgen te maken over de details van de uitvoeringsgrafiek, maar het is beschikbaar via het kenmerk [Pipeline.graph.](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline.pipeline?view=azure-ml-py#attributes) 


### <a name="a-simple-python-pipeline"></a>Een eenvoudige Python-pijplijn

Dit fragment toont de objecten en aanroepen `Pipeline`die nodig zijn om een basisbasis te maken en uit te voeren:

```python
ws = Workspace.from_config() 
blob_store = Datastore(ws, "workspaceblobstore")
compute_target = ws.compute_targets["STANDARD_NC6"]
experiment = Experiment(ws, 'MyExperiment') 

input_data = DataReference(
    datastore=Datastore(ws, blob_store),
    data_reference_name="test_data",
    path_on_datastore="20newsgroups/20news.pkl")

output_data = PipelineData(
    "output_data",
    datastore=blob_store,
    output_name="output_data1")

steps = [ PythonScriptStep(
    script_name="train.py",
    arguments=["--input", input_data, "--output", output_data],
    inputs=[input_data],
    outputs=[output_data],
    compute_target=compute_target,
    source_directory="myfolder"
) ]

pipeline = Pipeline(workspace=ws, steps=steps)

pipeline_run = experiment.submit(pipeline)
pipeline_run.wait_for_completion()
```

Het fragment begint met algemene Azure `Workspace`Machine `Datastore`Learning-objecten, een `Experiment`, a , een [ComputeTarget](https://docs.microsoft.com/python/api/azureml-core/azureml.core.computetarget?view=azure-ml-py)en een . Vervolgens maakt de code de `input_data` `output_data`objecten die u wilt vasthouden en . De `steps` array bevat één `PythonScriptStep` element, een element dat de `compute_target`gegevensobjecten gebruikt en wordt uitgevoerd op de . Vervolgens wordt het object zelf `Pipeline` geinstantt door de code zelf te instantiëren en wordt het in de array werkruimte en stappen doorgegeven. De aanroep begint `experiment.submit(pipeline)` met de Azure ML-pijplijn. De oproep `wait_for_completion()` om te blokkeren totdat de pijplijn is voltooid. 

Zie de artikelen [Hoe u gegevens openen](how-to-access-data.md) en [gegevenssets registreren](how-to-create-register-datasets.md)voor meer informatie over het koppelen van uw pijplijn aan uw gegevens. 

## <a name="best-practices-when-using-pipelines"></a>Aanbevolen procedures bij het gebruik van pijplijnen

Zoals u zien, is het maken van een Azure ML-pijplijn iets complexer dan het starten van een script. Voor pijplijnen moeten enkele Python-objecten worden geconfigureerd en gemaakt. 

Enkele situaties die suggereren dat u een pijplijn gebruikt:

* In een teamomgeving: verdeel ML-taken in meerdere onafhankelijke stappen, zodat ontwikkelaars zelfstandig kunnen werken en hun programma's kunnen ontwikkelen. 

* Wanneer u in of in de buurt van implementatie bent: de configuratie vastspijkeren en geplande en gebeurtenisgestuurde bewerkingen gebruiken om op de hoogte te blijven van veranderende gegevens.

* In de beginfase van een ML-project of alleen werken: gebruik pijpleidingen om de build te automatiseren. Als u zich zorgen gaat maken over het opnieuw maken van de configuratie- en computerstatus voordat u een nieuw idee implementeert, is dat een signaal dat u zou kunnen overwegen een pijplijn te gebruiken om de werkstroom te automatiseren. 

Het is gemakkelijk om enthousiast te worden over het hergebruik van in de cache opgeslagen resultaten, fijnmazige controle over rekenkosten en procesisolatie, maar pijplijnen hebben wel kosten. Enkele anti-patronen zijn:

* Het gebruik van pijpleidingen als enige middel om problemen te scheiden. Python's ingebouwde functies, objecten en modules gaan een lange weg om te voorkomen dat verwarrende programmatische toestand! Een pijplijnstap is veel duurder dan een functieaanroep.

* Zware koppeling tussen pijpleidingstappen. Als het regelmatig wijzigen van de uitvoer van een eerdere stap vereist dat een afhankelijke stap wordt aangepast, is het waarschijnlijk dat afzonderlijke stappen momenteel meer kosten dan een voordeel zijn. Een andere aanwijzing dat stappen overdreven zijn gekoppeld, zijn argumenten voor een stap die geen gegevens zijn, maar vlaggen om de verwerking te beheren. 

* Compute resources voortijdig optimaliseren. Bijvoorbeeld, er zijn vaak verschillende stadia om de voorbereiding van gegevens en men kan `MpiStep` vaak zien "Oh, hier is een plek `PythonScriptStep` waar ik kon een gebruiken voor parallelle programmering, maar hier is een plek waar ik kon gebruiken met een minder krachtige compute target," enzovoort. En misschien, op de lange termijn, het creëren van fijnkorrelige stappen als dat zou kunnen blijken de moeite waard, vooral als er een mogelijkheid om cacheresultaten te gebruiken in plaats van altijd herberekenen. Maar pijpleidingen zijn niet bedoeld als vervanging voor `multiprocessing` python's native module. 

Totdat een project groot of bijna wordt geïmplementeerd, moeten uw pijplijnen grover zijn in plaats van fijnkorrelig. Als u uw ML-project ziet als _fasen_ en een pijplijn als een complete workflow om u door een bepaalde fase te leiden, bent u op het juiste pad. 

## <a name="key-advantages"></a>Belangrijkste voordelen

De belangrijkste voordelen van het gebruik van pijplijnen voor uw machine learning-workflows zijn:

|Belangrijk voordeel|Beschrijving|
|:-------:|-----------|
|**Onbeheerde&nbsp;uitvoeringen**|Plan stappen om parallel of in volgorde op een betrouwbare en onbewaakte manier uit te voeren. Gegevensvoorbereiding en modellering kunnen dagen of weken duren en met pijplijnen u zich concentreren op andere taken terwijl het proces wordt uitgevoerd. |
|**Heterogene rekenkracht**|Gebruik meerdere pijplijnen die betrouwbaar zijn gecoördineerd in heterogene en schaalbare compute resources en opslaglocaties. Maak efficiënt gebruik van beschikbare compute resources door afzonderlijke pijplijnstappen uit te voeren op verschillende compute targets, zoals HDInsight, GPU Data Science VM's en Databricks.|
|**Herbruikbaarheid**|Maak pijplijnsjablonen voor specifieke scenario's, zoals omscholing en batch-scoring. Activeer gepubliceerde pijplijnen van externe systemen via eenvoudige REST-oproepen.|
|**Tracking en versiebeheer**|In plaats van handmatig gegevens- en resultaatpaden bij te houden terwijl u deze wijzigt, gebruikt u de pijplijnen SDK om uw gegevensbronnen, invoer en uitvoer expliciet te benoemen en te gebruiken. U scripts en gegevens ook afzonderlijk beheren voor een hogere productiviteit.|
| **Modulariteit** | Door gebieden van zorgen te scheiden en veranderingen te isoleren, kan software sneller evolueren met een hogere kwaliteit. | 
|**Samenwerking**|Met pijplijnen kunnen gegevenswetenschappers samenwerken op alle gebieden van het machine learning-ontwerpproces, terwijl ze tegelijkertijd kunnen werken aan pijplijnstappen.|

### <a name="choosing-the-proper-pipelinestep-subclass"></a>De juiste subklasse PipelineStep kiezen

Het `PythonScriptStep` is de meest flexibele `PipelineStep`subklasse van de abstracte . Andere subklassen, zoals `EstimatorStep` subklassen `DataTransferStep` en kunnen specifieke taken uitvoeren met minder code. Een `EstimatorStep` kan bijvoorbeeld worden gemaakt door simpelweg een naam `Estimator`in te voeren voor de stap, een en een rekendoel. U ook invoer en uitvoer, pijplijnparameters en argumenten overschrijven. Zie [Modellen trainen met Azure Machine Learning met behulp van schatter](how-to-train-ml-models.md)voor meer informatie. 

Het `DataTransferStep` maakt het gemakkelijk om gegevens te verplaatsen tussen gegevensbronnen en putten. De code om dit handmatig te doen is eenvoudig, maar repetitief. In plaats daarvan kunt `DataTransferStep` u gewoon een met een naam, verwijzingen naar een gegevensbron en een gegevenssink en een rekendoel maken. De Azure [Machine Learning Pipeline met DataTransferStep](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-data-transfer.ipynb) toont deze flexibiliteit aan.

## <a name="modules"></a>Modules

Terwijl pijplijnstappen het hergebruik van de resultaten van een vorige uitvoering mogelijk maken, wordt in veel gevallen ervan uitgegaan dat de vereiste scripts en afhankelijke bestanden lokaal beschikbaar moeten zijn. Als een gegevenswetenschapper bovenop bestaande code wil voortbouwen, moeten de scripts en afhankelijkheden vaak worden gekloond uit een aparte opslagplaats.

Modules zijn qua gebruik vergelijkbaar met pijplijnstappen, maar bieden versiebeheer die via de werkruimte wordt gefaciliteerd, waardoor samenwerking en herbruikbaarheid op schaal mogelijk zijn. Modules zijn ontworpen om te worden hergebruikt in meerdere pijplijnen en kunnen evolueren om een specifieke berekening aan te passen aan verschillende use-cases. Gebruikers kunnen de volgende taken uitvoeren via de werkruimte, zonder externe opslagplaatsen te gebruiken:

* Nieuwe modules maken en nieuwe versies van bestaande modules publiceren
* Bestaande versies depreprecate
* Versies markeren die zijn uitgeschakeld om te voorkomen dat consumenten die versie kunnen gebruiken
* Standaardversies aanwijzen
* Modules per versie ophalen uit de werkruimte, om ervoor te zorgen dat teams dezelfde code gebruiken

Zie het [notitieblok](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-how-to-use-modulestep.ipynb) voor codevoorbeelden voor het maken, verbinden en gebruiken van modules in Azure Machine Learning-pijplijnen.

## <a name="next-steps"></a>Volgende stappen

Azure ML-pijplijnen zijn een krachtige faciliteit die in de vroege ontwikkelingsfase waarde begint te leveren. De waarde neemt toe naarmate het team en project groeit. In dit artikel wordt uitgelegd hoe pijplijnen worden opgegeven met de Azure Machine Learning Python SDK en georkestreerd op Azure. Je hebt een aantal basisbroncode gezien en is `PipelineStep` geïntroduceerd in een paar van de klassen die beschikbaar zijn. U moet een idee hebben van wanneer Azure ML-pijplijnen moeten worden gebruikt en hoe Azure deze uitvoert. 


+ Meer informatie over het [maken van uw eerste pijplijn.](how-to-create-your-first-pipeline.md)

+ Meer informatie over het [uitvoeren van batchvoorspellingen op grote gegevens](tutorial-pipeline-batch-scoring-classification.md ).

+ Zie de SDK-referentiedocumenten voor [pijplijnkern-](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) en [pijplijnstappen](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py).

+ Probeer voorbeeld Jupyter-notitieblokken uit met [Azure Machine Learning-pijplijnen](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines). Meer informatie over het [uitvoeren van notitieblokken om deze service te verkennen.](samples-notebooks.md)
