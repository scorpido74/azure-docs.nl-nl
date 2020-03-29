---
title: Bedrijfsinzicht in Team Data Science Process
description: De doelen, taken en deliverables voor de bedrijfsinzichtsfase van uw data-science projecten in het Team Data Science Process.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76710330"
---
# <a name="the-business-understanding-stage-of-the-team-data-science-process-lifecycle"></a>De fase van bedrijfsbegrip van de levenscyclus van het Team Data Science Process

In dit artikel worden de doelen, taken en deliverables beschreven die zijn gekoppeld aan de fase van bedrijfsinzicht van het Team Data Science Process (TDSP). Dit proces biedt een aanbevolen levenscyclus die u gebruiken om uw data-science-projecten te structureren. De levenscyclus schetst de belangrijkste fasen die projecten doorgaans uitvoeren, vaak iteratief:

   1. **Inzicht in het bedrijf**
   2. **Gegevens verzamelen en begrijpen**
   3. **Modelleren**
   4. **Implementatie**
   5. **Aanvaarding van de klant**

Hier is een visuele weergave van de TDSP-levenscyclus: 

![TDSP-levenscyclus](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Doelstellingen
* Geef de belangrijkste variabelen op die als modeldoelen moeten dienen en waarvan de bijbehorende statistieken worden gebruikt, bepaalt het succes van het project.
* Identificeer de relevante gegevensbronnen waartoe het bedrijf toegang heeft of moet verkrijgen.

## <a name="how-to-do-it"></a>Hoe doe je dat?
Er zijn twee hoofdtaken die in deze fase aan de orde komen: 

   * **Doelstellingen definiëren:** werk samen met uw klant en andere belanghebbenden om de bedrijfsproblemen te begrijpen en te identificeren. Formuleer vragen die de bedrijfsdoelstellingen definiëren die de data science-technieken kunnen targeten.
   * **Gegevensbronnen identificeren:** zoek de relevante gegevens waarmee u de vragen beantwoorden die de doelstellingen van het project definiëren.

### <a name="define-objectives"></a>Doelstellingen definiëren
1. Een centrale doelstelling van deze stap is het identificeren van de belangrijkste bedrijfsvariabelen die de analyse moet voorspellen. We verwijzen naar deze variabelen als de *modeldoelen*en we gebruiken de bijbehorende statistieken om het succes van het project te bepalen. Twee voorbeelden van dergelijke doelen zijn verkoopprognoses of de waarschijnlijkheid dat een bestelling frauduleus is.

2. Definieer de projectdoelen door "scherpe" vragen te stellen en te verfijnen die relevant, specifiek en ondubbelzinnig zijn. Data science is een proces dat namen en getallen gebruikt om dergelijke vragen te beantwoorden. U gebruikt gegevenswetenschap of machine learning meestal om vijf soorten vragen te beantwoorden:
 
   * Hoeveel of hoeveel? (regressie)
   * Welke categorie? (indeling)
   * Welke groep? (clustering)
   * Is dit raar? (anomaliedetectie)
   * Welke optie moet worden genomen? (aanbeveling)

   Bepaal welke van deze vragen u stelt en hoe het beantwoorden ervan uw bedrijfsdoelstellingen bereikt.

3. Definieer het projectteam door de rollen en verantwoordelijkheden van de leden op te geven. Ontwikkel een mijlpaalplan op hoog niveau dat u aangeeft als u meer informatie ontdekt. 

4. Definieer de successtatistieken. U bijvoorbeeld een voorspelling van het klantverloop bereiken. U hebt een nauwkeurigheidspercentage van "x" procent nodig tegen het einde van dit drie maanden durende project. Met deze gegevens u klantpromoties aanbieden om de churn te verminderen. De statistieken moeten **SMART**zijn: 

   * **S**pecific 
   * **M**easurable
   * **Een**chievable 
   * **R**elevant 
   * **T**ime-gebonden 

### <a name="identify-data-sources"></a>Gegevensbronnen identificeren
Identificeer gegevensbronnen die bekende voorbeelden van antwoorden op uw scherpe vragen bevatten. Zoek naar de volgende gegevens:

* Gegevens die relevant zijn voor de vraag. Heeft u metingen van het doel en functies die gerelateerd zijn aan het doel?
* Gegevens die een nauwkeurige meting zijn van uw modeldoel en de kenmerken van belang.

U bijvoorbeeld merken dat de bestaande systemen extra soorten gegevens moeten verzamelen en registreren om het probleem op te lossen en de projectdoelen te bereiken. In deze situatie u op zoek gaan naar externe gegevensbronnen of uw systemen bijwerken om nieuwe gegevens te verzamelen.

## <a name="artifacts"></a>Artefacten
Hier zijn de deliverables in deze fase:

   * [Charterdocument](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Charter.md): Een standaardsjabloon wordt geleverd in de definitie van de TDSP-projectstructuur. Het charterdocument is een levend document. U werkt de sjabloon tijdens het hele project bij terwijl u nieuwe ontdekkingen doet en naarmate de vereisten van het bedrijf veranderen. De sleutel is om dit document te herhalen en meer details toe te voegen naarmate u verderkomt in het detectieproces. Houd de klant en andere belanghebbenden betrokken bij het aanbrengen van de wijzigingen en communiceer duidelijk de redenen voor de wijzigingen aan hen.  
   * [Gegevensbronnen:](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Data_Report/Data%20Defintion.md#raw-data-sources)de sectie **Ruwe gegevensbronnen** van het rapport **Gegevensdefinities** dat is gevonden in de rapportmap TDSP-projectGegevens bevat de gegevensbronnen. **Data report** In deze sectie worden de oorspronkelijke en doellocaties voor de ruwe gegevens opgegeven. In latere stadia vult u aanvullende details in, zoals de scripts om de gegevens naar uw analytische omgeving te verplaatsen.  
   * [Gegevenswoordenboeken](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/Data_Dictionaries): Dit document bevat beschrijvingen van de gegevens die door de client worden verstrekt. Deze beschrijvingen bevatten informatie over het schema (de gegevenstypen en informatie over de eventuele validatieregels) en de entiteitsrelatiediagrammen, indien beschikbaar.

## <a name="next-steps"></a>Volgende stappen

Hier vindt u koppelingen naar elke stap in de levenscyclus van de TDSP:

   1. [Inzicht in het bedrijf](lifecycle-business-understanding.md)
   2. [Gegevens verzamelen en begrijpen](lifecycle-data.md)
   3. [Modelleren](lifecycle-modeling.md)
   4. [Implementatie](lifecycle-deployment.md)
   5. [Aanvaarding van de klant](lifecycle-acceptance.md)

We bieden volledige walkthroughs die alle stappen in het proces voor specifieke scenario's demonstreren. Het artikel [Voorbeeld walkthroughs](walkthroughs.md) bevat een lijst met de scenario's met koppelingen en miniatuurbeschrijvingen. De walkthroughs illustreren hoe u cloud-, on-premises tools en services combineren in een workflow of pijplijn om een intelligente toepassing te maken. 
