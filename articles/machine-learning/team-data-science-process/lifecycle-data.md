---
title: Gegevens verzamelen en begrijpen van Team Data Science Process
description: De doelen, taken en producten voor de gegevens verzamelen en begrijpen fase van uw data-science-projecten
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
ms.openlocfilehash: 3c299e9ec42d63812804b5ff7e50324a2de94200
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720500"
---
# <a name="data-acquisition-and-understanding-stage-of-the-team-data-science-process"></a>Gegevens verzamelen en begrijpen fase van het Team Data Science Process

In dit artikel bevat een overzicht van de doelen, taken en producten die zijn gekoppeld aan de gegevens verzamelen en begrijpen fase van het Team Data Science Process (TDSP). Deze procedure biedt de levensduur van een aanbevolen die u gebruiken kunt voor het structureren van uw data-science-projecten. De levenscyclus van geeft een overzicht van de belangrijke fasen die projecten doorgaans worden uitgevoerd, vaak iteratief:

   1. **Zakelijke inzichten**
   2. **Gegevens verkrijgen en meer informatie**
   3. **Model**
   4. **Implementatie**
   5. **Klant acceptatie**

Hier volgt een visuele representatie van de TDSP-levenscyclus: 

![TDSP-levenscyclus](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Doelstellingen
* Een schone, hoogwaardige gegevensset waarvan de relatie met de doelvariabelen wordt begrepen produceren. Zoek de gegevensset in de juiste analytics-omgeving, zodat u klaar voor het model bent.
* Ontwikkel een oplossingsarchitectuur van de pijplijn die wordt vernieuwd en worden de gegevens regelmatig beoordeelt.

## <a name="how-to-do-it"></a>Hoe voer ik
Er zijn drie belangrijkste taken in deze fase behandeld:

   * **De gegevens** opnemen in de doel analyse omgeving.
   * **Verken de gegevens** om te bepalen of de gegevens kwaliteit voldoende is om de vraag te beantwoorden. 
   * **Stel een gegevens pijplijn** in om nieuwe of regel matig vernieuwde gegevens te beoordelen.

### <a name="ingest-the-data"></a>De gegevens opnemen
Instellen van het proces voor de gegevens van de bronlocaties verplaatsen naar de doellocaties waar u analytics-bewerkingen, zoals training en voorspellingen uitgevoerd. Zie [gegevens laden in opslag omgevingen voor analyse](ingest-data.md)voor technische details en opties voor het verplaatsen van gegevens met verschillende Azure-gegevens Services. 

### <a name="explore-the-data"></a>De gegevens verkennen
Voordat u uw modellen trainen, moet u voor het ontwikkelen van een goed begrip van de gegevens. Echte gegevenssets zijn vaak ruis, ontbreken waarden of hebben een groot aantal andere verschillen. U kunt overzichten en visualisatie gebruiken om te controleren van de kwaliteit van uw gegevens en informatie die u nodig hebt voor het verwerken van de gegevens voordat deze klaar voor modellen is. Dit proces is vaak iteratieve.

TDSP biedt een geautomatiseerd hulp programma met de naam [idee](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils)om de gegevens te visualiseren en rapporten van gegevens overzichten voor te bereiden. Het is raadzaam dat u begint met IDEAR eerst de om gegevens te verkennen om te helpen begrijpen van de initiële gegevens interactief met geen codering ontwikkelen. Vervolgens kunt u aangepaste code voor de gegevens verkennen en visualiseren. Zie [taken om gegevens voor te bereiden voor verbeterde machine learning](prepare-data.md)voor meer informatie over het opschonen van de gegevens.  

Nadat u tevreden met de kwaliteit van de gegevens opgeschoond bent, worden de volgende stap is de patronen die inherent aan de gegevens zijn beter te begrijpen. Deze gegevens analyse helpt u bij het kiezen en ontwikkelen van een geschikt voorspellend model voor uw doel. Uiterlijk is voor bewijs van hoe u de gegevens goed verbonden met het doel. Vervolgens kunt u bepalen of er voldoende gegevens om na te gaan met de volgende stappen voor modellering is. Dit proces is, vaak iteratieve. U moet mogelijk nieuwe gegevensbronnen met nauwkeurigere of meer relevante gegevens om te verbeteren van de gegevensset in eerste instantie worden geïdentificeerd in de vorige fase vinden. 

### <a name="set-up-a-data-pipeline"></a>Instellen van een pijplijn
Naast de initiële opname van gegevens en opschonen van de gegevens, moet u meestal voor het instellen van een proces voor het scoren van nieuwe gegevens of de gegevens regelmatig vernieuwt als onderdeel van een doorlopende leerproces. Score kan worden voltooid met een gegevens pijplijn of werk stroom. Het [verplaatsen van gegevens van een on-premises SQL Server-instantie naar Azure SQL database met Azure Data Factory](move-sql-azure-adf.md) artikel geeft een voor beeld van het instellen van een pijp lijn met [Azure Data Factory](https://azure.microsoft.com/services/data-factory/). 

In deze fase moet u een architectuur van de pijplijn-oplossing ontwikkelen. Ontwikkelen van de pijplijn in combinatie met de volgende fase van de data science-project. Afhankelijk van de behoeften van uw bedrijf en de beperkingen van uw bestaande systemen waarin deze oplossing wordt geïntegreerd, kan de pijp lijn een van de volgende opties zijn: 

   * Op basis van batch
   * Streaming of realtime 
   * Een hybride 

## <a name="artifacts"></a>Artefacten
Hier volgen de producten in deze fase:

   * [Rapport gegevens kwaliteit](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Data_Report/DataSummaryReport.md): dit rapport bevat gegevens overzichten, de relaties tussen de verschillende kenmerken en doelen, de classificatie van variabelen en meer. Het hulp programma voor het [idee](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils) dat is opgegeven als onderdeel van TDSP kan dit rapport snel genereren voor elke tabellaire gegevensset, zoals een CSV-bestand of een relationele tabel. 
   * **Oplossings architectuur**: de oplossings architectuur kan een diagram of beschrijving van uw gegevens pijplijn zijn die u gebruikt om scores of voor spellingen uit te voeren op nieuwe gegevens nadat u een model hebt gemaakt. Het bevat ook de pijplijn opnieuw trainen van uw model op basis van nieuwe gegevens. Sla het document op in de [projectmap](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/Project) wanneer u de TDSP-sjabloon voor de directory structuur gebruikt.
   * **Beslissing van het controle punt**: voordat u begint met het bouwen van het ontwerp van de functie en het model, kunt u het project opnieuw evalueren om te bepalen of de verwachte waarde voldoende is om door te gaan. U kan bijvoorbeeld zijn klaar om door te gaan, moet meer gegevens verzamelen, of het project afbreken als de gegevens niet bestaat als u wilt de vraag worden beantwoord.

## <a name="next-steps"></a>Volgende stappen

Hier vindt u koppelingen naar elke stap in de levenscyclus van de TDSP:

   1. [Zakelijke inzichten](lifecycle-business-understanding.md)
   2. [Gegevens verkrijgen en meer informatie](lifecycle-data.md)
   3. [Model](lifecycle-modeling.md)
   4. [Implementatie](lifecycle-deployment.md)
   5. [Klant acceptatie](lifecycle-acceptance.md)

We bieden volledige instructies voor het demonstreren van alle stappen in het proces voor specifieke scenario's. Het artikel [voorbeeld](walkthroughs.md) scenario's bevat een lijst met de scenario's met koppelingen en miniaturen. De scenario's laten zien hoe u cloud, on-premises hulpprogramma's en services combineren in een werkstroom of een pijplijn te maken van een intelligente toepassingen. 

Zie [de TDSP gebruiken met Azure machine learning](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/lifecycle-data)voor voor beelden van het uitvoeren van stappen in TDSPs die gebruikmaken van Azure machine learning Studio.
