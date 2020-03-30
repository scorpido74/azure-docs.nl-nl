---
title: Modulefouten oplossen
titleSuffix: Azure Machine Learning
description: Moduleuitzonderingen oplossen in Azure Machine Learning-ontwerper met foutcodes
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 12/03/2019
ms.openlocfilehash: cda499b81a61a5b78ca86a96372640e368f90357
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80364188"
---
# <a name="exceptions-and-error-codes-for-the-designer-preview"></a>Uitzonderingen en foutcodes voor de ontwerper (voorbeeld)

In dit artikel worden de foutberichten en uitzonderingscodes in Azure Machine Learning designer (preview) beschreven om u te helpen bij het oplossen van uw machine learning-pijplijnen.

Er zijn twee manieren om de volledige tekst van een foutbericht in de ontwerper te krijgen:  

- Klik op de koppeling, **uitvoerlogboek weergeven**in het rechterdeelvenster en schuif naar de onderkant. Het gedetailleerde foutbericht wordt weergegeven in de laatste twee regels van het venster.  
  
- Selecteer de module met de fout en klik op de rode X. Alleen de relevante fouttekst wordt weergegeven.

## <a name="error-0001"></a>Fout 0001  
 Er treedt een uitzondering op als een of meer opgegeven kolommen met gegevensset niet kunnen worden gevonden.  

 Deze fout ontvangt u als er een kolomselectie voor een module wordt gemaakt, maar de geselecteerde kolom(en) niet bestaan in de invoergegevensset. Deze fout kan optreden als u handmatig een kolomnaam hebt getypt of als de kolomkiezer een voorgestelde kolom heeft verstrekt die niet in uw gegevensset bestond toen u de pijplijn hebt uitgevoerd.  

**Oplossing:** Ga opnieuw naar de module met deze uitzondering en valideer of de kolomnaam of -namen juist zijn en dat alle kolommen waarnaar wordt verwezen, bestaan.  

|Uitzonderingsberichten|
|------------------------|
|Een of meer opgegeven kolommen zijn niet gevonden.|
|Kolom met naam of index "{column_id}" niet gevonden.|
|Kolom met naam of index "{column_id}" bestaat niet in "{arg_name_missing_column}".|
|Kolom met naam of index "{column_id}" bestaat niet in {arg_name_missing_column}, maar bestaat in {arg_name_has_column}".|
|Kolommen met naam of index "{column_names}" niet gevonden.|
|Kolommen met naam of index "{column_names}" bestaan niet in {arg_name_missing_column}.|
|Kolommen met naam of index "{column_names}" bestaat niet in {arg_name_missing_column}, maar bestaat in {arg_name_has_column}.'.|


## <a name="error-0002"></a>Fout 0002  
 Uitzondering treedt op als een of meer parameters niet kunnen worden ontleed of omgezet van opgegeven type naar vereist door doelmethodetype.  

 Deze fout treedt op in Azure Machine Learning wanneer u een parameter opgeeft als invoer en het waardetype verschilt van het type dat wordt verwacht en impliciete conversie kan niet worden uitgevoerd.  

**Oplossing:** Controleer de modulevereisten en bepaal welk waardetype vereist is (tekenreeks, geheel getal, dubbel, enz.)  

|Uitzonderingsberichten|
|------------------------|
|Kan parameter niet ontsmetten.|
|Kan de parameter {arg_name_or_column} niet ontzien.|
|Kan de parameter {arg_name_or_column} converteren naar {to_type}.|
|Kan de parameter {arg_name_or_column}niet converteren van {from_type}" naar {to_type}.|
|Kan de parameterwaarde {arg_name_or_column}, {arg_value}, niet converteren van {from_type}" naar {to_type}.|
|Kan de waarde {arg_value} in kolom {arg_name_or_column}" niet converteren van {from_type}" naar {to_type}" met het gebruik van de indeling "{fmt}" die is verstrekt.|


## <a name="error-0003"></a>Fout 0003  
 Uitzondering treedt op als een of meer ingangen nietig of leeg zijn.  

 U ontvangt deze fout in Azure Machine Learning als invoer of parameters voor een module nietig of leeg zijn.  Deze fout kan bijvoorbeeld optreden wanneer u geen waarde intypvoor een parameter. Het kan ook gebeuren als u een gegevensset met ontbrekende waarden of een lege gegevensset hebt gekozen.  

**Resolutie:**

+ Open de module die de uitzondering heeft geproduceerd en controleer of alle ingangen zijn opgegeven. Zorg ervoor dat alle vereiste ingangen zijn opgegeven. 
+ Zorg ervoor dat gegevens die vanuit Azure-opslag worden geladen, toegankelijk zijn en dat de accountnaam of -sleutel niet is gewijzigd.  
+ Controleer de invoergegevens op ontbrekende waarden of nullen.
+ Als u een query op een gegevensbron gebruikt, controleert u of gegevens worden geretourneerd in de indeling die u verwacht. 
+ Controleer op typefouten of andere wijzigingen in de specificatie van gegevens.
  
|Uitzonderingsberichten|
|------------------------|
|Een of meer van de ingangen zijn nietig of leeg.|
|Invoer "{name}" is null of leeg.|


## <a name="error-0004"></a>Fout 0004  
 Uitzondering treedt op als de parameter kleiner is dan of gelijk is aan de specifieke waarde.  

 U ontvangt deze fout in Azure Machine Learning als de parameter in het bericht lager is dan een grenswaarde die nodig is voor het verwerken van de gegevens door de module.  

**Oplossing:** Ga opnieuw naar de module die de uitzondering gooit en wijzig de parameter om groter te zijn dan de opgegeven waarde.  

|Uitzonderingsberichten|
|------------------------|
|Parameter moet groter zijn dan de grenswaarde.|
|Parameter waarde "{arg_name}" moet groter zijn dan {lower_boundary}.|
|Parameter "{arg_name}" heeft de waarde "{actual_value}" die groter moet zijn dan {lower_boundary}.|


## <a name="error-0005"></a>Fout 0005  
 Uitzondering treedt op als de parameter kleiner is dan een specifieke waarde.  

 U ontvangt deze fout in Azure Machine Learning als de parameter in het bericht hieronder staat of gelijk is aan een grenswaarde die nodig is voor het verwerken van de gegevens door de module.  

**Oplossing:** Ga opnieuw naar de module die de uitzondering gooit en wijzig de parameter die groter is dan of gelijk is aan de opgegeven waarde.  

|Uitzonderingsberichten|
|------------------------|
|Parameter moet groter zijn dan of gelijk zijn aan de grenswaarde.|
|Parameter waarde "{arg_name}" moet groter zijn dan of gelijk zijn aan {lower_boundary}.|
|Parameter "{arg_name}" heeft de waarde "{value}" die groter moet zijn dan of gelijk moet zijn aan {lower_boundary}.|


## <a name="error-0006"></a>Fout 0006  
 Uitzondering treedt op als de parameter groter is dan of gelijk is aan de opgegeven waarde.  

 U ontvangt deze fout in Azure Machine Learning als de parameter in het bericht groter is dan of gelijk is aan een grenswaarde die nodig is voor het verwerken van de gegevens door de module.  

**Oplossing:** Ga opnieuw naar de module die de uitzondering gooit en wijzig de parameter om kleiner te zijn dan de opgegeven waarde.  

|Uitzonderingsberichten|
|------------------------|
|Parameters mismatch. Een van de parameters moet minder zijn dan de andere.|
|Parameter waarde "{arg_name}" moet lager zijn dan parameter {upper_boundary_parameter_name}"-waarde.|
|Parameter "{arg_name}" heeft de waarde "{value}" die lager moet zijn dan {upper_boundary_parameter_name}.|


## <a name="error-0007"></a>Fout 0007  
 Uitzondering treedt op als de parameter groter is dan een specifieke waarde.  

 U ontvangt deze fout in Azure Machine Learning als u in de eigenschappen voor de module een waarde hebt opgegeven die groter is dan is toegestaan. U bijvoorbeeld een gegevens opgeven die buiten het bereik van ondersteunde datums vallen, of u aangeven dat vijf kolommen worden gebruikt wanneer er slechts drie kolommen beschikbaar zijn. 

 U deze fout ook zien als u twee sets gegevens opgeeft die op de een of andere manier moeten overeenkomen. Als u bijvoorbeeld de naam van kolommen wijzigt en de kolommen opgeeft op index, moet het aantal namen dat u aangeeft overeenkomen met het aantal kolomindexen. Een ander voorbeeld is een wiskundige bewerking waarbij twee kolommen worden gebruikt, waarbij de kolommen hetzelfde aantal rijen moeten hebben. 

**Resolutie:**

 + Open de betreffende module en bekijk eventuele numerieke eigenschapsinstellingen.
 + Controleer of parameterwaarden binnen het ondersteunde waardenbereik voor die eigenschap vallen.
 + Als de module meerdere ingangen neemt, moet u ervoor zorgen dat de ingangen van dezelfde grootte zijn.
<!-- + If the module has multiple properties that can be set, ensure that related properties have appropriate values. For example, when using [Group Data into Bins](group-data-into-bins.md), if you use the option to specify custom bin edges, the number of bins must match the number of values you provide as bin boundaries.-->
 + Controleer of de gegevensset of gegevensbron is gewijzigd. Soms is een waarde die met een vorige versie van de gegevens heeft gewerkt, mislukt nadat het aantal kolommen, de kolomgegevenstypen of de grootte van de gegevens is gewijzigd.  

|Uitzonderingsberichten|
|------------------------|
|Parameters mismatch. Een van de parameters moet kleiner zijn dan of gelijk zijn aan een andere.|
|Parameter waarde "{arg_name}" moet lager zijn dan of gelijk zijn aan parameter "{upper_boundary_parameter_name}" waarde.|
|Parameter "{arg_name}" heeft de waarde "{actual_value}" die lager moet zijn dan of gelijk moet zijn aan {upper_boundary}.|
|Parameter waarde {arg_name}{ actual_value} moet lager zijn dan of gelijk zijn aan parameter "{upper_boundary_parameter_name}" waarde {upper_boundary}.|


## <a name="error-0008"></a>Fout 0008  
 Uitzondering treedt op als de parameter zich niet binnen bereik bevindt.  

 U ontvangt deze fout in Azure Machine Learning als de parameter in het bericht buiten de grenzen valt die nodig zijn voor het verwerken van de gegevens door de module.  

 Deze fout wordt bijvoorbeeld weergegeven als u [Rijen](add-rows.md) toevoegen probeert te gebruiken om twee gegevenssets met een verschillend aantal kolommen te combineren.  

**Oplossing:** Ga opnieuw naar de module die de uitzondering gooit en wijzig de parameter om binnen het opgegeven bereik te liggen.  

|Uitzonderingsberichten|
|------------------------|
|Parameterwaarde bevindt zich niet in het opgegeven bereik.|
|Parameter "{arg_name}" waarde is niet binnen bereik.|
|Parameter "{arg_name}" waarde moet zich binnen het bereik van [{lower_boundary}, {upper_boundary}].|
|Parameter "{arg_name}" waarde is niet binnen bereik. {reden}|


## <a name="error-0009"></a>Fout 0009  
 Er treedt een uitzondering op wanneer de naam van het Azure-opslagaccount of de naam van het containernummer onjuist is opgegeven.  

Deze fout treedt op in Azure Machine Learning-ontwerper wanneer u parameters opgeeft voor een Azure-opslagaccount, maar de naam of het wachtwoord kan niet worden opgelost. Fouten in wachtwoorden of accountnamen kunnen om verschillende redenen optreden:

 + De rekening is het verkeerde type. Sommige nieuwe accounttypen worden niet ondersteund voor gebruik met Machine Learning-ontwerper. Zie [Gegevens importeren](import-data.md) voor meer informatie.
 + U hebt de onjuiste accountnaam ingevoerd
 + Het account bestaat niet meer
 + Het wachtwoord voor het opslagaccount is verkeerd of is gewijzigd
 + U hebt de containernaam niet opgegeven of de container bestaat niet
 + U hebt het bestandspad niet volledig opgegeven (pad naar de blob)
   

**Resolutie:**

Dergelijke problemen treden vaak op wanneer u handmatig de accountnaam, het wachtwoord of het containerpad probeert in te voeren. We raden u aan de nieuwe wizard te gebruiken voor de module [Gegevens importeren,](import-data.md) waarmee u namen opzoeken en controleren.

Controleer ook of het account, de container of de blob is verwijderd. Gebruik een ander Azure-opslaghulpprogramma om te controleren of de accountnaam en het wachtwoord correct zijn ingevoerd en of de container bestaat. 

Sommige nieuwere accounttypen worden niet ondersteund door Azure Machine Learning. De nieuwe typen 'warme' of 'koude' kunnen bijvoorbeeld niet worden gebruikt voor machine learning. Zowel klassieke opslagaccounts als opslagaccounts die als "Algemeen doel" zijn gemaakt, werken prima.

Als het volledige pad naar een blob is opgegeven, controleert u of het pad is opgegeven als **container/blobnaam**en of zowel de container als de blob in het account bestaan.  

 Het pad mag geen voorloopstreep bevatten. Bijvoorbeeld **/container/blob** is onjuist en moet worden ingevoerd als **container/blob**.  


