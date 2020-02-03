---
title: Toegang tot gegevenssets met Python-clientbibliotheek - Team Data Science Process
description: Installeer en gebruik van de Python-clientbibliotheek voor toegang tot en beheer van Azure Machine Learning-gegevens veilig vanuit een lokale Python-omgeving.
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
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720976"
---
# <a name="access-datasets-with-python-using-the-azure-machine-learning-python-client-library"></a>Toegang tot gegevenssets met Python met behulp van de clientbibliotheek van Azure Machine Learning Python
De Preview-versie van Microsoft Azure Machine Learning Python-clientbibliotheek kunt inschakelen van beveiligde toegang tot uw Azure Machine Learning-gegevenssets van een lokale Python-omgeving en kunt het maken en beheren van gegevenssets in een werkruimte.

In dit onderwerp bevat instructies over het:

* installeren van de Machine Learning Python-clientbibliotheek
* toegang tot en het uploaden van gegevenssets, zoals instructies voor het ophalen van de machtiging voor toegang tot Azure Machine Learning-gegevenssets van uw lokale omgeving voor Python
* toegang tot de tussenliggende gegevenssets van experimenten
* Gebruik de python-client bibliotheek om gegevens sets op te sommen, toegang tot meta gegevens te krijgen, de inhoud van een gegevensset te lezen, nieuwe gegevens sets te maken en bestaande gegevens sets bij te werken

## <a name="prerequisites"></a>Vereisten
De Python-clientbibliotheek is getest onder de volgende omgevingen:

* Windows, Mac en Linux
* Python 2.7, 3.3 en 3.4

Dit is afhankelijk van de volgende pakketten:

* aanvragen
* Python-dateutil
* pandas

