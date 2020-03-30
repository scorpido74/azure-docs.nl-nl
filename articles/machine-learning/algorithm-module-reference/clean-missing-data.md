---
title: 'Ontbrekende gegevens opschonen: moduleverwijzing'
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de module Ontbrekende gegevens in Azure Machine Learning om ontbrekende waarden te verwijderen, te vervangen of af te leiden.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: 14c3bc968da9d398fbc14eda74378047cf28277b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477694"
---
# <a name="clean-missing-data-module"></a>Module Ontbrekende gegevens opschonen

In dit artikel wordt een module beschreven in Azure Machine Learning designer (preview).

Gebruik deze module om ontbrekende waarden te verwijderen, te vervangen of af te leiden. 

Gegevenswetenschappers controleren vaak gegevens op ontbrekende waarden en voeren vervolgens verschillende bewerkingen uit om de gegevens vast te stellen of nieuwe waarden in te voegen. Het doel van dergelijke reinigingsoperaties is om problemen te voorkomen die worden veroorzaakt door ontbrekende gegevens die zich kunnen voordoen bij het trainen van een model. 

Deze module ondersteunt meerdere soorten bewerkingen voor het "reinigen" van ontbrekende waarden, waaronder:

+ Ontbrekende waarden vervangen door een tijdelijke aanduiding, gemiddelde of andere waarde
+ Rijen en kolommen met ontbrekende waarden volledig verwijderen
+ Conclusie waarden op basis van statistische methoden


Als u deze module gebruikt, wordt uw brongegevensset niet gewijzigd. In plaats daarvan wordt een nieuwe gegevensset in uw werkruimte gemaakt die u gebruiken in de volgende werkstroom. U de nieuwe, opgeschoonde gegevensset ook opslaan voor hergebruik.

Deze module geeft ook een definitie van de transformatie die wordt gebruikt om de ontbrekende waarden schoon te maken. U deze transformatie opnieuw gebruiken op andere gegevenssets met hetzelfde schema, met behulp van de module [Transformatie toepassen.](./apply-transformation.md)  

## <a name="how-to-use-clean-missing-data"></a>Ontbrekende gegevens opschonen gebruiken

Met deze module u een reinigingsbewerking definiëren. U de reinigingsbewerking ook opslaan, zodat u deze later toepassen op nieuwe gegevens. Bekijk de volgende secties over het maken en opslaan van een reinigingsproces: 
 