|Uitzonderingsberichten|
|------------------------|
|De naam van het Azure-opslagaccount of de naam van de container is onjuist.|
|De naam van het Azure-opslagaccount {account_name} of de containernaam {container_name} is onjuist. er werd een containernaam van de opmaakcontainer/blob verwacht.|


## <a name="error-0010"></a>Fout 0010  
 Er treedt een uitzondering op als invoergegevenssets kolomnamen bevatten die overeenkomen, maar niet.  

 U ontvangt deze fout in Azure Machine Learning als de kolomindex in het bericht verschillende kolomnamen in de twee invoergegevenssets bevat.  

**Oplossing:** Gebruik [Metagegevens bewerken](edit-metadata.md) of wijzig de oorspronkelijke gegevensset om dezelfde kolomnaam te hebben voor de opgegeven kolomindex.  

|Uitzonderingsberichten|
|------------------------|
|Kolommen met bijbehorende index in invoergegevenssets hebben verschillende namen.|
|Kolomnamen zijn niet hetzelfde voor kolom {col_index} (zero-based) van invoergegevenssets ({dataset1} en {dataset2} respectievelijk).|


## <a name="error-0011"></a>Fout 0011  
 Uitzondering treedt op als het argument van de doorgegeven kolomset niet van toepassing is op een van de gegevenssetkolommen.  

 U ontvangt deze fout in Azure Machine Learning als de opgegeven kolomselectie niet overeenkomt met een van de kolommen in de opgegeven gegevensset.  

 U deze fout ook krijgen als u geen kolom hebt geselecteerd en er ten minste één kolom nodig is om de module te laten werken.  

**Oplossing:** Wijzig de kolomselectie in de module zodat deze van toepassing is op de kolommen in de gegevensset.  

 Als de module vereist dat u een specifieke kolom selecteert, zoals een labelkolom, controleert u of de rechterkolom is geselecteerd.  

 Als ongepaste kolommen zijn geselecteerd, verwijdert u deze en voert u de pijplijn opnieuw uit.  

|Uitzonderingsberichten|
|------------------------|
|De opgegeven kolomset is niet van toepassing op een van de gegevenssetkolommen.|
|Opgegeven kolomset {column_set} is niet van toepassing op een van de gegevenssetkolommen.|


## <a name="error-0012"></a>Fout 0012  
 Er treedt een uitzondering op als instantie van klasse niet kan worden gemaakt met doorgegeven reeks argumenten.  

**Oplossing:** Deze fout is niet bruikbaar voor de gebruiker en zal worden afgeschaft in een toekomstige release.  

|Uitzonderingsberichten|
|------------------------|
|Ongetraind model, trein model eerst.|
|Ongetraind model ({arg_name}), gebruik getraind model.|


## <a name="error-0013"></a>Fout 0013  
 Er treedt een uitzondering op als de leerling die aan de module wordt doorgegeven een ongeldig type is.  

 Deze fout treedt op wanneer een getraind model niet compatibel is met de gekoppelde scoremodule. <!--For example, connecting the output of [Train Matchbox Recommender](train-matchbox-recommender.md) to [Score Model](score-model.md) (instead of [Score Matchbox Recommender](score-matchbox-recommender.md)) will generate this error when the pipeline is run.  -->

**Resolutie:**

Bepaal het type leerling dat wordt geproduceerd door de trainingsmodule en bepaal de scoremodule die geschikt is voor de leerling. 

Als het model is opgeleid met behulp van een van de gespecialiseerde trainingsmodules, sluit u het getrainde model alleen aan op de bijbehorende gespecialiseerde scoremodule. 


|Modeltype|Trainingsmodule| Scoremodule|
|----|----|----|
|elke classificatie|[Trainingsmodel](train-model.md) |[Score Model](score-model.md)|
|een regressiemodel|[Trainingsmodel](train-model.md) |[Score Model](score-model.md)|

<!--| clustering models| [Train Clustering Model](train-clustering-model.md) or [Sweep Clustering](sweep-clustering.md)| [Assign Data to Clusters](assign-data-to-clusters.md)|
| anomaly detection - One-Class SVM | [Train Anomaly Detection Model](train-anomaly-detection-model.md) |[Score Model](score-model.md)|
| anomaly detection - PCA |[Train Model](train-model.md) |[Score Model](score-model.md) </br> Some additional steps are required to evaluate the model. |
| anomaly detection - time series|  [Time Series Anomaly Detection](time-series-anomaly-detection.md) |Model trains from data and generates scores. The module does not create a trained learner and no additional scoring is required. |
| recommendation model| [Train Matchbox Recommender](train-matchbox-recommender.md) | [Score Matchbox Recommender](score-matchbox-recommender.md) |
| image classification | [Pretrained Cascade Image Classification](pretrained-cascade-image-classification.md) | [Score Model](score-model.md) |
|Vowpal Wabbit models| [Train Vowpal Wabbit Version 7-4 Model](train-vowpal-wabbit-version-7-4-model.md) | [Score Vowpal Wabbit Version 7-4 Model](score-vowpal-wabbit-version-7-4-model.md) |   
|Vowpal Wabbit models| [Train Vowpal Wabbit Version 7-10 Model](train-vowpal-wabbit-version-7-10-model.md) | [Score Vowpal Wabbit Version 7-10 Model](score-vowpal-wabbit-version-7-10-model.md) |
|Vowpal Wabbit models| [Train Vowpal Wabbit Version 8 Model](score-vowpal-wabbit-version-8-model.md) | [Score Vowpal Wabbit Version 8 Model](score-vowpal-wabbit-version-8-model.md) |-->

|Uitzonderingsberichten|
|------------------------|
|De leerling van ongeldig type wordt doorgegeven.|
|Leerling {arg_name}" heeft ongeldig type.|
|De leerling "{arg_name}" heeft ongeldig type {learner_type}.|


## <a name="error-0014"></a>Fout 0014  
 Er treedt een uitzondering op als het aantal unieke waarden van de kolom groter is dan toegestaan.  

 Deze fout treedt op wanneer een kolom te veel unieke waarden bevat.  U ziet deze fout bijvoorbeeld als u opgeeft dat een kolom als categorische gegevens wordt verwerkt, maar er te veel unieke waarden in de kolom zijn om de verwerking te voltooien. U deze fout ook zien als er een mismatch is tussen het aantal unieke waarden in twee ingangen.   

**Resolutie:**

Open de module die de fout heeft gegenereerd en identificeer de kolommen die als invoer worden gebruikt. Voor sommige modules u met de rechtermuisknop op de gegevenssetinvoer klikken en **Visualiseren** selecteren om statistieken over afzonderlijke kolommen te krijgen, inclusief het aantal unieke waarden en de verdeling ervan.

Voor kolommen die u wilt gebruiken voor het groeperen of categoriseren, neemt u stappen om het aantal unieke waarden in kolommen te verminderen. U op verschillende manieren verminderen, afhankelijk van het gegevenstype van de kolom. 
<!--
+ For text data, you might be able to use [Preprocess Text](preprocess-text.md) to collapse similar entries. 
+ For numeric data, you can create a smaller number of bins using [Group Data into Bins](group-data-into-bins.md), remove or truncate values using [Clip Values](clip-values.md), or use machine learning methods such as [Principal Component Analysis](principal-component-analysis.md) or [Learning with Counts](data-transformation-learning-with-counts.md) to reduce the dimensionality of the data.  
-->
> [!TIP]
> u geen oplossing vinden die overeenkomt met uw scenario? U feedback geven over dit onderwerp met de naam van de module die de fout heeft gegenereerd en het gegevenstype en de kardinaliteit van de kolom. We zullen de informatie gebruiken om meer gerichte stappen voor het oplossen van problemen te bieden voor veelvoorkomende scenario's.   

|Uitzonderingsberichten|
|------------------------|
|Het aantal unieke waarden van de kolom is groter dan toegestaan.|
|Aantal unieke waarden in kolom: {column_name}" is groter dan toegestaan.|
|Aantal unieke waarden in kolom: {column_name}" overschrijdt het aantal tuple van {limitation}.|


## <a name="error-0015"></a>Fout 0015  
 Er treedt een uitzondering op als de databaseverbinding is mislukt.  

 U ontvangt deze fout als u een onjuiste SQL-accountnaam, wachtwoord, databaseserver of databasenaam invoert of als een verbinding met de database niet kan worden gemaakt vanwege problemen met de database of server.  

**Oplossing:** Controleer of de accountnaam, het wachtwoord, de databaseserver en de database correct zijn ingevoerd en of het opgegeven account het juiste machtigingsniveau heeft. Controleer of de database momenteel toegankelijk is.  

|Uitzonderingsberichten|
|------------------------|
|Fout bij het maken van databaseverbinding.|
|Fout bij het maken van databaseverbinding: {connection_str}.|


## <a name="error-0016"></a>Fout 0016  
 Er treedt een uitzondering op als invoergegevenssets die naar de module worden doorgegeven, compatibele kolomtypen moeten hebben, maar dit niet doen.  

 U ontvangt deze fout in Azure Machine Learning als de typen kolommen die in twee of meer gegevenssets zijn doorgegeven, niet compatibel zijn met elkaar.  

**Oplossing:** [Metagegevens bewerken](edit-metadata.md) of de oorspronkelijke invoergegevensset wijzigen<!--, or use [Convert to Dataset](convert-to-dataset.md)--> om ervoor te zorgen dat de typen kolommen compatibel zijn.  

|Uitzonderingsberichten|
|------------------------|
|Kolommen met bijbehorende index in invoergegevenssets hebben wel onverenigbare typen.|
|Kolommen {first_col_names}' zijn onverenigbaar tussen trein- en testgegevens.|
|Kolommen {first_col_names}' en {second_col_names}' zijn onverenigbaar.|
|Kolomelementtypen zijn niet compatibel voor kolom {first_col_names}' (zero-based) van invoergegevenssets ({first_dataset_names} en {second_dataset_names} respectievelijk).|


## <a name="error-0017"></a>Fout 0017  
 Er treedt een uitzondering op als een geselecteerde kolom een gegevenstype gebruikt dat niet wordt ondersteund door de huidige module.  

 U deze fout bijvoorbeeld ontvangen in Azure Machine Learning als uw kolomselectie een kolom bevat met een gegevenstype dat niet door de module kan worden verwerkt, zoals een tekenreekskolom voor een wiskundige bewerking of een scorekolom waarin een categorische functiekolom is Vereist.  

**Resolutie:**
 1. Identificeer de kolom die het probleem is.
 2. Bekijk de vereisten van de module.
 3. Wijzig de kolom om deze aan de vereisten te laten voldoen. Mogelijk moet u verschillende van de volgende modules gebruiken om wijzigingen aan te brengen, afhankelijk van de kolom en de conversie die u probeert:
    + Gebruik [Metagegevens bewerken](edit-metadata.md) om het gegevenstype kolommen te wijzigen of om het kolomgebruik te wijzigen van functie naar numeriek, categorisch naar niet-categorisch, enzovoort.
<!--    + Use [Convert to Dataset](convert-to-dataset.md) to ensure that all included columns use data types that are supported by Azure Machine Learning.  If you cannot convert the columns, consider removing them from the input dataset.
    + Use the [Apply SQL Transformation](apply-sql-transformation.md) or [Execute R Script](execute-r-script.md) modules to cast or convert any columns that cannot be modified using [Edit Metadata](edit-metadata.md). These modules provide more flexibility for working with datetime data types.
    + For numeric data types, you can use the [Apply Math Operation](apply-math-operation.md) module to round or truncate values, or use the [Clip Values](clip-values.md) module to remove out of range values.  -->
 4. Als laatste redmiddel moet u mogelijk de oorspronkelijke invoergegevensset wijzigen.

> [!TIP]
> u geen oplossing vinden die overeenkomt met uw scenario? U feedback geven over dit onderwerp met de naam van de module die de fout heeft gegenereerd en het gegevenstype en de kardinaliteit van de kolom. We zullen de informatie gebruiken om meer gerichte stappen voor het oplossen van problemen te bieden voor veelvoorkomende scenario's. 

|Uitzonderingsberichten|
|------------------------|
|Kan kolom van huidig type niet verwerken. Het type wordt niet ondersteund door de module.|
|Kan kolom van type {col_type} niet verwerken. Het type wordt niet ondersteund door de module.|
|Kan kolom {col_name} van type {col_type} niet verwerken. Het type wordt niet ondersteund door de module.|
|Kan kolom {col_name} van type {col_type} niet verwerken. Het type wordt niet ondersteund door de module. Parameternaam: {arg_name}.|


## <a name="error-0018"></a>Fout 0018  
 Er treedt een uitzondering op als de invoergegevensset niet geldig is.  

**Oplossing:** Deze fout in Azure Machine Learning kan in veel contexten worden weergegeven, dus er is geen enkele oplossing. In het algemeen geeft de fout aan dat de gegevens die worden verstrekt als invoer voor een module het verkeerde aantal kolommen hebben of dat het gegevenstype niet overeenkomt met de vereisten van de module. Bijvoorbeeld:  

