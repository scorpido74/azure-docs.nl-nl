---
title: Functie-engineering in data science - Team Data Science Process
description: De toepassing van feature-engineering uitgelegd en bevat voorbeelden van de rol in de uitbreiding van de synchronisatiegegevens van machine learning.
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
ms.openlocfilehash: 02f109f250fa9bcd4c77cecd0b1b3e4514ecd8bc
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721129"
---
# <a name="feature-engineering-in-data-science"></a>Functie-engineering in datatechnologie
Dit artikel worden de doeleinden van feature-engineering en bevat voorbeelden van de rol in de uitbreiding van de synchronisatiegegevens van machine learning. De voorbeelden die wordt gebruikt ter illustratie van dit proces worden getekend van Azure Machine Learning Studio. 

Deze taak is een stap in het [team data Science process (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

Functie engineering pogingen te verhogen van de voorspellende kracht van de learning-algoritmen met het maken van de functies van onbewerkte gegevens die helpen bij het leerproces te vergemakkelijken. De techniek en selectie van functies is een onderdeel van de TDSP die wordt beschreven in de [informatie over de levens cyclus van het team data Science process.](overview.md) Functie techniek en selectie zijn onderdelen van de stap **functies ontwikkelen** van de TDSP. 

* **functie techniek**: dit proces probeert extra relevante functies te maken op basis van de bestaande onbewerkte functies in de gegevens en om de voorspellende kracht van het leer algoritme te verg Roten.
* **functie selectie**: dit proces selecteert de sleutel subset van de oorspronkelijke gegevens functies in een poging om de dimensionaliteit van het trainings probleem te reduceren.

Normaal gesp roken **functie techniek** wordt eerst toegepast om extra functies te genereren, en vervolgens wordt de **functie selectie** stap uitgevoerd om irrelevante, redundante of zeer gecorreleerde functies te elimineren.

De trainingsgegevens gebruikt in machine learning kan vaak worden verbeterd door extractie van functies van de onbewerkte gegevens die worden verzameld. Een voorbeeld van een Social engineering-functie in de context van het leren hoe u kunt voor het classificeren van de afbeeldingen van handgeschreven tekens is het maken van een en ander dichtheid kaart is samengesteld uit de gegevens van de distributie onbewerkte bits. Deze kaart kan helpen de randen van de tekens efficiënter dan gewoon rechtstreeks met de onbewerkte distributie vinden.

Voor het maken van functies voor gegevens in specifieke omgevingen, Zie de volgende artikelen:

* [Functies maken voor gegevens in SQL Server](create-features-sql-server.md)
* [Functies maken voor gegevens in een Hadoop-cluster met behulp van Hive-query's](create-features-hive.md)

## <a name="create-features-from-your-data---feature-engineering"></a>Functies maken van uw gegevens - functie-engineering
De trainingsgegevens bestaat uit een matrix die bestaan uit voorbeelden (records of metingen die zijn opgeslagen in rijen), die allemaal een set functies (variabelen of velden die zijn opgeslagen in kolommen). De functies die zijn opgegeven in het ontwerp van de experimentele wordt verwacht dat de beschrijving van de patronen in de gegevens. Hoewel veel van de velden van de onbewerkte gegevens kunnen rechtstreeks worden opgenomen in de geselecteerde functie gebruikt voor een model te trainen, is het vaak het geval dat extra (Social engineering) functies moeten worden samengesteld uit de functies in de onbewerkte gegevens voor het genereren van een uitgebreide training-gegevensset.

Wat voor soort functies moet worden gemaakt voor het verbeteren van de gegevensset bij het trainen van een model? Social engineering functies waarmee u de training kunt Geef informatie op die beter wordt onderscheid gemaakt de patronen in de gegevens tussen. De nieuwe functies worden voor aanvullende informatie die niet duidelijk vastgelegde of eenvoudig zichtbaar in de oorspronkelijke of een bestaande functieset verwacht. Maar dit proces is iets wat een afbeelding. Geluid en productieve beslissingen vereisen vaak de expertise van een domein.

Bij het starten met Azure Machine Learning, is het eenvoudigste ingewikkeld van dit proces concrete invulling te geven met behulp van voorbeelden die in de Studio. Twee voorbeelden worden hier weergegeven:

* Een voor beeld van een regressie voor voor [Spelling van het aantal fiets huren](https://gallery.cortanaintelligence.com/Experiment/Regression-Demand-estimation-4) in een experiment met toezicht waarbij de doel waarden bekend zijn
* Een voor beeld van een classificatie van tekst analyse met behulp van [functie-hashing](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/)

## <a name="example-1-add-temporal-features-for-a-regression-model"></a>Voorbeeld 1: Tijdelijke functies voor een regressiemodel toevoegen
We gebruiken het experiment ' vraag prognose van fietsen ' in Azure Machine Learning Studio (klassiek) om te laten zien hoe u functies voor een regressie taak kunt inbouwen. Het doel van dit experiment is om te voorspellen van de vraag naar de fietsen, dat wil zeggen, het aantal huurfietsen binnen een specifieke maand/dag/uur. De gegevensset ' fiets verhuur UCI gegevensset ' wordt gebruikt als de onbewerkte invoergegevens. Deze gegevensset is gebaseerd op echte gegevens uit het kapitaal Bikeshare-bedrijf dat een fiets huur-netwerk in Washington D.C. in de Verenigde Staten. De gegevensset geeft het aantal huurfietsen binnen een bepaald uur per dag in het jaar 2011 en jaar 2012 en 17379 rijen en 17 kolommen bevat. De onbewerkte functieset bevat weersomstandigheden (temperatuur/vochtigheid/windsnelheid) en het type van de dag (feestdagen/dag). Het veld om te voors pellen is het aantal ' cnt ' dat de fiets huren binnen een bepaald uur vertegenwoordigt en een bereik van 1 tot en met 977.

Met het doel van het maken van effectieve functies in de trainingsgegevens, vier regressie modellen zijn gebouwd met behulp van dezelfde algoritme, maar met vier verschillende training-gegevenssets. De vier gegevenssets dezelfde onbewerkte invoer gegevens vertegenwoordigen, maar met een toenemend aantal functies ingesteld. Deze functies zijn ingedeeld in vier categorieën:

1. Een = weer, feestdagen + weekdag + het weekend van functies voor de voorspelde dag
2. B hoeveel fietsen verhuurd zijn in elk van de afgelopen 12 uur =
3. C = aantal fietsen verhuurd zijn in elk van de afgelopen 12 dagen op hetzelfde uur
4. D = aantal fietsen dat in elk van de vorige 12 weken op hetzelfde uur en dezelfde dag zijn gehuurd

Naast de functie set A, die al in de oorspronkelijke onbewerkte gegevens bestaat, worden de andere drie sets van functies gemaakt via de functie voor engineering-proces. Met de functieset B worden recente vraag voor de fietsen vastgelegd. Functie instellen C legt de vraag naar fietsen op een bepaald uur. D opnamen vraag naar fietsen functie ingesteld op een bepaald uur en bepaalde dag van de week. De vier training gegevenssets die elke set-functie A, A + B, A + B + C en omvat A + B + C + D, respectievelijk.

Deze vier trainingdatasets worden in de Azure Machine Learning-experiment, via vier vertakkingen van de vooraf verwerkte invoergegevensset gevormd. Met uitzonde ring van de meest linkse vertakking bevat elk van deze vertakkingen een [Execute R-script](https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/) module, waarin de afgeleide functies (functieset B, C en D) respectievelijk zijn gebouwd en worden toegevoegd aan de geïmporteerde gegevensset. De volgende afbeelding ziet u het R-script gebruikt voor het maken van functieset B in de tweede links vertakking.

![Functies maken](./media/create-features/addFeature-Rscripts.png)

Een vergelijking van de prestatieresultaten van de vier modellen wordt in de volgende tabel samengevat: 

![vergelijking van resultaat](./media/create-features/result1.png)

De beste resultaten worden weergegeven door de functies A + B + C. De fout frequentie neemt af wanneer extra functie sets zijn opgenomen in de trainings gegevens. Er wordt gecontroleerd met de veronderstelling dat de functieset B, C meer relevante informatie voor de taak regressie bieden. Maar de D-functie toe te voegen lijkt niet te bieden een extra verlaging van de frequentie van fouten.

## <a name="example2"></a>Voor beeld 2: functies maken in tekst analyse
Feature-engineering is breed in taken met betrekking tot gegevensanalyse van tekst, zoals document classificatie en sentiment-analyse toegepast. Bijvoorbeeld, als u classificeren documenten in verschillende categorieën wilt, is een typische veronderstelling dat het woord/zinnen is opgenomen in één document categorie minder waarschijnlijk worden uitgevoerd in een andere categorie die doc-bestand. Met andere woorden, kan de frequentie van de distributie woorden/zinnen ander documentcategorieën karakteriseren. In text mining-toepassingen, omdat er afzonderlijke stukjes tekstinhoud meestal fungeren als de invoergegevens, de functie engineering-proces nodig om te maken van de functies met betrekking tot woord/bepaalde woordgroep frequenties.

Ter verkrijging van deze taak wordt een techniek met de naam **hashing** toegepast om op efficiënte wijze wille keurige tekst functies in indexen in te scha kelen. In plaats van elke tekstfunctie (woorden/zinnen) koppelen aan een bepaalde index, deze methode werkt door het toepassen van een hash-functie op de functies en het gebruik van de hashwaarden als indexen rechtstreeks.

In Azure Machine Learning is er een [functie-hash](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/) -module die de functies van Word/woordgroepen handig maakt. Volgende afbeelding toont een voorbeeld van het gebruik van deze module. De invoergegevensset bevat twee kolommen: het boek classificatie, variërend van 1 tot en met 5, en de inhoud van de werkelijke controleren. Het doel van deze [functie hashing](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/) -module is om een aantal nieuwe functies op te halen die de frequentie van het voorval van de corresponderende woord (en)/phrase (s) in de specifieke boek beoordeling tonen. Voor het gebruik van deze module, voert u de volgende stappen uit:

* Selecteer eerst de kolom waarin de ingevoerde tekst ("Col2" in dit voorbeeld).
* Ten tweede ingesteld 'Hashing bitsize' op 8, wat betekent dat 2 ^ 8 = 256 functies wordt gemaakt. Het woord of de fase in de tekst wordt naar 256 indexen worden gehasht. De parameter "Hashing bitsize" bereik van 1 tot en met 31. De tekst / r-zin(nen) waarschijnlijk minder worden opgedeeld in dezelfde index als deze een groter aantal instelt.
* Derde, stel de parameter 'N-grammen' op 2. Deze waarde wordt de frequentie van het exemplaar van unigrams (een functie voor elke één woord) en bigrams (een functie voor elke combinatie van een aaneengesloten woorden) opgehaald uit de invoertekst. De parameter "N-grammen" bereik van 0 tot en met 10, waarmee het maximum aantal opeenvolgende woorden moeten worden opgenomen in een functie wordt aangegeven.  

![Module "Functie Hashing"](./media/create-features/feature-Hashing1.png)

De volgende afbeelding ziet u wat deze nieuwe functie eruit.

![Voorbeeld van de "Functie Hashing"](./media/create-features/feature-Hashing2.png)

## <a name="conclusion"></a>Conclusie
Ontworpen en geselecteerde functies verhoogt de efficiëntie van de trainingsproces, dat wordt geprobeerd om op te halen van de gegevens van de sleutel die is opgenomen in de gegevens. Ze verbeteren de kracht van deze modellen voor het classificeren van de ingevoerde gegevens correct en om te voorspellen van uitkomsten van belang zijn meer databases robuuster worden ook. Feature-engineering en selectie kunnen ook zodat de learning meer rekenintensief tractable combineren. Dit gebeurt verbeteren en vervolgens het aantal functies die nodig zijn om te kalibreren of een model te trainen te verminderen. Wiskundig spreken, zijn de functies die zijn geselecteerd voor het model te trainen een minimale set van onafhankelijke variabelen die uitgelegd van de patronen in de gegevens en vervolgens de resultaten met succes voorspellen.

Het is niet altijd noodzakelijk om uit te voeren Functieselectie engineering of functie. Of het nodig is of niet afhankelijk zijn van de gegevens te leveren of verzameld, de algoritme die is geselecteerd en het doel van het experiment.

