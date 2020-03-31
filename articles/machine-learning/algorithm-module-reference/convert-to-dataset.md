---
title: 'Converteren naar gegevensset: moduleverwijzing'
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de module Converteren naar gegevensset in Azure Machine Learning om gegevensinvoer om te zetten in de interne gegevenssetindeling die wordt gebruikt door Microsoft Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: fd45db779b7a828d247f09cae38f90fc20d12c16
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456587"
---
# <a name="convert-to-dataset"></a>Converteren naar gegevensset

In dit artikel wordt beschreven hoe u de module Converteren naar gegevensset gebruiken in azure machine learning-ontwerper (voorbeeld) om gegevens voor een pijplijn om te zetten naar de interne indeling van de ontwerper.
  
Conversie is in de meeste gevallen niet nodig. Azure Machine Learning converteert gegevens impliciet naar de oorspronkelijke gegevenssetindeling wanneer een bewerking op de gegevens wordt uitgevoerd. 

We raden u aan gegevens op te slaan in de gegevenssetindeling als u een soort normalisatie of reiniging op een set gegevens hebt uitgevoerd en u ervoor wilt zorgen dat de wijzigingen in andere pijplijnen worden gebruikt.  
  
> [!NOTE]
> Als u converteert naar Gegevensset, wordt alleen de indeling van de gegevens gewijzigd. Er wordt geen nieuwe kopie van de gegevens in de werkruimte opgeslagen. Als u de gegevensset wilt opslaan, dubbelklikt u op de uitvoerpoort, selecteert u **Opslaan als gegevensset**en voert u een nieuwe naam in.  
  
## <a name="how-to-use-convert-to-dataset"></a>Converteren naar gegevensset gebruiken  

We raden u aan de module [Metagegevens bewerken](edit-metadata.md) te gebruiken om de gegevensset voor te bereiden voordat u Converteren naar gegevensset gebruikt. U kolomnamen toevoegen of wijzigen, gegevenstypen aanpassen en indien nodig andere wijzigingen aanbrengen.

1.  Voeg de module Converteren naar gegevensset toe aan uw pijplijn. U vindt deze module in de categorie **Gegevenstransformatie** in de ontwerper. 

2. Sluit deze aan op elke module die een gegevensset uitvoert.   

    Zolang de gegevens [in tabelvorm](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py)zijn, u deze converteren naar een gegevensset. Dit omvat gegevens die worden geladen via [Importgegevens](import-data.md), gegevens die zijn gemaakt via [Handmatig gegevens invoeren](enter-data-manually.md)of gegevenssets die zijn getransformeerd via Transformatie [toepassen](apply-transformation.md).

3.  Geef in de vervolgkeuzelijst **Actie** aan of u de gegevens wilt opschonen voordat u de gegevensset opslaat:  
  
    - **None**: Gebruik de gegevens zoals die zijn.  
  
    - **SetMissingValue**: Stel een specifieke waarde in op een ontbrekende waarde in de gegevensset. De standaardtijdelijke aanduiding is het vraagtekenteken (?), maar u de optie **Aangepaste ontbrekende waarde** gebruiken om een andere waarde in te voeren. Als u bijvoorbeeld **De** **ontbrekende waarde**van Taxi voor Aangepast invoert, worden alle exemplaren van **Taxi** in de gegevensset gewijzigd in de ontbrekende waarde.
  
    - **ReplaceValues**: Gebruik deze optie om één exacte waarde op te geven die moet worden vervangen door een andere exacte waarde. U ontbrekende waarden of aangepaste waarden vervangen door de methode Vervangen in **te** stellen:

      - **Ontbreken:** Kies deze optie om ontbrekende waarden in de invoergegevensset te vervangen. Voer **bij Nieuwe waarde**de waarde in waarmee de ontbrekende waarden moeten worden vervangen.
      - **Aangepast:** kies deze optie om aangepaste waarden in de invoergegevensset te vervangen. Voer **voor aangepaste waarde**de waarde in die u wilt vinden. Als uw gegevens bijvoorbeeld de `obs` tekenreeks bevatten die wordt gebruikt `obs`als tijdelijke aanduiding voor ontbrekende waarden, voert u . Voer **voor Nieuwe waarde**de nieuwe waarde in om de oorspronkelijke tekenreeks te vervangen door.
  
    Houd er rekening mee dat de bewerking **ReplaceValues** alleen van toepassing is op exacte overeenkomsten. Deze tekenreeksen worden bijvoorbeeld niet `obs.` `obsolete`beïnvloed: , .  
 
  
5.  Verzend de pijplijn.  

## <a name="results"></a>Resultaten

+  Als u de resulterende gegevensset met een nieuwe naam wilt opslaan, selecteert u in de **gegevensset Pictogram Register** onder het tabblad **Uitvoer** in het rechterdeelvenster van de module.  
  
## <a name="technical-notes"></a>Technische notities  

-   Elke module die een gegevensset als invoer neemt, kan ook gegevens in het CSV-bestand of het TSV-bestand bevatten. Voordat een modulecode wordt uitgevoerd, worden de ingangen voorbewerkt. Voorbewerking is gelijk aan het uitvoeren van de module Converteren naar gegevensset op de invoer.  
  
-   U niet converteren van de SVMLight-indeling naar een gegevensset.  
  
-   Wanneer u een aangepaste vervangingsbewerking opgeeft, is de zoek- en vervangbewerking van toepassing op volledige waarden. Gedeeltelijke overeenkomsten zijn niet toegestaan. U bijvoorbeeld een 3 vervangen door een -1 of 33, maar u een 3 niet vervangen in een getal van twee cijfers, zoals 35.  
  
-   Voor aangepaste vervangingsbewerkingen mislukt de vervanging in stilte als u een teken gebruikt dat niet voldoet aan het huidige gegevenstype van de kolom.  

  
## <a name="next-steps"></a>Volgende stappen

Bekijk de [set modules die beschikbaar zijn](module-reference.md) voor Azure Machine Learning. 