-   De module vereist een labelkolom, maar geen kolom is gemarkeerd als label of u hebt nog geen labelkolom geselecteerd.  
  
-   De module vereist dat gegevens categorisch zijn, maar uw gegevens zijn numeriek.  

<!---   The module requires a specific data type. For example, ratings provided to [Train Matchbox Recommender](train-matchbox-recommender.md) can be either numeric or categorical, but cannot be floating point numbers.  -->

-   De gegevens zijn in het verkeerde formaat.  
  
-   Geïmporteerde gegevens bevatten ongeldige tekens, slechte waarden of waarden buiten bereik.  
-   De kolom is leeg of bevat te veel ontbrekende waarden.  

 Als u wilt bepalen wat de vereisten en hoe uw gegevens kunnen worden gebruikt, controleert u het helponderwerp voor de module die de gegevensset als invoer verbruikt.  

 <!--We also recommend that you use [Summarize Data](summarize-data.md) or [Compute Elementary Statistics](compute-elementary-statistics.md) to profile your data, and use these modules to fix metadata and clean values: [Edit Metadata](edit-metadata.md) and [Clean Missing Data](clean-missing-data.md), [Clip Values](clip-values.md)-->.  

|Uitzonderingsberichten|
|------------------------|
|De gegevensset is niet geldig.|
|{dataset1} bevat ongeldige gegevens.|
|{dataset1} en {dataset2} moeten consistent zijn op kolom.|
|{dataset1} bevat ongeldige gegevens, {reason}.|
|{dataset1} bevat {invalid_data_category}. {troubleshoot_hint}|
|{dataset1} is niet geldig, {reason}. {troubleshoot_hint}|


## <a name="error-0019"></a>Fout 0019  
 Uitzondering treedt op als kolom wordt verwacht gesorteerde waarden bevatten, maar dit niet het geval is.  

 U ontvangt deze fout in Azure Machine Learning als de opgegeven kolomwaarden niet in orde zijn.  

**Oplossing:** Sorteer de kolomwaarden door de invoergegevensset handmatig te wijzigen en de module opnieuw uit te voeren.  

|Uitzonderingsberichten|
|------------------------|
|Waarden in de kolom worden niet gesorteerd.|
|Waarden in kolom {col_index} worden niet gesorteerd.|
|Waarden in kolom {col_index}" van gegevensset {dataset}" worden niet gesorteerd.|


## <a name="error-0020"></a>Fout 0020  
 Er treedt een uitzondering op als het aantal kolommen in sommige gegevenssets dat naar de module wordt doorgegeven, te klein is.  

 U ontvangt deze fout in Azure Machine Learning als er niet genoeg kolommen zijn geselecteerd voor een module.  

**Oplossing:** Ga opnieuw naar de module en zorg ervoor dat kolomkiezer het juiste aantal kolommen heeft geselecteerd.  

|Uitzonderingsberichten|
|------------------------|
|Het aantal kolommen in de invoergegevensset is minder dan het toegestane minimum.|
|Het aantal kolommen in de invoergegevensset {arg_name} is minder dan het toegestane minimum.|
|Het aantal kolommen in de invoergegevensset is kleiner dan het toegestane minimum van {required_columns_count} kolom(en).|
|Het aantal kolommen in de invoergegevensset {arg_name} is minder dan toegestaan minimum van {required_columns_count} kolom(en).|


## <a name="error-0021"></a>Fout 0021  
 Er treedt een uitzondering op als het aantal rijen in sommige gegevenssets dat naar de module wordt doorgegeven, te klein is.  

 Deze fout in Azure Machine Learning wanneer er niet genoeg rijen in de gegevensset zijn om de opgegeven bewerking uit te voeren. U ziet deze fout bijvoorbeeld als de invoergegevensset leeg is of als u een bewerking probeert uit te voeren waarvoor een minimumaantal rijen geldig moet zijn. Dergelijke bewerkingen kunnen bestaan uit (maar zijn niet beperkt tot) groepering of classificatie op basis van statistische methoden, bepaalde soorten binning en leren met tellingen.  

**Resolutie:**

 + Open de module die de fout heeft geretourneerd en controleer de invoergegevensset en module-eigenschappen. 
 + Controleer of de invoergegevensset niet leeg is en of er voldoende rijen met gegevens zijn om te voldoen aan de vereisten die in de modulehelp worden beschreven.  
 + Als uw gegevens vanuit een externe bron worden geladen, controleert u of de gegevensbron beschikbaar is en dat er geen fout of wijziging optreedt in de gegevensdefinitie waardoor het importproces minder rijen krijgt.
 + Als u een bewerking uitvoert op de gegevens stroomopwaarts van de module die van invloed kunnen zijn op het type gegevens of het aantal waarden, zoals schoonmaken, splitsen of deelnemen aan bewerkingen, controleert u de uitvoer van deze bewerkingen om het aantal geretourneerde rijen te bepalen.  

|Uitzonderingsberichten|
|------------------------|
|Het aantal rijen in de invoergegevensset is minder dan het toegestane minimum.|
|Het aantal rijen in de invoergegevensset is kleiner dan het toegestane minimum van {required_rows_count} rij(en).|
|Het aantal rijen in de invoergegevensset is kleiner dan het toegestane minimum van {required_rows_count} rij(en). {reden}|
|Het aantal rijen in de invoergegevensset {arg_name} is minder dan toegestaan minimum van {required_rows_count} rij(en).|
|Aantal rijen in de invoergegevensset {arg_name}is {actual_rows_count}, minder dan toegestaan minimum van {required_rows_count} rij(s).|
|Aantal rijen "{row_type}" in invoergegevensset {arg_name}is {actual_rows_count}, minder dan toegestaan minimum van {required_rows_count} rij(s).|


## <a name="error-0022"></a>Fout 0022  
 Uitzondering treedt op als het aantal geselecteerde kolommen in de invoergegevensset niet gelijk is aan het verwachte aantal.  

 Deze fout in Azure Machine Learning kan optreden wanneer de downstreammodule of -bewerking een specifiek aantal kolommen of ingangen vereist en u te weinig of te veel kolommen of ingangen hebt opgegeven. Bijvoorbeeld:  

-   U geeft één kolom of sleutelkolom op en hebt per ongeluk meerdere kolommen geselecteerd.  
  
-   U hernoemt kolommen, maar geeft meer of minder namen op dan er kolommen zijn.  
  
-   Het aantal kolommen in de bron of bestemming is gewijzigd of komt niet overeen met het aantal kolommen dat door de module wordt gebruikt.  
  
-   U hebt een door komma's gescheiden lijst met waarden voor invoer opgegeven, maar het aantal waarden komt niet overeen of meerdere ingangen worden niet ondersteund.  

**Oplossing:** Bekijk de module opnieuw en controleer de kolomselectie om ervoor te zorgen dat het juiste aantal kolommen is geselecteerd. Controleer de output van upstreammodules en de vereisten van downstream-bewerkingen.  

 Als u een van de opties voor kolomselectie hebt gebruikt waarmee meerdere kolommen kunnen worden geselecteerd (kolomindexen, alle functies, alle numerieke functies, enz.), valideert u het exacte aantal kolommen dat door de selectie wordt geretourneerd.  

 <!--If you are trying to specify a comma-separated list of datasets as inputs to [Unpack Zipped Datasets](unpack-zipped-datasets.md), unpack only one dataset at a time. Multiple inputs are not supported.  -->

 Controleer of het aantal of het type upstreamkolommen niet is gewijzigd.  

 Als u een aanbevelingsgegevensset gebruikt om een model te trainen, moet u er rekening mee nemen dat de aanbevelingsgever een beperkt aantal kolommen verwacht, overeenkomend met gebruikersitemparen of user-item-rankings. Verwijder extra kolommen voordat u het model traint of aanbevelingsgegevenssets splitst. Zie [Gegevens splitsen voor](split-data.md)meer informatie .  

|Uitzonderingsberichten|
|------------------------|
|Het aantal geselecteerde kolommen in de invoergegevensset is niet gelijk aan het verwachte aantal.|
|Het aantal geselecteerde kolommen in de invoergegevensset is niet gelijk aan {expected_col_count}.|
|Kolomselectiepatroon {selection_pattern_friendly_name}, biedt het aantal geselecteerde kolommen in invoergegevensset dat niet gelijk is aan {expected_col_count}.|
|Kolomselectiepatroon "{selection_pattern_friendly_name}" zal naar verwachting {expected_col_count} kolom(en) bieden die zijn geselecteerd in de invoergegevensset, maar {selected_col_count} kolom(en) is/worden daadwerkelijk geleverd.|


## <a name="error-0023"></a>Fout 0023  

Uitzondering treedt op als doelkolom van invoergegevensset niet geldig is voor de huidige opleidermodule.  

Deze fout in Azure Machine Learning treedt op als de doelkolom (zoals geselecteerd in de moduleparameters) niet van het geldige gegevenstype is, alle ontbrekende waarden bevat of niet categorisch was zoals verwacht.  

**Oplossing:** Bekijk de module-invoer opnieuw om de inhoud van de label/doelkolom te inspecteren. Zorg ervoor dat het niet alle ontbrekende waarden heeft. Als de module verwacht dat de doelkolom categorisch is, moet u ervoor zorgen dat er meer dan één afzonderlijke waarden in de doelkolom zijn.  

|Uitzonderingsberichten|
|------------------------|
|Invoergegevensset heeft niet-ondersteunde doelkolom.|
|Invoergegevensset heeft niet-ondersteunde doelkolom {column_index}.|
|Invoergegevensset heeft niet-ondersteunde doelkolom {column_index}voor de leerling van het type {learner_type}.|


## <a name="error-0024"></a>Fout 0024  
Er treedt een uitzondering op als de gegevensset geen labelkolom bevat.  

 Deze fout in Azure Machine Learning treedt op wanneer de module een labelkolom vereist en de gegevensset geen labelkolom heeft. De evaluatie van een gescoorde gegevensset vereist bijvoorbeeld meestal dat een labelkolom aanwezig is om nauwkeurigheidsstatistieken te berekenen.  

Het kan ook voorkomen dat een labelkolom aanwezig is in de gegevensset, maar niet correct wordt gedetecteerd door Azure Machine Learning.

**Resolutie:**

+ Open de module die de fout heeft gegenereerd en bepaal of er een labelkolom aanwezig is. De naam of het gegevenstype van de kolom doet er niet toe, zolang de kolom één resultaat (of afhankelijke variabele) bevat die u probeert te voorspellen. Als u niet zeker weet welke kolom het label heeft, zoekt u naar een algemene naam zoals *Klasse* of *Doel.* 
+  Als de gegevensset geen labelkolom bevat, is het mogelijk dat de labelkolom expliciet of per ongeluk stroomopwaarts is verwijderd. Het kan ook zijn dat de gegevensset niet de output is van een upstream scoringmodule.
+ Als u de kolom expliciet wilt markeren als de kolom met het label, voegt u de module [Metagegevens bewerken](edit-metadata.md) toe en verbindt u de gegevensset. Selecteer alleen de labelkolom en selecteer **Label** in de vervolgkeuzelijst **Velden.** 
+ Als de verkeerde kolom als label wordt gekozen, u **label wissen** selecteren in de **velden** om de metagegevens in de kolom op te lossen. 
  
|Uitzonderingsberichten|
|------------------------|
|Er is geen labelkolom in de gegevensset.|
|Er is geen labelkolom in {dataset_name}.|


## <a name="error-0025"></a>Fout 0025  
 Uitzondering treedt op als de gegevensset geen scorekolom bevat.  

 Deze fout in Azure Machine Learning treedt op als de invoer naar het evaluatiemodel geen geldige scorekolommen bevat. De gebruiker probeert bijvoorbeeld een gegevensset te evalueren voordat deze is beoordeeld met een correct getraind model of de scorekolom is expliciet stroomopwaarts gedropt. Deze uitzondering treedt ook op als de scorekolommen op de twee gegevenssets niet compatibel zijn. U probeert bijvoorbeeld de nauwkeurigheid van een lineaire regressor te vergelijken met een binaire classificatie.  

**Oplossing:** Bekijk de invoer naar het evaluatiemodel opnieuw en kijk of deze een of meer scorekolommen bevat. Als dit niet het zo is, is de gegevensset niet gescoord of zijn de scorekolommen in een upstreammodule verwijderd.  

|Uitzonderingsberichten|
|------------------------|
|Er is geen scorekolom in de gegevensset.|
|Er is geen scorekolom in {dataset_name}".|
|Er is geen scorekolom in {dataset_name}" die wordt geproduceerd door een {learner_type}. Scoor de gegevensset met het juiste type leerling.|


## <a name="error-0026"></a>Fout 0026  
 Uitzondering treedt op als kolommen met dezelfde naam niet zijn toegestaan.  

 Deze fout in Azure Machine Learning treedt op als meerdere kolommen dezelfde naam hebben. Een manier waarop u deze fout ontvangen, is als de gegevensset geen koptekstrij heeft en kolomnamen automatisch worden toegewezen: Col0, Col1, enz.  