+ [Ontbrekende waarden vervangen](#replace-missing-values)
  
+ [Een reinigingstransformatie toepassen op nieuwe gegevens](#apply-a-saved-cleaning-operation-to-new-data)
 
> [!IMPORTANT]
> De reinigingsmethode die u gebruikt voor het verwerken van ontbrekende waarden kan uw resultaten drastisch beïnvloeden. We raden u aan te experimenteren met verschillende methoden. Houd rekening met zowel de rechtvaardiging voor het gebruik van een bepaalde methode als de kwaliteit van de resultaten.

### <a name="replace-missing-values"></a>Ontbrekende waarden vervangen  

Elke keer dat u de module [Ontbrekende gegevens opschonen](./clean-missing-data.md) toepast op een set gegevens, wordt dezelfde reinigingsbewerking toegepast op alle kolommen die u selecteert. Als u daarom verschillende kolommen met verschillende methoden moet reinigen, gebruikt u afzonderlijke exemplaren van de module.

1.  Voeg de module [Ontbrekende gegevens opschonen](./clean-missing-data.md) toe aan uw pijplijn en verbind de gegevensset met ontbrekende waarden.  
  
2.  Als **u kolommen wilt laten reinigen,** kiest u de kolommen die de ontbrekende waarden bevatten die u wilt wijzigen. U meerdere kolommen kiezen, maar u moet dezelfde vervangingsmethode gebruiken in alle geselecteerde kolommen. Daarom moet u meestal tekenreekskolommen en numerieke kolommen afzonderlijk schoonmaken.

    Bijvoorbeeld om te controleren op ontbrekende waarden in alle numerieke kolommen:

    1. Selecteer de module **Ontbrekende gegevens opschonen** en klik op **Kolom bewerken** in het rechterdeelvenster van de module.

    3. Selecteer **Kolomtypen** in de vervolgkeuzelijst en selecteer **Numeriek**voor **Opnemen.** 
  
    Elke reinigings- of vervangingsmethode die u kiest, moet van toepassing zijn op **alle** kolommen in de selectie. Als de gegevens in een kolom niet compatibel zijn met de opgegeven bewerking, retourneert de module een fout en stopt de pijplijn.
  
3.  Geef **bij Minimale ontbrekende waardeverhouding**het minimumaantal ontbrekende waarden op dat nodig is voor de uit te voeren bewerking.  
  
    U gebruikt deze optie in combinatie met **maximale waardeverhouding** om de voorwaarden te definiëren waaronder een reinigingsbewerking op de gegevensset wordt uitgevoerd. Als er te veel of te weinig rijen ontbreken die waarden missen, kan de bewerking niet worden uitgevoerd. 
  
    Het getal dat u invoert, vertegenwoordigt de **verhouding** tussen ontbrekende waarden en alle waarden in de kolom. Standaard is de eigenschap **Minimale ontbrekende waardeverhouding** ingesteld op 0. Dit betekent dat ontbrekende waarden worden gereinigd, zelfs als er slechts één ontbrekende waarde is. 

    > [!WARNING]
    > Aan deze voorwaarde moet door elke kolom worden voldaan om de opgegeven bewerking toe te passen. Stel dat u drie kolommen hebt geselecteerd en vervolgens de minimale verhouding van ontbrekende waarden instelt op 0,2 (20%), maar dat slechts één kolom daadwerkelijk 20% ontbrekende waarden heeft. In dit geval is de opruimbewerking alleen van toepassing op de kolom met meer dan 20% ontbrekende waarden. Daarom zouden de andere kolommen ongewijzigd blijven.
    > 
    > Als u twijfelt of ontbrekende waarden zijn gewijzigd, selecteert u de optie **Ontbrekende waardeindicatorkolom genereren**. Een kolom wordt aan de gegevensset toegevoegd om aan te geven of elke kolom al dan niet voldeed aan de opgegeven criteria voor de minimum- en maximumbereiken.  
  
4. Geef **bij Maximale ontbrekende waardeverhouding**het maximumaantal ontbrekende waarden op dat aanwezig kan zijn voor de bewerking die moet worden uitgevoerd.   
  
    U bijvoorbeeld alleen ontbrekende waardesubstitutie uitvoeren als 30% of minder van de rijen ontbrekende waarden bevatten, maar de waarden laten zoals deze zijn als meer dan 30% van de rijen ontbrekende waarden hebben.  
  
    U definieert het getal als de verhouding tussen ontbrekende waarden en alle waarden in de kolom. Standaard is de **verhouding Maximale ontbrekende waarde** ingesteld op 1. Dit betekent dat ontbrekende waarden worden gereinigd, zelfs als 100% van de waarden in de kolom ontbreken.  
  
   
  
5. Selecteer **een**van de volgende opties voor het vervangen of verwijderen van ontbrekende waarden:  
  
  
    + **Aangepaste substitutiewaarde**: gebruik deze optie om een tijdelijke aanduidingswaarde (zoals een 0 of NA) op te geven die van toepassing is op alle ontbrekende waarden. De waarde die u als vervanging opgeeft, moet compatibel zijn met het gegevenstype van de kolom.
  
    + **Vervangen door gemiddelde**: berekent het kolomgemiddelde en gebruikt het gemiddelde als vervangingswaarde voor elke ontbrekende waarde in de kolom.  
  
        Geldt alleen voor kolommen met gehele, dubbele of Booleaanse gegevenstypen.  
  
    + **Vervangen door mediaan**: berekent de mediaanwaarde van de kolom en gebruikt de mediaanwaarde als vervanging voor eventuele ontbrekende waarde in de kolom.  
  
        Geldt alleen voor kolommen met gehele of dubbele gegevenstypen. 
  
    + **Vervangen door modus**: berekent de modus voor de kolom en gebruikt de modus als vervangingswaarde voor elke ontbrekende waarde in de kolom.  
  
        Is van toepassing op kolommen met gehele, dubbele, booleaanse of categorische gegevenstypen. 
  
    + **Hele rij verwijderen:** hiermee verwijdert u elke rij in de gegevensset met een of meer ontbrekende waarden. Dit is handig als de ontbrekende waarde kan worden beschouwd als willekeurig ontbreekt.  
  
    + **Hele kolom verwijderen**: hiermee verwijdert u alle kolommen in de gegevensset met een of meer ontbrekende waarden.  
  
    
  
6. De **optie Vervangingswaarde** is beschikbaar als u de optie **Aangepaste substitutiewaarde**hebt geselecteerd. Typ een nieuwe waarde die u wilt gebruiken als vervangingswaarde voor alle ontbrekende waarden in de kolom.  
  
    Houd er rekening mee dat u deze optie alleen gebruiken in kolommen met de gehele getallen, Dubbel, Booleaan of Tekenreeks.
  
7. **Kolom Ontbrekende waardeindicator genereren:** Selecteer deze optie als u een indicatie wilt geven of de waarden in de kolom voldeden aan de criteria voor het reinigen van de waarde. Deze optie is vooral handig wanneer u een nieuwe reinigingsbewerking instelt en ervoor wilt zorgen dat deze werkt zoals ontworpen.
  
8. Verzend de pijplijn.

### <a name="results"></a>Resultaten

De module retourneert twee uitgangen:  

-   **Opgeschoonde gegevensset**: een gegevensset bestaande uit de geselecteerde kolommen, waarbij ontbrekende waarden worden verwerkt zoals opgegeven, samen met een indicatorkolom, als u die optie hebt geselecteerd.  

    Kolommen die niet zijn geselecteerd voor reiniging worden ook "doorgegeven".  
  
-  **Reinigingstransformatie**: een gegevenstransformatie die wordt gebruikt voor het schoonmaken, die in uw werkruimte kan worden opgeslagen en later op nieuwe gegevens kan worden toegepast.

### <a name="apply-a-saved-cleaning-operation-to-new-data"></a>Een opgeslagen reinigingsbewerking toepassen op nieuwe gegevens  

Als u vaak reinigingsbewerkingen moet herhalen, raden we u aan uw recept voor gegevensreiniging op te slaan als een *transformatie,* om opnieuw te gebruiken met dezelfde gegevensset. Het opslaan van een reinigingstransformatie is vooral handig als u regelmatig opnieuw moet importeren en vervolgens gegevens met hetzelfde schema moet verwijderen.  
      
1.  Voeg de module [Transformatie toepassen](./apply-transformation.md) toe aan uw pijplijn.  
  
2.  Voeg de gegevensset toe die u wilt schoonmaken en sluit de gegevensset aan op de juiste invoerpoort.  
  
3.  Vouw de groep **Transformaties** uit in het linkerdeelvenster van de ontwerper. Zoek de opgeslagen transformatie en sleep deze naar de pijplijn.  

4.  Verbind de opgeslagen transformatie met de linkerinvoerpoort [van Transformatie toepassen](./apply-transformation.md). 

    Wanneer u een opgeslagen transformatie toepast, u niet de kolommen selecteren waarop de transformatie wordt toegepast. Dat komt omdat de transformatie al is gedefinieerd en automatisch van toepassing is op de kolommen die in de oorspronkelijke bewerking zijn opgegeven.

    Stel dat u een transformatie hebt gemaakt op een subset van numerieke kolommen. U deze transformatie toepassen op een gegevensset van gemengde kolomtypen zonder dat er een fout optreedt, omdat de ontbrekende waarden alleen worden gewijzigd in de overeenkomende numerieke kolommen.

6.  Verzend de pijplijn.  

## <a name="next-steps"></a>Volgende stappen

Bekijk de [set modules die beschikbaar zijn](module-reference.md) voor Azure Machine Learning. 