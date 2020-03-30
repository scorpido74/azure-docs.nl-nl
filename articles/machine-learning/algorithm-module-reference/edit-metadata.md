---
title: 'Metagegevens bewerken: naslaginformatie over module'
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de module Metagegevens bewerken in de Azure Machine Learning om metagegevens te wijzigen die zijn gekoppeld aan kolommen in een gegevensset.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: 9853a3decc8d145fee58d1da526926e224ee2030
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064247"
---
# <a name="edit-metadata-module"></a>Module Metagegevens bewerken

In dit artikel wordt een module beschreven die is opgenomen in Azure Machine Learning designer (preview).

Gebruik de module Metagegevens bewerken om metagegevens te wijzigen die zijn gekoppeld aan kolommen in een gegevensset. De waarde en het gegevenstype van de gegevensset worden gewijzigd na gebruik van de module Metagegevens bewerken.

Typische wijzigingen in metagegevens kunnen zijn:
  
+ Booleaanse of numerieke kolommen behandelen als categorische waarden.
  
+ Als u aangeeft welke kolom het **klassenlabel** bevat of de waarden bevat die u wilt categoriseren of voorspellen.
  
+ Kolommen markeren als kenmerken.
  
+ Datum-tijdwaarden wijzigen in numerieke waarden of vice versa.
  
+ Kolommen hernoemen.
  
 Gebruik Metagegevens bewerken wanneer u de definitie van een kolom moet wijzigen, meestal om te voldoen aan de vereisten voor een downstreammodule. Sommige modules werken bijvoorbeeld alleen met specifieke gegevenstypen of vereisen `IsFeature` `IsCategorical`vlaggen op de kolommen, zoals of .  
  
 Nadat u de vereiste bewerking hebt uitgevoerd, u de metagegevens in de oorspronkelijke staat opnieuw instellen.
  
## <a name="configure-edit-metadata"></a>Metagegevens bewerken configureren
  
1. Voeg in Azure Machine Learning-ontwerper de module Metagegevens bewerken toe aan uw pijplijn en sluit u de gegevensset aan die u wilt bijwerken. U de module vinden in de categorie **Gegevenstransformatie.**
  
1. Klik **op Kolom bewerken** in het rechterdeelvenster van de module en kies de kolom of set kolommen waarmee u wilt werken. U kolommen afzonderlijk kiezen op naam of index of u een groep kolommen op type kiezen.  
  
1. Selecteer de optie **Gegevenstype** als u een ander gegevenstype aan de geselecteerde kolommen wilt toewijzen. Mogelijk moet u het gegevenstype voor bepaalde bewerkingen wijzigen. Als in uw brongegevensset bijvoorbeeld getallen als tekst worden verwerkt, moet u deze wijzigen in een numeriek gegevenstype voordat u wiskundige bewerkingen gebruikt.

    + De ondersteunde gegevenstypen zijn **String**, **Integer**, **Double**, **Boolean**en **DateTime**.

    + Als u meerdere kolommen selecteert, moet u de metagegevenswijzigingen toepassen op *alle* geselecteerde kolommen. Stel dat u twee of drie numerieke kolommen kiest. U ze allemaal wijzigen in een tekenreeksgegevenstype en ze in één bewerking een andere naam geven. U echter geen kolom wijzigen in een tekenreeksgegevenstype en een andere kolom van een zwevende naar een geheel getal.
  
    + Als u geen nieuw gegevenstype opgeeft, blijven de metagegevens van de kolom ongewijzigd.

    + Het kolomtype en de waarden worden gewijzigd nadat u de bewerking Metagegevens bewerken hebt uitgevoerd. U het oorspronkelijke gegevenstype op elk gewenst moment herstellen met met metagegevens bewerken om het kolomgegevenstype opnieuw in te stellen.  

    > [!NOTE]
    > Als u een type nummer wijzigt in het **type DateTime,** laat u het veld **DateTime-notatie** leeg. Momenteel is het niet mogelijk om de doelgegevensindeling op te geven.  