**Oplossing:** Als kolommen dezelfde naam hebben, voegt u een module [Metagegevens bewerken](edit-metadata.md) in tussen de invoergegevensset en de module. Gebruik de kolomkiezer in [Metagegevens bewerken](edit-metadata.md) om kolommen te selecteren om de naam te wijzigen en typ de nieuwe namen in het tekstvak **Nieuwe kolomnamen.**  

|Uitzonderingsberichten|
|------------------------|
|Gelijke kolomnamen worden opgegeven in argumenten. Gelijke kolomnamen zijn niet toegestaan per module.|
|Gelijke kolomnamen in argumenten {arg_name_1}" en {arg_name_2}" zijn niet toegestaan. Geef verschillende namen op.|


## <a name="error-0027"></a>Fout 0027  
 Uitzondering treedt op wanneer twee objecten van dezelfde grootte moeten zijn, maar dat niet zijn.  

 Dit is een veel voorkomende fout in Azure Machine Learning en kan worden veroorzaakt door vele voorwaarden.  

**Oplossing:** Er is geen specifieke resolutie. U echter wel controleren op voorwaarden zoals:  

-   Als u de naam van kolommen wijzigt, moet u ervoor zorgen dat elke lijst (de invoerkolommen en de lijst met nieuwe namen) hetzelfde aantal items heeft.  
  
-   Als u twee gegevenssets aansluit of samenvoegt, moet u ervoor zorgen dat ze hetzelfde schema hebben.  
  
-   Als u twee gegevenssets met meerdere kolommen aansluit, controleert u of de sleutelkolommen hetzelfde gegevenstype hebben en selecteert u de optie **Duplicaten toestaan en de kolomvolgorde behouden in selectie**.  

|Uitzonderingsberichten|
|------------------------|
|De grootte van doorgegeven objecten is inconsistent.|
|De grootte van {friendly_name1}" is niet in overeenstemming met de grootte van {friendly_name2}.|


## <a name="error-0028"></a>Fout 0028  
 Uitzondering treedt op in het geval dat kolomset gedupliceerde kolomnamen bevat en dit niet is toegestaan.  

 Deze fout in Azure Machine Learning treedt op wanneer kolomnamen worden gedupliceerd. dat wil zeggen, niet uniek.  

**Oplossing:** Als kolommen dezelfde naam hebben, voegt u een instantie van [Metagegevens bewerken](edit-metadata.md) toe tussen de invoergegevensset en de module waardoor de fout wordt gegenereerd. Gebruik de kolomkiezer in [Metagegevens bewerken](edit-metadata.md) om kolommen te selecteren om de naam te wijzigen en typ de nieuwe kolommennamen in het tekstvak **Nieuwe kolomnamen.** Als u de naam van meerdere kolommen wijzigt, moet u ervoor zorgen dat de waarden die u typt in de **kolomnamen Nieuw** uniek zijn.  

|Uitzonderingsberichten|
|------------------------|
|Kolomset bevat gedupliceerde kolomnaam(en).|
|De naam {duplicated_name} wordt gedupliceerd.|
|De naam {duplicated_name}" wordt gedupliceerd in {arg_name}.|
|De naam {duplicated_name} wordt gedupliceerd. Details: {details}|


## <a name="error-0029"></a>Fout 0029  
 Uitzondering treedt op wanneer ongeldige URI wordt doorgegeven.  

 Deze fout in Azure Machine Learning treedt op wanneer ongeldige URI wordt doorgegeven.  U ontvangt deze fout als een van de volgende voorwaarden waar is:  

-   De openbare of SAS URI die is voorzien voor Azure Blob Storage voor lezen of schrijven, bevat een fout.  
  
-   Het tijdvenster voor de SAS is verlopen.  
  
-   De web-URL via HTTP-bron vertegenwoordigt een bestand of een loopback URI.  
  
-   De web-URL via HTTP bevat een onjuist opgemaakte URL.  
  
-   De URL kan niet worden opgelost door de externe bron.  

**Oplossing:** Ga opnieuw naar de module en controleer de indeling van de URI. Als de gegevensbron een web-URL via HTTP is, controleert u of de beoogde bron geen bestand of een loopback URI (localhost) is.  

|Uitzonderingsberichten|
|------------------------|
|Invaliduri is geslaagd.|
|De Uri "{invalid_url}" is ongeldig.|


## <a name="error-0030"></a>Fout 0030  
 Uitzondering treedt op in het geval dat het niet mogelijk is om een bestand te downloaden.  

 Deze uitzondering in Azure Machine Learning treedt op wanneer het niet mogelijk is om een bestand te downloaden. U ontvangt deze uitzondering wanneer een poging tot lezen van een HTTP-bron is mislukt na drie (3) pogingen opnieuw proberen.  

**Oplossing:** Controleer of de URI naar de HTTP-bron correct is en of de site momenteel toegankelijk is via internet.  

|Uitzonderingsberichten|
|------------------------|
|Kan een bestand niet downloaden.|
|Fout bij het downloaden van het bestand: {file_url}.|


## <a name="error-0031"></a>Fout 0031  
 Er treedt een uitzondering op als het aantal kolommen in kolomset kleiner is dan nodig is.  

 Deze fout in Azure Machine Learning treedt op als het aantal geselecteerde kolommen kleiner is dan nodig is.  U ontvangt deze fout als het minimaal vereiste aantal kolommen niet is geselecteerd.  

**Oplossing:** Voeg extra kolommen toe aan de kolomselectie met behulp van **kolomkiezer**.  

|Uitzonderingsberichten|
|------------------------|
|Het aantal kolommen in kolomset is kleiner dan vereist.|
|Ten minste {required_columns_count} kolom(en) moet worden opgegeven voor invoerargument {arg_name}.|
|Ten minste {required_columns_count} kolom(en) moet worden opgegeven voor invoerargument {arg_name}. Het werkelijke aantal opgegeven kolommen is {input_columns_count}.|


## <a name="error-0032"></a>Fout 0032  
 Uitzondering treedt op als argument geen getal is.  

 U ontvangt deze fout in Azure Machine Learning als het argument een dubbele of NaN is.  

**Oplossing:** Wijzig het opgegeven argument om een geldige waarde te gebruiken.  

|Uitzonderingsberichten|
|------------------------|
|Argument is geen getal.|
|{arg_name}" is geen getal.|


## <a name="error-0033"></a>Fout 0033  
 Uitzondering treedt op als argument is Oneindigheid.  

 Deze fout in Azure Machine Learning treedt op als het argument oneindig is. U ontvangt deze fout als `double.NegativeInfinity` het `double.PositiveInfinity`argument is of .  

**Oplossing:** Wijzig het opgegeven argument als een geldige waarde.  

|Uitzonderingsberichten|
|------------------------|
|Het argument moet eindig zijn.|
|{arg_name}" is niet eindig.|


## <a name="error-0034"></a>Fout 0034  
 Uitzondering treedt op als er meer dan één beoordeling bestaat voor een bepaald gebruikersitempaar.  

 Deze fout in Azure Machine Learning treedt op in aanbeveling als een gebruikers-itempaar meer dan één classificatiewaarde heeft.  

**Oplossing:** Zorg ervoor dat het gebruikers-item paar slechts één beoordelingswaarde heeft.  

|Uitzonderingsberichten|
|------------------------|
|Er bestaat meer dan één beoordeling voor de waarde(s) in de gegevensset.|
|Meer dan één beoordeling voor gebruiker {gebruiker} en item {item} in de gegevenstabel voor beoordelingsvoorspelling.|
|Meer dan één beoordeling voor gebruiker {gebruiker} en item {item} in {dataset}.|


## <a name="error-0035"></a>Fout 0035  
 Uitzondering treedt op als er geen functies zijn opgegeven voor een bepaalde gebruiker of item.  

 Deze fout in Azure Machine Learning treedt op u probeert een aanbevelingsmodel te gebruiken om te scoren, maar een functievector kan niet worden gevonden.  

**Resolutie:**

De Matchbox-recommender heeft bepaalde vereisten waaraan moet worden voldaan bij het gebruik van itemfuncties of gebruikersfuncties.  Deze fout geeft aan dat een functievector ontbreekt voor een gebruiker of item dat u als invoer hebt opgegeven. Controleer of er een vector van functies beschikbaar is in de gegevens voor elke gebruiker of item.  

 Als u bijvoorbeeld een aanbevelingsmodel hebt getraind met functies zoals de leeftijd, locatie of inkomsten van de gebruiker, maar nu scores wilt maken voor nieuwe gebruikers die niet zijn gezien tijdens de training, moet u een aantal gelijkwaardige functies opgeven (namelijk leeftijd, locatie en inkomenswaarden) voor de nieuwe gebruikers om daarvoor passende voorspellingen te doen. 

 Als u geen functies voor deze gebruikers hebt, u functie-engineering overwegen om de juiste functies te genereren.  Als u bijvoorbeeld geen individuele gebruikersleeftijd of inkomenswaarden hebt, u geschatte waarden genereren die u voor een groep gebruikers moet gebruiken. 

<!--When you are scoring from a recommendation mode, you can use item or user features only if you previously used item or user features during training. For more information, see [Score Matchbox Recommender](score-matchbox-recommender.md).

For general information about how the Matchbox recommendation algorithm works, and how to prepare a dataset of item features or user features, see [Train Matchbox Recommender](train-matchbox-recommender.md).  -->

 > [!TIP]
 > Oplossing niet van toepassing op uw zaak? U bent van harte welkom om feedback te sturen over dit artikel en informatie te geven over het scenario, inclusief de module en het aantal rijen in de kolom. We zullen deze informatie gebruiken om in de toekomst meer gedetailleerde stappen voor het oplossen van problemen te bieden.

|Uitzonderingsberichten|
|------------------------|
|Er zijn geen functies verstrekt voor een vereiste gebruiker of item.|
|Functies voor {required_feature_name} vereist, maar niet geleverd.|


## <a name="error-0036"></a>Fout 0036  
 Er treedt een uitzondering op als er meerdere functievectoren zijn opgegeven voor een bepaalde gebruiker of item.  

 Deze fout in Azure Machine Learning treedt op als een functievector meerdere tijd wordt gedefinieerd.  

**Oplossing:** Zorg ervoor dat de functievector niet meer dan één keer wordt gedefinieerd.  

|Uitzonderingsberichten|
|------------------------|
|Functiedefinitie dupliceren voor een gebruiker of item.|


## <a name="error-0037"></a>Fout 0037  
 Er treedt een uitzondering op als meerdere labelkolommen zijn opgegeven en slechts één is toegestaan.  

 Deze fout in Azure Machine Learning treedt op als er meer dan één kolom is geselecteerd als de nieuwe labelkolom. De meeste algoritmen voor begeleid leren vereisen dat één kolom wordt gemarkeerd als doel of label.  

**Oplossing:** Selecteer één kolom als de nieuwe labelkolom.  

|Uitzonderingsberichten|
|------------------------|
|Er zijn meerdere labelkolommen opgegeven.|
|Meerdere labelkolommen worden opgegeven in {dataset_name}.|


## <a name="error-0039"></a>Fout 0039  
 Er treedt een uitzondering op als een bewerking is mislukt.  

 Deze fout in Azure Machine Learning treedt op wanneer een interne bewerking niet kan worden voltooid.  

