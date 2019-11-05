---
title: Problemen met module fouten oplossen
titleSuffix: Azure Machine Learning
description: Problemen met module-uitzonde ringen in Azure Machine Learning Studio oplossen met fout codes
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: b07b1fc14ac7ac72a30f75a098b4aab35f2de713
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73497734"
---
# <a name="exceptions-and-error-codes-for-algorithm--module-reference"></a>Uitzonde ringen en fout codes voor de verwijzing naar de algoritme-& module

Meer informatie over de fout berichten en uitzonderings codes die u kunt tegen komen met behulp van modules in Azure Machine Learning Designer (preview). 

Om het probleem op te lossen, raadpleegt u de fout in dit artikel voor meer informatie over veelvoorkomende oorzaken. Er zijn twee manieren om de volledige tekst van een fout bericht in de ontwerp functie te verkrijgen:  
 
- Klik op de koppeling, **Bekijk het uitvoer logboek**in het rechterdeel venster en schuif naar beneden. Het gedetailleerde fout bericht wordt weer gegeven in de laatste twee regels van het venster.  
  
- Selecteer de module met de fout en klik op de rode X. Alleen de relevante fout tekst wordt weer gegeven.  
  
Als de tekst van het fout bericht niet nuttig is, stuur ons informatie over de context en eventuele gewenste toevoegingen of wijzigingen door feedback in te dienen.


## <a name="error-0001"></a>Fout 0001  
 Uitzonde ring treedt op als een of meer opgegeven kolommen met gegevensset niet zijn gevonden.  
  
 U ontvangt deze fout melding als er een kolom selectie is gemaakt voor een module, maar de geselecteerde kolom (men) niet aanwezig zijn in de gegevensset voor invoer. Deze fout kan optreden als u hand matig hebt getypt in een kolom naam of als de kolom kiezer een voorgestelde kolom heeft opgegeven die niet in uw gegevensset voor komt toen u de pijp lijn uitvoerde.  
  
**Oplossing:** Ga terug naar de module met deze uitzonde ring en controleer of de kolom naam of-namen juist zijn en of alle kolommen waarnaar wordt verwezen bestaan.  
  
|Uitzonderings berichten|  
|------------------------|  
|Een of meer opgegeven kolommen zijn niet gevonden|  
|Kan geen kolom met de naam of de index{0}vinden|  
|De kolom met de naam of index '{0}' bestaat niet in{1}|  
 

## <a name="error-0002"></a>Fout 0002  
 Uitzonde ring treedt op als een of meer para meters niet kunnen worden geparseerd of geconverteerd van een opgegeven type naar vereist door het type doel methode.  
  
 Deze fout treedt op in Azure Machine Learning wanneer u een para meter opgeeft als invoer en het waardetype verschilt van het verwachte type en er geen impliciete conversie kan worden uitgevoerd.  
  
**Oplossing:** Controleer de module vereisten en bepaal welk waardetype vereist is (teken reeks, geheel getal, dubbel enzovoort)  
  
|Uitzonderings berichten|  
|------------------------|  
|Kan para meter niet parseren|  
|Parseren van de para meter{0}is mislukt|  
|Kan para meter "{0}" niet parseren (converteren) naar{1}|  
|Kan para meter "{0}" niet converteren van "{1}" naar "{2}"|  
|Kan de parameter waarde '{0}'{1}' niet converteren van '{2}' naar '{3}'|  
|Kan de waarde '{0}' in kolom '{1}' niet converteren van '{2}' naar '{3}' met het gebruik van de indeling '{4}'|  
  

## <a name="error-0003"></a>Fout 0003  
 Uitzonde ring treedt op als een of meer invoer waarden null of leeg zijn.  
  
 Deze fout wordt weer gegeven in Azure Machine Learning als invoer of para meters voor een module null of leeg zijn.  Deze fout kan optreden als u bijvoorbeeld geen waarde voor een para meter hebt getypt. Dit kan ook gebeuren als u een gegevensset hebt gekozen met ontbrekende waarden of een lege gegevensset.  
  
**Opgelost**
 
+ Open de module die de uitzonde ring heeft veroorzaakt en controleer of alle invoer waarden zijn opgegeven. Zorg ervoor dat alle vereiste invoer waarden zijn opgegeven. 
+ Zorg ervoor dat de gegevens die worden geladen vanuit Azure Storage toegankelijk zijn en dat de account naam of-sleutel niet is gewijzigd.  
+ Controleer de invoer gegevens voor ontbrekende waarden of nullen.
+ Als u een query op een gegevens bron gebruikt, controleert u of de gegevens worden geretourneerd in de indeling die u verwacht. 
+ Controleer op typfouten of andere wijzigingen in de specificatie van gegevens.
  
|Uitzonderings berichten|  
|------------------------|  
|Een of meer invoer waarden zijn null of leeg|  
|De invoer{0}is null of leeg|  
  

## <a name="error-0004"></a>Fout 0004  
 Uitzonde ring treedt op als de para meter kleiner dan of gelijk aan een specifieke waarde is.  
  
 Deze fout wordt weer gegeven in Azure Machine Learning als de para meter in het bericht onder een grens waarde valt die vereist is voor de module om de gegevens te verwerken.  
  
**Oplossing:** Ga naar de module die de uitzonde ring opgooit en wijzig de para meter in een hogere waarde dan de opgegeven waarden.  
  
|Uitzonderings berichten|  
|------------------------|  
|De para meter moet groter zijn dan de grens waarde.|  
|De waarde van de para meter{0}moet groter zijn dan {1}.|  
|De para meter{0}heeft de waarde{1}die groter moet zijn dan {2}|  
  


## <a name="error-0005"></a>Fout 0005  
 Uitzonde ring treedt op als de para meter kleiner is dan een specifieke waarde.  
  
 U ontvangt deze fout in Azure Machine Learning als de para meter in het bericht lager is dan of gelijk is aan een grens waarde die vereist is voor de module om de gegevens te verwerken.  
  
**Oplossing:** Ga naar de module die de uitzonde ring opgooit en wijzig de para meter in groter dan of gelijk aan de opgegeven waarde.  
  
|Uitzonderings berichten|  
|------------------------|  
|De para meter moet groter dan of gelijk aan de grens waarde zijn.|  
|De waarde van de para meter{0}moet groter dan of gelijk zijn aan {1}.|  
|De para meter{0}heeft de waarde{1}die groter is dan of gelijk is aan {2}.|  
  

## <a name="error-0006"></a>Fout 0006  
 Uitzonde ring treedt op als de para meter groter is dan of gelijk is aan de opgegeven waarde.  
  
 Deze fout wordt weer gegeven in Azure Machine Learning als de para meter in het bericht groter is dan of gelijk is aan een grens waarde die vereist is voor de module om de gegevens te verwerken.  
  
**Oplossing:** Ga naar de module om de uitzonde ring op te treden en wijzig de para meter zo dat deze kleiner is dan de opgegeven waarde.  
  
|Uitzonderings berichten|  
|------------------------|  
|De para meters komen niet overeen. Een van de para meters moet kleiner zijn dan de andere.|  
|De waarde van de para meter{0}moet kleiner zijn dan de waarde van de para meter{1}.|  
|De para meter{0}heeft de waarde{1}die kleiner moet zijn dan {2}.|  
  

## <a name="error-0007"></a>Fout 0007  
 Uitzonde ring treedt op als de para meter groter is dan een specifieke waarde.  
  
 Deze fout wordt weer gegeven in Azure Machine Learning als u in de eigenschappen van de module een waarde hebt opgegeven die groter is dan is toegestaan. U kunt bijvoorbeeld een gegevens opgeven die zich buiten het bereik van ondersteunde datums bevinden, maar u kunt ook aangeven dat er vijf kolommen moeten worden gebruikt wanneer er slechts drie kolommen beschikbaar zijn. 
 
 Deze fout kan ook worden weer gegeven als u twee gegevens sets opgeeft die op een of andere manier moeten overeenkomen. Als u bijvoorbeeld de naam van kolommen wijzigt en de kolommen op index opgeeft, moet het aantal namen dat u opgeeft overeenkomen met het aantal kolom indexen. Een ander voor beeld is mogelijk een reken kundige bewerking die gebruikmaakt van twee kolommen, waarbij de kolommen hetzelfde aantal rijen moeten bevatten. 
  
**Opgelost**
 
 + Open de betreffende module en controleer de instellingen van de numerieke eigenschappen.
 + Zorg ervoor dat alle parameter waarden binnen het ondersteunde bereik van waarden voor die eigenschap vallen.
 + Als de module meerdere invoer heeft, moet u ervoor zorgen dat de invoer dezelfde grootte heeft.
<!-- + If the module has multiple properties that can be set, ensure that related properties have appropriate values. For example, when using [Group Data into Bins](group-data-into-bins.md), if you use the option to specify custom bin edges, the number of bins must match the number of values you provide as bin boundaries.-->
 + Controleer of de gegevensset of gegevens bron is gewijzigd. Soms mislukt een waarde die heeft gewerkt met een eerdere versie van de gegevens nadat het aantal kolommen, de kolom gegevens typen of de grootte van de gegevens is gewijzigd.  
  
|Uitzonderings berichten|  
|------------------------|  
|De para meters komen niet overeen. Een van de para meters moet kleiner zijn dan of gelijk zijn aan een andere.|  
|De waarde van de para meter{0}moet kleiner zijn dan of gelijk zijn aan de waarde van de para meter{1}.|  
|De para meter{0}heeft de waarde{1}die kleiner moet zijn dan of gelijk is aan {2}.|  
  

## <a name="error-0008"></a>Fout 0008  
 Uitzonde ring treedt op als de para meter niet binnen het bereik valt.  
  
 U ontvangt deze fout in Azure Machine Learning als de para meter in het bericht buiten de grenzen valt die vereist zijn voor de module om de gegevens te verwerken.  
  
 Deze fout wordt bijvoorbeeld weer gegeven als u [rijen toevoegen](add-rows.md) probeert te gebruiken om twee gegevens sets met een verschillend aantal kolommen te combi neren.  
  
**Oplossing:** Ga naar de module om de uitzonde ring te genereren en wijzig de para meter in het opgegeven bereik.  
  
|Uitzonderings berichten|  
|------------------------|  
|De parameter waarde bevindt zich niet in het opgegeven bereik.|  
|De waarde voor de para meter{0}ligt niet in het bereik.|  
|De waarde van de para meter{0}moet tussen [{1}, {2}] liggen.|  
  

## <a name="error-0009"></a>Fout 0009  
 Uitzonde ring treedt op wanneer de naam van het Azure-opslag account of de container naam onjuist is opgegeven.  
  
Deze fout treedt op in Azure Machine Learning Designer wanneer u para meters opgeeft voor een Azure Storage-account, maar de naam of het wacht woord niet kan worden omgezet. Fouten met betrekking tot wacht woorden of account namen kunnen om verschillende redenen optreden:
 
 + Het account is van het verkeerde type. Sommige nieuwe account typen worden niet ondersteund voor gebruik met Machine Learning Designer. Zie [gegevens importeren](import-data.md) voor meer informatie.
 + U hebt de onjuiste account naam opgegeven
 + Het account bestaat niet meer
 + Het wacht woord voor het opslag account is onjuist of gewijzigd
 + U hebt geen container naam opgegeven, of de container bestaat niet
 + U hebt niet volledig het bestandspad opgegeven (pad naar de blob)
   
**Opgelost**

Dergelijke problemen treden vaak op wanneer u de account naam, het wacht woord of het pad van de container hand matig wilt invoeren. U wordt aangeraden de nieuwe wizard te gebruiken voor de module [gegevens importeren](import-data.md) , waarmee u namen kunt opzoeken en controleren.

Controleer ook of het account, de container of de blob is verwijderd. Gebruik een ander Azure-opslag programma om te controleren of de account naam en het wacht woord juist zijn ingevoerd en of de container bestaat. 

Sommige nieuwere account typen worden niet ondersteund door Azure Machine Learning. De nieuwe opslag typen ' hot ' of ' koud ' kunnen bijvoorbeeld niet worden gebruikt voor machine learning. Zowel klassieke opslag accounts als opslag accounts die zijn gemaakt als "algemeen gebruik" werken prima.

Als het volledige pad naar een blob is opgegeven, controleert u of het pad is opgegeven als **container/blobnaam**en of de container en de BLOB aanwezig zijn in het account.  
  
 Het pad mag geen voorloop streep bevatten. Bijvoorbeeld **/container/BLOB** is onjuist en moet worden ingevoerd als **container/BLOB**.  

  
|Uitzonderings berichten|  
|------------------------|  
|De naam van het Azure-opslag account of de container naam is onjuist.|  
|De naam van het Azure-opslag account "{0}" of container naam "{1}" is onjuist; Er wordt een container naam van de indeling container/BLOB verwacht.|  
  

## <a name="error-0010"></a>Fout 0010  
 Uitzonde ring treedt op als invoer gegevens sets kolom namen bevatten die moeten overeenkomen, maar niet.  
  
 Deze fout wordt weer gegeven in Azure Machine Learning als de kolom index in het bericht andere kolom namen in de twee invoer gegevens sets bevat.  
  
**Oplossing:** Gebruik [meta gegevens bewerken](edit-metadata.md) of wijzig de oorspronkelijke gegevensset zodat deze dezelfde kolom naam heeft voor de opgegeven kolom index.  
  
|Uitzonderings berichten|  
|------------------------|  
|Kolommen met een bijbehorende index in invoer gegevens sets hebben verschillende namen.|  
|Kolom namen zijn niet hetzelfde voor kolom {0} (op nul gebaseerd) van invoer gegevens sets ({1} en {2} respectievelijk).|  
  

## <a name="error-0011"></a>Fout 0011  
 Uitzonde ring treedt op als door gegeven kolom set argument niet van toepassing is op een van de gegevensset-kolommen.  
  
 Deze fout wordt weer gegeven in Azure Machine Learning als de opgegeven kolom selectie niet overeenkomt met een van de kolommen in de opgegeven gegevensset.  
  
 U kunt deze fout ook ophalen als u geen kolom hebt geselecteerd en ten minste één kolom is vereist om de module te laten werken.  
  
**Oplossing:** Wijzig de kolom selectie in de module zodat deze van toepassing is op de kolommen in de gegevensset.  
  
 Als de module vereist dat u een specifieke kolom selecteert, zoals een kolom Label, controleert u of de rechter kolom is geselecteerd.  
  
 Als er ongepaste kolommen zijn geselecteerd, verwijdert u deze en voert u de pijp lijn opnieuw uit.  
  
|Uitzonderings berichten|  
|------------------------|  
|De opgegeven kolomset is niet van toepassing op een van de gegevensset-kolommen.|  
|Opgegeven column set "{0}" is niet van toepassing op een van de gegevensset-kolommen.|  
  

## <a name="error-0012"></a>Fout 0012  
 Uitzonde ring treedt op als het exemplaar van de klasse niet kan worden gemaakt met de door gegeven set argumenten.  
  
**Oplossing:** Deze fout kan niet optreden door de gebruiker en zal in een toekomstige versie worden afgeschaft.  
  
|Uitzonderings berichten|  
|------------------------|  
|Niet-traind model, Train model eerst.|  
|Niet-traind model ({0}), gebruik getraind model.|  
  

## <a name="error-0013"></a>Fout 0013  
 Uitzonde ring treedt op als de kenniser die is door gegeven aan de module een ongeldig type is.  
  
 Deze fout treedt op wanneer een getraind model niet compatibel is met de verbonden Score-module. <!--For example, connecting the output of [Train Matchbox Recommender](train-matchbox-recommender.md) to [Score Model](score-model.md) (instead of [Score Matchbox Recommender](score-matchbox-recommender.md)) will generate this error when the pipeline is run.  -->
  
**Opgelost**

Bepaal het type leerer dat wordt geproduceerd door de trainings module en bepaal de Score module die geschikt is voor de kenniser. 

Als het model is getraind met een van de gespecialiseerde trainings modules, koppelt u het getrainde model alleen aan de bijbehorende gespecialiseerde Score module. 


|Model type|Trainings module| Score module|
|----|----|----|
|een classificatie|[Model trainen](train-model.md) |[Score model](score-model.md)|
|een regressie model|[Model trainen](train-model.md) |[Score model](score-model.md)|

<!--| clustering models| [Train Clustering Model](train-clustering-model.md) or [Sweep Clustering](sweep-clustering.md)| [Assign Data to Clusters](assign-data-to-clusters.md)|
| anomaly detection - One-Class SVM | [Train Anomaly Detection Model](train-anomaly-detection-model.md) |[Score Model](score-model.md)|
| anomaly detection - PCA |[Train Model](train-model.md) |[Score Model](score-model.md) </br> Some additional steps are required to evaluate the model. |
| anomaly detection - time series|  [Time Series Anomaly Detection](time-series-anomaly-detection.md) |Model trains from data and generates scores. The module does not create a trained learner and no additional scoring is required. |
| recommendation model| [Train Matchbox Recommender](train-matchbox-recommender.md) | [Score Matchbox Recommender](score-matchbox-recommender.md) |
| image classification | [Pretrained Cascade Image Classification](pretrained-cascade-image-classification.md) | [Score Model](score-model.md) |
|Vowpal Wabbit models| [Train Vowpal Wabbit Version 7-4 Model](train-vowpal-wabbit-version-7-4-model.md) | [Score Vowpal Wabbit Version 7-4 Model](score-vowpal-wabbit-version-7-4-model.md) |   
|Vowpal Wabbit models| [Train Vowpal Wabbit Version 7-10 Model](train-vowpal-wabbit-version-7-10-model.md) | [Score Vowpal Wabbit Version 7-10 Model](score-vowpal-wabbit-version-7-10-model.md) |
|Vowpal Wabbit models| [Train Vowpal Wabbit Version 8 Model](score-vowpal-wabbit-version-8-model.md) | [Score Vowpal Wabbit Version 8 Model](score-vowpal-wabbit-version-8-model.md) |-->
  
