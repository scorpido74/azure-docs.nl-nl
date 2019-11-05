---
title: 'Converteren naar DataSet: module-verwijzing'
titleSuffix: Azure Machine Learning service
description: Meer informatie over het gebruik van de module converteren naar gegevensset in Azure Machine Learning-service om gegevens invoer te converteren naar de indeling van de interne gegevensset die door Microsoft Azure Machine Learning wordt gebruikt.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: b19d812d45b625531583aff726571c7da59f97f4
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73516251"
---
# <a name="convert-to-dataset"></a>Converteren naar gegevensset

In dit artikel wordt beschreven hoe u de module [converteren naar gegevensset](convert-to-dataset.md) in azure machine learning Designer (preview) gebruikt om gegevens voor een pijp lijn om te zetten in de interne indeling die wordt gebruikt door de ontwerper.
  
Conversie is niet vereist in de meeste gevallen omdat Azure Machine Learning impliciet gegevens converteert naar de indeling van de systeem eigen gegevensset wanneer een bewerking wordt uitgevoerd op de gegevens. 

Het opslaan van gegevens naar de indeling gegevensset wordt echter aanbevolen als u een bepaalde vorm van normalisatie of reiniging hebt uitgevoerd voor een set gegevens en u ervoor wilt zorgen dat de wijzigingen worden gebruikt in verdere pijp lijnen.  
  
> [!NOTE]
> [Converteren naar dataset](convert-to-dataset.md) wijzigt alleen de indeling van de gegevens. Er wordt geen nieuwe kopie van de gegevens opgeslagen in de werk ruimte. Als u de gegevensset wilt opslaan, dubbelklikt u op de uitvoer poort, selecteert u **Opslaan als gegevensset**en typt u een nieuwe naam.  
  
## <a name="how-to-use-convert-to-dataset"></a>Converteren naar dataset gebruiken  

U wordt aangeraden de module [meta gegevens bewerken](edit-metadata.md) te gebruiken om de gegevensset voor te bereiden voordat u [converteren naar gegevensset](convert-to-dataset.md)gebruikt.  U kunt kolom namen toevoegen of wijzigen, gegevens typen aanpassen, enzovoort.

1.  Voeg de module [converteren naar gegevensset](convert-to-dataset.md) toe aan de pijp lijn. U kunt deze module vinden in de categorie **gegevens transformatie** in de ontwerp functie. 

2. Verbind het met een wille keurige module die een gegevensset uitvoert.   

    Zolang de gegevens [in tabel vorm](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py)zijn, kunt u deze converteren naar een gegevensset. Dit geldt ook voor gegevens die zijn geladen met behulp van gegevens [importeren](import-data.md), gegevens die zijn gemaakt met [gegevens hand matig invoeren](enter-data-manually.md)of data sets die zijn getransformeerd met behulp van [Apply trans formatie](apply-transformation.md).

3.  Geef in de vervolg keuzelijst **actie** aan of u de gegevens wilt opschonen voordat u de gegevensset opslaat:  
  
    - **Geen**: gebruik de gegevens als.  
  
    - **SetMissingValue**: Stel een specifieke waarde in op ontbrekende waarde in de gegevensset. De standaard aanduiding is het vraag teken (?), maar u kunt de aangepaste optie voor **ontbrekende waarden** gebruiken om een andere waarde in te voeren. Als u bijvoorbeeld ' taxi ' typt voor **aangepaste ontbrekende waarde**, wordt alle ' taxi ' in de gegevensset gewijzigd in ontbrekende waarde.
  
    - **ReplaceValues**: gebruik deze optie om een enkele exacte waarde op te geven die moet worden vervangen door een andere exacte waarde.  U kunt ontbrekende waarden of aangepaste waarden vervangen door de methode **vervangen** :- **ontbreekt**: Kies deze optie om ontbrekende waarden in de invoer-gegevensset te vervangen. Voor **nieuwe waarde**typt u de waarde om de ontbrekende waarden te vervangen door.
            - **aangepast**: Kies deze optie om aangepaste waarden in de invoer-gegevensset te vervangen. Typ voor **aangepaste waarde**de waarde die u wilt zoeken. Als uw gegevens bijvoorbeeld de teken reeks bevatten `obs` gebruikt als tijdelijke aanduiding voor ontbrekende waarden, typt u `obs`. Voor **nieuwe waarde**typt u de nieuwe waarde om de oorspronkelijke teken reeks te vervangen door.
  
    Houd er rekening mee dat de bewerking **ReplaceValues** alleen van toepassing is op exacte overeenkomsten. Deze teken reeksen worden bijvoorbeeld niet beïnvloed: `obs.`, `obsolete`.  
 
  
5.  Voer de pijp lijn uit of klik met de rechter muisknop op de module [converteren naar gegevensset](convert-to-dataset.md) en selecteer **geselecteerde uitvoeren**.  

## <a name="results"></a>Resultaten

+  Als u de resulterende gegevensset met een nieuwe naam wilt opslaan, klikt u met de rechter muisknop op de uitvoer van [converteren naar dataset](convert-to-dataset.md) en selecteert u **Opslaan als gegevensset**.  
  
## <a name="technical-notes"></a>Technische opmerkingen  

Deze sectie bevat implementatie details, tips en antwoorden op veelgestelde vragen.

-   Elke module die een gegevensset als invoer gebruikt, kan ook gegevens in het CSV-of TSV-bestand opnemen. Voordat een module code wordt uitgevoerd, wordt de invoer vóór verwerking uitgevoerd, wat gelijk is aan het uitvoeren van de module [converteren naar gegevensset](convert-to-dataset.md) op de invoer.  
  
-   U kunt niet converteren van de SVMLight-indeling naar DataSet.  
  
-   Wanneer u een aangepaste Vervang bewerking opgeeft, is de zoek-en vervang bewerking van toepassing op de waarden volt ooien; gedeeltelijke overeenkomsten zijn niet toegestaan. U kunt bijvoorbeeld een 3 vervangen door een-1 of met 33, maar u kunt een 3 niet vervangen in een getal van twee cijfers zoals 35.  
  
-   Voor aangepaste Vervang bewerkingen mislukt de vervanging op de achtergrond als u een wille keurig teken gebruikt dat niet overeenkomt met het huidige gegevens type van de kolom.  

  
## <a name="next-steps"></a>Volgende stappen

Bekijk de [set beschik bare modules](module-reference.md) voor Azure machine learning service. 
