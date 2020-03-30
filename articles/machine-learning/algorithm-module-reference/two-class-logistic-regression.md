---
title: 'Logistieke regressie van twee klassen: modulereferentie'
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de tweeklassenmodule voor logistieke regressie in Azure Machine Learning om een logistiek regressiemodel te maken dat kan worden gebruikt om twee (en slechts twee) resultaten te voorspellen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 7eb1ad00c3c947c3ed6d4ca450bddc0956a08d71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79455822"
---
# <a name="two-class-logistic-regression-module"></a>Twee-Klasse Logistieke Regressiemodule

In dit artikel wordt een module beschreven in Azure Machine Learning designer (preview).

Gebruik deze module om een logistiek regressiemodel te maken dat kan worden gebruikt om twee (en slechts twee) resultaten te voorspellen. 

Logistieke regressie is een bekende statistische techniek die wordt gebruikt voor het modelleren van vele soorten problemen. Dit algoritme is een *begeleide leermethode;*  Daarom moet u een gegevensset opgeven die al de resultaten bevat om het model te trainen.  

### <a name="about-logistic-regression"></a>Over logistieke regressie  

Logistieke regressie is een bekende methode in statistieken die wordt gebruikt om de waarschijnlijkheid van een resultaat te voorspellen, en is vooral populair voor classificatietaken. Het algoritme voorspelt de waarschijnlijkheid van het optreden van een gebeurtenis door gegevens aan te passen aan een logistieke functie.
  
In deze module is het classificatiealgoritme geoptimaliseerd voor dichotomeuze of binaire variabelen. Als u meerdere resultaten moet classificeren, gebruikt u de [multiclass logistic regression-module.](./multiclass-logistic-regression.md)

##  <a name="how-to-configure"></a>Configureren  

Als u dit model wilt trainen, moet u een gegevensset opgeven die een label of klassenkolom bevat. Omdat deze module is bedoeld voor problemen met twee klassen, moet het label of de kolom klasse precies twee waarden bevatten. 

De labelkolom kan bijvoorbeeld [Gestemd] zijn met mogelijke waarden 'Ja' of 'Nee'. Of, het kan [Credit Risk], met mogelijke waarden van "Hoog" of "Laag". 
  
1.  Voeg de **Tweeklasse Logistieke Regressiemodule** toe aan uw pijplijn.  
  
2.  Geef op hoe u het model wilt trainen door de optie **Trainer-modus maken in** te stellen.  
  
    -   **Eén parameter:** als u weet hoe u het model wilt configureren, u een specifieke set waarden als argumenten opgeven.  

    -   **Parameterbereik:** Als u niet zeker bent van de beste parameters, u de optimale parameters vinden met behulp van de module Hyperparameters van [tunemodel.](tune-model-hyperparameters.md) U geeft een aantal waarden en de trainer wijzigt meerdere combinaties van de instellingen om de combinatie van waarden te bepalen die het beste resultaat oplevert.
  
3.  Geef **bij Optimalisatietolerantie**een drempelwaarde op die u moet gebruiken bij het optimaliseren van het model. Als de verbetering tussen iteraties onder de opgegeven drempelwaarde valt, wordt het algoritme geacht te zijn geconvergeerd op een oplossing en stopt de training.  
  
4.  Voor **L1-regularisatiegewicht** en **L2-regularisatiegewicht**typt u een te gebruiken waarde voor de regularisatieparameters L1 en L2. Voor beide wordt een niet-nulwaarde aanbevolen.  
     *Regularisatie* is een methode om overfitting te voorkomen door modellen met extreme coëfficiënten te bestraffen. Regularisatie werkt door het toevoegen van de straf die wordt geassocieerd met coëfficiënten waarden aan de fout van de hypothese. Zo zou een nauwkeurig model met extreme coëfficiënten waarden meer worden bestraft, maar een minder nauwkeurig model met meer conservatieve waarden zou minder worden bestraft.  
  
     L1 en L2 regularisatie hebben verschillende effecten en toepassingen.  
  
    -   L1 kan worden toegepast op schaarse modellen, wat handig is bij het werken met hoogdimensionale gegevens.  
  
    -   L2-regularisatie heeft daarentegen de voorkeur voor gegevens die niet schaars zijn.  
  
     Dit algoritme ondersteunt een lineaire combinatie van L1- en <code>x = L1</code> L2-regularisatiewaarden: dat wil zeggen, als en <code>y = L2</code>vervolgens <code>ax + by = c</code> de lineaire overspanning van de regularisatietermen definieert.  
  
    > [!NOTE]
    >  Wilt u meer weten over L1 en L2 regularisatie? Het volgende artikel geeft een bespreking van hoe L1 en L2 regularisatie zijn verschillend en hoe ze van invloed zijn model montage, met code monsters voor logistieke regressie en neurale netwerk modellen: [L1 en L2 Regularisatie voor Machine Learning](https://msdn.microsoft.com/magazine/dn904675.aspx)  
    >
    > Voor logistieke regressiemodellen zijn verschillende lineaire combinaties van L1- en L2-termen bedacht: bijvoorbeeld [elastische nettoregularisatie](https://wikipedia.org/wiki/Elastic_net_regularization). We raden u aan om naar deze combinaties te verwijzen om een lineaire combinatie te definiëren die effectief is in uw model.
      
5.  Voor **geheugengrootte voor L-BFGS**geeft u de hoeveelheid geheugen op die moet worden gebruikt voor *L-BFGS-optimalisatie.*  
  
     L-BFGS staat voor "limited memory Broyden-Fletcher-Goldfarb-Shanno". Het is een optimalisatiealgoritme dat populair is voor parameterschatting. Deze parameter geeft het aantal posities en verlopen in het verleden aan dat moet worden opgeslagen voor de berekening van de volgende stap.  
  
     Deze optimalisatieparameter beperkt de hoeveelheid geheugen die wordt gebruikt om de volgende stap en richting te berekenen. Wanneer u minder geheugen opgeeft, is de training sneller, maar minder nauwkeurig.  
  
6.  Typ **bij Willekeurig getalzaad**een gehele waarde. Het definiëren van een zaadwaarde is belangrijk als u wilt dat de resultaten reproduceerbaar zijn over meerdere runs van dezelfde pijplijn.  
  
  
8. Voeg een gecodeerde gegevensset toe aan de pijplijn en verbind een van de [trainingsmodules](module-reference.md).  
  
    -   Als u **De trainermodus maken** instelt op Eén **parameter,** gebruikt u de module [Treinmodel.](./train-model.md)  
  
9. Verzend de pijplijn.  
  
## <a name="results"></a>Resultaten

Na de training is voltooid:
 
  
+ Als u voorspellingen wilt doen over nieuwe gegevens, gebruikt u het getrainde model en de nieuwe gegevens als invoer voor de module [Scoremodel.](./score-model.md) 


## <a name="next-steps"></a>Volgende stappen

Bekijk de [set modules die beschikbaar zijn](module-reference.md) voor Azure Machine Learning. 