|Uitzonderings berichten|  
|------------------------|  
|De leerer van een ongeldig type is door gegeven.|  
|De leerer{0}heeft een ongeldig type.|  


## <a name="error-0014"></a>Fout 0014  
 Uitzonde ring treedt op als het aantal kolom unieke waarden groter is dan is toegestaan.  
  
 Deze fout treedt op wanneer een kolom te veel unieke waarden bevat.  U ziet deze fout bijvoorbeeld als u opgeeft dat een kolom moet worden verwerkt als categorische-gegevens, maar er zijn te veel unieke waarden in de kolom om te voor komen dat de verwerking is voltooid. Deze fout kan ook worden weer geven als het aantal unieke waarden in twee invoer niet overeenkomt.   
  
**Opgelost**

Open de module die de fout heeft gegenereerd en Identificeer de kolommen die als invoer worden gebruikt. Voor sommige modules kunt u met de rechter muisknop op de gegevensset-invoer klikken en **visualiseren** selecteren om statistieken op te halen voor afzonderlijke kolommen, inclusief het aantal unieke waarden en de distributie.

Voor kolommen die u wilt gebruiken voor groepering of categorisatie, neemt u stappen om het aantal unieke waarden in kolommen te verminderen. U kunt op verschillende manieren beperken, afhankelijk van het gegevens type van de kolom. 
<!--
+ For text data, you might be able to use [Preprocess Text](preprocess-text.md) to collapse similar entries. 
+ For numeric data, you can create a smaller number of bins using [Group Data into Bins](group-data-into-bins.md), remove or truncate values using [Clip Values](clip-values.md), or use machine learning methods such as [Principal Component Analysis](principal-component-analysis.md) or [Learning with Counts](data-transformation-learning-with-counts.md) to reduce the dimensionality of the data.  
-->
> [!TIP]
> Kunt u geen oplossing vinden die overeenkomt met uw scenario? U kunt feedback geven in dit onderwerp met de naam van de module die de fout heeft gegenereerd, en het gegevens type en de kardinaliteit van de kolom. We gebruiken de informatie om meer gerichte probleemoplossings stappen te bieden voor veelvoorkomende scenario's.   
  
|Uitzonderings berichten|  
|------------------------|  
|Het aantal unieke kolom waarden is groter dan toegestaan.|  
|Aantal unieke waarden in kolom: "{0}" overschrijdt het aantal Tuples van {1}.|  
  

## <a name="error-0015"></a>Fout 0015  
 Uitzonde ring treedt op als de database verbinding is mislukt.  
  
 Deze fout wordt weer gegeven als u een onjuiste SQL-account naam, een wacht woord, een database server of een database naam opgeeft, of als er geen verbinding met de data base kan worden gemaakt vanwege problemen met de data base of server.  
  
**Oplossing:** Controleer of de account naam, het wacht woord, de database server en de data base correct zijn ingevoerd en of het opgegeven account het juiste machtigings niveau heeft. Controleer of de Data Base op dit moment toegankelijk is.  
  
|Uitzonderings berichten|  
|------------------------|  
|Fout bij het maken van de database verbinding.|  
|Fout bij het maken van een database verbinding: {0}.|  
  


## <a name="error-0016"></a>Fout 0016  
 Uitzonde ring treedt op als invoer gegevens sets die zijn door gegeven aan de module compatibele kolom typen moeten hebben, maar dat niet.  
  
 U ontvangt deze fout in Azure Machine Learning als de typen kolommen die in twee of meer gegevens sets zijn door gegeven, niet met elkaar compatibel zijn.  
  
**Oplossing:** [Meta gegevens bewerken](edit-metadata.md) gebruiken of de oorspronkelijke invoer gegevensset wijzigen<!--, or use [Convert to Dataset](convert-to-dataset.md)--> om ervoor te zorgen dat de typen kolommen compatibel zijn.  
  
|Uitzonderings berichten|  
|------------------------|  
|Kolommen met een bijbehorende index in invoer gegevens sets hebben incompatibele typen.|  
|De kolommen {0} en {1} zijn niet compatibel.|  
|Kolom element typen zijn niet compatibel voor kolom {0} (op nul gebaseerd) van invoer gegevens sets ({1} en {2}).|  
  

## <a name="error-0017"></a>Fout 0017  
 Uitzonde ring treedt op als een geselecteerde kolom een gegevens type gebruikt dat niet door de huidige module wordt ondersteund.  
  
 U kunt deze fout bijvoorbeeld ontvangen in Azure Machine Learning als uw kolom selectie een kolom bevat met een gegevens type dat niet kan worden verwerkt door de module, zoals een teken reeks kolom voor een wiskundige bewerking, of een score kolom met een categorische-functie kolom Vereist.  
  
**Opgelost**
 1. Bepaal de kolom waarin het probleem zich voordoet.
 2. Bekijk de vereisten van de module.
 3. Wijzig de kolom zodat deze voldoet aan de vereisten. U moet mogelijk een aantal van de volgende modules gebruiken om wijzigingen aan te brengen, afhankelijk van de kolom en de conversie die u probeert uit te voeren:
    + Gebruik [meta gegevens bewerken](edit-metadata.md) om het gegevens type van kolommen te wijzigen, of om het kolom gebruik van de functie te wijzigen in numeriek, categorische naar niet-categorische, enzovoort.
<!--    + Use [Convert to Dataset](convert-to-dataset.md) to ensure that all included columns use data types that are supported by Azure Machine Learning.  If you cannot convert the columns, consider removing them from the input dataset.
    + Use the [Apply SQL Transformation](apply-sql-transformation.md) or [Execute R Script](execute-r-script.md) modules to cast or convert any columns that cannot be modified using [Edit Metadata](edit-metadata.md). These modules provide more flexibility for working with datetime data types.
    + For numeric data types, you can use the [Apply Math Operation](apply-math-operation.md) module to round or truncate values, or use the [Clip Values](clip-values.md) module to remove out of range values.  -->
 4. Als laatste redmiddel moet u de oorspronkelijke invoer gegevensset mogelijk wijzigen.

> [!TIP]
> Kunt u geen oplossing vinden die overeenkomt met uw scenario? U kunt feedback geven in dit onderwerp met de naam van de module die de fout heeft gegenereerd, en het gegevens type en de kardinaliteit van de kolom. We gebruiken de informatie om meer gerichte probleemoplossings stappen te bieden voor veelvoorkomende scenario's. 
  
|Uitzonderings berichten|  
|------------------------|  
|Kan kolom van het huidige type niet verwerken. Het type wordt niet ondersteund door de module.|  
|Kan geen kolom van het type {0}verwerken. Het type wordt niet ondersteund door de module.|  
|Kan kolom{1}van het type {0}niet verwerken. Het type wordt niet ondersteund door de module.|  
|Kan kolom{1}van het type {0}niet verwerken. Het type wordt niet ondersteund door de module. Parameter naam: {2}|  
  

## <a name="error-0018"></a>Fout 0018  
 Uitzonde ring treedt op als de ingevoerde gegevensset ongeldig is.  
  
**Oplossing:** Deze fout in Azure Machine Learning kan worden weer gegeven in veel contexten, dus er is geen enkele oplossing. In het algemeen geeft de fout aan dat de gegevens die zijn opgegeven als invoer voor een module, een onjuist aantal kolommen hebben of dat het gegevens type niet overeenkomt met de vereisten van de module. Bijvoorbeeld:  
  
-   Voor de module is een kolom Label vereist, maar er is geen kolom gemarkeerd als label of u hebt nog geen kolom label geselecteerd.  
  
-   De module vereist dat de gegevens categorische zijn, maar dat de gegevens numeriek zijn.  
  
<!---   The module requires a specific data type. For example, ratings provided to [Train Matchbox Recommender](train-matchbox-recommender.md) can be either numeric or categorical, but cannot be floating point numbers.  -->
  
-   De gegevens hebben de verkeerde indeling.  
  
-   Geïmporteerde gegevens bevatten ongeldige tekens, ongeldige waarden of waarden buiten het bereik.  
-   De kolom is leeg of bevat te veel ontbrekende waarden.  
  
 Als u wilt bepalen welke vereisten en hoe uw gegevens mogelijk zijn, raadpleegt u het Help-onderwerp voor de module die de gegevensset gaat gebruiken als invoer.  
  
 <!--We also recommend that you use [Summarize Data](summarize-data.md) or [Compute Elementary Statistics](compute-elementary-statistics.md) to profile your data, and use these modules to fix metadata and clean values: [Edit Metadata](edit-metadata.md) and [Clean Missing Data](clean-missing-data.md), [Clip Values](clip-values.md)-->.  
  
|Uitzonderings berichten|  
|------------------------|  
|De gegevensset is niet geldig.|  
|{0} bevat ongeldige gegevens.|  
|{0} en {1} moeten een consistente kolom voor u zijn.|  
  

## <a name="error-0019"></a>Fout 0019  
 Uitzonde ring treedt op als wordt verwacht dat de kolom gesorteerde waarden bevat, maar dit is niet het geval.  
  
 Deze fout wordt weer gegeven in Azure Machine Learning als de opgegeven kolom waarden niet in de juiste volg orde staan.  
  
**Oplossing:** Sorteer de kolom waarden door de invoer gegevensset hand matig te wijzigen en de module opnieuw uit te voeren.  
  
|Uitzonderings berichten|  
|------------------------|  
|De waarden in de kolom worden niet gesorteerd.|  
|De waarden in de kolom{0}zijn niet gesorteerd.|  
|De waarden in de kolom{0}van de gegevensset{1}zijn niet gesorteerd.|  
  

## <a name="error-0020"></a>Fout 0020  
 Uitzonde ring treedt op als het aantal kolommen in sommige gegevens sets die aan de module zijn door gegeven, te klein is.  
  
 U ontvangt deze fout in Azure Machine Learning als er onvoldoende kolommen zijn geselecteerd voor een module.  
  
**Oplossing:** Ga terug naar de module en zorg ervoor dat voor de kolom kiezer het juiste aantal kolommen is geselecteerd.  
  
|Uitzonderings berichten|  
|------------------------|  
|Het aantal kolommen in de invoer gegevensset is kleiner dan het toegestane minimum.|  
|Het aantal kolommen in de invoer gegevensset is kleiner dan het toegestane minimum van {0} kolom (men).|  
|Het aantal kolommen in de invoer gegevensset{0}is kleiner dan het toegestane minimum van {1} kolom (men).|

## <a name="error-0021"></a>Fout 0021  
 Uitzonde ring treedt op als het aantal rijen in een deel van de gegevens sets die aan de module zijn door gegeven, te klein is.  
  
 Deze fout wordt weer gegeven in Azure Machine Learning wanneer er onvoldoende rijen in de gegevensset zijn om de opgegeven bewerking uit te voeren. U kunt deze fout bijvoorbeeld zien als de gegevensset leeg is of als u een bewerking probeert uit te voeren waarvoor een minimum aantal rijen moet worden opgegeven. Deze bewerkingen kunnen onder meer (maar niet beperkt tot) groeperingen of classificatie zijn gebaseerd op statistische methoden, bepaalde soorten binning en leren met aantallen.  
  
**Opgelost**
 
 + Open de module die de fout heeft geretourneerd en controleer de invoer-gegevensset en module-eigenschappen. 
 + Controleer of de ingevoerde gegevensset niet leeg is en of er voldoende rijen met gegevens zijn om te voldoen aan de vereisten die in de Help van module zijn beschreven.  
 + Als uw gegevens worden geladen vanuit een externe bron, moet u ervoor zorgen dat de gegevens bron beschikbaar is en dat er geen fout is of een wijziging in de definitie van de gegevens die ervoor zorgt dat het import proces minder rijen krijgt.
 + Als u een bewerking uitvoert op de gegevens upstream van de module die van invloed kan zijn op het type gegevens of het aantal waarden, zoals het opschonen, splitsen of samen voegen van bewerkingen, controleert u de uitvoer van deze bewerkingen om het aantal geretourneerde rijen te bepalen.  



## <a name="error-0022"></a>Fout 0022  
 Uitzonde ring treedt op als het aantal geselecteerde kolommen in de invoer-gegevensset niet gelijk is aan het verwachte aantal.  
  
 Deze fout in Azure Machine Learning kan optreden wanneer de downstream-module of-bewerking een specifiek aantal kolommen of invoer waarden vereist, en u hebt te weinig of te veel kolommen of invoer waarden opgegeven. Bijvoorbeeld:  
  
-   U geeft een kolom of sleutel kolom met één label op en per ongeluk meerdere kolommen geselecteerd.  
  
-   U wijzigt de naam van kolommen, maar geeft meer of minder namen op dan er kolommen zijn.  
  
-   Het aantal kolommen in de bron of het doel is gewijzigd of komt niet overeen met het aantal kolommen dat door de module wordt gebruikt.  
  
-   U hebt een door komma's gescheiden lijst met waarden voor invoer opgegeven, maar het aantal waarden komt niet overeen, of meerdere invoer wordt niet ondersteund.  
  
**Oplossing:** Ga naar de module en controleer de kolom selectie om ervoor te zorgen dat het juiste aantal kolommen is geselecteerd. Controleer de uitvoer van upstream-modules en de vereisten van stroomafwaartse bewerkingen.  
  
 Als u een van de opties voor kolom selectie hebt gebruikt waarmee meerdere kolommen kunnen worden geselecteerd (kolom indexen, alle functies, alle cijfers, enzovoort), moet u het exacte aantal kolommen valideren dat door de selectie wordt geretourneerd.  
  
 <!--If you are trying to specify a comma-separated list of datasets as inputs to [Unpack Zipped Datasets](unpack-zipped-datasets.md), unpack only one dataset at a time. Multiple inputs are not supported.  -->
  
 Controleer of het aantal of het type van de upstream-kolommen niet is gewijzigd.  
  
 Als u een aanbevelings-gegevensset gebruikt voor het trainen van een model, moet u er rekening mee houden dat de aanbevolener een beperkt aantal kolommen verwacht, overeenkomend met gebruikers-en gebruikers items. Verwijder aanvullende kolommen voordat u het model uitoefent of de aanbevelings gegevens sets splitst. Zie [gesplitste gegevens](split-data.md)voor meer informatie.  
  
|Uitzonderings berichten|  
|------------------------|  
|Het aantal geselecteerde kolommen in de invoer-gegevensset is niet gelijk aan het verwachte aantal.|  
|Het aantal geselecteerde kolommen in de invoer-gegevensset is niet gelijk aan {0}.|  
|Het kolom selectie patroon '{0}' bevat het aantal geselecteerde kolommen in de ingevoerde gegevensset die niet gelijk is aan {1}.|  
|Het kolom selectie patroon '{0}' wordt verwacht om {1} kolom (men) op te geven die zijn geselecteerd in de invoer gegevensset, maar {2} kolom (men) is/zijn opgegeven.|  



## <a name="error-0023"></a>Fout 0023  
 Uitzonde ring treedt op als de doel kolom van de invoer gegevensset niet geldig is voor de huidige trainer-module.  
  
 Deze fout in Azure Machine Learning treedt op als de doel kolom (zoals geselecteerd in de module parameters) niet van het geldige gegevens type is, alle ontbrekende waarden bevat of niet categorische zoals verwacht.  
  
**Oplossing:** Ga naar de module-invoer om de inhoud van de kolom Label/doel te controleren. Zorg ervoor dat deze niet alle ontbrekende waarden bevat. Als de module doel kolom verwacht te categorische, moet u ervoor zorgen dat er meer dan één afzonderlijke waarde in de doel kolom is.  
  
|Uitzonderings berichten|  
|------------------------|  
|De invoer gegevensset bevat een niet-ondersteunde doel kolom.|  
|De invoer gegevensset bevat een niet-ondersteunde doel kolom{0}.|  
|De invoer gegevensset bevat een niet-ondersteunde doel kolom{0}voor meer informatie over type {1}.|  
 

## <a name="error-0024"></a>Fout 0024  
Uitzonde ring treedt op als DataSet geen kolom Label bevat.  

 Deze fout in Azure Machine Learning treedt op wanneer de module een label kolom vereist en de DataSet geen kolom label heeft. De evaluatie van een gescoorde gegevensset vereist bijvoorbeeld gewoonlijk dat er een kolom Label aanwezig is voor het berekenen van nauw keurigheid van gegevens.  
 
Het kan ook gebeuren dat een label kolom aanwezig is in de gegevensset, maar niet correct is gedetecteerd door Azure Machine Learning.
  
**Opgelost**

+ Open de module die de fout heeft gegenereerd en controleer of er een label kolom aanwezig is. De naam of het gegevens type van de kolom is niet van belang, op voor waarde dat de kolom een enkel resultaat (of een afhankelijke variabele) bevat die u wilt voors pellen. Als u niet zeker weet welke kolom het label heeft, zoekt u naar een algemene naam, bijvoorbeeld *klasse* of *doel*. 
+  Als de gegevensset geen kolom Label bevat, is het mogelijk dat de kolom Label expliciet of per ongeluk een upstream heeft verwijderd. Het kan ook zijn dat de gegevensset niet de uitvoer van een upstream-Score module is.
+ Als u de kolom expliciet wilt markeren als de kolom Label, voegt u de module [meta gegevens bewerken](edit-metadata.md) toe en verbindt u de gegevensset. Selecteer alleen de kolom Label en selecteer **Label** in de vervolg keuzelijst **velden** . 
+ Als de verkeerde kolom als label is gekozen, kunt u **label wissen** uit de **velden** selecteren om de meta gegevens van de kolom te herstellen. 
  
|Uitzonderings berichten|  
|------------------------|  
|De gegevensset bevat geen label kolom.|  
|Er bevindt zich geen kolom Label in{0}.|  
  

## <a name="error-0025"></a>Fout 0,025  
 Uitzonde ring treedt op als DataSet geen score kolom bevat.  
  
 Deze fout in Azure Machine Learning treedt op als de invoer voor het Evalueer model geen geldige Score kolommen bevat. De gebruiker probeert bijvoorbeeld een gegevensset te evalueren voordat deze met een juist getraind model werd gescoord of de Score kolom is expliciet verwijderd uit de upstream. Deze uitzonde ring treedt ook op als de Score kolommen op de twee gegevens sets incompatibel zijn. U kunt bijvoorbeeld proberen de nauw keurigheid van een lineaire regressor hierop te vergelijken met die van een binaire classificatie.  
  
