---
title: 'Functie selectie op basis van filters: module verwijzing'
titleSuffix: Azure Machine Learning service
description: Meer informatie over het gebruik van de module voor het selecteren van onderdelen in Azure Machine Learning service om de functies in een gegevensset met de grootste voorspellende energie te identificeren.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: ac1421c93f1a4ca42d7f1d94bb898c423c380a57
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73517967"
---
# <a name="filter-based-feature-selection"></a>Functies selecteren op basis van filter

In dit artikel wordt beschreven hoe u de module voor het [selecteren van onderdelen op basis](filter-based-feature-selection.md) van Azure machine learning Designer (preview) gebruikt om de kolommen in de invoer-gegevensset te identificeren die de grootste voorspellende kracht hebben. 

In het algemeen verwijst het *selecteren van functies* naar het proces van het Toep assen van statistische tests op invoer, op basis van een opgegeven uitvoer, om te bepalen welke kolommen meer voor spelling van de uitvoer zijn. De module voor het [selecteren van functies op basis van filters](filter-based-feature-selection.md) bevat meerdere algoritmen voor het selecteren van onderdelen, inclusief correlatie methoden zoals Pearson-correlatie en Chi-kwadraat waarden. 

Wanneer u de module voor het [selecteren van functies op basis van filters](filter-based-feature-selection.md) gebruikt, geeft u een gegevensset op, identificeert u de kolom met het label of de afhankelijke variabele en geeft u vervolgens één methode op die moet worden gebruikt voor het meten van de functie urgentie.

De module levert een gegevensset die de beste functie kolommen bevat, zoals wordt aangegeven door voorspellende energie. Ook worden de namen van de functies en de scores van de geselecteerde metriek uitgevoerd.  

### <a name="what-is-filter-based-feature-selection-and-why-use-it"></a>Wat is selectie van functie op filter basis en waarom gebruiken?  

Deze module voor het selecteren van functies wordt ' filter gebaseerd ' genoemd, omdat u de geselecteerde metrische gegevens gebruikt om irrelevante kenmerken te identificeren en overbodige kolommen uit uw model te filteren.  U kiest één statistische maat eenheid die aansluit bij uw gegevens en de module berekent een score voor elke functie kolom. De kolommen worden weer gegeven op basis van hun functie scores. 

Door de juiste functies te kiezen, kunt u de nauw keurigheid en efficiëntie van de classificatie verbeteren. 

Normaal gesp roken gebruikt u alleen de kolommen met de beste scores om uw voorspellende model te bouwen. Kolommen met slechte onderdelen selectie scores kunnen in de gegevensset worden achtergelaten en worden genegeerd wanneer u een model bouwt.  

### <a name="how-to-choose-a-feature-selection-metric"></a>De metriek van een functie selecteren

De **functie selectie op basis van filters** biedt diverse metrische gegevens voor het beoordelen van de informatie waarde in elke kolom.  Deze sectie bevat een algemene beschrijving van elke metriek en hoe deze wordt toegepast. Aanvullende vereisten voor het gebruik van elke metriek worden vermeld in het gedeelte [technische opmerkingen](#technical-notes) en in de [instructies](#how-to-configure-filter-based-feature-selection) voor het configureren van elke module.

-   **Pearson-correlatie**  

     Correlatie statistieken van Pearson of correlatie coëfficiënt van Pearson worden ook bekend in statistische modellen als de `r` waarde. Voor twee variabelen retourneert deze een waarde die de sterkte van de correlatie aangeeft

     De correlatie coëfficiënt van Pearson wordt berekend door de covariantie van twee variabelen te nemen en te delen door het product van de standaard afwijkingen. De coëfficiënt wordt niet beïnvloed door wijzigingen van de schaal in de twee variabelen.  

-   **Chi-kwadraat**  

     De tweezijdige Chi-kwadraat test is een statistische methode waarmee wordt gemeten hoe het sluiten van verwachte waarden tot werkelijke resultaten leidt. De methode veronderstelt dat variabelen wille keurig zijn en worden getekend uit een adequaat voor beeld van onafhankelijke variabelen. Met de resulterende Chi-kwadraat statistiek wordt aangegeven hoe ver resultaten van het verwachte (wille keurig) resultaat zijn.  


> [!TIP]
> Als u een andere optie voor de selectie methode voor aangepaste functies nodig hebt, gebruikt u de module [R-script uitvoeren](execute-r-script.md) . 
##  <a name="how-to-configure-filter-based-feature-selection"></a>Selectie van functies op basis van filters configureren

U kiest een standaard statistische metrische waarde en de module berekent de correlatie tussen een paar kolommen: de kolom Label en een functie kolom

1.  Voeg de **functie selectie module voor het filteren van functies** toe aan uw pijp lijn. U kunt deze vinden in de categorie **functie selectie** in de ontwerper.

2. Verbind een invoer-gegevensset die ten minste twee kolommen bevat die mogelijke functies zijn.  

    Om ervoor te zorgen dat een kolom moet worden geanalyseerd en een gegenereerde functie Score, gebruikt u de module [meta gegevens bewerken](edit-metadata.md) om het kenmerk **IsFeature** in te stellen. 

    > [!IMPORTANT]
    > Zorg ervoor dat de kolommen die u opgeeft als invoer, mogelijke functies zijn. Een kolom met één waarde bevat bijvoorbeeld geen informatie waarde.
    >
    > Als u weet dat er kolommen zijn die onjuiste functies zouden kunnen vormen, kunt u ze uit de kolom selectie verwijderen. U kunt ook de module [meta gegevens bewerken](edit-metadata.md) gebruiken om ze als **categorische**te markeren. 
3.  Voor de **functie Score methode**kiest u een van de volgende statistische methoden die u kunt gebruiken bij het berekenen van scores.  

    | Methode              | Vereisten                             |
    | ------------------- | ---------------------------------------- |
    | Pearson-correlatie | Label kan tekst of numeriek zijn. De functies moeten numeriek zijn. |
    Chi-kwadraat| Labels en functies kunnen tekst of numeriek zijn. Gebruik deze methode voor het berekenen van de functie prioriteit voor twee categorische-kolommen.|

    > [!TIP]
    > Als u de geselecteerde metriek wijzigt, worden alle andere selecties opnieuw ingesteld. Zorg er dus voor dat u deze optie eerst instelt.)
