---
title: Implementatiefase van de levenscyclus van het Team Data Science Process
description: De doelen, taken en deliverables voor de implementatiefase van uw data-science projecten
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
ms.openlocfilehash: 1138c95274c769186a9a29aa4d35517e378baeae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720483"
---
# <a name="deployment-stage-of-the-team-data-science-process-lifecycle"></a>Implementatiefase van de levenscyclus van het Team Data Science Process

In dit artikel worden de doelen, taken en deliverables beschreven die zijn gekoppeld aan de implementatie van het Team Data Science Process (TDSP). Dit proces biedt een aanbevolen levenscyclus die u gebruiken om uw data-science-projecten te structureren. De levenscyclus schetst de belangrijkste fasen die projecten doorgaans uitvoeren, vaak iteratief:

   1. **Inzicht in het bedrijf**
   2. **Gegevens verzamelen en begrijpen**
   3. **Modelleren**
   4. **Implementatie**
   5. **Aanvaarding van de klant**

Hier is een visuele weergave van de TDSP-levenscyclus: 

![TDSP-levenscyclus](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goal"></a>Doel
Implementeer modellen met een gegevenspijplijn naar een productie- of productie-achtige omgeving voor acceptatie door de eindgebruiker. 

## <a name="how-to-do-it"></a>Hoe doe je dat?
De belangrijkste taak die in deze fase wordt behandeld:

**Het model operationaliseren**: Implementeer het model en de pijplijn naar een productie- of productie-achtige omgeving voor toepassingsverbruik.

### <a name="operationalize-a-model"></a>Een model operationeel maken
Nadat u een set modellen hebt die goed presteren, u ze operationeel maken voor andere toepassingen om te consumeren. Afhankelijk van de zakelijke vereisten worden voorspellingen in realtime of op batchbasis gedaan. Als u modellen wilt implementeren, stelt u ze bloot met een open API-interface. De interface maakt het mogelijk om het model gemakkelijk te gebruiken uit verschillende toepassingen, zoals:

   * Online websites
   * Spreadsheets 
   * Dashboards
   * Line-of-business toepassingen 
   * Back-endtoepassingen 

Zie [Een Azure Machine Learning-webservice implementeren](../studio/deploy-a-machine-learning-web-service.md)voor voorbeelden van modeloperationalisatie met een Azure Machine Learning-webservice. Het is een aanbevolen praktijk om telemetrie en monitoring in te bouwen in het productiemodel en de gegevenspijplijn die u implementeert. Deze praktijk helpt bij het volgende rapportering van de systeemstatus en het oplossen van problemen.  

## <a name="artifacts"></a>Artefacten

* Een statusdashboard met de systeemstatus en de belangrijkste statistieken
* Een definitief modelleringsrapport met implementatiegegevens
* Een definitief architectuurdocument voor oplossingen


## <a name="next-steps"></a>Volgende stappen

Hier vindt u koppelingen naar elke stap in de levenscyclus van de TDSP:

   1. [Inzicht in het bedrijf](lifecycle-business-understanding.md)
   2. [Data acquisitie en begrip](lifecycle-data.md)
   3. [Modelleren](lifecycle-modeling.md)
   4. [Implementatie](lifecycle-deployment.md)
   5. [Aanvaarding van de klant](lifecycle-acceptance.md)

We bieden volledige walkthroughs die alle stappen in het proces voor specifieke scenario's demonstreren. Het artikel [Voorbeeld walkthroughs](walkthroughs.md) bevat een lijst met de scenario's met koppelingen en miniatuurbeschrijvingen. De walkthroughs illustreren hoe u cloud-, on-premises tools en services combineren in een workflow of pijplijn om een intelligente toepassing te maken. 

Zie [De TDSP](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)gebruiken met Azure Machine Learning voor voorbeelden van het uitvoeren van stappen in TDSP's die Azure Machine Learning Studio gebruiken.