**Oplossing:** Ga naar het Evalueer model en controleer of het een of meer Score kolommen bevat. Als dat niet het geval is, wordt de gegevensset niet beoordeeld of zijn de Score kolommen verwijderd in een upstream-module.  
  
|Uitzonderings berichten|  
|------------------------|  
|Gegevensset bevat geen kolom met scores.|  
|Er bevindt zich geen kolom met scores in{0}.|  
|Er bevindt zich geen score kolom in{0}die wordt geproduceerd door een{1}. Geef de gegevensset een score met het juiste type kenniser.|  
  

## <a name="error-0026"></a>Fout 0026  
 Uitzonde ring treedt op als kolommen met dezelfde naam niet zijn toegestaan.  
  
 Deze fout in Azure Machine Learning treedt op als meerdere kolommen dezelfde naam hebben. Een van de manieren waarop deze fout kan worden weer gegeven, is als de gegevensset geen koprij heeft en de kolom namen automatisch worden toegewezen: Col0, Kol1, enzovoort.  
  
**Oplossing:** Als kolommen dezelfde naam hebben, voegt u een module voor het [bewerken van meta gegevens](edit-metadata.md) in tussen de invoer-gegevensset en de module. Gebruik de kolom kiezer in [meta gegevens bewerken](edit-metadata.md) om de kolommen te selecteren waarvan u de naam wilt wijzigen, en typ de nieuwe namen in het tekstvak **nieuwe kolom namen** .  
  
|Uitzonderings berichten|  
|------------------------|  
|Er zijn gelijke kolom namen opgegeven in argumenten. Gelijke kolom namen zijn niet toegestaan per module.|  
|Gelijke kolom namen in argumenten{0}en{1}zijn niet toegestaan. Geef verschillende namen op.|  
  

## <a name="error-0027"></a>Fout 0027  
 Uitzonde ring treedt op wanneer twee objecten van dezelfde grootte moeten zijn, maar niet.  
  
 Dit is een veelvoorkomende fout in Azure Machine Learning en kan worden veroorzaakt door veel voor waarden.  
  
**Oplossing:** Er is geen specifieke oplossing. U kunt echter controleren op voor waarden, zoals de volgende:  
  
-   Als u de naam van kolommen wijzigt, moet u ervoor zorgen dat elke lijst (de invoer kolommen en de lijst met nieuwe namen) hetzelfde aantal items heeft.  
  
-   Als u twee gegevens sets samenvoegt of koppelt, moet u ervoor zorgen dat ze hetzelfde schema hebben.  
  
-   Als u twee gegevens sets met meerdere kolommen samenvoegt, moet u ervoor zorgen dat de sleutel kolommen hetzelfde gegevens type hebben en selecteert u de optie **dubbele waarden toestaan en kolom volgorde behouden in selectie**.  
  
|Uitzonderings berichten|  
|------------------------|  
|De grootte van de door gegeven objecten is inconsistent.|  
|De grootte van '{0}' is inconsistent met de grootte van '{1}'.|  
  

## <a name="error-0028"></a>Fout 0028  
 Uitzonde ring treedt op in het geval dat kolom sets dubbele kolom namen bevat en niet is toegestaan.  
  
 Deze fout in Azure Machine Learning treedt op wanneer kolom namen worden gedupliceerd. Dit is niet uniek.  
  
**Oplossing:** Als er kolommen dezelfde naam hebben, voegt u een exemplaar van de [meta gegevens bewerken](edit-metadata.md) toe tussen de invoer-gegevensset en de module die de fout heeft opgevallen. Gebruik de kolom kiezer in [meta gegevens bewerken](edit-metadata.md) om de kolommen te selecteren waarvan u de naam wilt wijzigen en typ de namen van de nieuwe kolommen in het tekstvak **nieuwe kolom namen** . Als u de naam van meerdere kolommen wijzigt, moet u ervoor zorgen dat de waarden die u in de **nieuwe kolom namen** typt, uniek zijn.  
  
|Uitzonderings berichten|  
|------------------------|  
|De kolomset bevat dubbele kolom namen.|  
|De naam{0}is gedupliceerd.|  
|De naam{0}is gedupliceerd in{1}.|  
  

## <a name="error-0029"></a>Fout 0029  
 Uitzonde ring treedt op wanneer ongeldige URI is door gegeven.  
  
 Deze fout in Azure Machine Learning treedt op als er een ongeldige URI is door gegeven.  U ontvangt deze fout als aan een van de volgende voor waarden wordt voldaan:, of.  
  
-   De open bare of SAS-URI voor Azure Blob Storage voor lezen of schrijven bevat een fout.  
  
-   Het tijd venster voor de SA'S is verlopen.  
  
-   De web-URL via de HTTP-bron vertegenwoordigt een bestand of een loop back-URI.  
  
-   De web-URL via HTTP bevat een onjuist ingedeelde URL.  
  
-   De URL kan niet worden omgezet door de externe bron.  
  
**Oplossing:** Ga naar de module en controleer de indeling van de URI. Als de gegevens bron een web-URL is via HTTP, controleert u of de bedoelde bron geen bestand of een loop back-URI (localhost) is.  
  
|Uitzonderings berichten|  
|------------------------|  
|Ongeldige URI is door gegeven.|  
  

## <a name="error-0030"></a>Fout 0030  
 Uitzonde ring treedt op wanneer het niet mogelijk is om een bestand te downloaden.  
  
 Deze uitzonde ring in Azure Machine Learning treedt op wanneer het niet mogelijk is om een bestand te downloaden. Deze uitzonde ring wordt weer gegeven wanneer een poging tot het lezen van een HTTP-bron is mislukt na drie (3) pogingen.  
  
**Oplossing:** Controleer of de URI naar de HTTP-bron juist is en of de site momenteel toegankelijk is via internet.  
  
|Uitzonderings berichten|  
|------------------------|  
|Kan een bestand niet downloaden.|  
|Fout bij het downloaden van het bestand: {0}.|  
  

## <a name="error-0031"></a>Fout 0031  
 Uitzonde ring treedt op als het aantal kolommen in de kolomset kleiner is dan nodig is.  
  
 Deze fout in Azure Machine Learning treedt op als het aantal geselecteerde kolommen kleiner is dan nodig is.  Deze fout wordt weer gegeven als het minimale vereiste aantal kolommen niet is geselecteerd.  
  
**Oplossing:** Voeg extra kolommen aan de kolom selectie toe met behulp van de **kolom kiezer**.  
  
|Uitzonderings berichten|  
|------------------------|  
|Het aantal kolommen in de kolomset is kleiner dan vereist.|  
|Er moeten {0} kolom (men) worden opgegeven. Het werkelijke aantal opgegeven kolommen is {1}.|  

## <a name="error-0032"></a>Fout 0032  
 Uitzonde ring treedt op als argument geen getal is.  
  
 Deze fout wordt weer gegeven in Azure Machine Learning als het argument een double of NaN is.  
  
**Oplossing:** Wijzig het opgegeven argument om een geldige waarde te gebruiken.  
  
|Uitzonderings berichten|  
|------------------------|  
|Het argument is geen getal.|  
|'{0}' is geen getal.|  
  

## <a name="error-0033"></a>Fout 0033  
 Uitzonde ring treedt op als argument oneindig is.  
  
 Deze fout in Azure Machine Learning treedt op als het argument oneindig is. Deze fout treedt op als het argument `double.NegativeInfinity` of `double.PositiveInfinity`is.  
  
**Oplossing:** Wijzig het opgegeven argument in een geldige waarde.  
  
|Uitzonderings berichten|  
|------------------------|  
|Argument moet eindig zijn.|  
|'{0}' is niet eindig.|  
  

## <a name="error-0034"></a>Fout 0034  
 Uitzonde ring treedt op als er meer dan één beoordeling bestaat voor een gegeven combi natie van gebruikers items.  
  
 Deze fout in Azure Machine Learning treedt op als een gebruiker-item paar meer dan één classificatie waarde heeft.  
  
**Oplossing:** Zorg ervoor dat het gebruikers-item paar slechts één waarderings waarde bezit.  
  
|Uitzonderings berichten|  
|------------------------|  
|Er bestaat meer dan één classificatie voor de waarde (n) in de gegevensset.|  
|Meer dan één classificatie voor gebruikers {0} en item {1} in de Voorspellings gegevens tabel voor de waardering.|  
  

## <a name="error-0035"></a>Fout 0035  
 Uitzonde ring treedt op als er geen functies voor een gegeven gebruiker of item zijn opgegeven.  
  
 Deze fout in Azure Machine Learning treedt op wanneer u een aanbevelings model wilt gebruiken om te scoren, maar er geen functie Vector kan worden gevonden.  
  
**Opgelost**

De matchbox-aanbeveling heeft bepaalde vereisten waaraan moet worden voldaan wanneer u de onderdelen van het item of de gebruikers functies gebruikt.  Deze fout geeft aan dat er een functie Vector ontbreekt voor een gebruiker of een item dat u als invoer hebt opgegeven.  U moet ervoor zorgen dat er een vector van functies beschikbaar is in de gegevens voor elke gebruiker of elk item.  
  
 Als u bijvoorbeeld een aanbevelings model hebt getraind met behulp van functies zoals de leeftijd, locatie of baten van de gebruiker, maar nu ook scores wilt maken voor nieuwe gebruikers die tijdens de training niet werden gezien, moet u een vergelijk bare set functies opgeven (te weten, leeftijd, locatie en inkomsten waarden) voor de nieuwe gebruikers om passende voor spellingen te maken. 
 
 Als u geen functies voor deze gebruikers hebt, kunt u de functie techniek gebruiken om de juiste functies te genereren.  Als u bijvoorbeeld geen afzonderlijke gebruikers leeftijd of inkomsten hebt, kunt u geschatte waarden genereren om te gebruiken voor een groep gebruikers. 
 
<!--When you are scoring from a recommendation mode, you can use item or user features only if you previously used item or user features during training. For more information, see [Score Matchbox Recommender](score-matchbox-recommender.md).
 
For general information about how the Matchbox recommendation algorithm works, and how to prepare a dataset of item features or user features, see [Train Matchbox Recommender](train-matchbox-recommender.md).  -->
  
 > [!TIP]
 > De oplossing is niet van toepassing op uw aanvraag? U kunt feedback over dit artikel verzenden en informatie geven over het scenario, met inbegrip van de module en het aantal rijen in de kolom. Deze informatie wordt gebruikt om in de toekomst meer gedetailleerde stappen voor probleem oplossing te bieden.
   
|Uitzonderings berichten|  
|------------------------|  
|Er zijn geen functies opgegeven voor een vereiste gebruiker of een vereist item.|  
|Functies voor {0} vereist, maar niet opgegeven.|  
  

## <a name="error-0036"></a>Fout 0036  
 Uitzonde ring treedt op als er meerdere functie vectoren voor een bepaalde gebruiker of een item zijn opgegeven.  
  
 Deze fout in Azure Machine Learning treedt op als een functie Vector meermaals is gedefinieerd.  
  
**Oplossing:** Zorg ervoor dat de functie Vector niet meer dan één keer is gedefinieerd.  
  
|Uitzonderings berichten|  
|------------------------|  
|Dubbele functie definitie voor een gebruiker of een item.|  
|Dubbele functie definitie voor {0}.|  
  

## <a name="error-0037"></a>Fout 0037  
 Uitzonde ring treedt op als er meerdere label kolommen zijn opgegeven en er slechts één is toegestaan.  
  
 Deze fout in Azure Machine Learning treedt op als er meer dan één kolom is geselecteerd als de nieuwe label kolom. Voor de meeste bewaakte leer algoritmen moet één kolom worden gemarkeerd als het doel of label.  
  
**Oplossing:** Zorg ervoor dat u één kolom selecteert als de nieuwe label kolom.  
  
|Uitzonderings berichten|  
|------------------------|  
|Er zijn meerdere label kolommen opgegeven.|  
  

## <a name="error-0038"></a>Fout 0038  
 Uitzonde ring treedt op als het aantal verwachte elementen een exacte waarde moet zijn, maar niet.  
  
 Deze fout in Azure Machine Learning treedt op als het aantal verwachte elementen een exacte waarde moet zijn, maar niet.  Deze fout wordt weer gegeven als het aantal elementen niet gelijk is aan de geldige verwachte waarde.  
  
**Oplossing:** Wijzig de invoer om het juiste aantal elementen te krijgen.  
  
|Uitzonderings berichten|  
|------------------------|  
|Het aantal elementen is niet geldig.|  
|Het aantal elementen in{0}is niet geldig.|  
|Het aantal elementen in{0}is niet gelijk aan het geldige aantal {1} element (en).|  
  

## <a name="error-0039"></a>Fout 0039  
 Uitzonde ring treedt op als een bewerking is mislukt.  
  
 Deze fout in Azure Machine Learning treedt op wanneer een interne bewerking niet kan worden voltooid.  
  