U kunt het beste een python-distributie gebruiken, zoals [Anaconda](http://continuum.io/downloads#all) of [Canopy](https://store.enthought.com/downloads/), die wordt geleverd met python, IPython en de drie bovenstaande pakketten die hierboven zijn geïnstalleerd. Hoewel IPython niet strikt vereist is, is een geweldige omgeving voor het werken met en gegevens interactief te visualiseren.

### <a name="installation"></a>De Azure Machine Learning python-client bibliotheek installeren
Installeer de Azure Machine Learning python-client bibliotheek om de taken uit te voeren die in dit onderwerp worden beschreven. Deze bibliotheek is beschikbaar vanuit de [python-pakket index](https://pypi.python.org/pypi/azureml). Om het te installeren in uw Python-omgeving, voer de volgende opdracht uit uw lokale Python-omgeving:

    pip install azureml

U kunt ook downloaden en installeren vanuit de bronnen op [github](https://github.com/Azure/Azure-MachineLearning-ClientLibrary-Python).

    python setup.py install

Als u git op uw computer geïnstalleerd hebt, kunt u pip gebruiken om rechtstreeks vanuit de git-opslagplaats te installeren:

    pip install git+https://github.com/Azure/Azure-MachineLearning-ClientLibrary-Python.git


## <a name="datasetAccess"></a>Code fragmenten gebruiken voor toegang tot gegevens sets
De Python-clientbibliotheek biedt u programmatische toegang tot uw bestaande gegevenssets van experimenten die zijn uitgevoerd.

Vanuit de webinterface Azure Machine Learning Studio (klassiek) kunt u code fragmenten genereren die alle benodigde informatie bevatten voor het downloaden en deserialiseren van gegevens sets als Panda data frame-objecten op uw lokale computer.

### <a name="security"></a>Beveiliging voor gegevens toegang
De code fragmenten die worden geleverd door Azure Machine Learning Studio (klassiek) voor gebruik met de python-client bibliotheek bevatten uw werk ruimte-ID en autorisatie token. Deze bieden volledige toegang tot uw werkruimte en moeten worden beveiligd, zoals een wachtwoord.

Uit veiligheids overwegingen is de functie code fragment alleen beschikbaar voor gebruikers die hun rol hebben ingesteld als **eigenaar** voor de werk ruimte. Uw rol wordt weer gegeven in Azure Machine Learning Studio (klassiek) op de pagina **gebruikers** onder **instellingen**.

![Beveiliging][security]

Als uw rol niet is ingesteld als **eigenaar**, kunt u aanvragen om opnieuw te worden uitgenodigd als eigenaar of vraagt u de eigenaar van de werk ruimte u het code fragment te geven.

Als u het autorisatie token wilt ophalen, kunt u een van de volgende opties kiezen:

* Vragen om een token van een eigenaar. Eigen aren hebben toegang tot hun autorisatie tokens op de pagina instellingen van hun werk ruimte in Azure Machine Learning Studio (klassiek). Selecteer **instellingen** in het linkerdeel venster en klik op **autorisatie tokens** om de primaire en secundaire tokens weer te geven. Hoewel de primaire of de secundaire autorisatietokens kunnen worden gebruikt in het codefragment, verdient het aanbeveling dat de eigenaren van de secundaire autorisatietokens alleen delen.

   ![Autorisatietokens](./media/python-data-access/ml-python-access-settings-tokens.png)

* Vraag om te worden gepromoveerd tot rol van eigenaar: een huidige eigenaar van de werk ruimte moet u eerst uit de werk ruimte verwijderen en vervolgens als eigenaar uitnodigen.

Zodra ontwikkel aars de werk ruimte-ID en het autorisatie token hebben verkregen, kunnen ze toegang krijgen tot de werk ruimte met behulp van het code fragment, ongeacht hun rol.

Verificatie tokens worden beheerd op de pagina **autorisatie tokens** onder **instellingen**. U kunt ze genereren, maar deze procedure trekt u toegang tot het vorige token.

### <a name="accessingDatasets"></a>Gegevens sets openen vanuit een lokale python-toepassing
1. Klik in Machine Learning Studio (klassiek) op **gegevens sets** in de navigatie balk aan de linkerkant.
2. Selecteer de gegevensset die u wilt openen. U kunt een wille keurig gegevens sets selecteren in de lijst met **mijn gegevens sets** of in de lijst met voor **beelden** .
3. Klik in de onderste werk balk op **gegevens toegangs code genereren**. Als de gegevens in een indeling die niet compatibel met de Python-clientbibliotheek is, wordt deze knop is uitgeschakeld.
   
    ![Gegevenssets][datasets]
4. Selecteer het codefragment in het venster dat wordt weergegeven en deze naar het Klembord te kopiëren.
   
    ![Data access code knop genereren][dataset-access-code]
5. Plak de code in het notitieblok van uw lokale Python-toepassing.
   
    ![Plak de code in de notebook][ipython-dataset]

## <a name="accessingIntermediateDatasets"></a>Toegang tot tussenliggende gegevens sets vanuit Machine Learning experimenten
Nadat een experiment is uitgevoerd in Machine Learning Studio (klassiek), is het mogelijk om toegang te krijgen tot de tussenliggende gegevens sets van de uitvoer knooppunten van modules. Tussenliggende gegevenssets zijn de gegevens die is gemaakt en gebruikt voor de tussenliggende stappen wanneer een model-hulpprogramma is uitgevoerd.

Tussenliggende gegevenssets kunnen worden geopend, zolang de gegevensindeling compatibel met de Python-clientbibliotheek is.

De volgende indelingen worden ondersteund (constanten voor deze indelingen bevinden zich in de `azureml.DataTypeIds` klasse):

* Als tekst zonder opmaak
* GenericCSV
* GenericTSV
* GenericCSVNoHeader
* GenericTSVNoHeader

U kunt de indeling bepalen door de muiswijzer op een knooppunt van de uitvoer module. Deze wordt weergegeven, samen met de naam van het knooppunt in de knopinfo.

Sommige modules, zoals de [Splits][split] module, worden uitgevoerd op een indeling met de naam `Dataset`, die niet wordt ondersteund door de python-client bibliotheek.

![Indeling van de gegevensset][dataset-format]

U moet een conversie module, zoals [converteren naar CSV][convert-to-csv], gebruiken om een uitvoer in een ondersteunde indeling te krijgen.

![GenericCSV indeling][csv-format]

De volgende stappen ziet een voorbeeld van een experiment maakt, wordt uitgevoerd en heeft toegang tot de tussenliggende gegevensset.

1. Een nieuw experiment maken.
2. Voeg een module voor de **binaire classificatie van de volwassen telling** van de inkomsten toe.
3. Voeg een [Splits][split] module in en verbind de invoer met de module uitvoer van de gegevensset.
4. Voeg een [conversie naar CSV][convert-to-csv] -module toe en verbind de invoer met een van de [gesplitste][split] module-uitvoer.
5. Sla het experiment op, voer het uit en wacht totdat de taak is voltooid.
6. Klik op het uitvoer knooppunt in de module [converteren naar CSV][convert-to-csv] .
7. Wanneer het context menu wordt weer gegeven, selecteert u **gegevens toegangs code genereren**.
   
    ![Contextmenu][experiment]
8. Selecteer het codefragment en naar het Klembord kopiëren vanuit het venster dat wordt weergegeven.
   
    ![Code voor gegevenstoegang genereren in contextmenu][intermediate-dataset-access-code]
9. Plak de code in uw laptop.
   
    ![Plak de code in de notebook][ipython-intermediate-dataset]
10. U kunt de gegevens visualiseren met matplotlib. Dit wordt weergegeven in een histogram van de kolom Leeftijd:
    
    ![Histogram][ipython-histogram]

## <a name="clientApis"></a>De Machine Learning python-client bibliotheek gebruiken voor het openen, lezen, maken en beheren van gegevens sets
### <a name="workspace"></a>Werkruimte
De werkruimte is het toegangspunt voor de Python-clientbibliotheek. Geef de klasse `Workspace` op met uw werk ruimte-ID en autorisatie token om een exemplaar te maken:

    ws = Workspace(workspace_id='4c29e1adeba2e5a7cbeb0e4f4adfb4df',
                   authorization_token='f4f3ade2c6aefdb1afb043cd8bcf3daf')


### <a name="enumerate-datasets"></a>Sommen, gegevenssets
Het inventariseren van alle gegevenssets in een opgegeven werkruimte:

    for ds in ws.datasets:
        print(ds.name)

Alleen de gebruiker gemaakte gegevenssets inventariseren:

    for ds in ws.user_datasets:
        print(ds.name)

Het opsommen van alleen de voorbeeld-gegevenssets:

    for ds in ws.example_datasets:
        print(ds.name)

U kunt toegang tot een gegevensset met de naam (dit is hoofdlettergevoelig):

    ds = ws.datasets['my dataset name']

Of u het kunt openen met de index:

    ds = ws.datasets[0]


### <a name="metadata"></a>Metagegevens
Gegevenssets hebben metagegevens, naast de inhoud. (Tussenliggende gegevenssets vormen een uitzondering op deze regel en hoeft niet alle metagegevens.)

Sommige metagegevenswaarden van de zijn toegewezen door de gebruiker tijdens het maken:

    print(ds.name)
    print(ds.description)
    print(ds.family_id)
    print(ds.data_type_id)

Anderen zijn waarden die door Azure ML is toegewezen:

    print(ds.id)
    print(ds.created_date)
    print(ds.size)

Zie de klasse `SourceDataset` voor meer informatie over de beschik bare meta gegevens.

### <a name="read-contents"></a>Inhoud lezen
De code fragmenten die worden verschaft door Machine Learning Studio (klassiek), downloaden en deserialiseren de gegevensset automatisch naar een Panda data frame-object. Dit wordt gedaan met de `to_dataframe` methode:

    frame = ds.to_dataframe()

Als u liever de onbewerkte gegevens te downloaden en uitvoeren van de deserialisatie zelf, kan die worden gebruikt. Op dit moment is dit de enige optie voor indelingen zoals 'ARFF', de Python-clientbibliotheek kan niet worden gedeserialiseerd.

De inhoud lezen als tekst:

    text_data = ds.read_as_text()

De inhoud lezen als binair bestand:

    binary_data = ds.read_as_binary()

U kunt ook gewoon een stroom om de inhoud te openen:

    with ds.open() as file:
        binary_data_chunk = file.read(1000)


### <a name="create-a-new-dataset"></a>Maak een nieuwe gegevensset
De Python-clientbibliotheek kunt u het uploaden van gegevenssets van uw Python-programma. Deze gegevenssets zijn vervolgens beschikbaar voor gebruik in uw werkruimte.

Als u uw gegevens in een pandas DataFrame hebt, gebruikt u de volgende code:

    from azureml import DataTypeIds

    dataset = ws.datasets.add_from_dataframe(
        dataframe=frame,
        data_type_id=DataTypeIds.GenericCSV,
        name='my new dataset',
        description='my description'
    )

Als uw gegevens al is geserialiseerd, kunt u het volgende gebruiken:

    from azureml import DataTypeIds

    dataset = ws.datasets.add_from_raw_data(
        raw_data=raw_data,
        data_type_id=DataTypeIds.GenericCSV,
        name='my new dataset',
        description='my description'
    )

De python-client bibliotheek kan een Panda data frame serialiseren met de volgende indelingen (constanten voor deze bevinden zich in de `azureml.DataTypeIds` klasse):

* Als tekst zonder opmaak
* GenericCSV
* GenericTSV
* GenericCSVNoHeader
* GenericTSVNoHeader

### <a name="update-an-existing-dataset"></a>Bijwerken van een bestaande gegevensset
Als u probeert te uploaden van een nieuwe gegevensset met een naam die overeenkomt met een bestaande gegevensset, moet u een conflict opgetreden.

Voor het bijwerken van een bestaande gegevensset, moet u eerst een verwijzing naar de bestaande gegevensset opgehaald:

    dataset = ws.datasets['existing dataset']

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

Gebruik vervolgens `update_from_dataframe` om de inhoud van de gegevensset op Azure te serialiseren en te vervangen:

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(frame2)

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

Als u de gegevens wilt serialiseren naar een andere indeling, geeft u een waarde op voor de optionele para meter `data_type_id`.

    from azureml import DataTypeIds

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        data_type_id=DataTypeIds.GenericTSV,
    )

    print(dataset.data_type_id) # 'GenericTSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

U kunt desgewenst een nieuwe beschrijving instellen door een waarde op te geven voor de para meter `description`.

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        description='data up to feb 2015',
    )

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to feb 2015'

U kunt desgewenst een nieuwe naam instellen door een waarde voor de para meter `name` op te geven. Van nu af, zult u de gegevensset met behulp van de nieuwe naam alleen ophalen. Met de volgende code worden de gegevens, naam en beschrijving bijgewerkt.

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

De para meters `data_type_id`, `name` en `description` zijn optioneel en zijn standaard ingesteld op de vorige waarde. De para meter `dataframe` is altijd vereist.

Als uw gegevens al zijn geserialiseerd, gebruikt u `update_from_raw_data` in plaats van `update_from_dataframe`. Als u alleen de `raw_data` doorgeeft in plaats van `dataframe`, werkt het op een vergelijk bare manier.

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

