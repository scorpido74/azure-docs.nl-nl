---
title: 'Gegevens importeren: moduleverwijzing'
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de module Gegevens importeren in Azure Machine Learning om gegevens te laden in een machine learning-pijplijn van bestaande cloudgegevensservices.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: d124fdc15bd34743b237985a66cc35625f5d9a4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456196"
---
# <a name="import-data-module"></a>Module Gegevens importeren

In dit artikel wordt een module beschreven in Azure Machine Learning designer (preview).

Gebruik deze module om gegevens te laden in een machine learning-pijplijn van bestaande cloudgegevensservices. 

> [!Note]
> Alle functionaliteit die door deze module kan worden gedaan door **datastore** en **datasets** in de worksapce landing page. We raden u aan **datastore** en **dataset** te gebruiken die extra functies bevatten, zoals gegevensbewaking. Zie Het [artikel](../how-to-create-register-datasets.md) [Gegevenssets](../how-to-access-data.md) registreren voor meer informatie.
> Nadat u een gegevensset hebt geregistreerd, u deze vinden in de categorie **Gegevenssets** -> **Mijn gegevenssets** in de ontwerpinterface. Deze module is gereserveerd voor Studio(klassieke) gebruikers voor een vertrouwde ervaring. 
>

De module **Gegevens importeren** ondersteunt leesgegevens uit de volgende bronnen:

- URL via HTTP
- Azure-cloudopslag via [**Datastores**](../how-to-access-data.md))
    - Azure Blob-container
    - Azure-bestandsshare
    - Azure Data Lake
    - Azure Data Lake Gen2
    - Azure SQL Database
    - Azure PostgreSQL    

Voordat u cloudopslag gebruikt, moet u eerst een gegevensarchief registreren in uw Azure Machine Learning-werkruimte. Zie [Hoe toegang tot gegevens .](../how-to-access-data.md) 

Nadat u de gewenste gegevens hebt gedefinieerd en verbinding hebt gemaakt met de bron, **[leidt ImportGegevens](./import-data.md)** het gegevenstype van elke kolom af op basis van de waarden die deze bevat en worden de gegevens in uw ontwerppijplijn geladen. De uitvoer van **Importgegevens** is een gegevensset die kan worden gebruikt met elke ontwerppijplijn.

Als uw brongegevens worden gewijzigd, u de gegevensset vernieuwen en nieuwe gegevens toevoegen door [Gegevens importeren](./import-data.md)opnieuw uit te voeren.

## <a name="how-to-configure-import-data"></a>Gegevens importeren configureren

1. Voeg de module **Gegevens importeren** toe aan uw pijplijn. U vindt deze module in de categorie **Gegevensinvoer en Uitvoer** in de ontwerper.

1. Selecteer de module om het rechterdeelvenster te openen.

1. Selecteer **Gegevensbron**en kies het gegevensbrontype. Het kan HTTP of datastore zijn.

    Als u datastore kiest, u bestaande gegevenswinkels selecteren die zich al hebben geregistreerd bij uw Azure Machine Learning-werkruimte of een nieuw gegevensarchief maken. Definieer vervolgens het pad van gegevens dat u wilt importeren in het gegevensarchief. U eenvoudig door het pad bladeren door **op Pad** ![import-datapad bladeren](media/module/import-data-path.png)

1. Selecteer het voorbeeldschema om de kolommen te filteren die u wilt opnemen. U ook geavanceerde instellingen definiëren, zoals Delimiter in Parsing-opties.

    ![import-data-preview](media/module/import-data.png)

1. Het selectievakje **Regenerate output**bepaalt of de module moet worden uitgevoerd om de uitvoer bij het uitvoeren van de looptijd te regenereren. 

    Het is standaard niet geselecteerd, wat betekent dat als de module eerder met dezelfde parameters is uitgevoerd, het systeem de uitvoer van de laatste uitvoering opnieuw gebruikt om de runtime te verkorten. 

    Als deze optie is geselecteerd, voert het systeem de module opnieuw uit om de uitvoer te regenereren. Dus selecteer deze optie wanneer onderliggende gegevens in de opslag wordt bijgewerkt, kan het helpen om de nieuwste gegevens te krijgen.


1. Verzend de pijplijn.

    Wanneer Importgegevens de gegevens in de ontwerper laadt, wordt het gegevenstype van elke kolom afgetrokken op basis van de waarden die deze bevat, numeriek of categorisch.

    Als er een koptekst aanwezig is, wordt de koptekst gebruikt om de kolommen van de uitvoergegevensset een naam te geven.

    Als er geen bestaande kolomkoppen in de gegevens zijn, worden nieuwe kolomnamen gegenereerd met de indeling col1, col2,... , coln*.

## <a name="results"></a>Resultaten

Wanneer importeren is voltooid, klikt u op de uitvoergegevensset en selecteert **u Visualiseren** om te zien of de gegevens zijn geïmporteerd.

Als u de gegevens wilt opslaan voor hergebruik, in plaats van telkens een nieuwe set gegevens te importeren wanneer de pijplijn wordt uitgevoerd, selecteert u het pictogram **Gegevenssetgegevens registreren** onder het tabblad **Uitvoer** in het rechterdeelvenster van de module. Kies een naam voor de gegevensset. De opgeslagen gegevensset behoudt de gegevens op het moment van opslaan, de gegevensset wordt niet bijgewerkt wanneer de pijplijn opnieuw wordt uitgevoerd, zelfs als de gegevensset in de pijplijn wordt gewijzigd. Dit kan handig zijn voor het maken van snapshots van gegevens.

Na het importeren van de gegevens, kan het nodig zijn een aantal extra voorbereidingen voor modellering en analyse:

- Gebruik [Metagegevens bewerken](./edit-metadata.md) om kolomnamen te wijzigen, een kolom als een ander gegevenstype te verwerken of om aan te geven dat sommige kolommen labels of functies zijn.

- Gebruik [Kolommen selecteren in gegevensset](./select-columns-in-dataset.md) om een subset van kolommen te selecteren die u wilt transformeren of gebruiken in modellering. De getransformeerde of verwijderde kolommen kunnen eenvoudig worden samengevoegd met de oorspronkelijke gegevensset met behulp van de module [Kolommen toevoegen.](./add-columns.md)  

- Gebruik [Partitie en voorbeeld](./partition-and-sample.md) om de gegevensset te verdelen, steekproeven uit te voeren of de bovenste n-rijen te krijgen.

## <a name="next-steps"></a>Volgende stappen

Bekijk de [set modules die beschikbaar zijn](module-reference.md) voor Azure Machine Learning. 
