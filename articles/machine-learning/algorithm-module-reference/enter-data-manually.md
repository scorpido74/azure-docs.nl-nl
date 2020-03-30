---
title: 'Handmatig gegevens invoeren: naslaginformatie over de module'
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de module Gegevens handmatig invoeren in Azure Machine Learning om een kleine gegevensset te maken door waarden te typen. De gegevensset kan meerdere kolommen bevatten.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 35e31e5ace53654e8aad794dd3e25fc04bd9a088
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367513"
---
# <a name="enter-data-manually-module"></a>Module Gegevens handmatig invoeren

In dit artikel wordt een module beschreven in Azure Machine Learning designer (preview).

Gebruik de module **Gegevens handmatig invoeren** om een kleine gegevensset te maken door waarden te typen. De gegevensset kan meerdere kolommen bevatten.
  
Deze module kan handig zijn in scenario's zoals:  
  
- Het genereren van een kleine set waarden voor het testen.  
- Een korte lijst met labels maken.  
- Een lijst met kolomnamen typen die u wilt invoegen in een gegevensset.

## <a name="create-a-dataset"></a>Een gegevensset maken 
  
1. Voeg de module [Gegevens handmatig invoeren toe](./enter-data-manually.md) aan uw pijplijn. U vindt deze module in de categorie **Gegevensinvoer en Uitvoer** in Azure Machine Learning. 
  
1. Selecteer voor **Gegevensopmaak**een van de volgende opties. Deze opties bepalen hoe de gegevens die u verstrekt moeten worden ontleed. De eisen voor elk formaat verschillen sterk, dus zorg ervoor dat de gerelateerde onderwerpen te lezen.  
  
   - **ARFF**: Attribuut-relatie bestandsindeling gebruikt door Weka.   
   - **CSV**: door komma's gescheiden waarden notatie. Zie [Converteren naar CSV voor](./convert-to-csv.md)meer informatie.    
   - **SVMLight**: Formaat gebruikt door Vowpal Wabbit en andere machine learning frameworks.    
   - **TSV**: Indeling van door tabbladen gescheiden waarden.

   Als u een indeling kiest en geen gegevens verstrekt die voldoen aan de indelingsspecificaties, treedt er een runtime-fout op.
  
1. Klik in het tekstvak **Gegevens** om gegevens in te voeren. De volgende formaten vereisen speciale aandacht:  
  
   - **CSV**: Als u meerdere kolommen wilt maken, plakt u in door komma's gescheiden tekst of typt u meerdere kolommen met komma's tussen velden.
  
     Als u de optie **HasHeader** selecteert, u de eerste rij waarden als kolomkop gebruiken.  
  
     Als u deze optie deselecteert, worden de kolomnamen (Col1, Col2, enzovoort) gebruikt. U kolommen namen later toevoegen of wijzigen met behulp [van Metagegevens bewerken](./edit-metadata.md).  
  
   - **TSV**: Als u meerdere kolommen wilt maken, plakt u tekst met tabbladen in het kader of typt u meerdere kolommen met tabbladen tussen velden.  
  
     Als u de optie **HasHeader** selecteert, u de eerste rij waarden als kolomkop gebruiken.  
  
     Als u deze optie deselecteert, worden de kolomnamen (Col1, Col2, enzovoort) gebruikt. U kolommen namen later toevoegen of wijzigen met behulp [van Metagegevens bewerken](./edit-metadata.md).  
  
   - **ARFF:** Plakken in een bestaand ARFF-formaatbestand. Als u waarden rechtstreeks typt, moet u aan het begin van de gegevens de optionele koptekst en vereiste kenmerkvelden toevoegen. 

     De volgende kop- en kenmerkrijen kunnen bijvoorbeeld aan een eenvoudige lijst worden toegevoegd. De kolomkop `SampleText`zou zijn . Houd er rekening mee dat het type Tekenreeks niet wordt ondersteund.
    
     ```text
     % Title: SampleText.ARFF  
     % Source: Enter Data module  
     @ATTRIBUTE SampleText NUMERIC  
     @DATA  
     \<type first data row here>  
     ```

   - **SVMLight**: Typ of plak waarden in met de SVMLight-indeling.  
  
     Het volgende voorbeeld vertegenwoordigt bijvoorbeeld de eerste paar regels van de gegevensset Bloeddonatie, in svmlight-indeling:  
  
     ```text  
     # features are [Recency], [Frequency], [Monetary], [Time]  
     1 1:2 2:50 3:12500 4:98   
     1 1:0 2:13 3:3250 4:28   
     ```  
  
     Wanneer u de module [Gegevens handmatig invoeren](./enter-data-manually.md) uitvoert, worden deze regels als volgt geconverteerd naar een gegevensset met kolommen en indexwaarden:  
  
     |Kolom 1|Kolom 2|Col3 (Col3)|Col4 (Col4)|Labels|  
     |-|-|-|-|-|  
     |0.00016|0.004|0.999961|0.00784|1|  
     |0|0.004|0.999955|0.008615|1|  
  
1. Selecteer de toets Enter na elke rij om een nieuwe regel te starten.      
     
   Als u Meerdere keren enter selecteert om meerdere lege slepende rijen toe te voegen, worden de lege rijen verwijderd of bijgesneden.  
  
   Als u rijen maakt met ontbrekende waarden, u ze later altijd filteren.  
  
1. Sluit de uitvoerpoort aan op andere modules en voer de pijplijn uit.  
  
   Als u de gegevensset wilt weergeven, klikt u met de rechtermuisknop op de module en selecteert u **Visualiseren**.

## <a name="next-steps"></a>Volgende stappen

Bekijk de [set modules die beschikbaar zijn](module-reference.md) voor Azure Machine Learning. 