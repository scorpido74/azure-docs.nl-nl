---
title: Toegang tot gegevenssets met Python-clientbibliotheek - Team Data Science-proces
description: Installeer en gebruik de Python-clientbibliotheek om azure Machine Learning-gegevens veilig te openen en te beheren vanuit een lokale Python-omgeving.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 93ec5e740ac6acf9420a9d980092ed772ac1618e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720976"
---
# <a name="access-datasets-with-python-using-the-azure-machine-learning-python-client-library"></a>Toegang tot gegevenssets met Python met behulp van de clientbibliotheek van Azure Machine Learning Python
De preview van microsoft Azure Machine Learning Python-clientbibliotheek kan veilige toegang tot uw Azure Machine Learning-gegevenssets vanuit een lokale Python-omgeving mogelijk maken en het maken en beheren van gegevenssets in een werkruimte mogelijk maken en beheren.

In dit onderwerp vindt u instructies over het gebruik van:

* de Machine Learning Python-clientbibliotheek installeren
* gegevenssets openen en uploaden, inclusief instructies voor het krijgen van autorisatie voor toegang tot Azure Machine Learning-gegevenssets vanuit uw lokale Python-omgeving
* toegang tot tussenliggende gegevenssets uit experimenten
* de Python-clientbibliotheek gebruiken om gegevenssets op te sommen, metagegevens te openen, de inhoud van een gegevensset te lezen, nieuwe gegevenssets te maken en bestaande gegevenssets bij te werken

## <a name="prerequisites"></a><a name="prerequisites"></a>Vereisten
De Python-clientbibliotheek is getest onder de volgende omgevingen:

* Windows, Mac en Linux
* Python 2.7, 3.3 en 3.4

Het heeft een afhankelijkheid van de volgende pakketten:

* Verzoeken
* python-dateutil
* panda's