1. Selecteer de optie **Categorisch** om op te geven dat de waarden in de geselecteerde kolommen als categorieën moeten worden behandeld.

    U bijvoorbeeld een kolom hebben met de nummers 0, 1 en 2, maar weet dat de getallen in feite 'Roker', 'Niet-roker' en 'Onbekend' betekenen. In dat geval zorgt u ervoor dat de waarden alleen worden gebruikt om gegevens te groeperen en niet in numerieke berekeningen door de kolom als categorisch te markeren.
  
1. Gebruik de optie **Velden** als u de manier wilt wijzigen waarop Azure Machine Learning de gegevens in een model gebruikt.

    + **Functie:** gebruik deze optie om een kolom te markeren als functie in modules die alleen op functiekolommen werken. Standaard worden alle kolommen in eerste instantie behandeld als functies.  
  
    + **Label:** gebruik deze optie om het label te markeren, dat ook wel het voorspelbare kenmerk of doelvariabele wordt genoemd. Veel modules vereisen dat er precies één labelkolom aanwezig is in de gegevensset.

        In veel gevallen kan Azure Machine Learning afleiden dat een kolom een klassenlabel bevat. Door deze metagegevens in te stellen, u ervoor zorgen dat de kolom correct wordt geïdentificeerd. Als u deze optie instelt, worden de gegevenswaarden niet gewijzigd. Het verandert alleen de manier waarop sommige machine learning-algoritmen omgaan met de gegevens.
  
    > [!TIP]
    > Heeft u gegevens die niet in deze categorieën passen? Uw gegevensset kan bijvoorbeeld waarden bevatten, zoals unieke id's die niet nuttig zijn als variabelen. Soms kunnen dergelijke id's problemen veroorzaken bij gebruik in een model.
    >
    > Gelukkig bewaart Azure Machine Learning al uw gegevens, zodat u dergelijke kolommen niet uit de gegevensset hoeft te verwijderen. Wanneer u bewerkingen moet uitvoeren op een speciale set kolommen, verwijdert u alle andere kolommen tijdelijk met behulp van de module [Kolommen selecteren in gegevensset.](select-columns-in-dataset.md) Later u de kolommen weer samenvoegen in de gegevensset met behulp van de module [Kolommen toevoegen.](add-columns.md)  
  
1. Gebruik de volgende opties om eerdere selecties te wissen en metagegevens te herstellen naar de standaardwaarden.  
  
    + **Duidelijke functie**: Gebruik deze optie om de functievlag te verwijderen.  
  
         Alle kolommen worden in eerste instantie behandeld als functies. Voor modules die wiskundige bewerkingen uitvoeren, moet u deze optie mogelijk gebruiken om te voorkomen dat numerieke kolommen als variabelen worden behandeld.
  
    + **Label wissen:** gebruik deze optie om de **labelmetagegevens** uit de opgegeven kolom te verwijderen.  
  
    + **Duidelijke score**: Gebruik deze optie om de **metagegevens** van de score uit de opgegeven kolom te verwijderen.  
  
         U een kolom momenteel niet expliciet markeren als een score in Azure Machine Learning. Sommige bewerkingen resulteren er echter in dat een kolom intern wordt gemarkeerd als een score. Een aangepaste R-module kan ook scorewaarden uitzetten.

1. Voer **voor Nieuwe kolomnamen**de nieuwe naam van de geselecteerde kolom of kolommen in.  
  
    + Kolomnamen kunnen alleen tekens gebruiken die worden ondersteund door UTF-8-codering. Lege tekenreeksen, nullen of namen die volledig uit spaties bestaan, zijn niet toegestaan.  
  
    + Als u de naam van meerdere kolommen wilt wijzigen, voert u de namen in als een door komma's gescheiden lijst in volgorde van de kolomindexen.  
  
    + Alle geselecteerde kolommen moeten de naam worden gewijzigd. U kolommen niet weglaten of overslaan.  
  
1. Verzend de pijplijn.  

## <a name="next-steps"></a>Volgende stappen

Bekijk de [set modules die beschikbaar zijn](module-reference.md) voor Azure Machine Learning.
