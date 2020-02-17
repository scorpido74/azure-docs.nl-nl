---
title: Wat zijn ML-pijp lijnen
titleSuffix: Azure Machine Learning
description: In dit artikel vindt u informatie over de voor delen van de machine learning (ML) pijp lijnen die u kunt bouwen met de Azure Machine Learning SDK voor python. Machine learning-pijp lijnen worden door gegevens wetenschappers gebruikt om hun machine learning werk stromen te bouwen, te optimaliseren en te beheren.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: laobri
author: lobrien
ms.date: 11/06/2019
ms.openlocfilehash: fd10a3e62bcbe438eb17edfc71a5285ad071e29a
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/15/2020
ms.locfileid: "77366212"
---
# <a name="what-are-azure-machine-learning-pipelines"></a>Wat zijn Azure Machine Learning pijp lijnen?

Met Azure Machine Learning pijp lijnen kunt u werk stromen maken in uw machine learning projecten. Deze werk stromen hebben een aantal voor delen: 

+ Eenvoudig
+ Snelheid
+ Herhaal baarheid
+ Flexibiliteit
+ Versie beheer en tracering
+ Modulariteit 
+ Kwaliteits garantie
+ Kosten beheer

Deze voor delen worden aanzienlijk zodra uw machine learning project wordt verplaatst naar een niet-versneld onderzoek en naar herhalingen. Zelfs eenvoudige pijp lijnen met één stap kunnen waardevol zijn. Machine learning-projecten zijn vaak in een complexe status en kunnen een vrijs telling zijn om een enkele werk stroom een trivial proces te maken.

Meer informatie over het [maken van uw eerste pijp lijn](how-to-create-your-first-pipeline.md).

![Machine learning-pijp lijnen in Azure Machine Learning](./media/concept-ml-pipelines/pipeline-flow.png)

<a name="compare"></a>
### <a name="which-azure-pipeline-technology-should-i-use"></a>Welke Azure pipeline-technologie moet ik gebruiken?

De Azure-Cloud biedt verschillende andere pijp lijnen, elk met een ander doel. De volgende tabel geeft een lijst van de verschillende pijp lijnen en waarvoor ze worden gebruikt:

