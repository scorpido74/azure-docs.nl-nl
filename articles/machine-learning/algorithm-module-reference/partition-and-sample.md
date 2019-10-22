---
title: 'Partitie en voor beeld: module verwijzing'
titleSuffix: Azure Machine Learning service
description: Meer informatie over het gebruik van de partitie-en voorbeeld module in Azure Machine Learning service om steek proeven op een gegevensset uit te voeren of om partities te maken op basis van uw gegevensset.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: fcbf9fae3306c43613ef0b67a79c9c0b53f6b923
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693760"
---
# <a name="partition-and-sample-module"></a>Partitie en voorbeeld module

In dit artikel wordt een module van de Visual Interface (preview) voor de Azure Machine Learning-service beschreven.

Gebruik deze module om steek proeven op een gegevensset uit te voeren of om partities te maken op basis van uw gegevensset.

Steek proeven zijn een belang rijk hulp middel in machine learning omdat u hiermee de grootte van een gegevensset kunt reduceren en dezelfde verhouding van waarden behouden. Deze module biedt ondersteuning voor verschillende gerelateerde taken die belang rijk zijn in machine learning: 

- Gegevens delen in meerdere subsecties van dezelfde grootte. 

    U kunt de partities voor kruis validatie gebruiken of aanvragen toewijzen aan wille keurige groepen.

- Het scheiden van gegevens in groepen en het werken met gegevens uit een specifieke groep. 

    Wanneer u wille keurig cases aan verschillende groepen toewijst, moet u mogelijk de functies wijzigen die aan slechts één groep zijn gekoppeld.

- Proef. 

    U kunt een percentage van de gegevens extra heren, wille keurige steek proeven Toep assen of een kolom kiezen die u wilt gebruiken om de gegevensset te verdelen en stratified-steek proeven uit te voeren op waarden.

- Een kleinere gegevensset maken voor het testen. 

    Als u veel gegevens hebt, wilt u mogelijk alleen de eerste *n* rijen gebruiken tijdens het instellen van de pijp lijn en vervolgens overschakelen naar het gebruik van de volledige gegevensset wanneer u het model bouwt. U kunt ook steek proeven gebruiken om een kleinere gegevensset te maken voor gebruik in ontwikkeling.

## <a name="configure-partition-and-sample"></a>Partitie en voor beeld configureren

Deze module ondersteunt meerdere methoden voor het delen van uw gegevens in partities of voor steek proeven. Kies eerst de methode en stel vervolgens aanvullende opties in die vereist zijn voor de methode.

- Kop
- Sampling
- Aan vouwen toewijzen
- Vouw kiezen

### <a name="get-top-n-rows-from-a-dataset"></a>BOVENSTE N rijen uit een gegevensset ophalen

Gebruik deze modus om alleen de eerste *n* rijen op te halen. Deze optie is handig als u een pijp lijn wilt testen op een klein aantal rijen en u de gegevens niet op een wille keurige manier wilt uitbalanceren of bemonstert.

1. Voeg de **partitie-en voorbeeld** module toe aan uw pijp lijn in de interface en verbind de gegevensset.  

2. **Partitie of voorbeeld modus**: Stel deze optie in op **kop**.

3. **Aantal rijen dat moet worden geselecteerd**: Typ het aantal rijen dat u wilt retour neren.

    Het aantal rijen dat u opgeeft, moet een niet-negatief geheel getal zijn. Als het aantal geselecteerde rijen groter is dan het aantal rijen in de gegevensset, wordt de hele gegevensset geretourneerd.

4. Voer de pijplijn uit.

De module voert een enkele gegevensset uit met alleen het opgegeven aantal rijen. De rijen worden altijd gelezen vanaf de bovenkant van de gegevensset.

### <a name="create-a-sample-of-data"></a>Een voor beeld van gegevens maken

Deze optie ondersteunt eenvoudige wille keurige steek proeven of stratified wille keurige steek proeven. Dit is handig als u een kleinere representatieve voorbeeld gegevensset wilt maken voor het testen.

1. Voeg de **partitie-en voorbeeld** module toe aan uw pijp lijn en verbind de gegevensset.