We raden u aan een Python-distributie te gebruiken, zoals [Anaconda](http://continuum.io/downloads#all) of [Canopy,](https://store.enthought.com/downloads/)die worden geleverd met Python, IPython en de drie hierboven vermelde pakketten geïnstalleerd. Hoewel IPython niet strikt vereist is, is het een geweldige omgeving voor het interactief manipuleren en visualiseren van gegevens.

### <a name="how-to-install-the-azure-machine-learning-python-client-library"></a><a name="installation"></a>De Azure Machine Learning Python-clientbibliotheek installeren
Installeer de Azure Machine Learning Python-clientbibliotheek om de taken te voltooien die in dit onderwerp worden beschreven. Deze bibliotheek is beschikbaar via de [Python Package Index.](https://pypi.python.org/pypi/azureml) Voer de volgende opdracht uit vanuit uw lokale Python-omgeving om het in uw Python-omgeving te installeren:

    pip install azureml

U ook downloaden en installeren van de bronnen op [GitHub.](https://github.com/Azure/Azure-MachineLearning-ClientLibrary-Python)

    python setup.py install

Als je git op je machine hebt geïnstalleerd, kun je pip gebruiken om direct vanuit de git repository te installeren:

    pip install git+https://github.com/Azure/Azure-MachineLearning-ClientLibrary-Python.git


## <a name="use-code-snippets-to-access-datasets"></a><a name="datasetAccess"></a>Codefragmenten gebruiken om toegang te krijgen tot gegevenssets
De Python-clientbibliotheek geeft u programmatische toegang tot uw bestaande gegevenssets uit experimenten die zijn uitgevoerd.

Vanuit de webinterface van Azure Machine Learning Studio (klassieke) webinterface u codefragmenten genereren die alle benodigde informatie bevatten om gegevenssets te downloaden en te deserialiseren als panda's DataFrame-objecten op uw lokale machine.

### <a name="security-for-data-access"></a><a name="security"></a>Beveiliging voor toegang tot gegevens
De codefragmenten die door Azure Machine Learning Studio (klassiek) worden geleverd voor gebruik met de Python-clientbibliotheek, bevatten uw werkruimte-id en autorisatietoken. Deze bieden volledige toegang tot uw werkruimte en moeten worden beveiligd, zoals een wachtwoord.

Om veiligheidsredenen is de functionaliteit voor codefragmenten alleen beschikbaar voor gebruikers die hun rol hebben ingesteld als **eigenaar** voor de werkruimte. Uw rol wordt weergegeven in Azure Machine Learning Studio (klassiek) op de pagina **GEBRUIKERS** onder **Instellingen**.

![Beveiliging][security]

Als uw rol niet is ingesteld als **eigenaar,** u vragen om opnieuw te worden uitgenodigd als eigenaar, of de eigenaar van de werkruimte vragen om u het codefragment te verstrekken.

Als u het autorisatietoken wilt verkrijgen, u een van de volgende opties kiezen:

* Vraag om een token van een eigenaar. Eigenaren hebben toegang tot hun autorisatietokens via de pagina Instellingen van hun werkruimte in Azure Machine Learning Studio (klassiek). Selecteer **Instellingen** in het linkerdeelvenster en klik op **AUTORISATIETOKENS** om de primaire en secundaire tokens weer te geven. Hoewel de primaire of de secundaire autorisatietokens kunnen worden gebruikt in het codefragment, wordt aanbevolen dat eigenaren alleen de secundaire autorisatietokens delen.

   ![Autorisatietokens](./media/python-data-access/ml-python-access-settings-tokens.png)

* Vraag om te worden gepromoveerd tot rol van eigenaar: een huidige eigenaar van de werkruimte moet u eerst uit de werkruimte verwijderen en u opnieuw uitnodigen als eigenaar.

Zodra ontwikkelaars de werkruimte-id en het autorisatietoken hebben verkregen, hebben ze toegang tot de werkruimte met behulp van het codefragment, ongeacht hun rol.

Autorisatietokens worden beheerd op de pagina **AUTORISATIETOKENS** onder **INSTELLINGEN**. U ze opnieuw genereren, maar deze procedure trekt de toegang tot het vorige token in.

### <a name="access-datasets-from-a-local-python-application"></a><a name="accessingDatasets"></a>Toegang tot gegevenssets vanuit een lokale Python-toepassing
1. Klik in Machine Learning Studio (klassiek) op **GEGEVENSSETS** in de navigatiebalk aan de linkerkant.
2. Selecteer de gegevensset die u wilt openen. U een van de gegevenssets selecteren in de lijst **MIJN GEGEVENSSETS** of in de lijst **MET voorbeelden.**
3. Klik op de onderste werkbalk op **Gegevenstoegangscode genereren**. Als de gegevens in een indeling zijn die niet compatibel is met de Python-clientbibliotheek, wordt deze knop uitgeschakeld.
   
    ![Gegevenssets][datasets]
4. Selecteer het codefragment in het venster dat wordt weergegeven en kopieer het naar het klembord.
   
    ![Knop Gegevenstoegangscode genereren][dataset-access-code]
5. Plak de code in het notitieblok van uw lokale Python-toepassing.
   
    ![Code in het notitieblok plakken][ipython-dataset]

## <a name="access-intermediate-datasets-from-machine-learning-experiments"></a><a name="accessingIntermediateDatasets"></a>Toegang tot tussenliggende gegevenssets van Machine Learning-experimenten
Nadat een experiment is uitgevoerd in Machine Learning Studio (klassiek), is het mogelijk om toegang te krijgen tot de tussenliggende gegevenssets vanaf de uitvoerknooppunten van modules. Tussenliggende gegevenssets zijn gegevens die zijn gemaakt en worden gebruikt voor tussenliggende stappen wanneer een modelgereedschap is uitgevoerd.

Tussenliggende gegevenssets kunnen worden geopend zolang de gegevensindeling compatibel is met de Python-clientbibliotheek.

De volgende indelingen worden ondersteund (constanten `azureml.DataTypeIds` voor deze indelingen zijn in de klasse):

* PlainText
* GeneriekeCSV
* GenericTSV (GenericTSV)
* GeneriekeCSVNoHeader
* GenerictSVNoHeader

U de indeling bepalen door over een uitvoerknooppunt van een module te zweven. Het wordt weergegeven samen met de naam van het knooppunt, in een knopinfo.

Sommige modules, zoals de [Split-module,][split] worden `Dataset`uitgevoerd naar een indeling met de naam , die niet wordt ondersteund door de Python-clientbibliotheek.

![Gegevenssetindeling][dataset-format]

U moet een conversiemodule gebruiken, zoals [Converteren naar CSV,][convert-to-csv]om een uitvoer in een ondersteunde indeling te krijgen.

![GenericCSV-indeling][csv-format]

In de volgende stappen wordt een voorbeeld weergegeven dat een experiment maakt, wordt uitgevoerd en toegang krijgt tot de tussenliggende gegevensset.

1. Maak een nieuw experiment.
2. Voeg een module **voor binaire classificatie van inkomsten voor volwassenen in.**
3. Voeg een [Gesplitste][split] module in en sluit de invoer aan op de uitvoer van de gegevenssetmodule.
4. Voeg een [MODULE Converteren naar CSV][convert-to-csv] in en sluit de invoer aan op een van de [gesplitste][split] module-uitgangen.
5. Sla het experiment op, voer het uit en wacht tot de taak is voltooid.
6. Klik op het uitvoerknooppunt op de module [Converteren naar CSV.][convert-to-csv]
7. Wanneer het contextmenu wordt weergegeven, selecteert u **Gegevenstoegangscode genereren**.
   
    ![Contextmenu][experiment]
8. Selecteer het codefragment en kopieer het naar het klembord vanuit het venster dat wordt weergegeven.
   
    ![Toegangscode genereren vanuit contextmenu][intermediate-dataset-access-code]
9. Plak de code in uw notitieblok.
   
    ![Code plakken in notitieblok][ipython-intermediate-dataset]
10. U de gegevens visualiseren met behulp van matplotlib. Dit wordt weergegeven in een histogram voor de leeftijdskolom:
    
    ![Histogram][ipython-histogram]

## <a name="use-the-machine-learning-python-client-library-to-access-read-create-and-manage-datasets"></a><a name="clientApis"></a>Gebruik de Machine Learning Python-clientbibliotheek om gegevenssets te openen, te lezen, te maken en te beheren
### <a name="workspace"></a>Werkruimte
De werkruimte is het toegangspunt voor de Python-clientbibliotheek. Geef `Workspace` de klasse uw werkruimte-id en autorisatietoken om een instantie te maken:

    ws = Workspace(workspace_id='4c29e1adeba2e5a7cbeb0e4f4adfb4df',
                   authorization_token='f4f3ade2c6aefdb1afb043cd8bcf3daf')


### <a name="enumerate-datasets"></a>Gegevenssets opsommen
Ga als lid van het werkom alle gegevenssets in een bepaalde werkruimte op te sommen:

    for ds in ws.datasets:
        print(ds.name)

Ga als u alleen de door de gebruiker gemaakte gegevenssets opsommen:

    for ds in ws.user_datasets:
        print(ds.name)

Ga als u alleen de voorbeeldgegevenssets opsommen:

    for ds in ws.example_datasets:
        print(ds.name)

U hebt toegang tot een gegevensset op naam (die hoofdlettergevoelig is):

    ds = ws.datasets['my dataset name']

Of u het bereiken op index:

    ds = ws.datasets[0]


### <a name="metadata"></a>Metagegevens
Datasets hebben metadata, naast inhoud. (Tussenliggende gegevenssets zijn een uitzondering op deze regel en hebben geen metagegevens.)

Sommige metagegevenswaarden worden door de gebruiker toegewezen op het moment van maken:

    print(ds.name)
    print(ds.description)
    print(ds.family_id)
    print(ds.data_type_id)

Andere zijn waarden die zijn toegewezen door Azure ML:

    print(ds.id)
    print(ds.created_date)
    print(ds.size)

Zie `SourceDataset` de klasse voor meer informatie over de beschikbare metagegevens.

### <a name="read-contents"></a>Inhoud lezen
De codefragmenten die Machine Learning Studio (klassiek) verstrekt, downloaden en deserialiseren automatisch de gegevensset naar een pandas DataFrame-object. Dit wordt gedaan `to_dataframe` met de methode:

    frame = ds.to_dataframe()

Als u de ruwe gegevens liever downloadt en de deserialisatie zelf uitvoert, is dat een optie. Op dit moment is dit de enige optie voor formaten zoals 'ARFF', die de Python-clientbibliotheek niet kan deserialiseren.

Ga als volgt te werk om de inhoud als tekst te lezen:

    text_data = ds.read_as_text()

Ga als volgt te werk om de inhoud als binair te lezen:

    binary_data = ds.read_as_binary()

U ook gewoon een stream openen naar de inhoud:

    with ds.open() as file:
        binary_data_chunk = file.read(1000)


### <a name="create-a-new-dataset"></a>Een nieuwe gegevensset maken
Met de Python-clientbibliotheek u gegevenssets uploaden vanuit uw Python-programma. Deze gegevenssets zijn dan beschikbaar voor gebruik in uw werkruimte.

Als u uw gegevens in een pandas DataFrame hebt, gebruikt u de volgende code:

    from azureml import DataTypeIds

    dataset = ws.datasets.add_from_dataframe(
        dataframe=frame,
        data_type_id=DataTypeIds.GenericCSV,
        name='my new dataset',
        description='my description'
    )

Als uw gegevens al geserialiseerd zijn, u het als:

    from azureml import DataTypeIds

    dataset = ws.datasets.add_from_raw_data(
        raw_data=raw_data,
        data_type_id=DataTypeIds.GenericCSV,
        name='my new dataset',
        description='my description'
    )

De Python-clientbibliotheek kan een panda's DataFrame serialiseren naar de volgende `azureml.DataTypeIds` indelingen (constanten hiervoor zijn in de klasse):

* PlainText
* GeneriekeCSV
* GenericTSV (GenericTSV)
* GeneriekeCSVNoHeader
* GenerictSVNoHeader

### <a name="update-an-existing-dataset"></a>Een bestaande gegevensset bijwerken
Als u een nieuwe gegevensset probeert te uploaden met een naam die overeenkomt met een bestaande gegevensset, moet u een conflictfout krijgen.

Als u een bestaande gegevensset wilt bijwerken, moet u eerst een verwijzing naar de bestaande gegevensset krijgen:

    dataset = ws.datasets['existing dataset']

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

Vervolgens `update_from_dataframe` kunt u de inhoud van de gegevensset op Azure serialiseren en vervangen:

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(frame2)

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

Als u de gegevens wilt serialiseren naar een andere indeling, geeft u een waarde op voor de optionele `data_type_id` parameter.

    from azureml import DataTypeIds

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        data_type_id=DataTypeIds.GenericTSV,
    )

    print(dataset.data_type_id) # 'GenericTSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

U optioneel een nieuwe beschrijving instellen door `description` een waarde voor de parameter op te geven.

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        description='data up to feb 2015',
    )

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to feb 2015'

U optioneel een nieuwe naam instellen door `name` een waarde voor de parameter op te geven. Vanaf nu haalt u de gegevensset alleen op met alleen de nieuwe naam. Met de volgende code worden de gegevens, naam en beschrijving bijgewerkt.

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        name='existing dataset v2',
        description='data up to feb 2015',
    )

    print(dataset.data_type_id)                    # 'GenericCSV'
    print(dataset.name)                            # 'existing dataset v2'
    print(dataset.description)                     # 'data up to feb 2015'

    print(ws.datasets['existing dataset v2'].name) # 'existing dataset v2'
    print(ws.datasets['existing dataset'].name)    # IndexError

De `data_type_id` `name` parameters `description` en parameters zijn optioneel en standaard voor hun vorige waarde. De `dataframe` parameter is altijd vereist.

Als uw gegevens al geserialiseerd zijn, gebruikt u in `update_from_raw_data` plaats van `update_from_dataframe`. Als je gewoon `raw_data` passeren `dataframe`in plaats van , het werkt op een vergelijkbare manier.

<!-- Images -->
[security]:./media/python-data-access/security.png
[dataset-format]:./media/python-data-access/dataset-format.png
[csv-format]:./media/python-data-access/csv-format.png
[datasets]:./media/python-data-access/datasets.png
[dataset-access-code]:./media/python-data-access/dataset-access-code.png
[ipython-dataset]:./media/python-data-access/ipython-dataset.png
[experiment]:./media/python-data-access/experiment.png
[intermediate-dataset-access-code]:./media/python-data-access/intermediate-dataset-access-code.png
[ipython-intermediate-dataset]:./media/python-data-access/ipython-intermediate-dataset.png
[ipython-histogram]:./media/python-data-access/ipython-histogram.png


<!-- Module References -->
[convert-to-csv]: https://msdn.microsoft.com/library/azure/faa6ba63-383c-4086-ba58-7abf26b85814/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/