| Scenario | Primaire persoon | Azure-aanbieding | OSS-aanbieding | Canonieke pijp | Sterke punten | 
| -------- | --------------- | -------------- | ------------ | -------------- | --------- | 
| Model indeling (machine learning) | Gegevenswetenschapper | Azure Machine Learning pijp lijnen | Kubeflow-pijp lijnen | Model voor gegevens > | Distributie, caching, code, opnieuw gebruiken | 
| Gegevens indeling (gegevens voorbereiding) | Data Engineer | [Azure Data Factory-pijplijnen](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities) | Apache-lucht flow | Gegevens > gegevens | Sterk getypeerde verplaatsing. Gegevens gerichte activiteiten. |
| App-indeling voor code & (CI/CD) | App-ontwikkelaar/OPS | [Azure DevOps-pijp lijnen](https://azure.microsoft.com/services/devops/pipelines/) | Jenkins | Code + model-> app/service | Ondersteuning voor de meeste open en flexibile activiteiten, goedkeurings wachtrijen, fasen met beperking | 


## <a name="what-can-azure-ml-pipelines-do"></a>Wat kunnen Azure ML-pijp lijnen doen?

Een Azure Machine Learning pijp lijn is een onafhankelijk uitvoer bare werk stroom van een volledige machine learning taak. Subtaken worden ingekapseld als een reeks stappen in de pijplijn. Een Azure Machine Learning-pijp lijn kan net zo eenvoudig zijn als één om een python-script aan te roepen. dit _kan_ dus iets anders doen. Pijp lijnen _moeten_ zich richten op machine learning taken, zoals:

+ Gegevensvoorbereiding, waaronder importeren, valideren en opschonen, munging en transformatie, normalisatie en fasering
+ Trainingsconfiguratie met inbegrip van parameteriseren van argumenten, bestandspaden en logboek-/rapportageconfiguratie
+ Training en validatie efficiënt en herhaaldelijk. Efficiëntie kan afkomstig zijn van het opgeven van specifieke gegevens subsets, verschillende hardwarebronnen, gedistribueerde verwerking en voortgangs bewaking
+ Implementatie, inclusief versiebeheer, schalen, inrichten en toegangsbeheer 

Met onafhankelijke stappen kunnen er meerdere gegevens wetenschappers tegelijk op dezelfde pijp lijn werken zonder gegevens over reken bronnen te hoeven te gebruiken. Door afzonderlijke stappen te maken, kunt u voor elke stap ook eenvoudig verschillende reken typen/-groottes gebruiken.

Nadat de pijp lijn is ontworpen, is er vaak meer nauw keuriger om de training van de pijp lijn te verfijnen. Wanneer u een pijp lijn opnieuw uitvoert, springt de uitvoering naar de stappen die moeten worden uitgevoerd, zoals een bijgewerkt trainings script. De stappen die niet opnieuw hoeven te worden uitgevoerd, worden overgeslagen. Dezelfde analyse geldt voor ongewijzigde scripts die worden gebruikt voor het uitvoeren van de stap. Deze functionaliteit voor hergebruik helpt te voor komen dat kost bare en tijdrovende stappen zoals gegevens opname en trans formatie worden uitgevoerd als de onderliggende gegevens niet zijn gewijzigd.

Met Azure Machine Learning kunt u verschillende tool kits en frameworks, zoals PyTorch of tensor flow, gebruiken voor elke stap in de pijp lijn. Azure coördineert de verschillende [reken doelen](concept-azure-machine-learning-architecture.md) die u gebruikt, zodat uw tussenliggende gegevens kunnen worden gedeeld met de downstream Compute-doelen.

U kunt [de metrische gegevens voor uw pijplijn experimenten rechtstreeks bijhouden](https://docs.microsoft.com/azure/machine-learning/how-to-track-experiments) in azure portal of de [landings pagina van uw werk ruimte (preview)](https://ml.azure.com). Nadat een pijp lijn is gepubliceerd, kunt u een REST-eind punt configureren. Hiermee kunt u de pijp lijn opnieuw uitvoeren vanaf elk platform of elke stack.

Kortom, alle complexe taken van de machine learning levenscyclus kunnen worden geholpen met pijp lijnen. Andere Azure-pijplijn technologieën hebben hun eigen kracht, zoals [Azure Data Factory pijp lijnen](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities) voor het werken met gegevens en [Azure-pijp lijnen](https://azure.microsoft.com/services/devops/pipelines/) voor continue integratie en implementatie. Maar als uw focus machine learning is, zijn Azure Machine Learning pijp lijnen waarschijnlijk de beste keuze voor uw werk stroom behoeften. 

## <a name="what-are-azure-ml-pipelines"></a>Wat zijn Azure ML-pijp lijnen?

Een Azure ML-pijp lijn voert een volledige logische werk stroom uit met een geordende reeks stappen. Elke stap is een discrete verwerkings actie. Pijp lijnen worden uitgevoerd in de context van een Azure Machine Learning [experiment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py).

In de vroege stadia van een ML-project is het prima om één Jupyter-notebook of python-script te hebben dat alle werkzaamheden van de Azure-werk ruimte en resource configuratie, het voorbereiden van gegevens, het uitvoeren van de configuratie, training en validatie. Maar net zoals functies en klassen snel als voor keur worden beschouwd voor één enkel verplicht code blok, worden werk stromen snel voor keur aan een monolithische-notebook of-script. 

Door modularizing ML-taken ondersteunen pijp lijnen de computer wetenschappen die nood zakelijk is voor een onderdeel. Modulariteit is duidelijk van cruciaal belang voor het projecteren van succes bij het Program meren in teams, maar zelfs bij het werken met een klein ML-project zijn er afzonderlijke taken nodig, die elk een goede complexiteit hebben. Taken omvatten: werkruimte configuratie en gegevens toegang, gegevens voorbereiding, model definitie en-configuratie en implementatie. Terwijl de uitvoer van een of meer taken de invoer naar een andere taak vormt, zijn de exacte implementatie details van de ene taken, met de beste afleidingen in de volgende. In het ergste geval kan de reken status van de ene taak leiden tot een fout in een andere. 

### <a name="analyzing-dependencies"></a>Afhankelijkheden analyseren

Veel programmeer ecosystemen beschikken over hulpprogram ma's voor het organiseren van afhankelijkheden van resources, bibliotheken of compilatie. Over het algemeen gebruiken deze hulpprogram ma's bestands tempels voor het berekenen van afhankelijkheden. Wanneer een bestand wordt gewijzigd, worden alleen de bijbehorende afhankelijkheden bijgewerkt (gedownload, opnieuw gecompileerd of verpakt). Azure ML-pijp lijnen breiden dit concept aanzienlijk uit. Net als bij traditionele hulpprogram ma's voor builds, worden door pijp lijnen afhankelijkheden tussen de stappen berekend en worden alleen de benodigde herberekeningen uitgevoerd. 

De afhankelijkheids analyse in azure ML-pijp lijnen is geavanceerder dan eenvoudige tijds tempels. Elke stap kan worden uitgevoerd in een andere hardware-en software omgeving. Het voorbereiden van gegevens kan een tijdrovend proces zijn, maar het is niet nodig om te worden uitgevoerd op hardware met krachtige Gpu's. voor bepaalde stappen kunnen specifieke software zijn vereist, kunt u gebruikmaken van gedistribueerde training, enzovoort. Hoewel de kosten besparingen voor het optimaliseren van resources mogelijk aanzienlijk zijn, kan het erg groot zijn om hand matig alle verschillende variaties in hardware-en software bronnen verwerking te maken. Het is nog moeilijker om alles te doen, zonder dat u een fout hebt gemaakt in de gegevens die u tussen stappen overbrengt. 

Dit probleem wordt opgelost met pijp lijnen. Azure Machine Learning zijn automatisch alle afhankelijkheden tussen pijplijn stappen. Deze indeling kan het volgende omvatten: omhoog en omlaag docker-installatie kopieën, het koppelen en loskoppelen van reken resources en het verplaatsen van gegevens tussen de stappen op consistente en automatische wijze.

### <a name="reusing-results"></a>Resultaten opnieuw gebruiken

Daarnaast kan de uitvoer van een stap, indien gewenst, opnieuw worden gebruikt. Als u hergebruik als een mogelijkheid opgeeft en er geen upstream-afhankelijkheden zijn waarmee herberekening wordt geactiveerd, gebruikt de pipeline-service een versie van de resultaten in de cache. Een dergelijk hergebruik kan de ontwikkelings tijd aanzienlijk verminderen. Als u een complexe gegevens voorbereidings taak hebt, moet u deze waarschijnlijk vaker opnieuw uitvoeren dan strikt nood zakelijk is. Met pijp lijnen hebt u het volgende gedaan: als dat nodig is, wordt de stap uitgevoerd, als dat niet het geval is.

Al deze afhankelijkheids analyse, indeling en activering worden afgehandeld door Azure Machine Learning wanneer u een [pijplijn](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline(class)?view=azure-ml-py) object maakt, het aan een `Experiment`doorgeeft en `submit()`aanroept. 

### <a name="coordinating-the-steps-involved"></a>De betrokken stappen coördineren

Wanneer u een `Pipeline`-object maakt en uitvoert, worden de volgende stappen op hoog niveau uitgevoerd:

+ Voor elke stap berekent de service vereisten voor:
    + Hardware Compute-resources
    + OS-resources (docker-installatie kopie (en))
    + Software bronnen (Conda/virtualenv-afhankelijkheden)
    + Gegevens invoer 
+ De service bepaalt de afhankelijkheden tussen de stappen, wat resulteert in een dynamische uitvoerings grafiek
+ Wanneer elk knoop punt in de uitvoerings grafiek wordt uitgevoerd:
    + Met de service worden de benodigde hardware-en software omgeving geconfigureerd (mogelijk worden bestaande resources opnieuw gebruikt)
    + De stap wordt uitgevoerd, waarmee informatie over logboek registratie en bewaking kan worden verstrekt aan het betreffende `Experiment`-object
    + Wanneer de stap is voltooid, worden de uitvoer voor bereid als invoer voor de volgende stap en/of naar opslag geschreven
    + Resources die niet meer nodig zijn, worden voltooid en ontkoppeld

![Pijplijn stappen](./media/concept-ml-pipelines/run_an_experiment_as_a_pipeline.png)

## <a name="building-pipelines-with-the-python-sdk"></a>Pijp lijnen bouwen met de python-SDK

In de [Azure machine learning PYTHON SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)is een pijp lijn een python-object dat is gedefinieerd in de module `azureml.pipeline.core`. Een [pijplijn](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline%28class%29?view=azure-ml-py) object bevat een geordende sequentie van een of meer [PipelineStep](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.builder.pipelinestep?view=azure-ml-py) -objecten. De klasse `PipelineStep` is abstract en de daad werkelijke stappen zijn subklassen, zoals [EstimatorStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimatorstep?view=azure-ml-py), [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.pythonscriptstep?view=azure-ml-py)of [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py). De [ModuleStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.modulestep?view=azure-ml-py) -klasse bevat een herbruikbare reeks stappen die kunnen worden gedeeld tussen pijp lijnen. Een `Pipeline` wordt uitgevoerd als onderdeel van een `Experiment`.

Een Azure ML-pijp lijn is gekoppeld aan een Azure Machine Learning-werk ruimte en een pijplijn stap is gekoppeld aan een berekenings doel dat in die werk ruimte beschikbaar is. Zie [Azure machine learning-werk ruimten maken en beheren in de Azure Portal](https://docs.microsoft.com/azure/machine-learning/how-to-manage-workspace) of [Wat zijn reken doelen in azure machine learning?](https://docs.microsoft.com/azure/machine-learning/concept-compute-target)voor meer informatie.

In Azure Machine Learning is een compute-doel de omgeving waarin een ML-fase plaatsvindt. De software omgeving kan een externe virtuele machine zijn, Azure Machine Learning compute, Azure Databricks, Azure Batch, enzovoort. De hardware-omgeving kan ook aanzienlijk variëren, afhankelijk van de GPU-ondersteuning, het geheugen, de opslag, enzovoort. U kunt het berekenings doel voor elke stap opgeven, zodat u de kosten nauw keurig kunt controleren. U kunt meer of minder krachtige bronnen gebruiken voor de specifieke actie, het gegevens volume en de prestatie behoeften van uw project. 

## <a name="building-pipelines-with-the-designer"></a>Pijp lijnen bouwen met de ontwerper

Ontwikkel aars die de voor keur geven aan een visueel ontwerp vlak kunnen de Azure Machine Learning Designer gebruiken om pijp lijnen te maken. U kunt dit hulp programma openen via de selectie van de **ontwerp functie** op de start pagina van uw werk ruimte.  Met de ontwerper kunt u stappen naar het ontwerp oppervlak slepen en neerzetten. Voor snelle ontwikkeling kunt u bestaande modules in het spectrum van ML-taken gebruiken. bestaande modules dekken alles van gegevens transformatie tot het selecteren van algoritmen om te oefenen met implementatie. U kunt ook een volledig aangepaste pijp lijn maken door uw eigen stappen te combi neren die in python-scripts zijn gedefinieerd.

Wanneer u pijp lijnen visueel ontwerpt, worden de invoer en uitvoer van een stap zichtbaar weer gegeven. U kunt gegevens verbindingen slepen en neerzetten om de gegevensstroom van uw pijp lijn snel te begrijpen en te wijzigen.
 
![Voor beeld van Azure Machine Learning Designer](./media/concept-designer/designer-drag-and-drop.gif)

### <a name="understanding-the-execution-graph"></a>Uitleg over de uitvoerings grafiek

De stappen binnen een pijp lijn kunnen afhankelijkheden hebben met andere stappen. De Azure ML pijp lijn-service is het werk van deze afhankelijkheden te analyseren en te organiseren. De knoop punten in het resulterende ' uitvoerings grafiek ' zijn verwerkings stappen. Elke stap kan betrekking hebben op het maken of opnieuw gebruiken van een bepaalde combi natie van hardware en software, het opnieuw gebruiken van resultaten in de cache, enzovoort. De indeling en Optima Lise ring van de service van deze uitvoerings grafiek kan een aanzienlijk snellere fase versnellen en de kosten verlagen. 

Omdat stappen onafhankelijk van elkaar worden uitgevoerd, moeten objecten die de invoer-en uitvoer gegevens bevatten, extern worden gedefinieerd. Dit is de rol van [DataReference](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py), [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)en bijbehorende klassen. Deze gegevens objecten zijn gekoppeld aan een [gegevens opslag](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore%28class%29?view=azure-ml-py) object dat de opslag configuratie inkapselt. De basis klasse `PipelineStep` wordt altijd gemaakt met een `name` teken reeks, een lijst met `inputs`en een lijst met `outputs`. Normaal gesp roken bevat het ook een lijst met `arguments` en is er vaak een lijst met `resource_inputs`. Subklassen hebben over het algemeen extra argumenten (bijvoorbeeld `PythonScriptStep` vereist de bestands naam en het pad van het script dat moet worden uitgevoerd). 

De uitvoerings grafiek is acyclische, maar pijp lijnen kunnen worden uitgevoerd op een terugkerende planning en kunnen python-scripts uitvoeren die status informatie kunnen schrijven naar het bestands systeem, waardoor het mogelijk is om complexe profielen te maken. Als u uw pijp lijn zo ontwerpt dat bepaalde stappen parallel of asynchroon worden uitgevoerd, Azure Machine Learning de afhankelijkheids analyse en coördinatie van de uitwaaiers en de samen werking op transparante wijze afhandelen. Over het algemeen hoeft u zich niet meer te houden aan de details van het uitvoerings diagram, maar het is beschikbaar via het kenmerk [pipeline. Graph](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline.pipeline?view=azure-ml-py#attributes) . 


### <a name="a-simple-python-pipeline"></a>Een eenvoudige python-pijp lijn

Dit fragment toont de objecten en aanroepen die nodig zijn om een basis `Pipeline`te maken en uit te voeren:

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

Het fragment begint met algemene Azure Machine Learning objecten, een `Workspace`, een `Datastore`, een [ComputeTarget](https://docs.microsoft.com/python/api/azureml-core/azureml.core.computetarget?view=azure-ml-py)en een `Experiment`. Vervolgens maakt de code de objecten om `input_data` en `output_data`te bewaren. De matrix `steps` één element bevat, een `PythonScriptStep` die de gegevens objecten gaat gebruiken en op de `compute_target`worden uitgevoerd. Vervolgens maakt de code een exemplaar van het `Pipeline`-object zelf, waarbij de werk ruimte en de stappen matrix worden door gegeven. De aanroep van `experiment.submit(pipeline)` begint met het uitvoeren van de pijp lijn van Azure ML. De aanroep naar `wait_for_completion()` blokken totdat de pijp lijn is voltooid. 

Zie voor meer informatie over het verbinden van uw pijp lijn met uw gegevens de artikelen [over toegang tot gegevens](how-to-access-data.md) en [het registreren van data sets](how-to-create-register-datasets.md). 

## <a name="best-practices-when-using-pipelines"></a>Aanbevolen procedures voor het gebruik van pijp lijnen

Zoals u kunt zien, is het maken van een Azure ML-pijp lijn iets ingewik kelder dan het starten van een script. Voor pijp lijnen moeten enkele python-objecten worden geconfigureerd en gemaakt. 

In sommige situaties wordt een pijp lijn voorgesteld:

* In een team omgeving: Splits taken in meerdere onafhankelijke stappen zodat ontwikkel aars hun Program ma's onafhankelijk van elkaar kunnen gebruiken en ontwikkelen. 

* Wanneer in of near-implementatie: de configuratie benadert en geplande en gebeurtenis gerichte bewerkingen gebruiken om te blijven passen aan de gewijzigde gegevens.

* In de vroege stadia van een ML-project of alleen werken: gebruik pijp lijnen om de build te automatiseren. Als u hebt begonnen met het opnieuw maken van de configuratie en de verwerkings status voordat u een nieuw idee implementeert, is dat een signaal dat u kunt overwegen een pijp lijn te gebruiken om de werk stroom te automatiseren. 

Het is eenvoudig om enthousiast te raken over het opnieuw gebruiken van in de cache geplaatste resultaten, nauw keurige controle over reken kosten en proces isolatie, maar er zijn wel kosten verbonden aan pijp lijnen. Enkele anti patronen zijn:

* Het gebruik van pijp lijnen als enige manier om problemen te scheiden. De ingebouwde functies, objecten en modules van python bieden een lange manier om Verwar ring programmatisch te voor komen! Een pijplijn stap is veel duurder dan een functie aanroep.

* Zware koppeling tussen pijplijn stappen. Als u een afhankelijke stap vaak nodig hebt om de uitvoer van een vorige stap te wijzigen, is het waarschijnlijk dat de afzonderlijke stappen momenteel meer kosten in beslag nemen dan een voor deel. Een andere aanwijzing dat de stappen zijn gekoppeld, zijn argumenten voor een stap die geen gegevens bevat, maar vlaggen voor het beheren van de verwerking. 

* De reken resources voor tijdig optimaliseren. Er zijn bijvoorbeeld vaak verschillende fasen voor het voorbereiden van gegevens en een van de volgende ' Oh ': dit is een plek waar ik een `MpiStep` kan gebruiken voor parallelle programmering, maar dit is een plek waar ik een `PythonScriptStep` met een minder krachtig reken doel zou kunnen gebruiken, enzovoort. En wellicht, in de lange uitvoering, het maken van nauw keurige stappen zoals die mogelijk de moeite waard zijn, met name als er sprake is van het gebruik van in cache opgeslagen resultaten in plaats van altijd opnieuw te hoeven worden berekend. Maar pijp lijnen zijn niet bedoeld als vervanging voor de module `multiprocessing`. 

Totdat een project een grote of bijnae implementatie krijgt, moeten uw pijp lijnen grof zijn in plaats van nauw keurig te zijn. Als u uw ML-project wilt beschouwen als _fasen_ en een pijp lijn als u een volledige werk stroom levert om u door een bepaalde fase te verplaatsen, bent u op het juiste pad. 

## <a name="key-advantages"></a>Belangrijkste voordelen

De belangrijkste voor delen van het gebruik van pijp lijnen voor uw machine learning-werk stromen zijn:

|Groot voordeel|Beschrijving|
|:-------:|-----------|
|**&nbsp;uitvoeringen zonder toezicht**|Stappen plannen om parallel of op een betrouw bare en zonder toezicht te worden uitgevoerd. De voor bereiding en het model leren van gegevens kunnen de afgelopen dagen of weken zijn en met pijp lijnen kunt u zich richten op andere taken terwijl het proces wordt uitgevoerd. |
|**Heterogene compute**|Gebruik meerdere pijp lijnen die betrouwbaar zijn afgestemd op heterogene en schaal bare reken resources en opslag locaties. Maak efficiënt gebruik van beschik bare reken resources door afzonderlijke pijplijn stappen uit te voeren op verschillende reken doelen, zoals HDInsight, GPU data Science Vm's en Databricks.|
|**Herbruikbaarheid**|Maak pijplijn sjablonen voor specifieke scenario's, zoals retraining en batch-scores. Activeer gepubliceerde pijp lijnen van externe systemen via eenvoudige REST-aanroepen.|
|**Bijhouden en versie beheer**|In plaats van gegevens en resultaat paden hand matig te traceren tijdens het herhalen, gebruikt u de SDK van de pijp lijnen om uw gegevens bronnen, invoer en uitvoer expliciet een naam en versie te gegeven. U kunt ook scripts en gegevens afzonderlijk beheren voor een verhoogde productiviteit.|
| **Modulariteit** | Het scheiden van aandachtspunten en het isoleren van wijzigingen stelt software in staat om sneller te werken met een hogere kwaliteit. | 
|**Werking**|Met pijp lijnen kunnen gegevens wetenschappers samen werken aan alle gebieden van het machine learning-ontwerp proces, terwijl ze gelijktijdig kunnen werken aan pijplijn stappen.|

## <a name="modules"></a>Modules

Tijdens de stappen voor de pijp lijn kunnen de resultaten van een vorige uitvoering opnieuw worden gebruikt. in veel gevallen neemt de bouw van de stap uit dat de vereiste scripts en afhankelijke bestanden lokaal beschikbaar moeten zijn. Als een gegevens wetenschapper boven op bestaande code wil bouwen, moeten de scripts en afhankelijkheden vaak worden gekloond van een afzonderlijke opslag plaats.

Modules zijn vergelijkbaar in het gebruik van pijplijn stappen, maar bieden versie beheer via de werk ruimte, waardoor samen werking en bruikbaarheid op schaal mogelijk is. Modules zijn ontworpen om opnieuw te worden gebruikt in meerdere pijp lijnen en kunnen een specifieke berekening aanpassen aan verschillende gebruiks voorbeelden. Gebruikers kunnen de volgende taken uitvoeren via de werk ruimte, zonder het gebruik van externe opslag plaatsen:

* Nieuwe modules maken en nieuwe versies van bestaande modules publiceren
* Bestaande versies uitafschaffen
* Versies markeren als uitgeschakeld om te voor komen dat gebruikers deze versie gebruiken
* Standaard versies toewijzen
* Modules ophalen op basis van de versie van de werk ruimte, om ervoor te zorgen dat teams dezelfde code gebruiken

Raadpleeg het [notitie blok](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-how-to-use-modulestep.ipynb) voor code voorbeelden voor het maken, verbinden en gebruiken van modules in azure machine learning pijp lijnen.

## <a name="next-steps"></a>Volgende stappen

Azure ML-pijp lijnen vormen een krachtige faciliteit voor het leveren van de waarde in de vroege ontwikkel stadia. De waarde neemt toe naarmate het team en project groeien. In dit artikel wordt uitgelegd hoe u pijp lijnen kunt opgeven met de Azure Machine Learning python SDK en die zijn georganiseerd op Azure. U hebt een aantal Basic-bron code gezien en deze is geïntroduceerd in enkele van de beschik bare `PipelineStep` klassen. U moet een idee hebben van het gebruik van Azure ML-pijp lijnen en hoe Azure deze uitvoert. 


+ Meer informatie over het [maken van uw eerste pijp lijn](how-to-create-your-first-pipeline.md).

+ Meer informatie over het [uitvoeren van batch voorspellingen voor grote gegevens](tutorial-pipeline-batch-scoring-classification.md ).

+ Zie de SDK-documentatie voor [pijplijn kernen](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) en [pijplijn stappen](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py).

+ Probeer een voor beeld van Jupyter-notitie blokken [Azure machine learning-pijp lijnen](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines). Meer informatie over het [uitvoeren van notitie blokken om deze service te verkennen](samples-notebooks.md).