2. **Partitie of voorbeeld modus**: Stel dit in op **steek proeven**.

3. **Sampling frequentie**: Typ een waarde tussen 0 en 1. met deze waarde wordt het percentage rijen van de bron-gegevensset opgegeven dat moet worden opgenomen in de uitvoer gegevensset.

    Als u bijvoorbeeld alleen de helft van de oorspronkelijke gegevensset wilt, typt u `0.5` om aan te geven dat de sampling frequentie 50% moet zijn.

    De rijen van de invoer gegevensset worden in een wille keurige volg orde geplaatst en op basis van de opgegeven verhouding op selectief ingesteld in de uitvoer gegevensset.

4. **Wille keurig zaad voor steek proeven**: Typ eventueel een geheel getal dat moet worden gebruikt als een Seed-waarde.

    Deze optie is belang rijk als u wilt dat de rijen op dezelfde manier worden gedeeld als elke keer. De standaard waarde is 0, wat betekent dat een begin Seed wordt gegenereerd op basis van de systeem klok. Dit kan leiden tot enigszins verschillende resultaten telkens wanneer u de pijp lijn uitvoert.

5. **Stratified splitsen voor steek proeven**: Selecteer deze optie als het belang rijk is dat de rijen in de gegevensset gelijkmatig moeten worden verdeeld over een bepaalde sleutel kolom vóór de steek proef.

    Voor een **stratificatie sleutel kolom voor steek proeven**selecteert u één *Strata kolom* die moet worden gebruikt bij het delen van de gegevensset. De rijen in de gegevensset worden vervolgens als volgt onderverdeeld:

    1. Alle invoer rijen worden gegroepeerd (stratified) door de waarden in de opgegeven kolom Strata.

    2. Rijen worden in elke groep in een andere volg orde geplaatst.

    3. Elke groep wordt selectief toegevoegd aan de uitvoer gegevensset om te voldoen aan de opgegeven verhouding.


6. Voer de pijplijn uit.

    Met deze optie voert de module een enkele gegevensset uit die een representatieve steek proef van de gegevens bevat. Het resterende, niet-bemonsterde gedeelte van de gegevensset wordt niet uitgevoerd. 

## <a name="split-data-into-partitions"></a>Gegevens in partities splitsen

Gebruik deze optie als u de gegevensset wilt verdelen over subsets van de gegevens. Deze optie is ook handig als u een aangepast aantal vouwen wilt maken voor kruis validatie of als u rijen wilt splitsen in verschillende groepen.

1. Voeg de **partitie-en voorbeeld** module toe aan uw pijp lijn en verbind de gegevensset.

2. Voor **partitie of voorbeeld modus**selecteert **u toewijzen aan vouwen**.

3. **Vervanging gebruiken in partitioneren**: Selecteer deze optie als u wilt dat de geplaatste rij wordt weer gegeven in de pool met rijen voor een mogelijke hergebruik. Als gevolg hiervan kan dezelfde rij worden toegewezen aan verschillende vouwen.

    Als u geen vervanging gebruikt (de standaard optie), wordt de geplaatste rij niet weer gegeven in de pool met rijen voor een mogelijke hergebruik. Als gevolg hiervan kan elke rij slechts aan één vouw worden toegewezen.

4. **Wille keurige splitsing**: Selecteer deze optie als u wilt dat rijen wille keurig worden toegewezen aan vouwen.

    Als u deze optie niet selecteert, worden de rijen aan de vouwen met de Round-Robin methode toegewezen.

5. **Wille keurig zaad**: Typ eventueel een geheel getal dat moet worden gebruikt als de Seed-waarde. Deze optie is belang rijk als u wilt dat de rijen op dezelfde manier worden gedeeld als elke keer. Anders is de standaard waarde 0 betekent dat een wille keurige begin Seed wordt gebruikt.

