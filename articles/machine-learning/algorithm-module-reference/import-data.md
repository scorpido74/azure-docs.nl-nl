---
title: 'Gegevens importeren: Module verwijzing'
titleSuffix: Azure Machine Learning service
description: Informatie over het gebruik van de module gegevens importeren in Azure Machine Learning-service om gegevens te laden in een machine learning experiment van bestaande Cloud-gegevens Services.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: e8522c759efb0cc599bbcf8e7569f6045da571a8
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128789"
---
# <a name="import-data-module"></a>Gegevens module importeren

In dit artikel wordt een module van de Visual Interface (preview) voor de Azure Machine Learning-service beschreven.

Gebruik deze module om gegevens te laden in een machine learning experiment vanuit bestaande Cloud Data Services.  
De module bevat nu een wizard die u helpt bij het kiezen van een opslag optie en het selecteren van een van de bestaande abonnementen en accounts voor het snel configureren van alle opties. Wilt u een bestaande gegevens verbinding bewerken? Geen probleem; de wizard laadt alle vorige configuratie details zodat u niet opnieuw hoeft te beginnen. 
  
Nadat u de gewenste gegevens hebt gedefinieerd en verbinding wilt maken met de [](./import-data.md) bron, wordt het gegevens type van elke kolom op basis van de waarden die deze bevat, weer gegeven en worden de gegevens in uw Azure machine learning-werk ruimte geladen. De uitvoer van [import gegevens](./import-data.md) is een gegevensset die bij elk experiment kan worden gebruikt.

  
Als de bron gegevens worden gewijzigd, kunt u de gegevensset vernieuwen en nieuwe gegevens toevoegen door [import gegevens](./import-data.md)opnieuw uit te voeren. Als u echter niet telkens wanneer u het experiment uitvoert opnieuw wilt lezen uit de bron, selecteert u de optie **in cache opgeslagen resultaten gebruiken** op waar. Als deze optie is geselecteerd, controleert de module of het experiment eerder dezelfde bron en dezelfde invoer opties heeft gebruikt. Als er een vorige run wordt gevonden, worden de gegevens in de cache gebruikt, in plaats van de gegevens uit de bron opnieuw te laden.
 

## <a name="data-sources"></a>Gegevensbronnen

De module gegevens importeren ondersteunt de volgende gegevens bronnen. Klik op de koppelingen voor gedetailleerde instructies en voor beelden van het gebruik van elke gegevens bron. 
 
Als u niet zeker weet hoe u uw gegevens moet opslaan, raadpleegt u deze hand leiding voor algemene gegevens scenario's in het data Science-proces:  [Scenario's voor geavanceerde analyses in azure machine learning](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-plan-sample-scenarios). 


|Gegevensbron| Gebruiken met|
|-----------|-----------|  
|[Web-URL via HTTP](./import-from-web-url-via-http.md)|Gegevens ophalen die worden gehost op een web-URL die gebruikmaakt van HTTP en die is opgenomen in de CSV-, TSV-, ARFF-of SvmLight-indelingen|  
|[Importeren uit Azure Blob Storage](./import-from-azure-blob-storage.md) |Gegevens ophalen die zijn opgeslagen in de Azure Blob-service|  

## <a name="how-to-use-import-data"></a>Gegevens importeren gebruiken
 
1. Voeg de module **gegevens importeren** toe aan uw experiment. Deze module vindt u in de categorie **gegevens invoer en uitvoer** van de interface.

2. Klik op **wizard gegevens importeren starten** om de gegevens bron te configureren met behulp van een wizard.

    De wizard haalt de account naam en referenties op en helpt u bij het configureren van andere opties. Als u een bestaande configuratie bewerkt, worden eerst de huidige waarden geladen.

3. Als u de wizard niet wilt gebruiken, klikt u op **gegevens bron**en kiest u het type Cloud opslag waarvan u een lees bewerking hebt gemaakt. 

    Aanvullende instellingen zijn afhankelijk van het type opslag dat u kiest, en of de opslag is beveiligd of niet. Mogelijk moet u de account naam, het bestands type of de referenties opgeven. Voor sommige bronnen is geen verificatie vereist; voor anderen moet u mogelijk de account naam, een sleutel of de naam van de container weten.

4. Selecteer de optie **in cache opgeslagen resultaten gebruiken** als u de gegevensset in de cache wilt opslaan voor hergebruik op opeenvolgende uitvoeringen.

    Ervan uitgaande dat er geen andere wijzigingen zijn aangebracht in de module parameters, laadt het experiment de gegevens alleen de eerste keer dat de module wordt uitgevoerd en maakt vervolgens een versie van de gegevensset in de cache.

    Schakel deze optie uit als u de gegevens telkens opnieuw moet laden wanneer u het experiment uitvoert.

5. Voer het experiment uit.

    Wanneer gegevens importeren de gegevens in de interface laadt, wordt het gegevens type van elke kolom afgeleid op basis van de waarden die het bevat, ofwel numeriek of categorische.

    - Als er een header aanwezig is, wordt de header gebruikt om de kolommen van de uitvoer gegevensset te benoemen.

    - Als er geen bestaande kolom koppen in de gegevens staan, worden nieuwe kolom namen gegenereerd met de indeling Kol1, col2,... , coln*.

## <a name="results"></a>Resultaten

Wanneer het importeren is voltooid, klikt u op de uitvoer gegevensset en selecteert u visualiseren om te zien of de gegevens zijn geïmporteerd.

Als u de gegevens wilt opslaan voor hergebruik in plaats van een nieuwe set gegevens te importeren telkens wanneer het experiment wordt uitgevoerd, klikt u met de rechter muisknop op de uitvoer en selecteert u **Opslaan als gegevensset**. Kies een naam voor de gegevensset. De opgeslagen gegevensset behoudt de gegevens op het moment van opslaan en er worden geen gegevens bijgewerkt wanneer het experiment opnieuw wordt uitgevoerd, zelfs als de gegevensset in het experiment wordt gewijzigd. Dit kan handig zijn voor het maken van moment opnamen van gegevens.

Na het importeren van de gegevens zijn mogelijk extra voor bereid voor het maken en analyseren van modellen nodig:


- Gebruik [meta gegevens bewerken](./edit-metadata.md) om kolom namen te wijzigen, om een kolom te verwerken als een ander gegevens type, of om aan te geven dat sommige kolommen labels of functies zijn.

- Gebruik [select columns in dataset](./select-columns-in-dataset.md) om een subset van kolommen te selecteren die u wilt transformeren of gebruiken in model lering. De getransformeerde of verwijderde kolommen kunnen eenvoudig opnieuw worden samengevoegd met de oorspronkelijke gegevensset met behulp van de module [add columns](./add-columns.md) .  

- Gebruik [partitie en voor beeld](./partition-and-sample.md) om de gegevensset te verdelen, steek proeven uit te voeren of de bovenste n rijen te verkrijgen.

## <a name="next-steps"></a>Volgende stappen

Bekijk de [set beschik bare modules](module-reference.md) voor Azure machine learning service. 