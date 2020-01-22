---
title: Gegevens importeren
titleSuffix: Azure Machine Learning
description: Meer informatie over het importeren van gegevens in Azure Machine Learning Designer vanuit verschillende gegevens bronnen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
author: peterclu
ms.author: peterlu
ms.date: 01/16/2020
ms.openlocfilehash: 5df00d68b7114cc489b0708f5c2e401c87d0be15
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2020
ms.locfileid: "76314025"
---
# <a name="import-your-data-into-azure-machine-learning-designer"></a>Uw gegevens importeren in Azure Machine Learning Designer

In dit artikel leert u hoe u uw eigen gegevens in de ontwerp functie kunt importeren om aangepaste oplossingen te maken. Er zijn twee manieren waarop u gegevens kunt importeren in de ontwerp functie: 

* **Azure machine learning gegevens sets** : [gegevens sets](concept-data.md#datasets) registreren in azure machine learning om geavanceerde functies in te scha kelen die u helpen bij het beheren van uw gegevens.
* **Gegevens module importeren** : gebruik de module [gegevens importeren](algorithm-module-reference/import-data.md) om rechtstreeks toegang te krijgen tot gegevens uit online gegevens bronnen.

## <a name="use-azure-machine-learning-datasets"></a>Azure Machine Learning gegevens sets gebruiken

U wordt aangeraden gegevens [sets](concept-data.md#datasets) te gebruiken voor het importeren in de ontwerp functie. Wanneer u een gegevensset registreert, kunt u profiteren van geavanceerde gegevens functies, zoals [versie beheer en bijhouden](how-to-version-track-datasets.md) van [gegevens](how-to-monitor-datasets.md).

### <a name="register-a-dataset"></a>Een gegevensset registreren

U kunt bestaande gegevens sets [programmatisch registreren met de SDK](how-to-create-register-datasets.md#use-the-sdk) of [visueel in azure machine learning Studio](how-to-create-register-datasets.md#use-the-ui).

U kunt de uitvoer voor elke ontwerp module ook registreren als een gegevensset.

1. Selecteer de module die de gegevens levert die u wilt registreren.

1. Selecteer in het deel venster Eigenschappen de optie **uitvoer** > **gegevensset registreren**.

    ![Scherm afbeelding die laat zien hoe u kunt navigeren naar de optie gegevensset registreren](media/how-to-designer-import-data/register-dataset-designer.png)

### <a name="use-a-dataset"></a>Een gegevensset gebruiken

U kunt de geregistreerde gegevens sets vinden in het palet module, onder **gegevens sets** > **mijn gegevens sets**. Als u een gegevensset wilt gebruiken, sleept u deze naar het pijp lijn-canvas. Verbind vervolgens de uitvoer poort van de gegevensset met andere modules in het palet.

![Scherm opname van de locatie van opgeslagen gegevens sets in het palet voor ontwerpen](media/how-to-designer-import-data/use-datasets-designer.png)

Alle [bestanden](how-to-create-register-datasets.md#dataset-types) die in uw machine learning-werk ruimte zijn geregistreerd, worden weer gegeven in het palet van de module. U bent niet beperkt tot het gebruik van gegevens sets die zijn gemaakt in de ontwerp functie.

> [!NOTE]
> De ontwerp functie biedt momenteel alleen ondersteuning voor het verwerken van [tabellaire gegevens sets](how-to-create-register-datasets.md#dataset-types). Als u [bestands gegevens sets](how-to-create-register-datasets.md#dataset-types)wilt gebruiken, gebruikt u de Azure machine learning SDK die beschikbaar is voor python en R.

## <a name="import-data-using-the-import-data-module"></a>Gegevens importeren met behulp van de module gegevens importeren

We raden u aan om gegevens sets te gebruiken voor het importeren van informatie, maar u kunt ook de module [gegevens importeren](algorithm-module-reference/import-data.md) gebruiken. De module gegevens importeren slaat de registratie van uw gegevensset in Azure Machine Learning over en importeert gegevens rechtstreeks vanuit een [Data Store](concept-data.md#datastores) -of http-URL.

Zie de [referentie pagina gegevens importeren](algorithm-module-reference/import-data.md)voor gedetailleerde informatie over het gebruik van de module gegevens importeren.


## <a name="supported-sources"></a>Ondersteunde bronnen

In deze sectie vindt u de gegevens bronnen die worden ondersteund door de ontwerper. Gegevens worden in de ontwerp functie geleverd vanuit een gegevens opslag of een [gegevensset in tabel vorm](how-to-create-register-datasets.md#dataset-types).

### <a name="datastore-sources"></a>Data Store-bronnen
Zie [toegang tot gegevens in azure Storage-services](how-to-access-data.md#supported-data-storage-service-types)voor een lijst met ondersteunde gegevens opslag bronnen.

### <a name="tabular-dataset-sources"></a>Bron tabel van gegevensset

De ontwerp functie ondersteunt tabellaire gegevens sets die zijn gemaakt op basis van de volgende bronnen:
 * Bestanden met scheidings tekens
 * JSON-bestanden
 * Parquet-bestanden
 * SQL-query's

## <a name="data-types"></a>Gegevenstypen

De Designer herkent intern de volgende gegevens typen:

* Tekenreeks
* Geheel getal
* Decimal
* Booleaans
* Datum

De ontwerp functie maakt gebruik van een intern gegevens type om gegevens tussen modules door te geven. U kunt uw gegevens in de indeling van de gegevens tabel expliciet converteren met behulp van de module [converteren naar dataset](algorithm-module-reference/convert-to-dataset.md) . Alle modules die andere indelingen dan de interne indeling accepteren, worden de gegevens op de achtergrond geconverteerd voordat deze aan de volgende module worden door gegeven.

## <a name="data-constraints"></a>Gegevens beperkingen

Modules in de ontwerp functie worden beperkt door de grootte van het berekenings doel. Voor grotere gegevens sets moet u een grotere Azure Machine Learning Reken Resource gebruiken. Zie [Wat zijn Compute-doelen in azure machine learning?](concept-compute-target.md#azure-machine-learning-compute-managed) voor meer informatie over Azure machine learning compute.

## <a name="next-steps"></a>Volgende stappen

Leer de basis beginselen van de ontwerp functie met [de zelf studie: prijs van auto Mobile voor speld met de ontwerp functie](tutorial-designer-automobile-price-train-score.md).