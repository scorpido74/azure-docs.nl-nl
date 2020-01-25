---
title: Inzicht in het bedrijf in Team Data Science Process
description: De doelen, taken en producten voor de fase van het inzicht in bedrijven van uw data-science-projecten in het Team Data Science Process.
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
ms.openlocfilehash: a7aaed519f8f97a9be77a263568aeed5257c16d6
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76710330"
---
# <a name="the-business-understanding-stage-of-the-team-data-science-process-lifecycle"></a>De zakelijke understanding fase van de levenscyclus van het Team Data Science Process

In dit artikel bevat een overzicht van de doelen, taken en producten die zijn gekoppeld aan de zakelijke understanding fase van het Team Data Science Process (TDSP). Deze procedure biedt de levensduur van een aanbevolen die u gebruiken kunt voor het structureren van uw data-science-projecten. De levenscyclus van geeft een overzicht van de belangrijke fasen die projecten doorgaans worden uitgevoerd, vaak iteratief:

   1. **Inzicht in het bedrijf**
   2. **Gegevens verzamelen en begrijpen**
   3. **Modeling**
   4. **Implementatie**
   5. **Aanvaarding van de klant**

Hier volgt een visuele representatie van de TDSP-levenscyclus: 

![TDSP-levenscyclus](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Doelstellingen
* Geef de sleutel variabelen die fungeren als de doelen van het model en waarvan gerelateerde metrische gegevens worden gebruikt, bepalen het succes van het project.
* Identificeer de relevante gegevensbronnen die het bedrijf heeft toegang tot of nodig heeft om op te halen.

## <a name="how-to-do-it"></a>Hoe voer ik
Er zijn twee belangrijke taken behandeld in deze fase: 

   * **Doelstellingen bepalen**: werken met uw klant en andere betrokkenen om te begrijpen en de zakelijke problemen identificeren. Formuleren vragen die de zakelijke doelstellingen die zijn gericht op de data science-technieken kunnen definiëren.
   * **De gegevensbronnen identificeren**: zoeken naar de relevante gegevens waarmee u beantwoord de vragen die de doelstellingen van het project definiëren.

### <a name="define-objectives"></a>Doelstellingen bepalen
1. Een centrale doel van deze stap is het identificeren van de belangrijkste zakelijke variabelen die de analyse nodig heeft om te voorspellen. We verwijzen naar deze variabelen als de *model doelen*, en we de metrische gegevens die zijn gekoppeld aan deze gebruiken om te bepalen het succes van het project. Twee voorbeelden van dergelijke doelen zijn verkoopprognoses of de waarschijnlijkheid van een order fraude.

2. Definieer de projectdoelstellingen waarin wordt gevraagd en "scherpe" vragen die betrokken, specifieke en ondubbelzinnige zijn verfijnen. Wetenschappelijke gegevens is een proces dat gebruikmaakt van namen en getallen om dergelijke vragen te beantwoorden. Doorgaans gebruikt u data science of machine learning om de vijf soorten vragen beantwoorden:
 
   * Hoeveel of hoe vaak? (regressie)
   * Welke categorie? (classificatie)
   * Welke groep? (clustering)
   * Is dit vreemd? (anomaliedetectie)
   * Welke optie moet worden gehouden? (aanbevolen)

   Bepalen welke van deze vragen u vraagt en hoe beantwoorden bereikt uw bedrijfsdoelen.

3. Definieer het projectteam door op te geven van de rollen en verantwoordelijkheden van de leden. Ontwikkel een mijlpaalplan op hoog niveau die u herhalen als u meer informatie. 

4. Definieer de maatstaven voor succes. U wilt bijvoorbeeld een gebruikersverloop bereiken. U moet een nauwkeurigheid van de 'x' procent aan het einde van dit project drie maanden. Met deze gegevens, kunt u de acties van de klant te verminderen verloop aanbieden. De metrische gegevens moet **slimme**: 

   * **S**pecifieke 
   * **M**easurable
   * **Een**chievable 
   * **R**elevant 
   * **T**ime-gebonden 

### <a name="identify-data-sources"></a>De gegevensbronnen identificeren
Identificeer gegevensbronnen die enkele voorbeelden van antwoorden op uw vragen sharp bevatten. Zoek naar de volgende gegevens:

* Gegevens die relevant is voor de vraag. Hebt u metingen van het doel en de functies die zijn gerelateerd aan het doel?
* Gegevens die een nauwkeurig beeld geeft van de doel-model en de functies van belang zijn.

Bijvoorbeeld, wellicht dat de bestaande systemen moeten verzamelen en meld u aanvullende soorten gegevens Verhelp het probleem en de project-doelen kan bereiken. In dit geval is het raadzaam om te zoeken naar externe gegevensbronnen of bijwerken van uw systemen voor het verzamelen van nieuwe gegevens.

## <a name="artifacts"></a>Artifacts
Dit zijn de producten in deze fase:

   * [Handvest document](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Charter.md): een standard-sjabloon is opgegeven in de definitie van de TDSP-project-structuur. Het Handvest-document is een levend document. U kunt de sjabloon in het project bijwerken naarmate u nieuwe detecties en als zakelijke vereisten veranderen. De sleutel is het herhalen van dit document nader bekeken, als u wordt uitgevoerd via het detectieproces toe te voegen. Houd de klant en andere belanghebbenden betrokken zijn bij het maken van de wijzigingen en duidelijk de redenen voor de wijzigingen aan te brengen.  
   * [Gegevensbronnen](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Data_Report/Data%20Defintion.md#raw-data-sources): de **onbewerkte gegevensbronnen** sectie van de **gegevensdefinities** rapport dat gevonden in de TDSP-project **rapport** map bevat de gegevens bronnen. In deze sectie wordt de oorspronkelijke en locaties voor de onbewerkte gegevens. In latere fasen, moet u aanvullende informatie zoals de scripts worden de gegevens verplaatsen naar uw analytische omgeving invullen.  
   * [Gegevens woordenlijsten](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/Data_Dictionaries): dit document bevat beschrijvingen van de gegevens die wordt geleverd door de client. Deze beschrijvingen bevatten informatie over het schema (de gegevenstypen en informatie over de validatieregels, indien van toepassing) en de entiteit-relation-diagrammen, indien beschikbaar.

## <a name="next-steps"></a>Volgende stappen

Hier vindt u koppelingen naar elke stap in de levenscyclus van de TDSP:

   1. [Inzicht in het bedrijf](lifecycle-business-understanding.md)
   2. [Gegevens verzamelen en begrijpen](lifecycle-data.md)
   3. [Modeling](lifecycle-modeling.md)
   4. [Implementatie](lifecycle-deployment.md)
   5. [Aanvaarding van de klant](lifecycle-acceptance.md)

We bieden volledige instructies voor het demonstreren van alle stappen in het proces voor specifieke scenario's. De [voorbeeld walkthroughs](walkthroughs.md) artikel geeft een lijst van de scenario's met koppelingen en beschrijvingen van miniaturen. De scenario's laten zien hoe u cloud, on-premises hulpprogramma's en services combineren in een werkstroom of een pijplijn te maken van een intelligente toepassingen. 
