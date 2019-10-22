---
title: 'Gegevens importeren: module verwijzing'
titleSuffix: Azure Machine Learning service
description: Meer informatie over het gebruik van de module gegevens importeren in Azure Machine Learning service voor het laden van gegevens in een machine learning pijp lijn vanuit bestaande Cloud gegevens Services.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: fef7d686479b24b0402ab6f1e6990df74231b8d6
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693138"
---
# <a name="import-data-module"></a>Gegevens module importeren

In dit artikel wordt een module van de Visual Interface (preview) voor de Azure Machine Learning-service beschreven.

Gebruik deze module om gegevens te laden in een machine learning pijp lijn vanuit bestaande Cloud Data Services.  

Kies eerst het type op de cloud gebaseerde opslag dat u wilt lezen en voltooi de aanvullende instellingen. Nadat u de gewenste gegevens hebt gedefinieerd en verbinding wilt maken met de [bron, wordt](./import-data.md) het gegevens type van elke kolom op basis van de waarden die deze bevat, weer gegeven en worden de gegevens in uw Azure machine learning-werk ruimte geladen. De uitvoer van [import gegevens](./import-data.md) is een gegevensset die met elke pijp lijn kan worden gebruikt.

  
Als de bron gegevens worden gewijzigd, kunt u de gegevensset vernieuwen en nieuwe gegevens toevoegen door [import gegevens](./import-data.md)opnieuw uit te voeren. Als u echter niet wilt dat elke keer dat u de pijp lijn uitvoert, de bron opnieuw moet worden gelezen, selecteert u de optie **in cache opgeslagen resultaten gebruiken** op waar. Als deze optie is geselecteerd, controleert de module of de pijp lijn eerder is uitgevoerd met dezelfde bron en dezelfde invoer opties. Als er een vorige run wordt gevonden, worden de gegevens in de cache gebruikt, in plaats van de gegevens uit de bron opnieuw te laden.
 

## <a name="data-sources"></a>Gegevensbronnen

De module gegevens importeren ondersteunt de volgende gegevens bronnen. Klik op de koppelingen voor gedetailleerde instructies en voor beelden van het gebruik van elke gegevens bron. 
 
Als u niet zeker weet hoe u uw gegevens moet opslaan, raadpleegt u deze hand leiding voor algemene gegevens scenario's in het data Science-proces: [scenario's voor geavanceerde analyses in azure machine learning](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-plan-sample-scenarios). 


|Gegevens bron| Gebruiken met|
|-----------|-----------|  
|[Web-URL via HTTP](./import-from-web-url-via-http.md)|Gegevens ophalen die worden gehost op een web-URL die gebruikmaakt van HTTP en die is opgenomen in de CSV-, TSV-, ARFF-of SvmLight-indelingen|  
|[Importeren uit Azure Blob Storage](./import-from-azure-blob-storage.md) |Gegevens ophalen die zijn opgeslagen in de Azure Blob-service|  
|[Importeren uit Azure SQL Database](./import-from-azure-sql-database.md) |Gegevens ophalen uit Azure SQL Database|

## <a name="how-to-configure-import-data"></a>Import gegevens configureren
 
1. Voeg de module **gegevens importeren** toe aan de pijp lijn. Deze module vindt u in de categorie **gegevens invoer en uitvoer** van de interface.

1. Klik op **gegevens bron**en kies het type op de cloud gebaseerde opslag waarvan u een lees bewerking wilt maken. 

    Aanvullende instellingen zijn afhankelijk van het type opslag dat u kiest, en of de opslag is beveiligd of niet. Mogelijk moet u de account naam, het bestands type of de referenties opgeven. Voor sommige bronnen is geen verificatie vereist; voor anderen moet u mogelijk de account naam, een sleutel of de naam van de container weten.

1. Selecteer de optie **in cache opgeslagen resultaten gebruiken** als u de gegevensset in de cache wilt opslaan voor hergebruik op opeenvolgende uitvoeringen.

    Ervan uitgaande dat er geen andere wijzigingen zijn aangebracht in de module parameters, laadt de pijp lijn de gegevens alleen de eerste keer dat de module wordt uitgevoerd en wordt vervolgens een versie van de gegevensset in de cache gebruikt.

    Schakel deze optie uit als u de gegevens telkens opnieuw wilt laden wanneer u de pijp lijn uitvoert.

1. Voer de pijplijn uit.

    Wanneer gegevens importeren de gegevens in de interface laadt, wordt het gegevens type van elke kolom afgeleid op basis van de waarden die het bevat, ofwel numeriek of categorische.

    - Als er een header aanwezig is, wordt de header gebruikt om de kolommen van de uitvoer gegevensset te benoemen.

    - Als er geen bestaande kolom koppen in de gegevens staan, worden nieuwe kolom namen gegenereerd met de indeling Kol1, col2,... , coln*.

## <a name="results"></a>Resultaten

Wanneer het importeren is voltooid, klikt u op de uitvoer gegevensset en selecteert u **visualiseren** om te zien of de gegevens zijn geïmporteerd.

Als u de gegevens voor hergebruik wilt opslaan, in plaats van een nieuwe set gegevens te importeren telkens wanneer de pijp lijn wordt uitgevoerd, klikt u met de rechter muisknop op de uitvoer en selecteert u **Opslaan als gegevensset**. Kies een naam voor de gegevensset. De opgeslagen gegevensset behoudt de gegevens op het moment van opslaan en er worden geen gegevens bijgewerkt wanneer de pijp lijn opnieuw wordt uitgevoerd, zelfs als de gegevensset in de pijp lijn wordt gewijzigd. Dit kan handig zijn voor het maken van moment opnamen van gegevens.

Na het importeren van de gegevens zijn mogelijk extra voor bereid voor het maken en analyseren van modellen nodig:


- Gebruik [meta gegevens bewerken](./edit-metadata.md) om kolom namen te wijzigen, om een kolom te verwerken als een ander gegevens type, of om aan te geven dat sommige kolommen labels of functies zijn.

- Gebruik [select columns in dataset](./select-columns-in-dataset.md) om een subset van kolommen te selecteren die u wilt transformeren of gebruiken in model lering. De getransformeerde of verwijderde kolommen kunnen eenvoudig opnieuw worden samengevoegd met de oorspronkelijke gegevensset met behulp van de module [add columns](./add-columns.md) .  

- Gebruik [partitie en voor beeld](./partition-and-sample.md) om de gegevensset te verdelen, steek proeven uit te voeren of de bovenste n rijen te verkrijgen.

## <a name="next-steps"></a>Volgende stappen

Bekijk de [set beschik bare modules](module-reference.md) voor Azure machine learning service. 