6. **Geef de partitioner-methode**op: Geef aan hoe u wilt dat gegevens worden verdeeld over elke partitie, met behulp van de volgende opties:

    - **Gelijkmatige partitie**: gebruik deze optie om een gelijk aantal rijen in elke partitie te plaatsen. Als u het aantal uitvoer partities wilt opgeven, typt u een geheel getal in het **aantal vouwen dat u wilt splitsen, gelijkmatig** in het tekstvak.

    - **Partitie met aangepaste verhoudingen**: gebruik deze optie om de grootte van elke partitie op te geven als een lijst met door komma's gescheiden waarden.

        Als u bijvoorbeeld drie partities wilt maken, met de eerste partitie met 50% van de gegevens en de resterende twee partities elk 25% van de gegevens bevatten, klikt u op de **lijst met verhoudingen gescheiden door komma's** en typt u deze getallen: @no_ _t_1

        De som van alle partitie grootten moet precies 1 zijn.

        - Als u getallen opgeeft die Maxi maal **1**zijn, wordt er een extra partitie gemaakt om de resterende rijen te bewaren. Als u bijvoorbeeld de waarden .2 en .3 typt, wordt er een derde partitie gemaakt die het resterende 50 procent van alle rijen bevat.

        - Als u getallen opgeeft die Maxi maal **1**zijn, treedt er een fout op wanneer u de pijp lijn uitvoert.

7. **Stratified splitsen**: Selecteer deze optie als u wilt dat de rijen worden stratified wanneer deze worden gesplitst en kies vervolgens de _kolom Strata_.

8. Voer de pijplijn uit.

    Met deze optie voert de module meerdere gegevens sets uit, gepartitioneerd met de regels die u hebt opgegeven.

### <a name="use-data-from-a-predefined-partition"></a>Gegevens van een vooraf gedefinieerde partitie gebruiken  

Deze optie wordt gebruikt wanneer u een gegevensset hebt onderverdeeld in meerdere partities en nu elke partitie op zijn beurt wilt laden voor verdere analyse of verwerking.

1. Voeg de **partitie en de voorbeeld** module toe aan de pijp lijn.

2. Verbind deze met de uitvoer van een eerder exemplaar van de **partitie en**het voor beeld. Dat exemplaar moet de optie **toewijzen aan vouwen** hebben gebruikt om een aantal partities te genereren.

3. **Partitie of voorbeeld modus**: Selecteer **vouw vouwen**.

4. **Geef op met welke vouw moet worden steek proef**: Selecteer een te gebruiken partitie door de index ervan te typen. Partitie-indexen zijn op 1 gebaseerd. Als u de gegevensset bijvoorbeeld in drie delen hebt verdeeld, hebben de partities de indices 1, 2 en 3.

    Als u een ongeldige index waarde typt, wordt de volgende fout weer gegeven: ' fout 0018: gegevensset bevat ongeldige gegevens '.

    Naast het groeperen van de gegevensset door vouwen, kunt u de gegevensset in twee groepen scheiden: een doel vouwen en alle andere. Hiertoe typt u de index van één vouw en selecteert u vervolgens de optie, een **aanvulling op de geselecteerde vouw**, om alles op te halen, behalve de gegevens in de opgegeven vouwen.

5. Als u met meerdere partities werkt, moet u extra exemplaren van de **partitie en voorbeeld** module toevoegen om elke partitie af te handelen.

    U kunt bijvoorbeeld eerder gepartitioneerde patiënten in vier vouwen met leeftijd. Als u met elke afzonderlijke vouw wilt werken, hebt u vier kopieën van de **partitie en voorbeeld** module nodig en selecteert u een andere vouw, zoals hieronder wordt weer gegeven. Het is niet juist om de uitvoer van de functie **toewijzen aan vouwen** rechtstreeks te gebruiken.  

    [![Partition en voor beeld](./media/partition-and-sample/partition-and-sample.png)](./media/partition-and-sample/partition-and-sample-lg.png#lightbox)

5. Voer de pijplijn uit.

    Met deze optie voert de module een enkele gegevensset uit die alleen de rijen bevat die aan de vouwen zijn toegewezen.

> [!NOTE]
>  U kunt de gevouwen ontwerps niet rechtstreeks weer geven. ze zijn alleen aanwezig in de meta gegevens.

## <a name="next-steps"></a>Volgende stappen

Bekijk de [set beschik bare modules](module-reference.md) voor Azure machine learning service. 