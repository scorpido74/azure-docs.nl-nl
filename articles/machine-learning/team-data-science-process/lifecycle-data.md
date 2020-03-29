---
title: Data-acquisitie en begrip van Team Data Science Process
description: De doelen, taken en deliverables voor de data-acquisitie en inzichtsfase van uw data-science projecten
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720500"
---
# <a name="data-acquisition-and-understanding-stage-of-the-team-data-science-process"></a>Data-acquisitie en inzicht in de fase van het Team Data Science Process

In dit artikel worden de doelen, taken en deliverables beschreven die zijn gekoppeld aan de fase van gegevensverzameling en -inzicht van het Team Data Science Process (TDSP). Dit proces biedt een aanbevolen levenscyclus die u gebruiken om uw data-science-projecten te structureren. De levenscyclus schetst de belangrijkste fasen die projecten doorgaans uitvoeren, vaak iteratief:

   1. **Inzicht in het bedrijf**
   2. **Gegevens verzamelen en begrijpen**
   3. **Modelleren**
   4. **Implementatie**
   5. **Aanvaarding van de klant**

Hier is een visuele weergave van de TDSP-levenscyclus: 

![TDSP-levenscyclus](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Doelstellingen
* Maak een schone, hoogwaardige gegevensset waarvan de relatie met de doelvariabelen wordt begrepen. Zoek de gegevensset in de juiste analyseomgeving, zodat u klaar bent om te modelleren.
* Ontwikkel een oplossingsarchitectuur van de datapijplijn die de gegevens regelmatig vernieuwt en scoort.

## <a name="how-to-do-it"></a>Hoe doe je dat?
In deze fase worden drie hoofdtaken behandeld:

   * **De gegevens innemen** in de doelanalytische omgeving.
   * **Verken de gegevens** om te bepalen of de gegevenskwaliteit voldoende is om de vraag te beantwoorden. 
   * **Stel een gegevenspijplijn in** om nieuwe of regelmatig vernieuwde gegevens te scoren.

### <a name="ingest-the-data"></a>De gegevens innemen
Stel het proces in om de gegevens van de bronlocaties naar de doellocaties te verplaatsen waar u analysebewerkingen uitvoert, zoals training en voorspellingen. Zie [Gegevens laden in opslagomgevingen voor analyse voor](ingest-data.md)technische details en opties voor het verplaatsen van de gegevens met verschillende Azure-gegevensservices. 

### <a name="explore-the-data"></a>De gegevens verkennen
Voordat u uw modellen traint, moet u een goed begrip van de gegevens ontwikkelen. Real-world datasets zijn vaak luidruchtig, ontbreken waarden of hebben een groot aantal andere verschillen. U gegevenssomslag en visualisatie gebruiken om de kwaliteit van uw gegevens te controleren en de informatie te verstrekken die u nodig hebt om de gegevens te verwerken voordat deze klaar zijn voor modellering. Dit proces is vaak iteratief.

TDSP biedt een geautomatiseerd hulpprogramma, [IDEAR](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils)genaamd, om de gegevens te visualiseren en gegevensoverzichtsrapporten voor te bereiden. We raden u aan eerst met IDEAR te beginnen om de gegevens te verkennen om het inzicht in de eerste gegevens interactief te ontwikkelen zonder codering. Vervolgens u aangepaste code schrijven voor gegevensverkenning en visualisatie. Zie Taken om gegevens [voor te bereiden](prepare-data.md)op verbeterde machine learning voor advies voor het schoonmaken van de gegevens.  

Nadat u tevreden bent met de kwaliteit van de gereinigde gegevens, is de volgende stap om de patronen die inherent zijn aan de gegevens beter te begrijpen. Deze data-analyse helpt u bij het kiezen en ontwikkelen van een geschikt voorspellend model voor uw doelgroep. Zoek naar bewijs voor hoe goed de gegevens zijn verbonden met het doel. Bepaal vervolgens of er voldoende gegevens zijn om verder te gaan met de volgende modelleringsstappen. Nogmaals, dit proces is vaak iteratief. Mogelijk moet u nieuwe gegevensbronnen met nauwkeurigere of relevantere gegevens vinden om de gegevensset uit te breiden die in de vorige fase aanvankelijk werd geïdentificeerd. 

### <a name="set-up-a-data-pipeline"></a>Een gegevenspijplijn instellen
Naast de inname en reiniging van de gegevens moet u doorgaans een proces instellen om nieuwe gegevens te scoren of de gegevens regelmatig te vernieuwen als onderdeel van een doorlopend leerproces. Scores kunnen worden voltooid met een gegevenspijplijn of werkstroom. De [gegevens verplaatsen van een on-premises SQL Server-instantie naar Azure SQL Database met Azure Data Factory,](move-sql-azure-adf.md) geeft een voorbeeld van het instellen van een pijplijn met Azure Data [Factory.](https://azure.microsoft.com/services/data-factory/) 

In deze fase ontwikkelt u een oplossingsarchitectuur van de datapijplijn. Je ontwikkelt de pijplijn parallel aan de volgende fase van het data science project. Afhankelijk van uw bedrijfsbehoeften en de beperkingen van uw bestaande systemen waarin deze oplossing wordt geïntegreerd, kan de pijplijn een van de volgende opties zijn: 

   * Batch-based
   * Streaming of real-time 
   * Een hybride 

## <a name="artifacts"></a>Artefacten
De volgende zijn de deliverables in deze fase:

   * [Rapport over gegevenskwaliteit](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Data_Report/DataSummaryReport.md): Dit rapport bevat gegevensoverzichten, de relaties tussen elk kenmerk en elk doel, variabele rangschikking en meer. Het [IDEAR-hulpprogramma](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils) dat wordt geleverd als onderdeel van TDSP kan dit rapport snel genereren op elke tabelgegevensset, zoals een CSV-bestand of een relationele tabel. 
   * **Oplossingsarchitectuur:** de oplossingsarchitectuur kan een diagram of beschrijving zijn van uw gegevenspijplijn die u gebruikt om scores of voorspellingen op nieuwe gegevens uit te voeren nadat u een model hebt gebouwd. Het bevat ook de pijplijn om uw model om te scholen op basis van nieuwe gegevens. Sla het document op in de [projectmap](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/Project) wanneer u de sjabloon TDSP-directorystructuur gebruikt.
   * **Controlepuntbeslissing:** Voordat u begint met full-feature engineering en modelbuilding, u het project opnieuw evalueren om te bepalen of de verwachte waarde voldoende is om deze te blijven nastreven. U bijvoorbeeld klaar zijn om verder te gaan, meer gegevens moeten verzamelen of het project moeten verlaten omdat de gegevens niet bestaan om de vraag te beantwoorden.

## <a name="next-steps"></a>Volgende stappen

Hier vindt u koppelingen naar elke stap in de levenscyclus van de TDSP:

   1. [Inzicht in het bedrijf](lifecycle-business-understanding.md)
   2. [Gegevens verzamelen en begrijpen](lifecycle-data.md)
   3. [Modelleren](lifecycle-modeling.md)
   4. [Implementatie](lifecycle-deployment.md)
   5. [Aanvaarding van de klant](lifecycle-acceptance.md)

We bieden volledige walkthroughs die alle stappen in het proces voor specifieke scenario's demonstreren. Het artikel [Voorbeeld walkthroughs](walkthroughs.md) bevat een lijst met de scenario's met koppelingen en miniatuurbeschrijvingen. De walkthroughs illustreren hoe u cloud-, on-premises tools en services combineren in een workflow of pijplijn om een intelligente toepassing te maken. 

Zie [De TDSP](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/lifecycle-data)gebruiken met Azure Machine Learning voor voorbeelden van het uitvoeren van stappen in TDSP's die Azure Machine Learning Studio gebruiken.