**Oplossing:** Deze fout wordt veroorzaakt door vele voorwaarden en er is geen specifieke remedie.  
 De volgende tabel bevat algemene berichten voor deze fout, die worden gevolgd door een specifieke beschrijving van de voorwaarde. 

 Als er geen details beschikbaar zijn, [stuurt u feedback](https://social.msdn.microsoft.com/forums/azure/home?forum=MachineLearning) en geeft u informatie over de modules die de fout en gerelateerde voorwaarden hebben gegenereerd.

|Uitzonderingsberichten|
|------------------------|
|De bewerking is mislukt.|
|Fout tijdens het voltooien van de bewerking: {failed_operation}".|
|Fout tijdens het voltooien van de bewerking: {failed_operation}". Reden: "{reason}".|


## <a name="error-0042"></a>Fout 0042  
 Uitzondering treedt op wanneer het niet mogelijk is om kolom om te zetten naar een ander type.  

 Deze fout in Azure Machine Learning treedt op wanneer het niet mogelijk is om kolom om te zetten naar het opgegeven type.  U ontvangt deze fout als een module een bepaald gegevenstype vereist, zoals datumtijd, tekst, een zwevend puntnummer of geheel getal, maar het is niet mogelijk om een bestaande kolom om te zetten naar het vereiste type.  

U bijvoorbeeld een kolom selecteren en proberen deze om te zetten in een numeriek gegevenstype voor gebruik in een wiskundige bewerking, en deze fout opte vragen als de kolom ongeldige gegevens bevat. 

Een andere reden waarom u deze fout zou kunnen krijgen als u een kolom met zwevende puntennummers of veel unieke waarden als categorische kolom probeert te gebruiken. 

**Resolutie:**

+ Open de Help-pagina voor de module die de fout heeft gegenereerd en controleer de vereisten voor het gegevenstype.
+ Controleer de gegevenstypen van de kolommen in de invoergegevensset.
+ Controleer gegevens die afkomstig zijn uit zogenaamde schemaloze gegevensbronnen.
+ Controleer de gegevensset op ontbrekende waarden of speciale tekens die de conversie naar het gewenste gegevenstype kunnen blokkeren. 
    + Numerieke gegevenstypen moeten consistent zijn: controleer bijvoorbeeld op zwevende puntnummers in een kolom met gehele getallen.
    + Zoek naar teksttekenreeksen of NA-waarden in een getalkolom. 
    + Booleaanse waarden kunnen worden geconverteerd naar een geschikte weergave, afhankelijk van het vereiste gegevenstype.
    + Tekstkolommen onderzoeken op niet-unicodetekens, tabtekens of besturingselementtekens
    + Datumtijdgegevens moeten consistent zijn om modelleringsfouten te voorkomen, maar het opschonen kan complex zijn vanwege de vele indelingen. Overweeg om <!--the [Execute R Script](execute-r-script.md) or -->[Python Script-modules uitvoeren](execute-python-script.md) om opschonen uit te voeren.  
+ Wijzig indien nodig de waarden in de invoergegevensset, zodat de kolom met succes kan worden geconverteerd. Wijziging kan bestaan uit binning, truncation of afronding operaties, eliminatie van uitschieters, of toerekening van ontbrekende waarden. Zie de volgende artikelen voor enkele veelvoorkomende scenario's voor gegevenstransformatie in machine learning:
    + [Ontbrekende gegevens opschonen](clean-missing-data.md)
    + [Gegevens normaliseren](normalize-data.md)
<!--+ [Clip Values](clip-values.md) 
    + [Group Data Into Bins](group-data-into-bins.md)
  -->

> [!TIP]
> Oplossing onduidelijk, of niet van toepassing op uw zaak? U bent van harte welkom om feedback te sturen over dit artikel en informatie te geven over het scenario, inclusief de module en het gegevenstype van de kolom. We zullen deze informatie gebruiken om in de toekomst meer gedetailleerde stappen voor het oplossen van problemen te bieden.  

|Uitzonderingsberichten|
|------------------------|
|Niet toegestaan conversie.|
|Kan kolom van type {type1} niet converteren naar kolom van type {type2}.|
|Kan kolom {col_name1}" van type {type1} niet converteren naar kolom van type {type2}.|
|Kan kolom {col_name1}" van type {type1} niet converteren naar kolom {col_name2}" van type {type2}.|


## <a name="error-0044"></a>Fout 0044  
 Uitzondering treedt op wanneer het niet mogelijk is om elementtype kolom af te leiden van de bestaande waarden.  

 Deze fout in Azure Machine Learning treedt op wanneer het niet mogelijk is om het type kolom of kolommen in een gegevensset af te leiden. Dit gebeurt meestal bij het gelijktijdigmaken van twee of meer gegevenssets met verschillende elementtypen. Als Azure Machine Learning niet in staat is een gemeenschappelijk type te bepalen dat alle waarden in een kolom of kolommen kan weergeven zonder verlies van informatie, genereert dit deze fout.  

**Oplossing:** Zorg ervoor dat alle waarden in een bepaalde kolom in beide gegevenssets die worden gecombineerd, van hetzelfde type zijn (numeriek, Boolean, categorisch, tekenreeks, datum, enz.) of kunnen worden afgebonden aan hetzelfde type.  

|Uitzonderingsberichten|
|------------------------|
|Kan het elementtype van de kolom niet afleiden.|
|Kan elementtype niet afleiden voor kolom {column_name}- alle elementen zijn null-verwijzingen.|
|Kan geen elementtype afleiden voor kolom {column_name}" van gegevensset {dataset_name}- alle elementen zijn null-verwijzingen.|


## <a name="error-0045"></a>Fout 0045  
 Uitzondering treedt op wanneer het niet mogelijk is om een kolom te maken vanwege gemengde elementtypen in de bron.  

 Deze fout in Azure Machine Learning wordt geproduceerd wanneer de elementtypen van twee gegevenssets die worden gecombineerd, verschillend zijn.  

**Oplossing:** Zorg ervoor dat alle waarden in een bepaalde kolom in beide gegevenssets die worden gecombineerd van hetzelfde type zijn (numeriek, Boolean, categorisch, tekenreeks, datum, enz.).  

|Uitzonderingsberichten|
|------------------------|
|Kan geen kolom maken met gemengde elementtypen.|
|Kan geen kolom maken met id {column_id}" van gemengde elementtypen:<br />Type gegevens[{row_1}, {column_id}] is "{type_1}". <br />Type gegevens[{row_2}, {column_id}] is "{type_2}".|
|Kan geen kolom maken met id {column_id}" van gemengde elementtypen:<br />Typ in stuk {chunk_id_1} is "{type_1}". <br />Typ in stuk {chunk_id_2} is "{type_2}" met brokgrootte: {chunk_size}.|


## <a name="error-0046"></a>Fout 0046  
 Uitzondering treedt op wanneer het niet mogelijk is om directory te maken op opgegeven pad.  

 Deze fout in Azure Machine Learning treedt op wanneer het niet mogelijk is om een map op het opgegeven pad te maken. U ontvangt deze fout als een deel van het pad naar de uitvoermap voor een Hive Query onjuist of ontoegankelijk is.  

**Oplossing:** Ga opnieuw naar de module en controleer of het mappad correct is opgemaakt en of het toegankelijk is met de huidige referenties.  

|Uitzonderingsberichten|
|------------------------|
|Geef een geldige uitvoermap op.|
|Map: {path} kan niet worden gemaakt. Geef een geldig pad op.|


## <a name="error-0047"></a>Fout 0047  
 Er treedt een uitzondering op als het aantal functiekolommen in sommige gegevenssets dat naar de module wordt doorgegeven, te klein is.  

 Deze fout in Azure Machine Learning treedt op als de invoergegevensset voor training niet het minimumaantal kolommen bevat dat door het algoritme vereist is. Meestal is de gegevensset leeg of bevat alleen trainingskolommen.  

**Oplossing:** Ga opnieuw naar de invoergegevensset om ervoor te zorgen dat er een of meer extra kolommen zijn, afgezien van de labelkolom.  

|Uitzonderingsberichten|
|------------------------|
|Het aantal functiekolommen in de invoergegevensset is minder dan het toegestane minimum.|
|Het aantal functiekolommen in de invoergegevensset is kleiner dan het toegestane minimum van {required_columns_count} kolom(en).|
|Aantal functiekolommen in de invoergegevensset {arg_name} is minder dan toegestaan minimum van {required_columns_count} kolom(en).|


## <a name="error-0048"></a>Fout 0048  
 Uitzondering treedt op in het geval dat het niet mogelijk is om een bestand te openen.  

 Deze fout in Azure Machine Learning treedt op wanneer het niet mogelijk is om een bestand te openen om te lezen of te schrijven. Mogelijk ontvangt u deze fout om de volgende redenen:  

-   De container of het bestand (blob) bestaat niet  
  
-   Het toegangsniveau van het bestand of de container geeft u geen toegang tot het bestand  
  
-   Het bestand is te groot om te lezen of de verkeerde indeling  

**Oplossing:** Bekijk opnieuw de module en het bestand dat u probeert te lezen.  

 Controleer of de namen van de container en het bestand correct zijn.  

 Gebruik de klassieke Azure-portal of een Azure-opslaghulpprogramma om te controleren of u toestemming hebt om toegang te krijgen tot het bestand.  

  <!--If you are trying to read an image file, make sure that it meets the requirements for image files in terms of size, number of pixels, and so forth. For more information, see [Import Images](import-images.md).  -->

|Uitzonderingsberichten|
|------------------------|
|Kan een bestand niet openen.|
|Fout bij het openen van het bestand: {file_name}.|
|Fout bij het openen van het bestand: {file_name}. Waarschuwing voor opslaguitzondering: {uitzondering}.|


## <a name="error-0049"></a>Fout 0049  
 Uitzondering treedt op in het geval dat het niet mogelijk is om een bestand te ontschepen.  

 Deze fout in Azure Machine Learning treedt op wanneer het niet mogelijk is om een bestand te ontwenen. Deze fout wordt weergegeven als de bestandsindeling die is geselecteerd in de module [Gegevens importeren](import-data.md) niet overeenkomt met de werkelijke indeling van het bestand of als het bestand een onherkenbaar teken bevat.  

**Oplossing:** Ga opnieuw naar de module en corrigeer de selectie van de bestandsindeling als deze niet overeenkomt met de indeling van het bestand. Indien mogelijk, inspecteer het bestand om te bevestigen dat het geen illegale tekens bevat.  

|Uitzonderingsberichten|
|------------------------|
|Kan een bestand niet ontsmetten.|
|Fout tijdens het ontwijsmaken van het {file_format}-bestand.|
|Fout tijdens het ontwijsmaken van het {file_format}-bestand: {file_name}.|
|Fout tijdens het ontwijsmaken van het {file_format}-bestand. Reden: {failure_reason}.|
|Fout tijdens het ontwijsmaken van het {file_format}-bestand: {file_name}. Reden: {failure_reason}.|


## <a name="error-0052"></a>Fout 0052  
 Er treedt een uitzondering op als de azure-opslagaccountsleutel onjuist is opgegeven.  

 Deze fout in Azure Machine Learning treedt op als de sleutel die wordt gebruikt om toegang te krijgen tot het Azure-opslagaccount onjuist is. U deze fout bijvoorbeeld zien als de Azure-opslagsleutel is afgekapt wanneer deze is gekopieerd en geplakt of als de verkeerde sleutel is gebruikt.  

 Zie [Opslagtoegangssleutels weergeven, kopiëren en regenereren](https://azure.microsoft.com/documentation/articles/storage-create-storage-account-classic-portal/)voor meer informatie over het ophalen van de sleutel voor een Azure-opslagaccount.  

**Oplossing:** Ga opnieuw naar de module en controleer of de Azure-opslagsleutel correct is voor het account. kopieer de sleutel indien nodig opnieuw van de klassieke Azure-portal.  

|Uitzonderingsberichten|
|------------------------|
|De Azure-opslagaccountsleutel is onjuist.|


## <a name="error-0053"></a>Fout 0053  
 Uitzondering treedt op in het geval dat er geen gebruikersfuncties of items zijn voor aanbevelingen voor luciferdoosjes.  

 Deze fout in Azure Machine Learning wordt geproduceerd wanneer een functievector niet kan worden gevonden.  

**Oplossing:** Controleer of er een functievector aanwezig is in de invoergegevensset.  

|Uitzonderingsberichten|
|------------------------|
|Gebruikersfuncties of/of items zijn vereist, maar niet verstrekt.|


## <a name="error-0056"></a>Fout 0056  
 Er treedt een uitzondering op als de kolommen die u voor een bewerking hebt geselecteerd, in strijd zijn met de vereisten.  

 Deze fout in Azure Machine Learning treedt op wanneer u kolommen kiest voor een bewerking waarvoor de kolom van een bepaald gegevenstype moet zijn. 

 Deze fout kan ook optreden als de kolom het juiste gegevenstype is, maar de module die u gebruikt, vereist dat de kolom ook wordt gemarkeerd als een functie, label of categorische kolom.  

  <!--For example, the [Convert to Indicator Values](convert-to-indicator-values.md) module requires that columns be categorical, and will raise this error if you select a feature column or label column.  -->

**Resolutie:**

1.  Controleer het gegevenstype van de kolommen die momenteel zijn geselecteerd. 

2. Controleer of de geselecteerde kolommen categorisch, label of functiekolommen zijn.  
  
3.  Bekijk het helponderwerp voor de module waarin u de kolomselectie hebt gemaakt, om te bepalen of er specifieke vereisten zijn voor gegevenstype of kolomgebruik.  
  
3.  Gebruik [Metagegevens bewerken](edit-metadata.md) om het kolomtype te wijzigen voor de duur van deze bewerking. Zorg ervoor dat u het kolomtype terugwijzigt in de oorspronkelijke waarde, met behulp van een ander exemplaar van [Metagegevens bewerken,](edit-metadata.md)als u het nodig hebt voor downstreambewerkingen.  

|Uitzonderingsberichten|
|------------------------|
|Een of meer geselecteerde kolommen bevonden zich niet in een toegestane categorie.|
|Kolom met naam "{col_name}" valt niet in een toegestane categorie.|


## <a name="error-0057"></a>Fout 0057  
 Uitzondering treedt op wanneer wordt geprobeerd een bestand of blob te maken dat al bestaat.  

 Deze uitzondering treedt op wanneer u de module [Gegevens exporteren](export-data.md) of een andere module gebruikt om resultaten van een pijplijn op te slaan in Azure Machine Learning naar Azure blob-opslag, maar u probeert een bestand of blob te maken dat al bestaat.   

**Resolutie:**

 U ontvangt deze fout alleen als u de schrijfmodus voor **Azure Blob-opslag** eerder instelt op **Fout**. Door het ontwerp, deze module verhoogt een fout als u probeert om een gegevensset te schrijven naar een blob die al bestaat.

 - Open de module-eigenschappen en wijzig de eigenschappen **azure blob-opslagschrijfmodus** in **Overschrijven**.
 - U ook de naam van een andere doelblob of -bestand typen en een blob opgeven die nog niet bestaat.  

|Uitzonderingsberichten|
|------------------------|
|Bestand of Blob bestaat al.|
|Bestand of Blob "{file_path}" bestaat al.|


## <a name="error-0058"></a>Fout 0058  
 Deze fout in Azure Machine Learning treedt op als de gegevensset de verwachte labelkolom niet bevat.  

 Deze uitzondering kan ook optreden wanneer de opgegeven labelkolom niet overeenkomt met de gegevens of het gegevenstype dat door de leerling wordt verwacht of de verkeerde waarden heeft. Deze uitzondering wordt bijvoorbeeld gemaakt bij het gebruik van een labelkolom met reële waarde bij het trainen van een binaire classificatie.  

**Oplossing:** De resolutie is afhankelijk van de leerling of trainer die u gebruikt en de gegevenstypen van de kolommen in uw gegevensset. Controleer eerst de vereisten van het machine learning-algoritme of trainingsmodule.  

 Ga opnieuw naar de invoergegevensset. Controleer of de kolom die u verwacht te worden behandeld als het label het juiste gegevenstype heeft voor het model dat u maakt.  

 Controleer ingangen op ontbrekende waarden en elimineer of vervang ze indien nodig.  

 Voeg indien nodig de module [Metagegevens bewerken](edit-metadata.md) toe en zorg ervoor dat de labelkolom als label is gemarkeerd.  

|Uitzonderingsberichten|
|------------------------|
|De waarden van de labelkolom en de waarden van de puntenkolom zijn niet vergelijkbaar.|
|De labelkolom is niet zoals verwacht in {dataset_name}.|
|De labelkolom is niet zoals verwacht in {dataset_name}, {reden}.|
|De labelkolom {column_name} wordt niet verwacht in {dataset_name}.|
|De labelkolom {column_name} wordt niet verwacht in {dataset_name}, {reden}.|


## <a name="error-0059"></a>Fout 0059  
 Er treedt een uitzondering op als een kolomindex die is opgegeven in een kolomkiezer niet kan worden ontleed.  

 Deze fout in Azure Machine Learning treedt op als een kolomindex die is opgegeven bij het gebruik van de kolomkiezer niet kan worden ontleed.  U ontvangt deze fout wanneer de kolomindex zich in een ongeldige indeling bevindt die niet kan worden ontleed.  

**Oplossing:** Wijzig de kolomindex om een geldige indexwaarde te gebruiken.  

|Uitzonderingsberichten|
|------------------------|
|Een of meer opgegeven kolomindexen of indexbereiken kunnen niet worden ontleed.|
|Kolomindex of bereik "{column_index_or_range}" kan niet worden ontleed.|


## <a name="error-0060"></a>Fout 0060  
 Er treedt een uitzondering op wanneer een bereik kolombereik is opgegeven in een kolomkiezer.  

 Deze fout in Azure Machine Learning treedt op wanneer een kolombereik buiten bereik is opgegeven in de kolomkiezer. U ontvangt deze fout als het kolombereik in de kolomkiezer niet overeenkomt met de kolommen in de gegevensset.  

**Oplossing:** Wijzig het kolombereik in de kolomkiezer om overeen te komen met de kolommen in de gegevensset.  

|Uitzonderingsberichten|
|------------------------|
|Ongeldig of buiten het bereik van de kolomindexbereik opgegeven.|
|Kolombereik "{column_range}" is ongeldig of buiten bereik.|


## <a name="error-0061"></a>Fout 0061  
 Uitzondering treedt op wanneer u een rij probeert toe te voegen aan een gegevenstabel met een ander aantal kolommen dan de tabel.  

 Deze fout in Azure Machine Learning treedt op wanneer u een rij probeert toe te voegen aan een gegevensset met een ander aantal kolommen dan de gegevensset.  U ontvangt deze fout als de rij die aan de gegevensset wordt toegevoegd, een ander aantal kolommen heeft dan de invoergegevensset.  De rij kan niet aan de gegevensset worden toegevoegd als het aantal kolommen anders is.  

**Oplossing:** Wijzig de invoergegevensset om hetzelfde aantal kolommen te hebben als de rij is toegevoegd of wijzig de rij die is toegevoegd om hetzelfde aantal kolommen te hebben als de gegevensset.  

|Uitzonderingsberichten|
|------------------------|
|Alle tabellen moeten hetzelfde aantal kolommen hebben.|
|Kolommen in brok {chunk_id_1} is anders met chunk "{chunk_id_2}" met brokgrootte: {chunk_size}.|
|Het aantal kolomen in bestand {filename_1}" (count={column_count_1}) is anders met bestand {filename_2}( count={column_count_2}).|


## <a name="error-0062"></a>Fout 0062  
 Uitzondering treedt op wanneer u twee modellen probeert te vergelijken met verschillende leerlingtypen.  

 Deze fout in Azure Machine Learning wordt geproduceerd wanneer evaluatiestatistieken voor twee verschillende gescoorde gegevenssets niet kunnen worden vergeleken. In dit geval is het niet mogelijk om de effectiviteit van de modellen die worden gebruikt om de twee gescoorde datasets te produceren te vergelijken.  

**Oplossing:** Controleer of de gescoorde resultaten worden geproduceerd door hetzelfde soort machine learning-model (binaire classificatie, regressie, classificatie van meerdere klassen, aanbeveling, clustering, anomaliedetectie, enz.) Alle modellen die u vergelijkt, moeten hetzelfde leerlingtype hebben.  

|Uitzonderingsberichten|
|------------------------|
|Alle modellen moeten hetzelfde leerlingtype hebben.|
|Heb je een onverenigbaar leerlingtype: "{actual_learner_type}". Verwachte leerlingtypen zijn: "{expected_learner_type_list}".|


## <a name="error-0064"></a>Fout 0064  
 Er treedt een uitzondering op als de naam of opslagsleutel van azure-opslagaccount onjuist is opgegeven.  

 Deze fout in Azure Machine Learning treedt op als de naam of opslagsleutel van het Azure-opslagaccount onjuist is opgegeven. U ontvangt deze fout als u een onjuiste accountnaam of wachtwoord invoert voor het opslagaccount. Dit kan gebeuren als u handmatig de accountnaam of het wachtwoord invoert. Het kan ook voorkomen als het account is verwijderd.  

**Oplossing:** Controleer of de accountnaam en het wachtwoord correct zijn ingevoerd en of het account bestaat.  

|Uitzonderingsberichten|
|------------------------|
|De naam of opslagsleutel van het Azure-opslagaccount is onjuist.|
|De naam van het Azure-opslagaccount {account_name} of de opslagsleutel voor de accountnaam is onjuist.|


## <a name="error-0065"></a>Fout 0065  
 Uitzondering treedt op als de naam azure blob onjuist is opgegeven.  

 Deze fout in Azure Machine Learning treedt op als de azure blob-naam onjuist is opgegeven.  U ontvangt de foutmelding als:  

-   De blob kan niet worden gevonden in de opgegeven container.  

 <!---   The fully qualified name of the blob specified for output in one of the [Learning with Counts](data-transformation-learning-with-counts.md) modules is greater than 512 characters.  -->

-   Alleen de container is opgegeven als bron in een aanvraag [voor gegevens importeren](import-data.md) wanneer de indeling Excel of CSV met codering was. samenvoeging van de inhoud van alle blobs in een container is niet toegestaan met deze indelingen.  
  
-   Een SAS URI bevat niet de naam van een geldige blob.  

**Oplossing:** Opnieuw de module gooien van de uitzondering. Controleer of de opgegeven blob wel bestaat in de container in het opslagaccount en dat met machtigingen de blob worden zien. Controleer of de invoer van de **formuliercontainernaam/bestandsnaam** is als u Excel of CSV hebt met coderingsindelingen. Controleer of een SAS URI de naam van een geldige blob bevat.  

|Uitzonderingsberichten|
|------------------------|
|De naam van de Azure-opslagblob is onjuist.|
|De naam '{blob_name}' van azure-opslagblob is onjuist.|
|De naam azure-opslagblob met voorvoegsel {blob_name_prefix} bestaat niet.|
|Kan geen Azure-opslagblobs vinden onder container {container_name}.|
|Kan geen Azure-opslagblobs met wildcardpad "{blob_wildcard_path} vinden.|


## <a name="error-0066"></a>Fout 0066  
 Er treedt een uitzondering op als een bron niet kan worden geüpload naar een Azure Blob.  

 Deze fout in Azure Machine Learning treedt op als een bron niet kan worden geüpload naar een Azure Blob.  <!--You will receive this message if [Train Vowpal Wabbit 7-4 Model](train-vowpal-wabbit-version-7-4-model.md) encounters an error attempting to save either the model or the hash created when training the model.--> Beide worden opgeslagen in hetzelfde Azure-opslagaccount als het account met het invoerbestand.  

**Oplossing:** Bekijk de module opnieuw. Controleer of de naam, opslagsleutel en container van het Azure-account correct zijn en of het account toestemming heeft om naar de container te schrijven.  

|Uitzonderingsberichten|
|------------------------|
|De bron kan niet worden geüpload naar Azure-opslag.|
|Het bestand {source_path} kan niet worden geüpload naar Azure-opslag als {dest_path}.|


## <a name="error-0067"></a>Fout 0067  
 Uitzondering treedt op als een gegevensset een ander aantal kolommen heeft dan verwacht.  

 Deze fout in Azure Machine Learning treedt op als een gegevensset een ander aantal kolommen heeft dan verwacht.  U ontvangt deze fout wanneer het aantal kolommen in de gegevensset verschilt van het aantal kolommen dat de module verwacht tijdens de uitvoering.  

**Oplossing:** Wijzig de invoergegevensset of de parameters.  

|Uitzonderingsberichten|
|------------------------|
|Onverwacht aantal kolommen in de gegevenstabel.|
|Onverwacht aantal kolommen in de gegevensset {dataset_name}.|
|Verwachte kolom(en) {expected_column_count}, maar in plaats daarvan de kolom {actual_column_count}.'.|
|In de invoergegevensset {dataset_name} wordt de kolom {expected_column_count},, maar in plaats daarvan de kolom {actual_column_count}gevonden.|


## <a name="error-0068"></a>Fout 0068  
 Uitzondering treedt op als het opgegeven Hive-script niet correct is.  

 Deze fout in Azure Machine Learning treedt op als er syntaxisfouten zijn in een Hive QL-script of als de Hive-tolk een fout tegenkomt tijdens het uitvoeren van de query of het script.  

**Resolutie:**

Het foutbericht van Hive wordt normaal gesproken gerapporteerd in het foutlogboek, zodat u actie ondernemen op basis van de specifieke fout. 

+ Open de module en controleer de query op fouten.  
+ Controleer of de query correct werkt buiten Azure Machine Learning door u aan te melden bij de Hive-console van uw Hadoop-cluster en de query uit te voeren.  
+ Probeer opmerkingen in je Hive-script in een aparte regel te plaatsen in plaats van uitvoerbare instructies en opmerkingen in één regel te mengen.  

### <a name="resources"></a>Resources

Zie de volgende artikelen voor hulp bij Hive-query's voor machine learning:

+ [Hive-tabellen maken en gegevens laden vanuit Azure Blob Storage](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-move-hive-tables)
+ [Gegevens in tabellen met Hive-query's verkennen](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-explore-data-hive-tables)
+ [Met Hive-query’s functies maken voor gegevens in een Hadoop-cluster](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-create-features-hive)
+ [Hive voor SQL-gebruikers Cheat Sheet (PDF)](http://hortonworks.com/wp-content/uploads/2013/05/hql_cheat_sheet.pdf)

  
|Uitzonderingsberichten|
|------------------------|
|Hive script is onjuist.|


## <a name="error-0069"></a>Fout 0069  
 Uitzondering treedt op als het opgegeven SQL-script niet correct is.  

 Deze fout in Azure Machine Learning treedt op als het opgegeven SQL-script syntaxisproblemen heeft of als de kolommen of tabel die in het script zijn opgegeven, niet geldig zijn. 

 U ontvangt deze fout als de SQL-engine een fout tegenkomt tijdens het uitvoeren van de query of het script. Het SQL-foutbericht wordt normaal gesproken gerapporteerd in het foutlogboek, zodat u actie ondernemen op basis van de specifieke fout.  

**Oplossing:** Bekijk de module opnieuw en inspecteer de SQL-query op fouten.  

 Controleer of de query correct werkt buiten Azure ML door u rechtstreeks aan te melden bij de databaseserver en de query uit te voeren.  

 Als er een SQL-gegenereerd bericht wordt gerapporteerd door de moduleuitzondering, onderneemt u actie op basis van de gerapporteerde fout. De foutberichten bevatten bijvoorbeeld soms specifieke richtlijnen voor de waarschijnlijke fout:
+ *Geen dergelijke kolom of ontbrekende database*, wat aangeeft dat u mogelijk een kolomnaam verkeerd hebt getypt. Als u zeker weet dat de kolomnaam juist is, probeert u haakjes of aanhalingstekens te gebruiken om de kolom-id in te voegen.
+ *SQL-logicafout \<in\>de buurt van SQL-trefwoord*, wat aangeeft dat u mogelijk een syntaxisfout hebt voordat het opgegeven trefwoord

  
|Uitzonderingsberichten|
|------------------------|
|SQL-script is onjuist.|
|SQL-query "{sql_query}" is niet correct.|
|SQL-query "{sql_query}" is niet correct. Uitzonderingsbericht: {exception}.|


## <a name="error-0070"></a>Fout 0070  
 Uitzondering treedt op wanneer wordt geprobeerd toegang te krijgen tot niet-bestaande Azure-tabel.  

 Deze fout in Azure Machine Learning treedt op wanneer u probeert toegang te krijgen tot een niet-bestaande Azure-tabel. U ontvangt deze fout als u een tabel opgeeft in Azure-opslag, die niet bestaat bij het lezen van of het schrijven naar Azure Table Storage. Dit kan gebeuren als u de naam van de gewenste tabel verkeerd typt of als u een mismatch hebt tussen de doelnaam en het opslagtype. U was bijvoorbeeld van plan om uit een tabel te lezen, maar hebt in plaats daarvan de naam van een blob ingevoerd.  

**Oplossing:** Ga opnieuw naar de module om te controleren of de naam van de tabel juist is.  

|Uitzonderingsberichten|
|------------------------|
|Azure-tabel bestaat niet.|
|Azure-tabel {table_name} bestaat niet.|


## <a name="error-0072"></a>Fout 0072  
 Uitzondering treedt op in het geval van een time-out van de verbinding.  

 Deze fout in Azure Machine Learning treedt op wanneer een verbinding een time-out heeft. U ontvangt deze fout als er momenteel verbindingsproblemen zijn met de gegevensbron of -bestemming, zoals trage internetverbinding, of als de gegevensset groot is en/of de SQL-query die moet worden gelezen in de gegevens gecompliceerde verwerking uitvoert.  

**Oplossing:** Bepaal of er momenteel problemen zijn met langzame verbindingen met Azure-opslag of internet.  

|Uitzonderingsberichten|
|------------------------|
|Er is een time-out van de verbinding opgetreden.|


## <a name="error-0073"></a>Fout 0073  
 Uitzondering treedt op als er een fout optreedt tijdens het converteren van een kolom naar een ander type.  

 Deze fout in Azure Machine Learning treedt op wanneer het niet mogelijk is om kolom om te zetten naar een ander type.  U ontvangt deze fout als een module een bepaald type vereist en het niet mogelijk is om de kolom om te zetten naar het nieuwe type.  

**Oplossing:** Wijzig de invoergegevensset zodat de kolom kan worden geconverteerd op basis van de binnenste uitzondering.  

|Uitzonderingsberichten|
|------------------------|
|Kan kolom niet converteren.|
|Kan kolom niet converteren naar {target_type}.|


## <a name="error-0075"></a>Fout 0075  
Uitzondering treedt op wanneer een ongeldige binning-functie wordt gebruikt bij het kwantificeren van een gegevensset.  

Deze fout in Azure Machine Learning treedt op wanneer u gegevens probeert op te nemen met een niet-ondersteunde methode of wanneer de parametercombinaties ongeldig zijn.  

**Resolutie:**

Foutafhandeling voor deze gebeurtenis is geïntroduceerd in een eerdere versie van Azure Machine Learning, waardoor meer aanpassing van binning-methoden mogelijk werd. Momenteel zijn alle binning methoden zijn gebaseerd op een selectie uit een vervolgkeuzelijst, dus technisch gezien moet het niet langer mogelijk zijn om deze fout te krijgen.

 <!--If you get this error when using the [Group Data into Bins](group-data-into-bins.md) module, consider reporting the issue in the [Azure Machine Learning forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=MachineLearning), providing the data types, parameter settings, and the exact error message.  -->

|Uitzonderingsberichten|
|------------------------|
|Ongeldige binningfunctie gebruikt.|


## <a name="error-0077"></a>Fout 0077  
 Uitzondering treedt op wanneer de schrijfmodus voor onbekende blobbestanden is verstreken.  

 Deze fout in Azure Machine Learning treedt op als een ongeldig argument wordt doorgegeven in de specificaties voor een blob-bestandsbestemming of -bron.  

**Oplossing:** In bijna alle modules die gegevens importeren of exporteren van en naar Azure blob-opslag, worden parameterwaarden die de schrijfmodus beheren, toegewezen met behulp van een vervolgkeuzelijst. Daarom is het niet mogelijk om een ongeldige waarde door te geven en deze fout mag niet worden weergegeven. Deze fout wordt afgeschaft in een latere release.  

|Uitzonderingsberichten|
|------------------------|
|Niet-ondersteunde schrijfmodus voor blobs.|
|Niet-ondersteunde schrijfmodus voor blobs: {blob_write_mode}.|


## <a name="error-0078"></a>Fout 0078  
 Er treedt een uitzondering op wanneer de HTTP-optie voor [Gegevens importeren](import-data.md) een 3xx-statuscode ontvangt die omleiding aangeeft.  

 Deze fout in Azure Machine Learning treedt op wanneer de HTTP-optie voor [Gegevens importeren](import-data.md) een statuscode van 3xx (301, 302, 304, enz.) ontvangt die omleiding aangeeft. U ontvangt deze fout als u probeert verbinding te maken met een HTTP-bron die de browser doorverwijst naar een andere pagina. Om veiligheidsredenen is het omleiden van websites niet toegestaan als gegevensbronnen voor Azure Machine Learning.  

**Oplossing:** Als de website een vertrouwde website is, voert u de doorgestuurde URL rechtstreeks in.  

|Uitzonderingsberichten|
|------------------------|
|Http omleiding niet toegestaan.|


## <a name="error-0079"></a>Fout 0079  
 Er treedt een uitzondering op als de naam van de Azure-opslagcontainer onjuist is opgegeven.  

 Deze fout in Azure Machine Learning treedt op als de naam van de Azure-opslagcontainer onjuist is opgegeven. U ontvangt deze fout als u niet zowel de container als de blobnaam (bestands) hebt opgegeven met de optie **Pad naar blob, te beginnen met de containeroptie** bij het schrijven naar Azure Blob Storage.  

**Oplossing:** Ga opnieuw naar de module [Gegevens exporteren](export-data.md) en controleer of het opgegeven pad naar de blob zowel de container als de bestandsnaam bevat in de **indelingscontainer/bestandsnaam**.  

|Uitzonderingsberichten|
|------------------------|
|De naam van de Azure-opslagcontainer is onjuist.|
|De naam van de Azure-opslagcontainer {container_name} is onjuist. er werd een containernaam van de opmaakcontainer/blob verwacht.|


## <a name="error-0080"></a>Fout 0080  
 Uitzondering treedt op wanneer kolom met alle ontbrekende waarden niet per module is toegestaan.  

 Deze fout in Azure Machine Learning wordt geproduceerd wanneer een of meer kolommen die door de module worden verbruikt, alle ontbrekende waarden bevatten. Als een module bijvoorbeeld geaggregeerde statistieken voor elke kolom ingegevens verwerkt, kan deze niet werken op een kolom die geen gegevens bevat. In dergelijke gevallen wordt de uitvoering van de module met deze uitzondering stopgezet.  

**Oplossing:** Ga opnieuw naar de invoergegevensset en verwijder kolommen die alle ontbrekende waarden bevatten.  

|Uitzonderingsberichten|
|------------------------|
|Kolommen met alle ontbrekende waarden zijn niet toegestaan.|
|Kolom {col_index_or_name} heeft alle waarden ontbreken.|


## <a name="error-0081"></a>Fout 0081  
 Uitzondering treedt op in de PCA-module als het aantal te verminderen dimensies gelijk is aan het aantal functiekolommen in de invoergegevensset, die ten minste één schaarse functiekolom bevatten.  

 Deze fout in Azure Machine Learning wordt geproduceerd als aan de volgende voorwaarden is voldaan: (a) de invoergegevensset heeft ten minste één schaarse kolom en (b) het uiteindelijke aantal gevraagde dimensies is hetzelfde als het aantal invoerdimensies.  

**Oplossing:** Overweeg het aantal dimensies in de uitvoer te verminderen tot minder dan het aantal dimensies in de invoer. Dit is typisch in toepassingen van PCA.   <!--For more information, see [Principal Component Analysis](principal-component-analysis.md).  -->

|Uitzonderingsberichten|
|------------------------|
|Voor gegevensset met schaarse functiekolommen moet het aantal afmetingen kleiner zijn dan het aantal functiekolommen.|


## <a name="error-0082"></a>Fout 0082  
 Uitzondering treedt op wanneer een model niet kan worden gedeserialiseerd.  

 Deze fout in Azure Machine Learning treedt op wanneer een opgeslagen machine learning-model of -transformatie niet kan worden geladen door een nieuwere versie van de runtime azure machine learning als gevolg van een baanbrekende wijziging.  

**Oplossing:** De trainingspijplijn die het model of de transformatie heeft geproduceerd, moet opnieuw worden uitgevoerd en het model of de transformatie moet opnieuw worden opgeslagen.  

|Uitzonderingsberichten|
|------------------------|
|Model kan niet worden gedeserialiseerd omdat het waarschijnlijk geserialiseerd met een oudere serialisatie-indeling. Gelieve het model opnieuw te trainen en opnieuw op te slaan.|


## <a name="error-0083"></a>Fout 0083  
 Er treedt een uitzondering op als de gegevensset die voor training wordt gebruikt, niet kan worden gebruikt voor een concreet type leerling.  

 Deze fout in Azure Machine Learning wordt geproduceerd wanneer de gegevensset niet compatibel is met de leerling die wordt opgeleid. De gegevensset kan bijvoorbeeld in elke rij ten minste één ontbrekende waarde bevatten en als gevolg daarvan wordt de hele gegevensset overgeslagen tijdens de training. In andere gevallen verwachten sommige machine learning-algoritmen, zoals anomaliedetectie, niet dat labels aanwezig zijn en kunnen deze uitzondering worden gemaakt als er labels in de gegevensset aanwezig zijn.  

**Oplossing:** Raadpleeg de documentatie van de leerling die wordt gebruikt om de vereisten voor de invoergegevensset te controleren. Controleer de kolommen om te zien dat alle vereiste kolommen aanwezig zijn.  

|Uitzonderingsberichten|
|------------------------|
|De gegevensset die wordt gebruikt voor training is ongeldig.|
|{data_name} bevat ongeldige gegevens voor training.|
|{data_name} bevat ongeldige gegevens voor training. Leerlingtype: {learner_type}.|
|{data_name} bevat ongeldige gegevens voor training. Leerlingtype: {learner_type}. Reden: {reason}.|
|Kan geen actie {action_name}toepassen op trainingsgegevens {data_name}. Reden: {reason}.|


## <a name="error-0084"></a>Fout 0084  
 Uitzondering treedt op wanneer scores die zijn geproduceerd uit een R Script worden geëvalueerd. Dit wordt momenteel niet ondersteund.  

 Deze fout in Azure Machine Learning treedt op als u een van de modules probeert te gebruiken voor het evalueren van een model met uitvoer uit een R-script dat scores bevat.  

**Resolutie:**

|Uitzonderingsberichten|
|------------------------|
|Het evalueren van scores geproduceerd door Custom Model wordt momenteel niet ondersteund.|


## <a name="error-0085"></a>Fout 0085  
 Uitzondering treedt op wanneer de beoordeling van het script mislukt met een fout.  

 Deze fout in Azure Machine Learning treedt op wanneer u een aangepast script uitvoert dat syntaxisfouten bevat.  

**Oplossing:** Controleer uw code in een externe editor en controleer op fouten.  

|Uitzonderingsberichten|
|------------------------|
|Fout tijdens de evaluatie van het script.|
|De volgende fout is opgetreden tijdens de evaluatie van het script, bekijk het uitvoerlogboek voor meer informatie:<br />---------- Begin van het foutbericht van ---------- {script_language}<br />{bericht}<br />---------- Foutbericht beëindigen van de tolk {script_language} ----------|


## <a name="error-0090"></a>Fout 0090  
 Uitzondering treedt op wanneer het maken van hive-tabel mislukt.  

 Deze fout in Azure Machine Learning treedt op wanneer u [Exportgegevens](export-data.md) of een andere optie gebruikt om gegevens op te slaan in een HDInsight-cluster en de opgegeven Hive-tabel kan niet worden gemaakt.  

**Oplossing:** Controleer de naam van het Azure-opslagaccount dat aan het cluster is gekoppeld en controleer of u hetzelfde account gebruikt in de module-eigenschappen.  

|Uitzonderingsberichten|
|------------------------|
|De Hive-tabel kan niet worden gemaakt. Controleer voor een HDInsight-cluster of de azure-opslagaccountnaam die aan het cluster is gekoppeld, dezelfde is als wat via de parameter module wordt doorgegeven.|
|De Hive-tabel {table_name} kan niet worden gemaakt. Controleer voor een HDInsight-cluster of de azure-opslagaccountnaam die aan het cluster is gekoppeld, dezelfde is als wat via de parameter module wordt doorgegeven.|
|De Hive-tabel {table_name} kan niet worden gemaakt. Controleer voor een HDInsight-cluster of de naam van het Azure-opslagaccount dat aan het cluster is gekoppeld, {cluster_name} is.|


## <a name="error-0102"></a>Fout 0102  
 Gegooid wanneer een ZIP-bestand niet kan worden geëxtraheerd.  

 Deze fout in Azure Machine Learning treedt op wanneer u een pakket met rits importeert met de .zip-extensie, maar het pakket is geen zip-bestand of het bestand maakt geen gebruik van een ondersteunde zip-indeling.  

**Oplossing:** Zorg ervoor dat het geselecteerde bestand een geldig .zip-bestand is en dat het is gecomprimeerd met een van de ondersteunde compressiealgoritmen.  

 Als u deze fout opneemt bij het importeren van gegevenssets in gecomprimeerde indeling, controleert u of alle opgenomen bestanden een van de ondersteunde bestandsindelingen gebruiken en in unicode-indeling zijn.  <!--For more information, see [Unpack Zipped Datasets](unpack-zipped-datasets.md).  -->

 Probeer de gewenste bestanden te lezen in een nieuwe gecomprimeerde map en probeer de aangepaste module opnieuw toe te voegen.  

|Uitzonderingsberichten|
|------------------------|
|Gegeven ZIP-bestand is niet in de juiste indeling.|


## <a name="error-0105"></a>Fout 0105  
 Deze fout wordt weergegeven wanneer een moduledefinitiebestand een niet-ondersteund parametertype bevat  
  
 Deze fout in Azure Machine Learning wordt geproduceerd wanneer u een xml-definitie voor aangepaste modules maakt en het type parameter of argument in de definitie niet overeenkomt met een ondersteund type.  
  
**Oplossing:** Zorg ervoor dat de eigenschap Type van een **arg-element** in het xml-definitiebestand van de aangepaste module een ondersteund type is.  
  
|Uitzonderingsberichten|  
|------------------------|  
|Niet-ondersteunde parametertype.|  
|Niet-ondersteunde parametertype{0}' ' opgegeven.|  


## <a name="error-0107"></a>Fout 0107  
 Gegooid wanneer een moduledefinitiebestand een niet-ondersteund uitvoertype definieert  
  
 Deze fout in Azure Machine Learning wordt geproduceerd wanneer het type uitvoerpoort in een aangepaste xml-definitie van een aangepaste module niet overeenkomt met een ondersteund type.  
  
**Oplossing:** Controleer of de eigenschap type van een element Uitvoer in het xml-definitiebestand van de aangepaste module een ondersteund type is.  
  
|Uitzonderingsberichten|  
|------------------------|  
|Niet-ondersteunde uitvoertype.|  
|Niet-ondersteunde uitvoertype {output_type}' opgegeven.|  


## <a name="error-0125"></a>Fout 0125  
 Thrown when schema for multiple datasets does not match.  

**Resolutie:**

|Uitzonderingsberichten|
|------------------------|
|Het gegevenssetschema komt niet overeen.|


## <a name="error-0127"></a>Fout 0127  
 Afbeeldingspixelgrootte overschrijdt toegestane limiet  

 Deze fout treedt op als u afbeeldingen uit een afbeeldingsgegevensset leest voor classificatie en de afbeeldingen groter zijn dan het model aankan.  

 <!--**Resolution:**
 For more information about the image size and other requirements, see these topics:  

-   [Import Images](import-images.md)  
  
-   [Pretrained Cascade Image Classification](pretrained-cascade-image-classification.md)  -->

|Uitzonderingsberichten|
|------------------------|
|De grootte van de afbeeldingspixel overschrijdt de toegestane limiet.|
|De grootte van de afbeeldingspixel in het bestand {file_path}' overschrijdt de toegestane limiet: {size_limit}'.|


## <a name="error-0128"></a>Fout 0128  
 Aantal voorwaardelijke waarschijnlijkheden voor categorische kolommen overschrijdt de limiet.  

**Resolutie:**

|Uitzonderingsberichten|
|------------------------|
|Aantal voorwaardelijke waarschijnlijkheden voor categorische kolommen overschrijdt de limiet.|
|Aantal voorwaardelijke waarschijnlijkheden voor categorische kolommen overschrijdt de limiet. Kolommen '{column_name_or_index_1}' en {column_name_or_index_2}' zijn het problematische paar.|


## <a name="error-0129"></a>Fout 0129  
 Het aantal kolommen in de gegevensset overschrijdt de toegestane limiet.  

**Resolutie:**

|Uitzonderingsberichten|
|------------------------|
|Het aantal kolommen in de gegevensset overschrijdt de toegestane limiet.|
|Het aantal kolommen in de gegevensset in {dataset_name}' is langer toegestaan.|
|Het aantal kolommen in de gegevensset in {dataset_name}' overschrijdt de toegestane limiet van {component_name}'.|
|Het aantal kolommen in de gegevensset in {dataset_name}' overschrijdt de toegestane '{limit_columns_count}'-limiet van {component_name}.|


## <a name="error-0134"></a>Fout 0134
Uitzondering treedt op wanneer de labelkolom ontbreekt of onvoldoende gelabelde rijen heeft.  

Deze fout treedt op wanneer de module een labelkolom vereist, maar u hebt er geen in de kolomselectie opgenomen of de labelkolom mist te veel waarden.

Deze fout kan ook optreden wanneer een eerdere bewerking de gegevensset zodanig wijzigt dat er onvoldoende rijen beschikbaar zijn voor een downstreambewerking. Stel dat u een expressie in de **partitie- en voorbeeldmodule** gebruikt om een gegevensset te delen door waarden. Als er geen overeenkomsten worden gevonden voor uw expressie, is een van de gegevenssets die het gevolg zijn van de partitie leeg.

Oplossing: 

 Als u een labelkolom opneemt in de kolomselectie, maar deze niet wordt herkend, gebruikt u de module [Metagegevens bewerken](edit-metadata.md) om deze als labelkolom te markeren.

  <!--Use the [Summarize Data](summarize-data.md) module to generate a report that shows how many values are missing in each column. -->
  Vervolgens u de module [Ontbrekende gegevens opschonen](clean-missing-data.md) gebruiken om rijen met ontbrekende waarden in de labelkolom te verwijderen. 

 Controleer uw invoergegevenssets om ervoor te zorgen dat deze geldige gegevens bevatten en voldoende rijen om aan de vereisten van de bewerking te voldoen. Veel algoritmen genereren een foutmelding als ze een minimum aantal rijen gegevens vereisen, maar de gegevens bevatten slechts een paar rijen, of alleen een koptekst.

|Uitzonderingsberichten|
|------------------------|
|Uitzondering treedt op wanneer de labelkolom ontbreekt of onvoldoende gelabelde rijen heeft.|
|Uitzondering treedt op wanneer de labelkolom ontbreekt of minder dan {required_rows_count} gelabelde rijen heeft.|
|Uitzondering treedt op wanneer labelkolom in gegevensset {dataset_name} ontbreekt of minder dan {required_rows_count} gelabelde rijen heeft.|


## <a name="error-0138"></a>Fout 0138  
 Het geheugen is uitgeput en kan de werking van de module niet voltooien. Downsampling van de gegevensset kan helpen om het probleem te verlichten.  

 Deze fout treedt op wanneer de module die wordt uitgevoerd meer geheugen vereist dan beschikbaar is in de Azure-container. Dit kan gebeuren als u met een grote gegevensset werkt en de huidige bewerking niet in het geheugen past.  

**Oplossing:** Als u een grote gegevensset probeert te lezen en de bewerking niet kan worden voltooid, kan het helpen om de gegevensset te downsamplen.  

  <!--If you use the visualizations on datasets to check the cardinality of columns, only some rows are sampled. To get a full report, use [Summarize Data](summarize-data.md). You can also use the [Apply SQL Transformation](apply-sql-transformation.md) to check for the number of unique values in each column.  

 Sometimes transient loads can lead to this error. Machine support also changes over time. 

 Try using [Principal Component Analysis](principal-component-analysis.md) or one of the provided feature selection methods to reduce your dataset to a smaller set of more feature-rich columns: [Feature Selection](feature-selection-modules.md)  -->

|Uitzonderingsberichten|
|------------------------|
|Het geheugen is uitgeput en kan de werking van de module niet voltooien.|
|Het geheugen is uitgeput en kan de werking van de module niet voltooien. Details: {details}|


## <a name="error-0141"></a>Fout 0141  
 Er treedt een uitzondering op als het aantal geselecteerde numerieke kolommen en unieke waarden in de categorische en tekenreekskolommen te klein is.  

 Deze fout in Azure Machine Learning treedt op wanneer er niet genoeg unieke waarden in de geselecteerde kolom zijn om de bewerking uit te voeren.  

**Oplossing:** Sommige bewerkingen voeren statistische bewerkingen uit op functie- en categorische kolommen en als er niet genoeg waarden zijn, kan de bewerking mislukken of een ongeldig resultaat retourneren. Controleer uw gegevensset om te zien hoeveel waarden er zijn in de functie- en labelkolommen en bepaal of de bewerking die u probeert uit te voeren statistisch geldig is.  

 Als de brongegevensset geldig is, u ook controleren of een upstream gegevensmanipulatie of metagegevensbewerking de gegevens heeft gewijzigd en bepaalde waarden heeft verwijderd.  

 Controleer of de uitgangen het verwachte aantal rijen en waarden bevatten als upstreambewerkingen splitsen, bemonstering of opnieuw steekproeven omvatten.  

|Uitzonderingsberichten|
|------------------------|
|Het aantal geselecteerde numerieke kolommen en unieke waarden in de categorische en tekenreekskolommen is te klein.|
|Het totale aantal geselecteerde numerieke kolommen en unieke waarden in de categorische en tekenreekskolommen (momenteel {actual_num}) moet ten minste {lower_boundary} zijn.|


## <a name="error-0154"></a>Fout 0154  
 Uitzondering treedt op wanneer de gebruiker gegevens probeert samen te voegen op belangrijke kolommen met een onverenigbaar kolomtype.

|Uitzonderingsberichten|
|------------------------|
|Typen belangrijke kolomelementen zijn niet compatibel.|
|Typen belangrijke kolomelementen zijn niet compatibel. (links: {keys_left}; rechts: {keys_right})|


## <a name="error-0155"></a>Fout 0155  
 Uitzondering treedt op wanneer kolomnamen van de gegevensset niet tekenreeksen zijn.

|Uitzonderingsberichten|
|------------------------|
|De naam van de kolom gegevensframe moet tekenreekstype zijn. Kolomnamen zijn geen tekenreeks.|
|De naam van de kolom gegevensframe moet tekenreekstype zijn. Kolomnamen {column_names} zijn geen tekenreeks.|


## <a name="error-0156"></a>Fout 0156  
 Uitzondering treedt op wanneer gegevens uit Azure SQL Database niet kunnen worden gelezen.

|Uitzonderingsberichten|
|------------------------|
|Kan geen gegevens uit Azure SQL Database lezen.|
|Kan geen gegevens uit Azure SQL Database lezen: {detailed_message} DB: {database_server_name}:{database_name} Query: {sql_statement}|


## <a name="error-0157"></a>Fout 0157  
 Datastore niet gevonden.

|Uitzonderingsberichten|
|------------------------|
|Gegevensarchiefgegevens zijn ongeldig.|
|Gegevensarchiefgegevens zijn ongeldig. Kan AzureML-gegevensarchief {datastore_name}' niet in werkruimte {workspace_name}' krijgen.|


## <a name="error-0158"></a>Fout 0158
 Wordt gegooid wanneer een transformatiemap ongeldig is.

|Uitzonderingsberichten|
|------------------------------------------------------------|
|Given TransformationDirectory is ongeldig.|
|TransformationDirectory {arg_name}" is ongeldig. Reden: {reason}. Voer het trainingsexperiment opnieuw uit waarmee het transformatiebestand wordt gegenereerd. Als het trainingsexperiment is verwijderd, moet u het transformatiebestand opnieuw maken en opslaan.|


## <a name="error-1000"></a>Fout 1000  
Uitzondering voor interne bibliotheek.  

Deze fout is bedoeld om onverwerkte interne motorfouten vast te leggen. Daarom kan de oorzaak van deze fout verschillen, afhankelijk van de module die de fout heeft gegenereerd.  

Om meer hulp te krijgen, raden we u aan het gedetailleerde bericht te plaatsen dat de fout vergezelt op het Azure Machine Learning-forum, samen met een beschrijving van het scenario, inclusief de gegevens die als invoer worden gebruikt. Deze feedback zal ons helpen om fouten te prioriteren en de belangrijkste problemen voor verder werk te identificeren.  

|Uitzonderingsberichten|
|------------------------|
|Uitzondering bibliotheek.|
|Uitzondering in bibliotheek: {exception}.|
|Uitzondering voor onbekende bibliotheek: {exception}. {customer_support_guidance}.|