4.  Selecteer de optie **alleen werk kolommen gebruiken** om een score te genereren voor de kolommen die eerder als functies zijn gemarkeerd. 

    Als u deze optie uitschakelt, wordt door de module een score gemaakt voor elke kolom die anders aan de criteria voldoet, tot het aantal kolommen dat is opgegeven in het **aantal gewenste functies**.  

5.  Klik bij **doel kolom**op **starten kolom kiezer** om de kolom label te kiezen op naam of op index (indexen zijn gebaseerd op één).  

     Een label kolom is vereist voor alle methoden waarbij statistische correlatie is betrokken. De module retourneert een fout in de ontwerp fase als u geen label kolom of meerdere label kolommen selecteert. 

6.  Voor het **aantal gewenste functies**typt u het aantal functie kolommen dat als resultaat moet worden geretourneerd.  

     - Het minimum aantal functies dat u kunt opgeven, is 1, maar we raden u aan deze waarde te verhogen.  

     - Als het opgegeven aantal gewenste functies groter is dan het aantal kolommen in de gegevensset, worden alle functies geretourneerd, zelfs de onderdelen met een Score van nul.  

    - Als u minder resultaat kolommen opgeeft dan er functie kolommen zijn, worden de functies gerangschikt op aflopende Score en worden alleen de belangrijkste functies geretourneerd. 

7.  Voer de pijp lijn uit of selecteer de module [filter op basis van de functie selectie](filter-based-feature-selection.md) en klik vervolgens op **geselecteerde uitvoeren**.


## <a name="results"></a>Resultaten

Nadat de verwerking is voltooid:

+ Klik met de rechter muisknop op de module, selecteer **onderdelen**en klik op **visualiseren**voor een volledige lijst met de geanalyseerde functie kolommen en de bijbehorende scores.  

+ Als u de gegevensset wilt weer geven die is gegenereerd op basis van de selectie criteria van uw functie, klikt u met de rechter muisknop op de module, selecteert u **gegevensset**en klikt u op **visualiseren**. 

Als de gegevensset minder kolommen bevat dan u had verwacht, controleert u de module-instellingen en de gegevens typen van de kolommen die zijn opgegeven als invoer. Als u bijvoorbeeld het **aantal gewenste functies** instelt op 1, bevat de uitvoer gegevensset slechts twee kolommen: de kolom Label en de meest gerangschikte functie kolom.


##  <a name="technical-notes"></a>Technische opmerkingen  

### <a name="implementation-details"></a>Implementatie Details

Als u Pearson-correlatie gebruikt voor een numerieke functie en een categorische-label, wordt de functie score als volgt berekend:  

1.  Voor elk niveau in de kolom categorische berekent u het voorwaardelijke gemiddelde van de numerieke kolom.  

2.  Correleer de kolom van voorwaardelijke middelen met de numerieke kolom.  

### <a name="requirements"></a>Vereisten  

-   Er kan geen score voor de functie selectie worden gegenereerd voor een kolom die is ingesteld als een **Label** of als een **Score** kolom.  

-   Als u probeert een score methode te gebruiken met een kolom van een gegevens type dat niet door de methode wordt ondersteund, wordt een fout door de module gegenereerd of wordt er een nul-score toegewezen aan de kolom.  

-   Als een kolom logische waarden (True/False) bevat, worden deze verwerkt als True = 1 en False = 0.  

-   Een kolom kan geen functie zijn als deze is aangewezen als een **Label** of **Score**.  

### <a name="how-missing-values-are-handled"></a>Hoe ontbrekende waarden worden verwerkt  

-   U kunt geen doel kolom (label) opgeven voor elke kolom met alle ontbrekende waarden.  

-   Als een kolom ontbrekende waarden bevat, worden deze genegeerd bij het berekenen van de score voor de kolom.  

-   Als een kolom die is opgegeven als een functie kolom, alle ontbrekende waarden bevat, wordt een Score van nul toegewezen.   


## <a name="next-steps"></a>Volgende stappen

Bekijk de [set beschik bare modules](module-reference.md) voor Azure machine learning service. 