**Oplossing:** Deze fout wordt veroorzaakt door veel omstandigheden en er is geen specifiek verhaal.  
 De volgende tabel bevat algemene berichten voor deze fout, gevolgd door een specifieke beschrijving van de voor waarde. 
 
 Als er geen details beschikbaar zijn, [verzendt u feedback](https://social.msdn.microsoft.com/forums/azure/home?forum=MachineLearning) en geeft u informatie op over de modules die de fout hebben gegenereerd en de bijbehorende voor waarden.
  
|Uitzonderings berichten|  
|------------------------|  
|De bewerking is mislukt.|  
|Fout bij het volt ooien van de bewerking: {0}.|  
  

## <a name="error-0040"></a>Fout 0040  
 Uitzonde ring treedt op bij het aanroepen van een afgeschafte module.  
  
 Deze fout in Azure Machine Learning wordt gemaakt bij het aanroepen van een afgeschafte module.  
  
**Oplossing:** De afgeschafte module vervangen door een ondersteunde versie. Raadpleeg het uitvoer logboek van de module voor informatie over welke module u in plaats daarvan kunt gebruiken.  
  
|Uitzonderings berichten|  
|------------------------|  
|De afgeschafte module wordt geopend.|  
|Module{0}is afgeschaft. Gebruik in plaats daarvan de module{1}.|  
 

## <a name="error-0041"></a>Fout 0041  
 Uitzonde ring treedt op bij het aanroepen van een afgeschafte module.  
  
 Deze fout in Azure Machine Learning wordt gemaakt bij het aanroepen van een afgeschafte module.  
  
**Oplossing:** Vervang de afgeschafte module door een set met ondersteunde waarden. Deze informatie moet zichtbaar zijn in het uitvoer logboek van de module.  
  
|Uitzonderings berichten|  
|------------------------|  
|De afgeschafte module wordt geopend.|  
|Module{0}is afgeschaft. Gebruik de modules{1}voor de aangevraagde functionaliteit.|  
 

## <a name="error-0042"></a>Fout 0042  
 Uitzonde ring treedt op wanneer het niet mogelijk is om een kolom naar een ander type te converteren.  
  
 Deze fout in Azure Machine Learning treedt op wanneer het niet mogelijk is om een kolom te converteren naar het opgegeven type.  U ontvangt deze fout melding als een module een bepaald gegevens type vereist, zoals datetime, Text, een getal met een drijvende komma of een geheel getal, maar het is niet mogelijk om een bestaande kolom te converteren naar het vereiste type.  
 
U kunt bijvoorbeeld een kolom selecteren en proberen deze te converteren naar een numeriek gegevens type voor gebruik in een reken kundige bewerking. deze fout wordt opgehaald als de kolom ongeldige gegevens bevat. 

Een andere reden hiervoor is dat u deze fout kunt krijgen als u een kolom met drijvende-komma getallen of een groot aantal unieke waarden wilt gebruiken als een categorische kolom. 
  
**Opgelost**

+ Open de Help-pagina voor de module die de fout heeft gegenereerd en controleer de vereisten voor het gegevens type.
+ Bekijk de gegevens typen van de kolommen in de invoer gegevensset.
+ Inspecteer gegevens die afkomstig zijn van zogenaamde schema-less-gegevens bronnen.
+ Controleer de gegevensset op ontbrekende waarden of speciale tekens die de conversie naar het gewenste gegevens type kunnen blok keren. 
    + Numerieke gegevens typen moeten consistent zijn: Controleer bijvoorbeeld op drijvende-komma getallen in een kolom met gehele getallen.
    + Zoek naar tekst reeksen of N.V.T. waarden in een kolom met getallen. 
    + Booleaanse waarden kunnen worden geconverteerd naar een geschikte weer gave, afhankelijk van het vereiste gegevens type.
    + Tekst kolommen voor niet-Unicode-tekens, tabtekens of besturings tekens controleren
    + Datetime-gegevens moeten consistent zijn om model fouten te voor komen, maar opschonen kan ingewikkeld zijn vanwege de vele notaties. Overweeg het gebruik van <!--the [Execute R Script](execute-r-script.md) or -->[Voer python-script](execute-python-script.md) modules uit om opschoning uit te voeren.  
+ Wijzig indien nodig de waarden in de invoer-gegevensset zodat de kolom kan worden geconverteerd. Aanpassing kan binning, afkap ping of Afrondings bewerkingen, eliminatie van uitschieters of toerekening van ontbrekende waarden bevatten. Raadpleeg de volgende artikelen voor enkele veelvoorkomende scenario's voor gegevens transformatie in machine learning:
    + [Ontbrekende gegevens opschonen](clean-missing-data.md)
    + [Gegevens normaliseren](normalize-data.md)
<!--+ [Clip Values](clip-values.md) 
    + [Group Data Into Bins](group-data-into-bins.md)
  -->
 
> [!TIP]
> Is de oplossing onduidelijk of niet van toepassing op uw aanvraag? U kunt feedback over dit artikel verzenden en informatie geven over het scenario, met inbegrip van de module en het gegevens type van de kolom. Deze informatie wordt gebruikt om in de toekomst meer gedetailleerde stappen voor probleem oplossing te bieden.  
  
|Uitzonderings berichten|  
|------------------------|  
|Conversie niet toegestaan.|  
|Kan kolom van het type {0} niet converteren naar een kolom van het type {1}.|  
|Kan kolom{2}van het type {0} niet converteren naar een kolom van het type {1}.|  
|Kan kolom{2}van het type {0} niet converteren naar kolom{3}van het type {1}.|  
  

## <a name="error-0043"></a>Fout 0043  
 Uitzonde ring treedt op wanneer element type niet expliciet gelijk is aan.  
  
 Deze fout in Azure Machine Learning wordt niet gebruikt en wordt afgeschaft.  
  
**Oplossing:** Geen.  
  
|Uitzonderings berichten|  
|------------------------|  
|Er is geen toegankelijke expliciete methode gevonden die overeenkomt met.|  
|Kan geen waarden vergelijken voor kolom \\{0}\\van het type {1}. Er is geen toegankelijke expliciete methode gevonden die overeenkomt met.|  


## <a name="error-0044"></a>Fout 0044  
 Uitzonde ring treedt op wanneer het niet mogelijk is om element type van kolom van de bestaande waarden af te leiden.  
  
 Deze fout in Azure Machine Learning treedt op wanneer het niet mogelijk is om het type van een kolom of kolommen in een gegevensset af te leiden. Dit gebeurt meestal bij het samen voegen van twee of meer gegevens sets met verschillende element typen. Als Azure Machine Learning geen gemeen schappelijk type kunt bepalen dat alle waarden in een kolom of kolommen kan vertegenwoordigen zonder gegevens verlies, wordt deze fout gegenereerd.  
  
**Oplossing:** Zorg ervoor dat alle waarden in een bepaalde kolom in beide gegevens sets die worden gecombineerd, van hetzelfde type zijn (numeriek, Booleaans, categorische, teken reeks, datum, enz.) of naar hetzelfde type kunnen worden gedwongen.  
  
|Uitzonderings berichten|  
|------------------------|  
|Element type van de kolom kan niet worden afgeleid.|  
|Kan element type niet afleiden voor kolom{0}--alle elementen zijn null-verwijzingen.|  
|Kan element type niet afleiden voor kolom{0}van gegevensset{1}--alle elementen zijn null-verwijzingen.|  
  

## <a name="error-0045"></a>Fout 0045  
 Uitzonde ring treedt op wanneer het niet mogelijk is om een kolom te maken vanwege gemengde element typen in de bron.  
  
 Deze fout in Azure Machine Learning wordt gemaakt wanneer de element typen van twee gegevens sets die worden gecombineerd, verschillend zijn.  
  
**Oplossing:** Zorg ervoor dat alle waarden in een bepaalde kolom in beide gegevens sets worden gecombineerd van hetzelfde type (numeriek, Booleaans, categorische, teken reeks, datum, enzovoort).  
  
|Uitzonderings berichten|  
|------------------------|  
|Kan geen kolom met gemengde element typen maken.|  
|Kan geen kolom maken met de ID '{0}' van gemengde element typen: \ n\tType van gegevens [{1}, {0}] is {2}\n\tType aan gegevens [{3}] {0}.{4}|  
  

## <a name="error-0046"></a>Fout 0046  
 Uitzonde ring treedt op wanneer het niet mogelijk is om een map te maken op het opgegeven pad.  
  
 Deze fout in Azure Machine Learning treedt op wanneer het niet mogelijk is om een map te maken op het opgegeven pad. Deze fout wordt weer gegeven als een deel van het pad naar de uitvoermap voor een Hive-query onjuist of niet toegankelijk is.  
  
**Oplossing:** Ga naar de module en controleer of het mappad juist is ingedeeld en of het toegankelijk is met de huidige referenties.  
  
|Uitzonderings berichten|  
|------------------------|  
|Geef een geldige uitvoermap op.|  
|Map: {0} kan niet worden gemaakt. Geef een geldig pad op.|  
  

## <a name="error-0047"></a>Fout 0047  
 Uitzonde ring treedt op als aantal functie kolommen in sommige gegevens sets die zijn door gegeven aan de module te klein is.  
  
 Deze fout in Azure Machine Learning treedt op als de invoer-gegevensset voor de training niet het minimale aantal kolommen bevat dat vereist is voor de algoritme. De gegevensset is meestal leeg of bevat alleen trainings kolommen.  
  
**Oplossing:** Ga naar de invoer gegevensset om ervoor te zorgen dat er een of meer extra kolommen uit de kolom Label komen.  
  
|Uitzonderings berichten|  
|------------------------|  
|Het aantal functie kolommen in de invoer gegevensset is lager dan het toegestane minimum.|  
|Het aantal functie kolommen in de invoer gegevensset is kleiner dan het toegestane minimum van {0} kolom (men).|  
|Het aantal functie kolommen in de invoer-gegevensset{0}is kleiner dan het toegestane minimum van {1} kolom (men).|  
  

## <a name="error-0048"></a>Fout 0048  
 Uitzonde ring treedt op wanneer het niet mogelijk is om een bestand te openen.  
  
 Deze fout in Azure Machine Learning treedt op wanneer het niet mogelijk is om een bestand te openen voor lezen of schrijven. Deze fout kan om de volgende redenen worden weer gegeven:  
  
-   De container of het bestand (BLOB) bestaat niet  
  
-   Het toegangs niveau van het bestand of de container biedt geen toegang tot het bestand  
  
-   Het bestand is te groot om te lezen of heeft een onjuiste indeling  
  
**Oplossing:** Ga naar de module en het bestand dat u probeert te lezen.  
  
 Controleer of de namen van de container en het bestand juist zijn.  
  
 Gebruik de klassieke Azure-portal of een Azure-opslag programma om te controleren of u gemachtigd bent om toegang te krijgen tot het bestand.  
  
  <!--If you are trying to read an image file, make sure that it meets the requirements for image files in terms of size, number of pixels, and so forth. For more information, see [Import Images](import-images.md).  -->
  
|Uitzonderings berichten|  
|------------------------|  
|Kan geen bestand openen.|  
|Fout bij het openen van het bestand: {0}.|  


## <a name="error-0049"></a>Fout 0049  
 Uitzonde ring treedt op wanneer het niet mogelijk is om een bestand te parseren.  
  
 Deze fout in Azure Machine Learning treedt op wanneer het niet mogelijk is om een bestand te parseren. U ontvangt deze fout melding als de bestands indeling die is geselecteerd in de module [gegevens importeren](import-data.md) niet overeenkomt met de daad werkelijke indeling van het bestand, of als het bestand een onherkenbaar teken bevat.  
  
**Oplossing:** Ga naar de module en corrigeer de selectie van de bestands indeling als deze niet overeenkomt met de indeling van het bestand. Controleer, indien mogelijk, het bestand om te bevestigen dat het geen ongeldige tekens bevat.  
  
|Uitzonderings berichten|  
|------------------------|  
|Een bestand kan niet worden geparseerd.|  
|Fout bij het parseren van het bestand: {0}.|  
  

## <a name="error-0050"></a>Fout 0050  
 Uitzonde ring treedt op als de invoer-en uitvoer bestanden hetzelfde zijn.  
  
**Oplossing:** Deze fout in Azure Machine Learning wordt niet gebruikt en wordt afgeschaft.  
  
|Uitzonderings berichten|  
|------------------------|  
|De opgegeven bestanden voor invoer en uitvoer kunnen niet hetzelfde zijn.|


## <a name="error-0051"></a>Fout 0051  
 Uitzonde ring treedt op wanneer verschillende uitvoer bestanden hetzelfde zijn.  
  
**Oplossing:** Deze fout in Azure Machine Learning wordt niet gebruikt en wordt afgeschaft.  
  
|Uitzonderings berichten|  
|------------------------|  
|Opgegeven bestanden voor uitvoer kunnen niet hetzelfde zijn.|


## <a name="error-0052"></a>Fout 0052  
 Uitzonde ring treedt op als de Azure Storage-account sleutel onjuist is opgegeven.  
  
 Deze fout in Azure Machine Learning treedt op als de sleutel die wordt gebruikt voor toegang tot het Azure Storage-account onjuist is. U kunt deze fout bijvoorbeeld zien als de Azure-opslag sleutel is afgekapt bij het kopiëren en plakken of als de verkeerde sleutel is gebruikt.  
  
 Zie [toegangs sleutels voor opslag weer geven, kopiëren en opnieuw genereren](https://azure.microsoft.com/documentation/articles/storage-create-storage-account-classic-portal/)voor meer informatie over het ophalen van de sleutel voor een Azure-opslag account.  
  
**Oplossing:** Ga naar de module en controleer of de Azure Storage-sleutel juist is voor het account. Kopieer de sleutel opnieuw vanuit de klassieke Azure-Portal, indien nodig.  
  
|Uitzonderings berichten|  
|------------------------|  
|De sleutel van het Azure-opslag account is onjuist.|  
  

## <a name="error-0053"></a>Fout 0053  
 Uitzonde ring treedt op wanneer er geen gebruikers functies of items zijn voor matchbox-aanbevelingen.  
  
 Deze fout in Azure Machine Learning wordt gemaakt wanneer een functie Vector niet kan worden gevonden.  
  
**Oplossing:** Zorg ervoor dat er een functie Vector aanwezig is in de ingevoerde gegevensset.  
  
|Uitzonderings berichten|  
|------------------------|  
|Gebruikers onderdelen of/en items zijn vereist, maar niet opgegeven.|  

## <a name="error-0054"></a>Fout 0054  
 Uitzonde ring treedt op als er te weinig verschillende waarden in de kolom zijn om de bewerking te volt ooien.  
  
**Oplossing:** Deze fout in Azure Machine Learning wordt niet gebruikt en wordt afgeschaft.  
  
|Uitzonderings berichten|  
|------------------------|  
|Gegevens hebben te weinig unieke waarden in de opgegeven kolom om de bewerking te volt ooien.|  
|Gegevens hebben te weinig unieke waarden in de opgegeven kolom om de bewerking te volt ooien. Het vereiste minimum is {0} elementen.|  
|Gegevens hebben te weinig unieke waarden in de kolom{1}om de bewerking te volt ooien. Het vereiste minimum is {0} elementen.|  
  

## <a name="error-0055"></a>Fout 0055  
 Uitzonde ring treedt op bij het aanroepen van een afgeschafte module.  Deze fout in Azure Machine Learning wordt weer gegeven als u probeert een module aan te roepen die is afgeschaft.
  
**Opgelost**
  
|Uitzonderings berichten|  
|------------------------|  
|De afgeschafte module wordt geopend.|  
|Module{0}is afgeschaft.|  

## <a name="error-0056"></a>Fout 0056  
 Uitzonde ring treedt op als de kolommen die u hebt geselecteerd voor een bewerking, de vereisten schenden.  
  
 Deze fout in Azure Machine Learning treedt op wanneer u kolommen kiest voor een bewerking waarvoor de kolom van een bepaald gegevens type is. 
 
 Deze fout kan ook optreden als de kolom het juiste gegevens type is, maar de module die u gebruikt vereist dat de kolom ook is gemarkeerd als een functie, label of categorische kolom.  
  
  <!--For example, the [Convert to Indicator Values](convert-to-indicator-values.md) module requires that columns be categorical, and will raise this error if you select a feature column or label column.  -->
  
**Opgelost**
  
1.  Controleer het gegevens type van de kolommen die momenteel zijn geselecteerd. 

2. Controleer of de geselecteerde kolommen categorische, label of functie kolommen zijn.  
  
3.  Raadpleeg het Help-onderwerp voor de module waarin u de kolom selectie hebt gemaakt om te bepalen of er specifieke vereisten gelden voor het gegevens type of het kolom gebruik.  
  
3.  Gebruik [meta gegevens bewerken](edit-metadata.md) om het kolom Type voor de duur van deze bewerking te wijzigen. Zorg ervoor dat u het kolom type weer op de oorspronkelijke waarde wijzigt, met behulp van een ander exemplaar van [meta gegevens bewerken](edit-metadata.md)als u dit nodig hebt voor downstream-bewerkingen.  
  
|Uitzonderings berichten|  
|------------------------|  
|Een of meer geselecteerde kolommen bevinden zich niet in een categorie die is toegestaan.|  
|De kolom met de naam{0}heeft geen toegestane categorie.|  
  

## <a name="error-0057"></a>Fout 0057  
 Uitzonde ring treedt op bij het maken van een bestand of BLOB die al bestaat.  
  
 Deze uitzonde ring treedt op wanneer u de module [gegevens exporteren](export-data.md) of een andere module gebruikt om resultaten van een pijp lijn in azure machine learning naar Azure Blob-opslag op te slaan, maar u probeert een bestand of BLOB te maken die al bestaat.   
  
**Opgelost**
 
 Deze fout wordt alleen weer gegeven als u eerder de eigenschap **schrijf modus van Azure Blob Storage** hebt ingesteld op **fout**. In deze module wordt een fout gegenereerd als u probeert een gegevensset te schrijven naar een blob die al bestaat.
 
 - Open de module-eigenschappen en wijzig de eigenschap **schrijf modus van Azure Blob Storage** in **overschrijven**.
 - U kunt ook de naam van een andere bestemmings-BLOB of-bestand typen en ervoor zorgen dat u een BLOB opgeeft die nog niet bestaat.  
  
|Uitzonderings berichten|  
|------------------------|  
|Het bestand of de BLOB bestaat al.|  
|Het bestand of de blob "{0}" bestaat al.|  
  

## <a name="error-0058"></a>Fout 0058  
 Deze fout in Azure Machine Learning treedt op als de DataSet de verwachte Label kolom niet bevat.  
  
 Deze uitzonde ring kan ook optreden als de opgegeven label kolom niet overeenkomt met de gegevens of het data type dat wordt verwacht door de cursist of onjuiste waarden heeft. Deze uitzonde ring wordt bijvoorbeeld gemaakt wanneer u een label kolom met een werkelijke waarde gebruikt bij het trainen van een binaire classificatie.  
  
**Oplossing:** De oplossing is afhankelijk van de leerer of trainer die u gebruikt, en de gegevens typen van de kolommen in uw gegevensset. Controleer eerst de vereisten van de module machine learning algoritme of training.  
  
 Ga naar de invoer gegevensset. Controleer of de kolom die u verwacht te behandelen, het juiste gegevens type heeft voor het model dat u maakt.  
  
 Controleer de invoer voor ontbrekende waarden en verwijder deze indien nodig.  
  
 Voeg, indien nodig, de module [meta gegevens bewerken](edit-metadata.md) toe en zorg ervoor dat de kolom Label is gemarkeerd als een label.  
  
|Uitzonderings berichten|  
|------------------------|  
|De kolom Label is niet zoals verwacht|  
|De kolom Label is niet zoals verwacht in{0}.|  
|De label kolom{0}wordt niet verwacht in{1}.|  
  

## <a name="error-0059"></a>Fout 0059  
 Uitzonde ring treedt op als een kolom index die is opgegeven in een kolom kiezer, niet kan worden geparseerd.  
  
 Deze fout in Azure Machine Learning treedt op als een kolom index die is opgegeven bij het gebruik van de kolom kiezer, niet kan worden geparseerd.  Deze fout wordt weer gegeven wanneer de kolom index een ongeldige indeling heeft die niet kan worden geparseerd.  
  
**Oplossing:** Wijzig de kolom index om een geldige index waarde te gebruiken.  
  
|Uitzonderings berichten|  
|------------------------|  
|Een of meer opgegeven kolom indexen of index bereiken kunnen niet worden geparseerd.|  
|De kolom index of het bereik{0}kan niet worden geparseerd.|  
  

## <a name="error-0060"></a>Fout 0060  
 Uitzonde ring treedt op wanneer een buiten bereik kolom bereik is opgegeven in een kolom kiezer.  
  
 Deze fout in Azure Machine Learning treedt op wanneer een out-of-Range-kolom bereik is opgegeven in de kolom kiezer. Deze fout wordt weer gegeven als het kolom bereik in de kolom kiezer niet overeenkomt met de kolommen in de gegevensset.  
  
**Oplossing:** Wijzig het kolom bereik in de kolom kiezer zodat dit overeenkomt met de kolommen in de gegevensset.  
  
|Uitzonderings berichten|  
|------------------------|  
|Ongeldig of buiten bereik opgegeven kolom index bereik.|  
|Het kolom bereik{0}is ongeldig of valt buiten het bereik.|  
  

## <a name="error-0061"></a>Fout 0061  
 Er treedt een uitzonde ring op bij het toevoegen van een rij aan een DataTable met een verschillend aantal kolommen dan de tabel.  
  
 Deze fout in Azure Machine Learning treedt op wanneer u een rij probeert toe te voegen aan een gegevensset met een ander aantal kolommen dan de gegevensset.  U ontvangt deze fout melding als de rij die wordt toegevoegd aan de gegevensset, een verschillend aantal kolommen uit de invoer gegevensset heeft.  De rij kan niet worden toegevoegd aan de gegevensset als het aantal kolommen niet overeenkomt.  
  
**Oplossing:** Wijzig de invoer-gegevensset zodat hetzelfde aantal kolommen hetzelfde is als de toegevoegde rij, of wijzig de rij die wordt toegevoegd aan hetzelfde aantal kolommen als de gegevensset.  
  
|Uitzonderings berichten|  
|------------------------|  
|Alle tabellen moeten hetzelfde aantal kolommen hebben.|  
  

## <a name="error-0062"></a>Fout 0062  
 Er treedt een uitzonde ring op wanneer u twee modellen met verschillende typen kennissen wilt vergelijken.  
  
 Deze fout in Azure Machine Learning wordt gemaakt wanneer de metrische gegevens voor de evaluatie van twee verschillende gescoorde gegevens sets niet kunnen worden vergeleken. In dit geval is het niet mogelijk om de effectiviteit te vergelijken van de modellen die worden gebruikt voor het produceren van de twee gescoorde gegevens sets.  
  
**Oplossing:** Controleer of de gescoorde resultaten worden geproduceerd door hetzelfde soort machine learning model (binaire classificatie, regressie, classificatie met meerdere klassen, aanbeveling, Clustering, afwijkings detectie enz.) Alle modellen die u vergelijkt, moeten hetzelfde type leerder hebben.  
  
|Uitzonderings berichten|  
|------------------------|  
|Alle modellen moeten hetzelfde informatie type hebben.|  
  

 <!--## Error 0063  
 This exception is raised when R script evaluation fails with an error.  
  
 This error occurs when you have provided an R script in one of the [R language modules](r-language-modules.md) in Azure Machine Learning, and the R code contains internal syntax errors. The exception can also occur if you provide the wrong inputs to the R script. 
 
 The error can also occur if the script is too large to execute in the workspace. The maximum script size for the **Execute R Script** module is 1,000 lines or 32 KB of work space, whichever is lesser.
  
**Resolution:**

1. In Azure Machine Learning designer, right-click the module that has the error, and select **View Log**.
2. Examine the standard error log of the module, which contains the stack trace.
    + Lines beginning with [ModuleOutput] indicate output from R.
    + Messages from R marked as **warnings** typically do not cause the pipeline to fail.
3. Resolve script issues.  
    + Check for R syntax errors. Check for variables that are defined but never populated.
    + Review the input data and the script to determine if either the data or variables in the script use characters not supported by Azure Machine Learning.
    + Check whether all package dependencies are installed.
    + Check whether your code loads required libraries that are not loaded by default.
    + Check whether the required packages are the correct version.
    + Make sure that any dataset that you want to output is converted to a data frame.  
4.  Resubmit the pipeline.

 <!--
> [!NOTE]
> These topics contains examples of R code that you can use, as well as links to pipelines in the [Cortana Intelligence Gallery](https://gallery.cortanaintelligence.com) that use R script.
> + [Execute R Script](execute-r-script.md)
> + [Create R Model](create-r-model.md)
-->  
|Uitzonderings berichten|  
|------------------------|  
|Fout tijdens de evaluatie van het R-script.|  
|De volgende fout is opgetreden tijdens de evaluatie van R-script:----------begin van fout bericht van R----------{0}-----------einde van het fout bericht van R-----------|  
|Tijdens de evaluatie van R-script "{1}" is de volgende fout opgetreden:----------begin van fout bericht van R----------{0}-----------einde van fout bericht van R-----------|  
  


## <a name="error-0064"></a>Fout 0064  
 Uitzonde ring treedt op als de naam van het Azure Storage-account of de opslag sleutel onjuist is opgegeven.  
  
 Deze fout in Azure Machine Learning treedt op als de naam van het Azure Storage-account of de opslag sleutel onjuist is opgegeven. Deze fout wordt weer gegeven als u een onjuiste account naam of een onjuist wacht woord voor het opslag account opgeeft. Dit kan gebeuren als u de account naam of het wacht woord hand matig invoert. Dit kan ook gebeuren als het account is verwijderd.  
  
**Oplossing:** Controleer of de account naam en het wacht woord juist zijn ingevoerd en of het account bestaat.  
  
|Uitzonderings berichten|  
|------------------------|  
|De naam van het Azure-opslag account of de opslag sleutel is onjuist.|  
|De naam van het Azure Storage-account "{0}" of de opslag sleutel voor de account naam is onjuist.|  
  

## <a name="error-0065"></a>Fout 0065  
 Uitzonde ring treedt op als de naam van de Azure-Blob onjuist is opgegeven.  
  
 Deze fout in Azure Machine Learning treedt op als de naam van de Azure-Blob onjuist is opgegeven.  De volgende fout wordt weer gegeven als:  
  
-   De blob is niet gevonden in de opgegeven container.  
  
 <!---   The fully qualified name of the blob specified for output in one of the [Learning with Counts](data-transformation-learning-with-counts.md) modules is greater than 512 characters.  -->
  
-   Alleen de container is opgegeven als bron in een aanvraag voor het [importeren van gegevens](import-data.md) wanneer de indeling Excel of CSV met code ring was. het samen voegen van de inhoud van alle blobs in een container is niet toegestaan met deze notaties.  
  
-   Een SAS-URI bevat niet de naam van een geldige blob.  
  
**Oplossing:** Ga naar de module om de uitzonde ring te genereren. Controleer of de opgegeven BLOB bestaat in de container in het opslag account en of de machtigingen de BLOB kunnen bekijken. Controleer of de invoer van het formulier **containerName/filename** is als u Excel of CSV met coderings indelingen hebt. Controleer of een SAS-URI de naam van een geldige BLOB bevat.  
  
|Uitzonderings berichten|  
|------------------------|  
|De Azure Storage-blob is onjuist.|  
|De naam van de Azure Storage-blob "{0}" is onjuist|  
  

## <a name="error-0066"></a>Fout 0066  
 Uitzonde ring treedt op als een bron niet naar een Azure-Blob kan worden geüpload.  
  
 Deze fout in Azure Machine Learning treedt op als een bron niet naar een Azure-Blob kan worden geüpload.  <!--You will receive this message if [Train Vowpal Wabbit 7-4 Model](train-vowpal-wabbit-version-7-4-model.md) encounters an error attempting to save either the model or the hash created when training the model.--> Beide worden opgeslagen in hetzelfde Azure-opslag account als het account dat het invoer bestand bevat.  
  
**Oplossing:** Ga naar de module. Controleer of de naam van het Azure-account, de opslag sleutel en de container juist zijn en of het account gemachtigd is om naar de container te schrijven.  
  
|Uitzonderings berichten|  
|------------------------|  
|De resource kan niet worden geüpload naar Azure Storage.|  
|Het bestand '{0}' kan niet worden geüpload naar Azure Storage als {1}.|  
  

## <a name="error-0067"></a>Fout 0067  
 Uitzonde ring treedt op als een dataset een verschillend aantal kolommen heeft dan verwacht.  
  
 Deze fout in Azure Machine Learning treedt op als een dataset een ander aantal kolommen heeft dan verwacht.  Deze fout wordt weer gegeven wanneer het aantal kolommen in de gegevensset afwijkt van het aantal kolommen dat de module verwacht tijdens de uitvoering.  
  
**Oplossing:** Wijzig de invoer gegevensset of de para meters.  
  
|Uitzonderings berichten|  
|------------------------|  
|Er is een onverwacht aantal kolommen in de DataTable.|  
|Er worden{0}kolommen verwacht, maar er zijn{1}kolommen gevonden.|  
  

## <a name="error-0068"></a>Fout 0068  
 Uitzonde ring treedt op als het opgegeven Hive-script onjuist is.  
  
 Deze fout in Azure Machine Learning treedt op als er syntaxis fouten in het script van een Hive QL zijn of als de Hive-interpreter een fout ontdekt tijdens het uitvoeren van de query of het script.  
  
**Opgelost**

Het fout bericht van Hive wordt normaal gesp roken weer gegeven in het fouten logboek, zodat u actie kunt ondernemen op basis van de specifieke fout. 

+ Open de module en Inspecteer de query op fouten.  
+ Controleer of de query correct werkt buiten Azure Machine Learning door u aan te melden bij de Hive-console van uw Hadoop-cluster en de query uit te voeren.  
+ Probeer opmerkingen in het Hive-script op een afzonderlijke regel te plaatsen, in tegens telling tot het combi neren van uitvoer bare instructies en opmerkingen op één regel.  

### <a name="resources"></a>Bronnen

Raadpleeg de volgende artikelen voor meer informatie over Hive-query's voor machine learning:

+ [Hive-tabellen maken en gegevens laden vanuit Azure Blob Storage](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-move-hive-tables)
+ [Gegevens in tabellen verkennen met hive-query's](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-explore-data-hive-tables)
+ [Functies maken voor gegevens in een Hadoop-cluster met behulp van Hive-query's](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-create-features-hive)
+ [Hive voor SQL-gebruikers Cheat Sheet (PDF)](http://hortonworks.com/wp-content/uploads/2013/05/hql_cheat_sheet.pdf)

  
|Uitzonderings berichten|  
|------------------------|  
|Het Hive-script is onjuist.|  
|Het Hive-script {0} is niet juist.|  
  

## <a name="error-0069"></a>Fout 0069  
 Uitzonde ring treedt op als het opgegeven SQL-script onjuist is.  
  
 Deze fout in Azure Machine Learning treedt op als het opgegeven SQL-script syntaxis problemen bevat of als de kolommen of tabel die is opgegeven in het script, ongeldig zijn. 
 
 Deze fout wordt weer gegeven als de SQL-engine een fout tegen komt tijdens het uitvoeren van de query of het script. Het SQL-fout bericht wordt normaal gesp roken weer gegeven in het fouten logboek, zodat u actie kunt ondernemen op basis van de specifieke fout.  
  
**Oplossing:** Ga naar de module en Inspecteer de SQL-query op fouten.  
  
 Controleer of de query correct werkt buiten Azure ML door u rechtstreeks aan te melden bij de database server en de query uit te voeren.  
  
 Als er een gegenereerd SQL-bericht wordt gemeld door de module-uitzonde ring, actie ondernemen op basis van de gerapporteerde fout. De fout berichten bevatten bijvoorbeeld soms specifieke richt lijnen voor de waarschijnlijke fout:
+ Een *dergelijke kolom of ontbrekende data base*geeft aan dat u een ongeldige kolom naam hebt opgegeven. Als u zeker weet dat de naam van de kolom juist is, gebruikt u accolades of aanhalings tekens om de kolom-id op te sluiten.
+ *SQL-logische fout in de buurt van \<SQL-tref woord\>* , wat aangeeft dat u een syntaxis fout hebt vóór het opgegeven tref woord

  
|Uitzonderings berichten|  
|------------------------|  
|SQL-script is onjuist.|  
|De SQL-query{0}is niet juist.|  
|De SQL-query{0}is niet juist: {1}|  
  

## <a name="error-0070"></a>Fout 0070  
 Uitzonde ring treedt op bij een poging toegang te krijgen tot een niet-bestaande Azure-tabel.  
  
 Deze fout in Azure Machine Learning treedt op wanneer u een niet-bestaande Azure-tabel probeert te openen. Deze fout wordt weer gegeven als u een tabel in azure Storage opgeeft die niet bestaat als u leest van of schrijft naar Azure Table Storage. Dit kan gebeuren als u de naam van de gewenste tabel onjuist typt of als de doel naam en het opslag type niet overeenkomen. U kunt bijvoorbeeld een tabel lezen, maar u kunt in plaats daarvan de naam van een BLOB opgeven.  
  
**Oplossing:** Ga naar de module om te controleren of de naam van de tabel juist is.  
  
|Uitzonderings berichten|  
|------------------------|  
|De Azure-tabel bestaat niet.|  
|De Azure-tabel{0}bestaat niet.|  
  
## <a name="error-0071"></a>Fout 0071  
 Uitzonde ring treedt op als de gegeven referenties onjuist zijn.  
  
 Deze fout in Azure Machine Learning treedt op als de gegeven referenties onjuist zijn.  
  
 Deze fout kan ook worden weer gegeven als de module geen verbinding kan maken met een HDInsight-cluster.  
  
**Oplossing:** Controleer de invoer voor de module en controleer de account naam en het wacht woord.  
  
 Controleer op de volgende problemen waardoor een fout kan optreden:  
  
-   Het schema van de gegevensset komt niet overeen met het schema van de doel-DataTable.  
  
-   Kolom namen ontbreken of zijn onjuist gespeld  
  
-   U schrijft naar een tabel met kolom namen met ongeldige tekens. Normaal gesp roken kunt u dergelijke kolom namen tussen vier Kante haken plaatsen, maar als dat niet werkt, moet u kolom namen bewerken om alleen letters en onderstrepings tekens (_) te gebruiken  
  
-   Teken reeksen die u probeert te schrijven, bevatten enkele aanhalings tekens  
  
 Als u probeert verbinding te maken met een HDInsight-cluster, controleert u of het doel cluster toegankelijk is met de opgegeven referenties.  
  
|Uitzonderings berichten|  
|------------------------|  
|Er zijn onjuiste referenties door gegeven.|  
|Er is een onjuiste gebruikers naam{0}of het wacht woord is door gegeven|  
  

## <a name="error-0072"></a>Fout 0072  
 Uitzonde ring treedt op in het geval van een time-out voor de verbinding.  
  
 Deze fout in Azure Machine Learning treedt op wanneer er een time-out optreedt voor een verbinding. U ontvangt deze fout melding als er momenteel verbindings problemen zijn met de gegevens bron of het doel, zoals een trage Internet verbinding, of als de gegevensset groot is en/of de SQL-query die in de gegevens wordt gelezen, complexe verwerking uitvoert.  
  
**Oplossing:** Bepaal of er momenteel problemen zijn met trage verbindingen met Azure Storage of Internet.  
  
|Uitzonderings berichten|  
|------------------------|  
|Verbindingstime-out opgetreden.|  
  

## <a name="error-0073"></a>Fout 0073  
 Uitzonde ring treedt op als er een fout optreedt tijdens het converteren van een kolom naar een ander type.  
  
 Deze fout in Azure Machine Learning treedt op wanneer het niet mogelijk is om een kolom naar een ander type te converteren.  U ontvangt deze fout melding als een module een bepaald type vereist en het niet mogelijk is om de kolom te converteren naar het nieuwe type.  
  
**Oplossing:** Wijzig de invoer gegevensset zodat de kolom kan worden geconverteerd op basis van de interne uitzonde ring.  
  
|Uitzonderings berichten|  
|------------------------|  
|Kan kolom niet converteren.|  
|Kan de kolom niet converteren naar {0}.|  
  

## <a name="error-0074"></a>Fout 0074  
 Uitzonde ring treedt op wanneer met de [meta gegevens](edit-metadata.md) van de bewerking een sparse kolom wordt omgezet in categorische.  
  
 Deze fout in Azure Machine Learning treedt op wanneer met de [meta gegevens](edit-metadata.md) van de bewerking een sparse kolom wordt omgezet in categorische.  U ontvangt deze fout bij het converteren van sparse kolommen naar categorische met de optie **categorische maken** .  Azure machine learning biedt geen ondersteuning voor sparse categorische-matrices, waardoor de module niet kan worden uitgevoerd.  
  
 <!--**Resolution:**
 Make the column dense by using [Convert to Dataset](convert-to-dataset.md) first or do not convert the column to categorical.  -->
  
|Uitzonderings berichten|  
|------------------------|  
|Sparse kolommen kunnen niet worden geconverteerd naar categorische.|  
  

## <a name="error-0075"></a>Fout 0075  
Uitzonde ring treedt op wanneer een ongeldige binning-functie wordt gebruikt wanneer een gegevensset wordt quantizing.  
  
Deze fout in Azure Machine Learning treedt op wanneer u gegevens op basis van een niet-ondersteunde methode probeert te bin of wanneer de parameter combinaties ongeldig zijn.  
  
**Opgelost**

Fout afhandeling voor deze gebeurtenis is geïntroduceerd in een eerdere versie van Azure Machine Learning die meer aanpassing van binning-methoden had toegestaan. Momenteel zijn alle binning-methoden gebaseerd op een selectie uit een vervolg keuzelijst, zodat deze fout niet meer kan worden weer geven.

 <!--If you get this error when using the [Group Data into Bins](group-data-into-bins.md) module, consider reporting the issue in the [Azure Machine Learning forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=MachineLearning), providing the data types, parameter settings, and the exact error message.  -->
  
|Uitzonderings berichten|  
|------------------------|  
|Er is een ongeldige binning-functie gebruikt.|  
  

## <a name="error-0077"></a>Fout 0077  
 Uitzonde ring treedt op wanneer er een onbekende schrijf modus voor BLOB-bestanden is door gegeven.  
  
 Deze fout in Azure Machine Learning treedt op als een ongeldig argument wordt door gegeven in de specificaties voor een doel of bron van een blob-bestand.  
  
**Oplossing:** In bijna alle modules die gegevens importeren of exporteren naar en van Azure Blob-opslag, worden parameter waarden die de schrijf modus beheren, toegewezen met behulp van een vervolg keuzelijst. Daarom is het niet mogelijk een ongeldige waarde door te geven en deze fout zou niet moeten worden weer gegeven. Deze fout wordt in een latere versie afgeschaft.  
  
|Uitzonderings berichten|  
|------------------------|  
|Niet-ondersteunde BLOB-schrijf modus.|  
|Niet-ondersteunde BLOB-schrijf modus: {0}.|  
  

## <a name="error-0078"></a>Fout 0078  
 Uitzonde ring treedt op wanneer de HTTP-optie voor [import gegevens](import-data.md) een 3xx-status code ontvangt die omleiding aangeeft.  
  
 Deze fout in Azure Machine Learning treedt op wanneer de HTTP-optie voor [import gegevens](import-data.md) een 3xx-status code (301, 302, 304, etc.) ontvangt die omleiding aangeeft. Deze fout wordt weer gegeven als u probeert verbinding te maken met een HTTP-bron waarmee de browser wordt omgeleid naar een andere pagina. Uit veiligheids overwegingen zijn het omleiden van websites niet toegestaan als gegevens bronnen voor Azure Machine Learning.  
  
**Oplossing:** Als de website een vertrouwde website is, voert u de omgeleide URL rechtstreeks in.  
  
|Uitzonderings berichten|  
|------------------------|  
|HTTP-omleiding is niet toegestaan|  
  

## <a name="error-0079"></a>Fout 0079  
 Uitzonde ring treedt op als de naam van de Azure storage-container onjuist is opgegeven.  
  
 Deze fout in Azure Machine Learning treedt op als de naam van de Azure storage-container onjuist is opgegeven. Deze fout wordt weer gegeven als u niet zowel de container als de BLOB (bestands naam) hebt opgegeven met **het pad naar de blob die begint met de container** optie bij het schrijven naar Azure Blob Storage.  
  
**Oplossing:** Ga naar de module [gegevens exporteren](export-data.md) en controleer of het opgegeven pad naar de BLOB zowel de container als de bestands naam bevat in de indeling **container/filename**.  
  
|Uitzonderings berichten|  
|------------------------|  
|De naam van de Azure storage-container is onjuist.|  
|De naam van de Azure storage-container "{0}" is onjuist; Er wordt een container naam van de indeling container/BLOB verwacht.|  
  

## <a name="error-0080"></a>Fout 0080  
 Uitzonde ring treedt op wanneer de kolom met alle ontbrekende waarden niet is toegestaan door de module.  
  
 Deze fout in Azure Machine Learning wordt gemaakt wanneer een of meer van de kolommen die door de module worden gebruikt, alle ontbrekende waarden bevat. Als een module bijvoorbeeld statistische statistieken voor elke kolom computing, kan deze niet worden gebruikt voor een kolom die geen gegevens bevat. In dergelijke gevallen wordt het uitvoeren van de module gestopt met deze uitzonde ring.  
  
**Oplossing:** Ga naar de invoer gegevensset en verwijder alle kolommen die alle ontbrekende waarden bevatten.  
  
|Uitzonderings berichten|  
|------------------------|  
|Kolommen met alle waarden ontbreken, zijn niet toegestaan.|  
|Voor de kolom {0} zijn alle waarden ontbreken.|  
  

## <a name="error-0081"></a>Fout 0081  
 Uitzonde ring treedt op in de PCA-module als het aantal dimensies dat moet worden verkleind, gelijk is aan het aantal functie kolommen in de invoer gegevensset, dat ten minste één sparse-functie kolom bevat.  
  
 Deze fout in Azure Machine Learning wordt gemaakt als aan de volgende voor waarden wordt voldaan: (a) de invoer gegevensset heeft ten minste één sparse kolom en (b) het uiteindelijke aantal aangevraagde dimensies is hetzelfde als het aantal invoer dimensies.  
  
**Oplossing:** Overweeg het aantal dimensies in de uitvoer te verminderen om minder te zijn dan het aantal dimensies in de invoer. Dit is gebruikelijk in toepassingen van PCA.   <!--For more information, see [Principal Component Analysis](principal-component-analysis.md).  -->
  
|Uitzonderings berichten|  
|------------------------|  
|Voor een gegevensset met sparse functie kolommen moet het aantal dimensies dat moet worden verminderd, kleiner zijn dan het aantal functie kolommen.|  
 

## <a name="error-0082"></a>Fout 0082  
 Uitzonde ring treedt op wanneer een model niet kan worden gedeserialiseerd.  
  
 Deze fout in Azure Machine Learning treedt op wanneer een opgeslagen machine learning model of trans formatie niet kan worden geladen met een nieuwere versie van de Azure Machine Learning-runtime als gevolg van een wijziging van het resultaat.  
  
**Oplossing:** De trainings pijplijn die het model of de trans formatie produceert, moet opnieuw worden uitgevoerd en het model of de trans formatie moet opnieuw worden opgeslagen.  
  
|Uitzonderings berichten|  
|------------------------|  
|Model kan niet worden gedeserialiseerd, omdat het waarschijnlijk met een oudere serialisatie-indeling is geserialiseerd. Train het model en sla het opnieuw op.|  
  

## <a name="error-0083"></a>Fout 0083  
 Uitzonde ring treedt op als de gegevensset die wordt gebruikt voor de training, niet kan worden gebruikt voor een concreet type leerder.  
  
 Deze fout in Azure Machine Learning wordt gemaakt wanneer de gegevensset niet compatibel is met de cursist die wordt getraind. De gegevensset kan bijvoorbeeld ten minste één ontbrekende waarde in elke rij bevatten. als gevolg hiervan wordt de hele gegevensset tijdens de training overgeslagen. In andere gevallen verwachten sommige machine learning-algoritmen, zoals afwijkings detectie, niet dat labels aanwezig zijn en kan deze uitzonde ring worden gegenereerd als labels aanwezig zijn in de gegevensset.  
  
**Oplossing:** Raadpleeg de documentatie van de kenniser die wordt gebruikt om de vereisten voor de invoer gegevensset te controleren. Bekijk de kolommen om te zien of alle vereiste kolommen aanwezig zijn.  
  
|Uitzonderings berichten|  
|------------------------|  
|De gegevensset die voor de training wordt gebruikt, is ongeldig.|  
|{0} bevat ongeldige gegevens voor training.|  
|{0} bevat ongeldige gegevens voor training. Type informatieer: {1}.|  
  

## <a name="error-0084"></a>Fout 0084  
 Uitzonde ring treedt op wanneer scores die zijn gegenereerd op basis van een R-script worden geëvalueerd. Dit wordt momenteel niet ondersteund.  
  
 Deze fout in Azure Machine Learning treedt op als u probeert een van de modules te gebruiken voor het evalueren van een model met uitvoer van een R-script dat scores bevat.  
  
**Opgelost**
  
|Uitzonderings berichten|  
|------------------------|  
|Beoordeling van scores die zijn geproduceerd met R wordt momenteel niet ondersteund.|  
  

## <a name="error-0085"></a>Fout 0085  
 Uitzonde ring treedt op wanneer de script evaluatie mislukt met een fout.  
  
 Deze fout in Azure Machine Learning treedt op wanneer u een aangepast script uitvoert dat syntaxis fouten bevat.  
  
**Oplossing:** Controleer uw code in een externe editor en controleer op fouten.  
  
|Uitzonderings berichten|  
|------------------------|  
|Er is een fout opgetreden tijdens de evaluatie van het script.|  
|De volgende fout is opgetreden tijdens het uitvoeren van een script, Bekijk het uitvoer logboek voor meer informatie:----------begin van fout bericht van {0} interpreter----------{1}----------einde van fout bericht van {0} interpreter----------|  
  

## <a name="error-0086"></a>Fout 0086  
 Uitzonde ring treedt op wanneer een telling van een trans formatie ongeldig is.  
  
 Deze fout in Azure Machine Learning treedt op wanneer u een trans formatie selecteert op basis van een count-tabel, maar de geselecteerde trans formatie niet compatibel is met de huidige gegevens of met de nieuwe tabel Count.  
  
**Oplossing:** De module biedt ondersteuning voor het opslaan van het aantal en de regels waaruit de trans formatie in twee verschillende notaties is gemaakt. Als u aantallen tabellen samenvoegt, moet u controleren of beide tabellen die u wilt samen voegen dezelfde indeling hebben.  
  
Over het algemeen kan een trans formatie op basis van een aantal alleen worden toegepast op gegevens sets die hetzelfde schema hebben als de gegevensset waarop de trans formatie oorspronkelijk is gemaakt.  
  
 <!-- For general information, see [Learning with Counts](data-transformation-learning-with-counts.md). For requirements specific to creating and merging count-based features, see these topics:  
  
-   [Merge Count Transform](merge-count-transform.md)  
  
-   [Import Count Table](import-count-table.md)  
  
-   [Modify Count Table Parameters](modify-count-table-parameters.md)  
  -->
|Uitzonderings berichten|  
|------------------------|  
|Ongeldige Count-Transform opgegeven.|  
|De Count-trans formatie op de invoer poort{0}is ongeldig.|  
|De Count-trans formatie op de invoer poort{0}kan niet worden samengevoegd met de Count-trans formatie op de invoer poort{1}. Controleer de meta gegevens die worden gebruikt voor het tellen van overeenkomsten.|  
  

## <a name="error-0087"></a>Fout 0087  
 Uitzonde ring treedt op wanneer een ongeldig aantal-tabel typen is opgegeven voor het leren met de Count-modules.  
  
 Deze fout in Azure Machine Learning treedt op wanneer u een bestaande Count-tabel probeert te importeren, maar de tabel is niet compatibel met de huidige gegevens of met de nieuwe tabel Count.  
  
**Oplossing:** Er zijn verschillende indelingen voor het opslaan van de aantallen en regels waaruit de trans formatie bestaat. Als u aantal tabellen samenvoegt, controleert u of beide dezelfde indeling gebruiken.  
  
 Over het algemeen kan een trans formatie op basis van een aantal alleen worden toegepast op gegevens sets die hetzelfde schema hebben als de gegevensset waarop de trans formatie oorspronkelijk is gemaakt.  
  
  <!--For general information, see [Learning with Counts](data-transformation-learning-with-counts.md). -->
  

## <a name="error-0088"></a>Fout 0088  
 Uitzonde ring treedt op wanneer een ongeldig teldatums type is opgegeven voor het leren van de Count-modules.  
  
 Deze fout in Azure Machine Learning treedt op wanneer u probeert een andere methode Count te gebruiken dan wordt ondersteund voor parametrisatie op basis van het aantal.  
  
**Oplossing:** In het algemeen worden de tellings methoden gekozen uit een vervolg keuzelijst, zodat u deze fout niet kunt zien.  
  
  <!--For general information, see [Learning with Counts](data-transformation-learning-with-counts.md). For requirements specific to creating and merging count-based features, see these topics:  
  
-   [Merge Count Transform](merge-count-transform.md)  
  
-   [Import Count Table](import-count-table.md)  
  
-   [Modify Count Table Parameters](modify-count-table-parameters.md)  
  -->
|Uitzonderings berichten|  
|------------------------|  
|Er is een ongeldig tellings type opgegeven.|  
|Het opgegeven Counting-type '{0}' is geen geldig type Count.|  
  

## <a name="error-0089"></a>Fout 0089  
 Uitzonde ring treedt op wanneer het opgegeven aantal klassen kleiner is dan het werkelijke aantal klassen in een gegevensset die wordt gebruikt voor het tellen.  
  
 Deze fout in Azure Machine Learning treedt op wanneer u een aantal tabel maakt en de kolom Label bevat een ander aantal klassen dan u hebt opgegeven in de module parameters.  
  
**Oplossing:** Controleer uw gegevensset en zoek in de kolom Label precies hoeveel verschillende waarden (mogelijke klassen) hebben. Wanneer u de Count-tabel maakt, moet u ten minste dit aantal klassen opgeven.  
  
 In de Count-tabel kan niet automatisch het aantal beschik bare klassen worden bepaald.  
  
 Wanneer u de tabel Count maakt, kunt u geen 0 of een getal opgeven dat kleiner is dan het werkelijke aantal klassen in de kolom Label.  
  
|Uitzonderings berichten|  
|------------------------|  
|Het aantal klassen is onjuist. Zorg ervoor dat het aantal klassen dat u opgeeft in het parameter deel venster groter is dan of gelijk is aan het aantal klassen in de kolom Label.|  
|Het opgegeven aantal klassen is{0}, maar dit is niet groter dan een label waarde{1}in de gegevensset die wordt gebruikt om te tellen. Zorg ervoor dat het aantal klassen dat u opgeeft in het parameter deel venster groter is dan of gelijk is aan het aantal klassen in de kolom Label.|  
  

## <a name="error-0090"></a>Fout 0090  
 Uitzonde ring treedt op wanneer het maken van de Hive-tabel is mislukt.  
  
 Deze fout in Azure Machine Learning treedt op wanneer u [gegevens exporteren](export-data.md) of een andere optie gebruikt om gegevens op te slaan in een HDInsight-cluster en de opgegeven Hive-tabel kan niet worden gemaakt.  
  
**Oplossing:** Controleer de naam van het Azure Storage-account dat aan het cluster is gekoppeld en controleer of u hetzelfde account in de module eigenschappen gebruikt.  
  
|Uitzonderings berichten|  
|------------------------|  
|De Hive-tabel kan niet worden gemaakt. Zorg ervoor dat voor een HDInsight-cluster de naam van het Azure Storage-account dat aan het cluster is gekoppeld, overeenkomt met wat wordt door gegeven via de module parameter.|  
|De Hive-tabel{0}kan niet worden gemaakt. Zorg ervoor dat voor een HDInsight-cluster de naam van het Azure Storage-account dat aan het cluster is gekoppeld, overeenkomt met wat wordt door gegeven via de module parameter.|  
|De Hive-tabel{0}kan niet worden gemaakt. Zorg ervoor dat voor een HDInsight-cluster de naam van het Azure Storage-account dat is gekoppeld aan het cluster '{1}' is.|  
 

## <a name="error-0100"></a>Fout 0100  
 Uitzonde ring treedt op wanneer er een niet-ondersteunde taal is opgegeven voor een aangepaste module.  
  
 Deze fout in Azure Machine Learning treedt op bij het maken van een aangepaste module en de eigenschap name van het **taal** element in een XML-definitie bestand van een aangepaste module heeft een ongeldige waarde. Op dit moment is de enige geldige waarde voor deze eigenschap `R`. Bijvoorbeeld:  
  
 `<Language name="R" sourceFile="CustomAddRows.R" entryPoint="CustomAddRows" />`  
  
**Oplossing:** Controleer of de eigenschap name van het **taal** element in het XML-definitie bestand van de aangepaste module is ingesteld op `R`. Sla het bestand op, werk het aangepaste module zip-pakket bij en probeer opnieuw de aangepaste module toe te voegen.  
  
|Uitzonderings berichten|  
|------------------------|  
|Niet-ondersteunde aangepaste module taal opgegeven|  
  

## <a name="error-0101"></a>Fout 0101  
 Alle poort-en parameter-Id's moeten uniek zijn.  
  
 Deze fout in Azure Machine Learning treedt op wanneer aan een of meer poorten of para meters dezelfde ID-waarde is toegewezen in een XML-definitie bestand van een aangepaste module.  
  
**Oplossing:** Controleer of de ID-waarden voor alle poorten en para meters uniek zijn. Sla het XML-bestand op, werk het aangepaste module zip-pakket bij en probeer opnieuw de aangepaste module toe te voegen.  
  
|Uitzonderings berichten|  
|------------------------|  
|Alle poort-en parameter-Id's voor een module moeten uniek zijn|  
|Module{0}heeft dubbele poort/argument-Id's. Alle poort/argument-Id's moeten uniek zijn voor een module.|  
  

## <a name="error-0102"></a>Fout 0102  
 Wordt gegenereerd wanneer een ZIP-bestand niet kan worden geëxtraheerd.  
  
 Deze fout in Azure Machine Learning treedt op wanneer u een gezipt pakket importeert met de extensie. zip, maar het pakket geen zip-bestand is, of als het bestand geen gebruik maakt van een ondersteunde zip-indeling.  
  
**Oplossing:** Zorg ervoor dat het geselecteerde bestand een geldig zip-bestand is en dat het is gecomprimeerd met een van de ondersteunde compressie algoritmen.  
  
 Als deze fout optreedt bij het importeren van gegevens sets in gecomprimeerde vorm, controleert u of alle opgenomen bestanden een van de ondersteunde bestands indelingen gebruiken en in Unicode-indeling zijn.  <!--For more information, see [Unpack Zipped Datasets](unpack-zipped-datasets.md).  -->
  
 Probeer de gewenste bestanden opnieuw toe te voegen aan een nieuwe gecomprimeerde map en probeer opnieuw de aangepaste module toe te voegen.  
  
|Uitzonderings berichten|  
|------------------------|  
|Het opgegeven ZIP-bestand heeft niet de juiste indeling|  


## <a name="error-0103"></a>Fout 0103  
 Wordt gegenereerd wanneer een ZIP-bestand geen. XML-bestanden bevat  
  
 Deze fout in Azure Machine Learning treedt op wanneer het zip-pakket van de aangepaste module geen module definitie bestanden (. XML) bevat. Deze bestanden moeten zich bevinden in de hoofdmap van het zip-pakket (bijvoorbeeld niet in een submap).  
  
**Oplossing:** Controleer of een of meer XML-module definitie bestanden zich in de hoofdmap van het zip-pakket bevinden door deze uit te pakken naar een tijdelijke map op uw schijf station. Alle XML-bestanden moeten zich direct in de map bevindt waarnaar u het zip-pakket hebt uitgepakt. Zorg ervoor dat u het zip-pakket maakt dat u geen map met XML-bestanden naar zip selecteert, omdat hiermee een submap in het zip-pakket wordt gemaakt met dezelfde naam als de map die u hebt geselecteerd om te worden gepost.  
  
|Uitzonderings berichten|  
|------------------------|  
|Het opgegeven ZIP-bestand bevat geen module definitie bestanden (XML-bestanden)|  


## <a name="error-0104"></a>Fout 0104  
 Wordt gegenereerd wanneer een module definitie bestand verwijst naar een script dat niet kan worden gevonden  
  
 Deze fout in Azure Machine Learning wordt gegenereerd wanneer een XML-definitie bestand van een aangepaste module verwijst naar een script bestand in het **taal** element dat niet voor komt in het zip-pakket. Het pad naar het script bestand wordt gedefinieerd in de eigenschap **sourceFile** van het **taal** element. Het pad naar het bron bestand is relatief ten opzichte van de hoofdmap van het zip-pakket (dezelfde locatie als de module XML-definitie bestanden). Als het script bestand zich in een submap bevindt, moet het relatieve pad naar het script bestand worden opgegeven. Als bijvoorbeeld alle scripts zijn opgeslagen in een **mijn** -map in het zip-pakket, zou het **taal** element dit pad moeten toevoegen aan de eigenschap **sourceFile** , zoals hieronder. Bijvoorbeeld:  
  
 `<Language name="R" sourceFile="myScripts/CustomAddRows.R" entryPoint="CustomAddRows" />`  
  
**Oplossing:** Zorg ervoor dat de waarde van de eigenschap **sourceFile** in het **taal** element van de XML-definitie van de aangepaste module juist is en dat het bron bestand zich in het juiste relatieve pad in het zip-pakket bevindt.  
  
|Uitzonderings berichten|  
|------------------------|  
|Het R-script bestand waarnaar wordt verwezen, bestaat niet.|  
|Het R-script bestand{0}waarnaar wordt verwezen, is niet gevonden. Zorg ervoor dat het relatieve pad naar het bestand juist is op de locatie van de definities.|  


## <a name="error-0105"></a>Fout 0105  
 Deze fout wordt weer gegeven wanneer een module definitie bestand een niet-ondersteund parameter type bevat  
  
 Deze fout in Azure Machine Learning wordt gemaakt wanneer u een XML-definitie voor een aangepaste module maakt en het type van een para meter of argument in de definitie komt niet overeen met een ondersteund type.  
  
**Oplossing:** Zorg ervoor dat de eigenschap type van een **ARG** -element in het XML-definitie bestand van de aangepaste module een ondersteund type is.  
  
|Uitzonderings berichten|  
|------------------------|  
|Niet-ondersteund parameter type.|  
|Niet-ondersteund parameter type '{0}' opgegeven.|  


## <a name="error-0106"></a>Fout 0106  
 Deze wordt gegenereerd wanneer een module definitie bestand een niet-ondersteund invoer type definieert  
  
 Deze fout in Azure Machine Learning wordt gemaakt wanneer het type van een invoer poort in een XML-definitie van een aangepaste module niet overeenkomt met een ondersteund type.  
  
**Oplossing:** Zorg ervoor dat de eigenschap type van een INPUT-element in het XML-definitie bestand van de aangepaste module een ondersteund type is.  
  
|Uitzonderings berichten|  
|------------------------|  
|Niet-ondersteund invoer type.|  
|Niet-ondersteund invoer type{0}opgegeven.|  


## <a name="error-0107"></a>Fout 0107  
 Deze wordt gegenereerd wanneer een module definitie bestand een niet-ondersteund uitvoer type definieert  
  
 Deze fout in Azure Machine Learning wordt gemaakt wanneer het type van een uitvoer poort in een XML-definitie van een aangepaste module niet overeenkomt met een ondersteund type.  
  
**Oplossing:** Zorg ervoor dat de eigenschap type van een uitvoer element in het XML-definitie bestand van de aangepaste module een ondersteund type is.  
  
|Uitzonderings berichten|  
|------------------------|  
|Niet-ondersteund uitvoer type.|  
|Niet-ondersteund uitvoer type{0}opgegeven.|  


## <a name="error-0108"></a>Fout 0108  
 Deze wordt gegenereerd wanneer een module definitie bestand meer invoer-of uitvoer poorten definieert dan wordt ondersteund  
  
 Deze fout in Azure Machine Learning wordt gemaakt wanneer er te veel invoer-of uitvoer poorten zijn gedefinieerd in een XML-definitie van een aangepaste module.  
  
**Oplossing:** Hiermee zorgt u ervoor dat het maximum aantal invoer-en uitvoer poorten dat is gedefinieerd in de XML-definitie van de aangepaste module, het maximum aantal ondersteunde poorten niet overschrijdt.  
  
|Uitzonderings berichten|  
|------------------------|  
|Het ondersteunde aantal invoer-of uitvoer poorten is overschreden.|  
|Het aantal ondersteunde poorten voor de{0}is overschreden. Het Maxi maal toegestane aantal{0}poorten is{1}.| 

## <a name="error-0109"></a>Fout 0109  
 Deze wordt gegenereerd wanneer een module definitie bestand een onjuiste kolom kiezer definieert  
  
 Deze fout in Azure Machine Learning wordt gemaakt wanneer de syntaxis voor een kolom kiezer argument een fout bevat in een XML-definitie van een aangepaste module.  
  
**Oplossing:** Deze fout wordt gegenereerd als de syntaxis voor een kolom kiezer argument een fout bevat in een XML-definitie van een aangepaste module.  
  
|Uitzonderings berichten|  
|------------------------|  
|Niet-ondersteunde syntaxis voor de kolom kiezer.|  
  

## <a name="error-0110"></a>Fout 0110  
 Deze wordt gegenereerd wanneer een module definitie bestand een kolom kiezer definieert die verwijst naar een niet-bestaande invoer poort-ID  
  
 Deze fout in Azure Machine Learning wordt gemaakt wanneer de eigenschap *portId* binnen het element Properties van een ARG van het type ColumnPicker niet overeenkomt met de id-waarde van een invoer poort.  
  
**Oplossing:** Zorg ervoor dat de eigenschap portId overeenkomt met de ID-waarde van een invoer poort die is gedefinieerd in de XML-definitie van de aangepaste module.  
  
|Uitzonderings berichten|  
|------------------------|  
|Kolom kiezer verwijst naar een niet-bestaande invoer poort-ID.|  
|Kolom kiezer verwijst naar een niet-bestaande invoer poort-ID{0}.|  
  

## <a name="error-0111"></a>Fout 0111  
 Dit wordt veroorzaakt wanneer een module definitie bestand een ongeldige eigenschap definieert  
  
 Deze fout in Azure Machine Learning wordt gemaakt wanneer een ongeldige eigenschap wordt toegewezen aan een-element in de XML-definitie van de aangepaste module.  
  
**Oplossing:** Zorg ervoor dat de eigenschap wordt ondersteund door het aangepaste module-element.  
  
|Uitzonderings berichten|  
|------------------------|  
|Eigenschaps definitie is ongeldig.|  
|De definitie van de eigenschap{0}is ongeldig.|  
  

## <a name="error-0112"></a>Fout 0112  
 Wordt gegenereerd wanneer een module definitie bestand niet kan worden geparseerd  
  
 Deze fout in Azure Machine Learning wordt gemaakt als er een fout is opgetreden in de XML-indeling waardoor de XML-definitie van de aangepaste module niet kan worden geparseerd als een geldig XML-bestand.  
  
**Oplossing:** Zorg ervoor dat elk element wordt geopend en correct is gesloten. Zorg ervoor dat er geen fouten zijn in de XML-indeling.  
  
|Uitzonderings berichten|  
|------------------------|  
|Het definitie bestand van de module kan niet worden geparseerd.|  
|Kan het definitie bestand voor de module{0}niet parseren.|  
  

## <a name="error-0113"></a>Fout 0113  
 Wordt gegenereerd wanneer een module definitie bestand fouten bevat.  
  
 Deze fout in Azure Machine Learning wordt gemaakt wanneer het XML-definitie bestand van de aangepaste module kan worden geparseerd, maar bevat fouten, zoals de definitie van elementen die niet worden ondersteund door aangepaste modules.  
  
**Oplossing:** Zorg ervoor dat in het bestand met de aangepaste module definitie elementen en eigenschappen worden gedefinieerd die door aangepaste modules worden ondersteund.  
  
|Uitzonderings berichten|  
|------------------------|  
|Het definitie bestand van de module bevat fouten.|  
|Het definitie bestand van de module{0}bevat fouten.|  
|Het definitie bestand van de module{0}bevat fouten. {1}|  
  

## <a name="error-0114"></a>Fout 0114  
 Het maken van een aangepaste module is mislukt.  
  
 Deze fout in Azure Machine Learning wordt gemaakt wanneer het maken van een aangepaste module mislukt. Dit doet zich voor wanneer er een of meer aangepaste fouten met betrekking tot de module worden aangetroffen tijdens het toevoegen van de aangepaste module. De extra fouten worden in dit fout bericht gerapporteerd.  
  
**Oplossing:** Los de fouten op die in dit uitzonderings bericht worden gemeld.  
  
|Uitzonderings berichten|  
|------------------------|  
|Kan de aangepaste module niet bouwen.|  
|Het opbouwen van aangepaste modules is mislukt met fout (en): {0}|  
  

## <a name="error-0115"></a>Fout 0115  
 Deze fout treedt op wanneer een standaard script van een aangepaste module een niet-ondersteunde extensie heeft.  
  
 Deze fout in Azure Machine Learning treedt op wanneer u een script voor een aangepaste module opgeeft die gebruikmaakt van een onbekende bestandsnaam extensie.  
  
**Oplossing:** Controleer de bestands indeling en de bestandsnaam extensie van alle script bestanden die in de aangepaste module zijn opgenomen.  
  
|Uitzonderings berichten|  
|------------------------|  
|Niet-ondersteunde uitbrei ding voor het standaard script.|  
|Niet-ondersteunde bestands extensie {0} voor het standaard script.|  
  

## <a name="error-0121"></a>Fout 0121  
 Wordt gegenereerd wanneer SQL-schrijf bewerkingen mislukken omdat de tabel niet schrijfbaar is  
  
 Deze fout in Azure Machine Learning wordt gemaakt wanneer u de module [gegevens exporteren](export-data.md) gebruikt om resultaten op te slaan in een tabel in een SQL database en er kan niet naar de tabel worden geschreven. Normaal gesp roken ziet u deze fout als er met de module [export Data](export-data.md) een verbinding met het SQL Server exemplaar is gemaakt, maar de inhoud van de Azure ml-gegevensset kan niet naar de tabel worden geschreven.  
  
**Opgelost**
 - Open het deel venster Eigenschappen van de module [gegevens exporteren](export-data.md) en controleer of de data base en tabel namen correct zijn ingevoerd. 
 - Controleer het schema van de gegevensset die u wilt exporteren en zorg ervoor dat de gegevens compatibel zijn met de doel tabel.
 - Controleer of de SQL-aanmelding bij de gebruikers naam en het wacht woord is gemachtigd om naar de tabel te schrijven. 
 - Als de uitzonde ring aanvullende fout informatie bevat van SQL Server, gebruikt u die informatie om correcties aan te brengen.  
  
|Uitzonderings berichten|  
|------------------------|  
|Verbonden met de server, kan niet naar de tabel schrijven.|  
|Kan niet schrijven naar SQL-tabel: {0}|  


## <a name="error-0122"></a>Fout 0122  
 Uitzonde ring treedt op als er meerdere gewichts kolommen zijn opgegeven en er slechts één is toegestaan.  
  
 Deze fout in Azure Machine Learning treedt op wanneer er te veel kolommen zijn geselecteerd als gewichts kolommen.  
  
**Oplossing:** Controleer de invoer gegevensset en de bijbehorende meta gegevens. Zorg ervoor dat slechts één kolom gewichten bevat.  
  
|Uitzonderings berichten|  
|------------------------|  
|Er zijn meerdere gewichts kolommen opgegeven.|  


## <a name="error-0123"></a>Fout 0123  
 Uitzonde ring treedt op als kolom met vectoren is opgegeven voor de label kolom.  
  
 Deze fout in Azure Machine Learning treedt op als u een vector als de label kolom gebruikt.  
  
**Oplossing:** Wijzig indien nodig de gegevens indeling van de kolom of kies een andere kolom.  
  
|Uitzonderings berichten|  
|------------------------|  
|Kolom met vectoren is opgegeven als label kolom.|  


## <a name="error-0124"></a>Fout 0124  
 Uitzonde ring treedt op als niet-numerieke kolommen zijn opgegeven als de kolom Weight.  
  
**Opgelost**
  
|Uitzonderings berichten|  
|------------------------|  
|Een niet-numerieke kolom is opgegeven als de kolom Weight.|  
  


## <a name="error-0125"></a>Fout 0125  
 Wordt gegenereerd wanneer het schema voor meerdere gegevens sets niet overeenkomt.  
  
**Opgelost**
  
|Uitzonderings berichten|  
|------------------------|  
|Het gegevensset-schema komt niet overeen.|  


## <a name="error-0126"></a>Fout 0126  
 Uitzonde ring treedt op als de gebruiker een SQL-domein opgeeft dat niet wordt ondersteund in azure ML.  
  
 Deze fout wordt gegenereerd wanneer de gebruiker een SQL-domein opgeeft dat niet wordt ondersteund in Azure Machine Learning. Deze fout wordt weer gegeven als u probeert verbinding te maken met een database server in een domein dat niet white list is. De toegestane SQL-domeinen zijn momenteel: '. database.windows.net ', '. cloudapp.net ' of '. database.secure.windows.net '. Dat wil zeggen dat de server een Azure SQL-Server of een server in een virtuele machine in azure moet zijn.  
  
**Oplossing:** Ga naar de module. Controleer of de SQL database server deel uitmaakt van een van de geaccepteerde domeinen:  
  
-   . database.windows.net  
  
-   . cloudapp.net  
  
-   . database.secure.windows.net  
  
|Uitzonderings berichten|  
|------------------------|  
|Niet-ondersteund SQL-domein.|  
|De {0} van het SQL-domein wordt momenteel niet ondersteund in azure ML|  
  

## <a name="error-0127"></a>Fout 0127  
 De pixel grootte van de afbeelding overschrijdt de toegestane limiet  
  
 Deze fout treedt op als u afbeeldingen uit een afbeeldings gegevensset voor classificatie leest en de installatie kopieën groter zijn dan het model kan verwerken.  
  
 <!--**Resolution:**
 For more information about the image size and other requirements, see these topics:  
  
-   [Import Images](import-images.md)  
  
-   [Pretrained Cascade Image Classification](pretrained-cascade-image-classification.md)  -->
  
|Uitzonderings berichten|  
|------------------------|  
|De pixel grootte van de afbeelding overschrijdt de toegestane limiet.|  
|De pixel grootte van de afbeelding in het bestand{0}overschrijdt de toegestane limiet:{1}|  


## <a name="error-0128"></a>Fout 0128  
 Het aantal voorwaardelijke kansen voor categorische kolommen overschrijdt de limiet.  
  
**Opgelost**
  
|Uitzonderings berichten|  
|------------------------|  
|Het aantal voorwaardelijke kansen voor categorische kolommen overschrijdt de limiet.|  
|Het aantal voorwaardelijke kansen voor categorische kolommen overschrijdt de limiet. De kolommen{0}en{1}zijn het problematische paar.|  


## <a name="error-0129"></a>Fout 0129  
 Het aantal kolommen in de gegevensset overschrijdt de toegestane limiet.  
  
**Opgelost**
  
|Uitzonderings berichten|  
|------------------------|  
|Het aantal kolommen in de gegevensset overschrijdt de toegestane limiet.|  
|Het aantal kolommen in de gegevensset in{0}overschrijdt toegestaan.|  
|Het aantal kolommen in de gegevensset in{0}overschrijdt de toegestane limiet van{1}.|  
|Het aantal kolommen in de gegevensset in{0}overschrijdt de toegestane{1}limiet van{2}.|  
## <a name="error-0130"></a>Fout 0130  
 Uitzonde ring treedt op wanneer alle rijen in de training-gegevensset ontbrekende waarden bevatten.  
  
 Dit probleem treedt op wanneer een deel van de kolom in de training-gegevensset leeg is.  
  
**Oplossing:** Gebruik de module [clean Missing Data](clean-missing-data.md) om kolommen met alle ontbrekende waarden te verwijderen.  
  
|Uitzonderings berichten|  
|------------------------|  
|Alle rijen in de training-gegevensset bevatten ontbrekende waarden.  Overweeg het gebruik van de module clean Missing Data om ontbrekende waarden te verwijderen.|  
 

## <a name="error-0131"></a>Fout 0131  
 Uitzonde ring treedt op als een of meer gegevens sets in een zip-bestand niet naar behoren kunnen worden uitgepakt en geregistreerd  
  
 Deze fout treedt op wanneer een of meer gegevens sets in een zip-bestand niet kunnen worden uitgepakt en correct kunnen worden gelezen. U ontvangt deze fout melding als het uitpakken mislukt omdat het zip-bestand zelf of een van de bestanden erin beschadigd is of omdat er een systeem fout is opgetreden bij het uitpakken en uitbreiden van een bestand.  
  
**Oplossing:** Gebruik de details in het fout bericht om te bepalen hoe u kunt door gaan.  
  
|Uitzonderings berichten|  
|------------------------|  
|Kan niet laden gezipte gegevens sets|  
|De gezipte gegevensset {0} is mislukt met het volgende bericht: {1}|  
|De gezipte gegevensset {0} is mislukt met een {1}-uitzonde ring met het bericht: {2}|  
  

## <a name="error-0132"></a>Fout 0132  
 Er is geen bestands naam opgegeven voor het uitpakken. Er zijn meerdere bestanden gevonden in het zip-bestand.  
  
 Deze fout wordt gegenereerd als er geen bestands naam is opgegeven voor het uitpakken. Er zijn meerdere bestanden gevonden in het zip-bestand. U ontvangt deze fout melding als het zip-bestand meer dan een gecomprimeerd bestand bevat, maar u geen bestand hebt opgegeven voor extractie in het tekstvak gegevensset voor het **uitpakken** van het tekstvak, in het **Eigenschappen** venster van de module. Op dit moment kan slechts één bestand worden geëxtraheerd telkens wanneer de module wordt uitgevoerd.  
  
**Oplossing:** Het fout bericht bevat een lijst met de bestanden in het zip-bestand. Kopieer de naam van het gewenste bestand en plak het in de **gegevensset om** het tekstvak uit te pakken.  
  
|Uitzonderings berichten|  
|------------------------|  
|Zip-bestand bevat meerdere bestanden. u moet het bestand opgeven dat moet worden uitgebreid.|  
|Het bestand bevat meer dan een bestand. Geef het bestand op dat moet worden uitgebreid. De volgende bestanden zijn gevonden: {0}|  
  

## <a name="error-0133"></a>Fout 0133  
 Het opgegeven bestand is niet gevonden in het zip-bestand  
  
 Deze fout wordt gegenereerd wanneer de bestands naam in het veld **gegevensset voor het uitpakken** van het **Eigenschappen** venster niet overeenkomt met de naam van een bestand dat in het zip-bestand is gevonden. De meest voorkomende oorzaken van deze fout zijn een type fout of zoeken in het verkeerde archief bestand om het bestand uit te breiden.  
  
**Oplossing:** Ga naar de module. Als de naam van het bestand dat u wilt decomprimeren wordt weer gegeven in de lijst met gevonden bestanden, kopieert u de bestands naam en plakt u deze in het vak **gegevensset voor het uitpakken** van de eigenschap. Als u de gewenste bestands naam niet in de lijst ziet, controleert u of u het juiste zip-bestand en de juiste naam voor het gewenste bestand hebt.  
  
|Uitzonderings berichten|  
|------------------------|  
|Het opgegeven bestand is niet gevonden int het zip-bestand.|  
|Het opgegeven bestand is niet gevonden. De volgende bestanden zijn gevonden: {0}|  
  

## <a name="error-0134"></a>Fout 0134
Uitzonde ring treedt op als de label kolom ontbreekt of als er onvoldoende rijen met labels zijn.  
  
Deze fout treedt op wanneer voor de module een label kolom is vereist, maar er geen waarde in de kolom selectie is opgegeven, of als de kolom label te veel waarden bevat.

Deze fout kan ook optreden wanneer een eerdere bewerking de gegevensset wijzigt, zodat er onvoldoende rijen beschikbaar zijn voor een stroomafwaartse bewerking. Stel dat u een expressie in de **partitie-en voorbeeld** module gebruikt om een gegevensset te verdelen op basis van waarden. Als er geen overeenkomsten worden gevonden voor uw expressie, zou een van de gegevens sets die het resultaat zijn van de partitie, leeg zijn.

Oplossing: 

 Als u een kolom Label in de kolom selectie opneemt, maar deze niet wordt herkend, gebruikt u de module [meta gegevens bewerken](edit-metadata.md) om deze te markeren als een label kolom.
  
  <!--Use the [Summarize Data](summarize-data.md) module to generate a report that shows how many values are missing in each column. -->Vervolgens kunt u de module [clean Missing Data](clean-missing-data.md) gebruiken om rijen te verwijderen met ontbrekende waarden in de kolom Label. 

 Controleer de invoer gegevens sets om er zeker van te zijn dat ze geldige gegevens bevatten, en voldoende rijen om te voldoen aan de vereisten van de bewerking. Bij veel algoritmen wordt een fout bericht gegenereerd als er een minimum aantal rijen met gegevens nodig is, maar de gegevens slechts een paar rijen of alleen een koptekst bevatten.
  
|Uitzonderings berichten|
|------------------------|
|Uitzonde ring treedt op als de label kolom ontbreekt of als er onvoldoende rijen met labels zijn.|  
|Uitzonde ring treedt op wanneer de label kolom ontbreekt of korter is dan {0} gelabelde rijen|  
  

## <a name="error-0135"></a>Fout 0135  
 Alleen op massa middelpunt gebaseerd cluster wordt ondersteund.  
  
**Oplossing:** Dit fout bericht kan optreden als u probeert een cluster model te evalueren dat is gebaseerd op een aangepast cluster algoritme dat geen gebruik maakt van centroids om het cluster te initialiseren.  
  
  <!--You can use [Evaluate Model](evaluate-model.md) to evaluate clustering models that are based on the  [K-Means Clustering](k-means-clustering.md) module. For custom algorithms, use the [Execute R Script](execute-r-script.md) module to create a custom evaluation script.  -->
  
|Uitzonderings berichten|  
|------------------------|  
|Alleen op massa middelpunt gebaseerd cluster wordt ondersteund.|  
  

## <a name="error-0136"></a>Fout 0136  
 Er is geen bestands naam geretourneerd. kan het bestand niet verwerken als resultaat.  
  
**Opgelost**
  
|Uitzonderings berichten|  
|------------------------|  
|Er is geen bestands naam geretourneerd. kan het bestand niet verwerken als resultaat.|  
  

## <a name="error-0137"></a>Fout 0137  
 Er is een fout opgetreden tijdens het converteren van de tabel eigenschappen en gegevensset tijdens het lezen of schrijven van de Azure Storage SDK.  
  
**Opgelost**
  
|Uitzonderings berichten|  
|------------------------|  
|Conversie fout tussen Azure Table Storage-eigenschap en gegevensset-kolom.|  
|Conversie fout tussen Azure Table Storage-eigenschap en gegevensset-kolom. Aanvullende informatie: {0}|  

## <a name="error-0138"></a>Fout 0138  
 Het geheugen is uitgeput, kan de uitvoering van de module niet volt ooien. Het downsamplen van de gegevensset kan helpen om het probleem op te lossen.  
  
 Deze fout treedt op wanneer voor de module die wordt uitgevoerd meer geheugen nodig is dan beschikbaar is in de Azure-container. Dit kan gebeuren als u met een grote gegevensset werkt en de huidige bewerking niet in het geheugen past.  
  
**Oplossing:** Als u een grote gegevensset probeert te lezen en de bewerking niet kan worden voltooid, kan het downsamplen van de gegevensset helpen.  
  
  <!--If you use the visualizations on datasets to check the cardinality of columns, only some rows are sampled. To get a full report, use [Summarize Data](summarize-data.md). You can also use the [Apply SQL Transformation](apply-sql-transformation.md) to check for the number of unique values in each column.  
  
 Sometimes transient loads can lead to this error. Machine support also changes over time. 
  
 Try using [Principal Component Analysis](principal-component-analysis.md) or one of the provided feature selection methods to reduce your dataset to a smaller set of more feature-rich columns: [Feature Selection](feature-selection-modules.md)  -->
  
|Uitzonderings berichten|  
|------------------------|  
|Het geheugen is uitgeput, kan de uitvoering van de module niet volt ooien.|  
  

## <a name="error-0139"></a>Fout 0139  
 Uitzonde ring treedt op wanneer het niet mogelijk is om een kolom naar een ander type te converteren.  
  
 Deze fout in Azure Machine Learning treedt op wanneer u een kolom probeert te converteren naar een ander gegevens type, maar dat type wordt niet ondersteund door de huidige bewerking of door de module.  
  
 De fout kan ook worden weer gegeven wanneer een module probeert gegevens impliciet te converteren om te voldoen aan de vereisten van de huidige module, maar de conversie is niet mogelijk.  
  
**Opgelost**

1. Controleer de ingevoerde gegevens en bepaal het exacte gegevens type van de kolom die u wilt gebruiken en het gegevens type van de kolom die de fout produceert. Soms denkt u dat het gegevens type juist is, maar dat een upstream-bewerking het gegevens type of het gebruik van een kolom heeft gewijzigd. Gebruik de module [meta gegevens bewerken](edit-metadata.md) om de oorspronkelijke status van de meta gegevens van de kolom te herstellen. 
2. Bekijk de Help-pagina van de module om de vereisten voor de opgegeven bewerking te controleren. Bepaal welke gegevens typen worden ondersteund door de huidige module en welk bereik van waarden wordt ondersteund. 
 <!--3. If values need to be truncated, rounded, or outliers removed, use the [Apply Math Operation](apply-math-operation.md) or [Clip Values](clip-values.md) modules to make corrections.-->
4. Bepaal of de kolom kan worden geconverteerd of geconverteerd naar een ander gegevens type. De volgende modules bieden allemaal veel flexibiliteit en kracht voor het wijzigen van gegevens: 
 <!--
   + [Apply SQL Transformation](apply-sql-transformation.md)
   + [Execute R Script](execute-r-script.md)
-->   
   + [Python-script uitvoeren](execute-python-script.md).  

> [!NOTE]
> Werkt nog steeds niet? Overweeg extra feedback te geven over het probleem, om ons te helpen bij het ontwikkelen van betere richt lijnen voor probleem oplossing. Verzend feedback op deze pagina en geef de naam op van de module die de fout heeft gegenereerd en de gegevens type conversie die is mislukt.
  
|Uitzonderings berichten|  
|------------------------|  
|Conversie niet toegestaan.|  
|Kan niet converteren: {0}.|  
|Kan niet converteren: {0}, op rij {1}.|  
|Kan kolom van het type {0} niet converteren naar een kolom van het type {1} op rij {2}.|  
|Kan kolom{2}van het type {0} niet converteren naar een kolom van het type {1} op rij {3}.|  
|Kan kolom '{2}' van het type {0} niet converteren naar kolom{3}van het type {1} op rij-{4}.| 

## <a name="error-0140"></a>Fout 0140  
 Uitzonde ring treedt op als het door gegeven argument van de kolom set geen andere kolommen bevat behalve een label kolom.  
  
 Deze fout treedt op als u een gegevensset hebt verbonden met een module waarvoor meerdere kolommen zijn vereist, met inbegrip van functies, maar u hebt alleen de label kolom.  
  
**Oplossing:** Kies ten minste één functie kolom die u wilt toevoegen aan de gegevensset.  
  
|Uitzonderings berichten|  
|------------------------|  
|Opgegeven kolomset bevat geen andere kolommen behalve een label kolom.|  
  

## <a name="error-0141"></a>Fout 0141  
 Uitzonde ring treedt op als het aantal geselecteerde numerieke kolommen en unieke waarden in de categorische-en reeks kolommen te klein is.  
  
 Deze fout in Azure Machine Learning treedt op wanneer er onvoldoende unieke waarden in de geselecteerde kolom zijn om de bewerking uit te voeren.  
  
**Oplossing:** Sommige bewerkingen voeren statistische bewerkingen uit op functie-en categorische-kolommen. als er onvoldoende waarden zijn, kan de bewerking mislukken of kan een ongeldig resultaat worden geretourneerd. Controleer uw gegevensset om te zien hoeveel waarden er in de kolommen functie en label staan, en bepaal of de bewerking die u probeert uit te voeren, statistisch geldig is.  
  
 Als de bron-gegevensset geldig is, kunt u ook controleren of een bewerking voor het door lopen van gegevens of de meta gegevens van de gegevens is gewijzigd en dat er waarden zijn verwijderd.  
  
 Als de upstream-bewerkingen splitsen, steek proeven of resampling omvatten, controleert u of de uitvoer het verwachte aantal rijen en waarden bevat.  
  
|Uitzonderings berichten|  
|------------------------|  
|Het aantal geselecteerde numerieke kolommen en unieke waarden in de categorische-en reeks kolommen is te klein.|  
|Het totale aantal geselecteerde numerieke kolommen en unieke waarden in de categorische-en teken reeks kolommen (momenteel {0}) moet ten minste {1}|  
  

## <a name="error-0142"></a>Fout 0142  
 Uitzonde ring treedt op wanneer het systeem het certificaat niet kan laden voor authenticatie.  
  
**Opgelost**
  
|Uitzonderings berichten|  
|------------------------|  
|Het certificaat kan niet worden geladen.|  
|De certificaat {0} kan niet worden geladen. De vinger afdruk is {1}.|  
  

## <a name="error-0143"></a>Fout 0143  
 Kan de door de gebruiker ingevoerde URL die van GitHub moet zijn, niet parseren.  
  
 Deze fout in Azure Machine Learning treedt op wanneer u een ongeldige URL opgeeft en de module een geldige GitHub-URL vereist.  
  
**Oplossing:** Controleer of de URL verwijst naar een geldige GitHub-opslag plaats. Andere site typen worden niet ondersteund.  
  
|Uitzonderings berichten|  
|------------------------|  
|De URL is niet van github.com.|  
|De URL is niet github.com: {0}|  

## <a name="error-0144"></a>Fout 0144  
 Het verwachte deel van de door de gebruiker ingevoerde GitHub-URL ontbreekt.  
  
 Deze fout in Azure Machine Learning treedt op wanneer u een GitHub-bestands bron opgeeft met een ongeldige URL-indeling.  
  
**Oplossing:** Controleer of de URL van de GitHub-opslag plaats geldig is en eindigt op \blob\ of \tree\\.  
  
|Uitzonderings berichten|  
|------------------------|  
|Kan de GitHub-URL niet parseren.|  
|Kan de GitHub-URL niet parseren (\blob\\' of ' \tree\\' wordt verwacht na de naam van de opslag plaats): {0}|  

## <a name="error-0145"></a>Fout 0145  
 Kan geen replicatie Directory maken om een of andere reden.  
  
 Deze fout in Azure Machine Learning treedt op wanneer de module de opgegeven map niet kan maken.  
  
**Opgelost**
  
|Uitzonderings berichten|  
|------------------------|  
|Kan de gerepliceerde map niet maken.|  
  

## <a name="error-0146"></a>Fout 0146  
 Wanneer de gebruikers bestanden in de lokale map worden uitgepakt, is het mogelijk dat het gecombineerde pad te lang is.  
  
 Deze fout in Azure Machine Learning treedt op wanneer u bestanden uitpakt, maar sommige bestands namen zijn te lang.  
  
**Oplossing:** Bewerk de bestands namen zodanig dat gecombineerde paden en bestands namen niet langer zijn dan 248 tekens.  
  
|Uitzonderings berichten|  
|------------------------|  
|Het replicatie-pad is langer dan 248 tekens, verklein de script naam of het pad.|  

## <a name="error-0147"></a>Fout 0147  
 Kan om een of andere reden geen spullen downloaden van GitHub  
  
 Deze fout in Azure Machine Learning treedt op wanneer u de opgegeven bestanden niet kunt lezen of downloaden vanuit GitHub.  
  
**Oplossing:** Het probleem kan tijdelijk zijn. u kunt proberen om de bestanden op een ander tijdstip te openen. Of Controleer of u over de benodigde machtigingen beschikt en of de bron geldig is.  
  
|Uitzonderings berichten|  
|------------------------|  
|GitHub-toegangs fout.|  
|GitHub-toegangs fout. {0}|  
  

## <a name="error-0148"></a>Fout 0148  
 Problemen met onbevoegde toegang tijdens het extra heren van gegevens of het maken van een map.  
  
 Deze fout in Azure Machine Learning treedt op wanneer u probeert een map te maken of gegevens uit de opslag te lezen, maar niet over de benodigde machtigingen beschikt.  
  
**Opgelost**
  
|Uitzonderings berichten|  
|------------------------|  
|Uitzonde ring voor onbevoegde toegang tijdens het extra heren van gegevens.|  
  

## <a name="error-0149"></a>Fout 0149  
 Het gebruikers bestand bevindt zich niet in de GitHub-bundel.  
  
 Deze fout in Azure Machine Learning treedt op wanneer het opgegeven bestand niet kan worden gevonden.  
  
Oplossing: 
  
|Uitzonderings berichten|  
|------------------------|  
|Het GitHub-bestand is niet gevonden.|  
|Het GitHub-bestand is niet gevonden.: {0}|  
  

## <a name="error-0150"></a>Fout 0150  
 De scripts die afkomstig zijn van het gebruikers pakket, kunnen niet worden uitgepakt, waarschijnlijk vanwege een conflict met GitHub-bestanden.  
  
 Deze fout in Azure Machine Learning treedt op wanneer een script niet kan worden geëxtraheerd, meestal wanneer er een bestaand bestand met dezelfde naam bestaat.  
  
Oplossing:
  
|Uitzonderings berichten|  
|------------------------|  
|Kan de bundel niet uitpakken; mogelijke naam conflicten met GitHub-bestanden.|  
  

## <a name="error-0151"></a>Fout 0151  
 Er is een fout opgetreden bij het schrijven naar de Cloud opslag. Controleer de URL.  
  
 Deze fout in Azure Machine Learning treedt op wanneer de module gegevens naar de Cloud opslag probeert te schrijven, maar de URL niet beschikbaar is of ongeldig is.  
  
Oplossing: Controleer de URL en controleer of deze schrijfbaar is.  
  
|Uitzonderings berichten|  
|------------------------|  
|Fout bij het schrijven naar de Cloud opslag (mogelijk een ongeldige URL).|  
|Fout bij het schrijven naar de Cloud opslag: {0}. Controleer de URL.|  
  
## <a name="error-0152"></a>Fout 0152  
 Het type van de Azure-Cloud is onjuist opgegeven in de module context.  
  
|Uitzonderings berichten|  
|------------------------|  
|Ongeldig Azure-Cloud type|  
|Ongeldig Azure-Cloud type: {0}|  
  
## <a name="error-0153"></a>Fout 0153  
 Het opgegeven opslag eindpunt is ongeldig.  
  
|Uitzonderings berichten|  
|------------------------|  
|Ongeldig Azure-Cloud type|  
|Ongeldig eind punt voor de opslag: {0}|  

## <a name="error-0154"></a>Fout 0154  
 De opgegeven server naam kan niet worden omgezet  
  
|Uitzonderings berichten|  
|------------------------|  
|De opgegeven server naam kan niet worden omgezet|  
|De opgegeven server {0}. documents.azure.com kan niet worden omgezet|

## <a name="error-0155"></a>Fout 0155  
 De DocDb-client heeft een uitzonde ring gegenereerd  
  
|Uitzonderings berichten|  
|------------------------|  
|De DocDb-client heeft een uitzonde ring gegenereerd|  
|DocDb-client: {0}|

## <a name="error-0156"></a>Fout 0156  
 Ongeldig antwoord voor HCatalog-server.  
  
|Uitzonderings berichten|  
|------------------------|  
|Ongeldig antwoord voor HCatalog-server. Controleer of alle services worden uitgevoerd.|  
|Ongeldig antwoord voor HCatalog-server. Controleer of alle services worden uitgevoerd. Foutdetails: {0}|

## <a name="error-0157"></a>Fout 0157  
 Er is een fout opgetreden tijdens het lezen van Azure Cosmos DB vanwege inconsistente of verschillende document schema's. Reader vereist dat alle documenten hetzelfde schema hebben.  
  
|Uitzonderings berichten|  
|------------------------|  
|Er zijn documenten gevonden met verschillende schema's. Zorg ervoor dat alle documenten hetzelfde schema hebben|

## <a name="error-1000"></a>Fout 1000  
Uitzonde ring interne bibliotheek.  
  
Deze fout wordt gegeven voor het vastleggen van anderszins onverwerkte interne Engine fouten. Daarom kan de oorzaak van deze fout afwijken, afhankelijk van de module die de fout heeft gegenereerd.  
  
Als u meer hulp nodig hebt, raden we u aan het gedetailleerde bericht te plaatsen dat bij de fout aan het Azure Machine Learning forum wordt geleverd, samen met een beschrijving van het scenario, inclusief de gegevens die worden gebruikt als invoer. Deze feedback helpt ons bij het bepalen van de prioriteit van fouten en het identificeren van de belangrijkste problemen voor verdere werkzaamheden.  
  
|Uitzonderings berichten|  
|------------------------|  
|Bibliotheek uitzondering.|  
|Uitzonde ring van bibliotheek: {0}|  
|uitzonde ring bij {0}-bibliotheek: {1}|  
