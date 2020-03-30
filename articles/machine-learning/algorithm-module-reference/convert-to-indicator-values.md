---
title: Converteren naar indicatorwaarden
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de module Omzetten naar indicatorwaarden in Azure Machine Learning om kolommen die categorische waarden bevatten om te zetten in een reeks binaire indicatorkolommen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: f1b194f2c65f95ad4daff0353d05ca589db9ce51
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477660"
---
# <a name="convert-to-indicator-values"></a>Converteren naar indicatorwaarden
In dit artikel wordt een module van Azure Machine Learning-ontwerper beschreven.

Gebruik de module **Converteren naar indicatorwaarden** in Azure Machine Learning-ontwerper om kolommen die categorische waarden bevatten om te zetten in een reeks binaire indicatorkolommen.  

In deze module wordt ook een definitie uitgevoerd van de transformatie die wordt gebruikt om te converteren naar indicatorwaarden. U deze transformatie opnieuw gebruiken op andere gegevenssets met hetzelfde schema, met behulp van de module [Transformatie toepassen.](apply-transformation.md)

## <a name="how-to-configure-convert-to-indicator-values"></a>Converteren naar indicatorwaarden configureren

1.  Zoek de **indicatorwaarden converteren** en sleep deze naar het pijplijnconcept. U deze module vinden onder de categorie **Gegevenstransformatie.**
    > [!NOTE]
    > U de module [Metagegevens bewerken](edit-metadata.md) gebruiken voordat de module **Indiciatorwaarden converteren naar indiciatorwaarden** wordt gebruikt om de doelkolom(en) als categorisch te markeren.

1. Sluit de module **Converteren naar indicatorwaarden** aan op de gegevensset met de kolommen die u wilt converteren. 

1. Selecteer **Kolom bewerken** om een of meer categorische kolommen te kiezen.

1. Selecteer de optie **Categorische kolommen overschrijven** als u **alleen** de nieuwe Booleaanse kolommen wilt uitvoeren. Standaard is deze optie uitgeschakeld.
    

    > [!TIP]
    >  Als u de optie kiest om te overschrijven, wordt de bronkolom niet daadwerkelijk verwijderd of gewijzigd. In plaats daarvan worden de nieuwe kolommen gegenereerd en weergegeven in de uitvoergegevensset en blijft de bronkolom beschikbaar in de werkruimte. Als u de oorspronkelijke gegevens wilt zien, u de module [Kolommen toevoegen](add-columns.md) op elk gewenst moment gebruiken om de bronkolom weer toe te voegen.

1. Verzend de pijplijn.

## <a name="results"></a>Resultaten

Stel dat u een kolom met scores hebt die aangeven of een server een hoge, gemiddelde of lage kans op falen heeft.  

| Server-id | Score voor mislukte mislukking |
| --------- | ------------- |
| 10301     | Laag           |
| 10302     | Middelgroot        |
| 10303     | Hoog          |

Wanneer u **Convert to Indicatorwaarden**toepast, converteert de ontwerper één kolom met labels in meerdere kolommen met Booleaanse waarden:  

| Server-id | Faalscore - Laag | Faalscore - Gemiddeld | Faalscore - Hoog |
| --------- | ------------------- | ---------------------- | -------------------- |
| 10301     | 1                   | 0                      | 0                    |
| 10302     | 0                   | 1                      | 0                    |
| 10303     | 0                   | 0                      | 1                    |

Zo werkt de conversie:  

-   In de kolom **Foutscore** waarin het risico wordt beschreven, zijn er slechts drie mogelijke waarden (Hoog, Gemiddeld en Laag) en geen ontbrekende waarden. Er worden dus precies drie nieuwe kolommen gemaakt.  

-   De nieuwe indicatorkolommen worden benoemd op basis van de kolomkoppen en waarden van de bronkolom, met behulp van dit patroon: * \<bronkolom>- \<gegevenswaarde>*.  

-   Er moet een 1 in precies een indicator kolom, en 0 in alle andere indicator kolommen, omdat elke server kan slechts een risicobeoordeling hebben.  

U de drie indicatorkolommen nu gebruiken als functies in een machine learning-model.

De module retourneert twee uitgangen:

- **Gegevensset Resultaten**: een gegevensset met kolommen met geconverteerde indicatorwaarden. Kolommen die niet zijn geselecteerd voor reiniging worden ook "doorgegeven".
- **Indicatorwaardentransformatie:** een gegevenstransformatie die wordt gebruikt voor het converteren naar indicatorwaarden, die in uw werkruimte kan worden opgeslagen en later op nieuwe gegevens kan worden toegepast.

## <a name="apply-a-saved-indicator-values-operation-to-new-data"></a>Een opgeslagen indicatorwaardenbewerking toepassen op nieuwe gegevens

Als u vaak indicatorwaardenbewerkingen moet herhalen, u uw stappen voor gegevensmanipulatie opslaan als een *transformatie* om deze opnieuw te gebruiken met dezelfde gegevensset. Dit is handig als u regelmatig gegevens met hetzelfde schema opnieuw moet importeren en vervolgens moet opschonen.

1. Voeg de module [Transformatie toepassen](apply-transformation.md) toe aan uw pijplijn.

1. Voeg de gegevensset toe die u wilt schoonmaken en sluit de gegevensset aan op de juiste invoerpoort.

1. Vouw de groep **Gegevenstransformatie** uit in het linkerdeelvenster van de ontwerper. Zoek de opgeslagen transformatie en sleep deze naar de pijplijn.

1. Verbind de opgeslagen transformatie met de linkerinvoerpoort [van Transformatie toepassen](apply-transformation.md).

   Wanneer u een opgeslagen transformatie toepast, u niet selecteren welke kolommen u wilt transformeren. Dit komt omdat de transformatie is gedefinieerd en automatisch van toepassing is op de gegevenstypen die in de oorspronkelijke bewerking zijn opgegeven.

1. Verzend de pijplijn.
 
## <a name="technical-notes"></a>Technische notities  

Deze sectie bevat implementatiedetails, tips en antwoorden op veelgestelde vragen.

### <a name="usage-tips"></a>Gebruikstips

-   Alleen kolommen die als categorisch zijn gemarkeerd, kunnen worden geconverteerd naar indicatorkolommen. Als u de volgende fout ziet, is het waarschijnlijk dat een van de geselecteerde kolommen niet categorisch is:  

     Fout 0056: Kolom \<met naamkolomnaam> valt niet in een toegestane categorie.  

     Standaard worden de meeste tekenreekskolommen behandeld als tekenreeksfuncties, dus u moet ze expliciet markeren als categorisch met behulp [van Metagegevens bewerken.](edit-metadata.md)  

-   Er is geen limiet aan het aantal kolommen dat u converteren naar indicatorkolommen. Omdat elke kolom met waarden echter meerdere indicatorkolommen kan opleveren, u slechts een paar kolommen tegelijk converteren en controleren.  

-   Als de kolom ontbrekende waarden bevat, wordt een aparte indicatorkolom gemaakt voor de ontbrekende categorie, met deze naam: * \<bronkolom>- Ontbrekend*  

-   Als de kolom die u converteert naar indicatorwaarden getallen bevat, moeten deze worden gemarkeerd als categorisch zoals elke andere functiekolom. Nadat u dit hebt gedaan, worden de getallen behandeld als afzonderlijke waarden. Als u bijvoorbeeld een numerieke kolom hebt met MPG-waarden variërend van 25 tot 30, wordt voor elke afzonderlijke waarde een nieuwe indicatorkolom gemaakt:  

    | Merk       | Snelweg mpg -25 | Snelweg mpg -26 | Snelweg mpg -27 | Snelweg mpg -28 | Snelweg mpg -29 | Snelweg mpg -30 |
    | ---------- | --------------- | --------------- | --------------- | --------------- | --------------- | --------------- |
    | Contoso Auto's | 0               | 0               | 0               | 0               | 0               | 1               |

- Om te voorkomen dat er te veel dimensies aan uw gegevensset worden toegevoegd. We raden u aan eerst het aantal waarden in de kolom te controleren en de gegevens op de juiste manier te beheren of te kwantificeren.  


## <a name="next-steps"></a>Volgende stappen

Bekijk de [set modules die beschikbaar zijn](module-reference.md) voor Azure Machine Learning. 
