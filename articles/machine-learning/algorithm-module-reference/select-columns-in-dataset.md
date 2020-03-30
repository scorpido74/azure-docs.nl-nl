---
title: 'Kolommen selecteren in gegevensset: moduleverwijzing'
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de module Kolommen selecteren in gegevenssetin Azure Machine Learning om een subset van kolommen te kiezen die u wilt gebruiken in downstreambewerkingen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: 714148815fa4ff543ecbf86ab21e8d51fe775543
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77153771"
---
# <a name="select-columns-in-dataset-module"></a>Kolommen selecteren in de module Gegevensset

In dit artikel wordt een module beschreven in Azure Machine Learning designer (preview).

Gebruik deze module om een subset van kolommen te kiezen die u wilt gebruiken in downstreambewerkingen. De module verwijdert de kolommen niet fysiek uit de brongegevensset; in plaats daarvan maakt het een subset van kolommen, net als een *databaseweergave* of *-projectie.*

Deze module is handig wanneer u de kolommen die beschikbaar zijn voor een downstreambewerking moet beperken of als u de grootte van de gegevensset wilt verkleinen door overbodige kolommen te verwijderen.

De kolommen in de gegevensset worden in dezelfde volgorde uitgevoerd als in de oorspronkelijke gegevens, zelfs als u ze in een andere volgorde opgeeft.

## <a name="how-to-use"></a>Gebruiksinstructies

Deze module heeft geen parameters. U gebruikt de kolomkiezer om de kolommen te kiezen die u wilt opnemen of uitsluiten.

### <a name="choose-columns-by-name"></a>Kolommen op naam kiezen

Er zijn meerdere opties in de module voor het kiezen van kolommen op naam: 

+ Filteren en zoeken

    Klik op de optie **DOOR NAAM.**

    Als u een gegevensset hebt aangesloten die al is ingevuld, moet er een lijst met beschikbare kolommen worden weergegeven. Als er geen kolommen worden weergegeven, moet u mogelijk upstreammodules uitvoeren om de kolomlijst weer te geven.

    Als u de lijst wilt filteren, typt u het zoekvak. Als u bijvoorbeeld de `w` letter in het zoekvak typt, wordt de lijst `w`gefilterd om de kolomnamen weer te geven die de letter bevatten.

    Selecteer kolommen en klik op de pijl-rechts knop om de geselecteerde kolommen naar de lijst in het rechterdeelvenster te verplaatsen.

    + Als u een continu bereik van kolomnamen wilt selecteren, drukt u op **Shift + Klik**op .
    + Als u afzonderlijke kolommen aan de selectie wilt toevoegen, drukt u op **Ctrl + Klik**op .

    Klik op de knop Vinkje om op te slaan en te sluiten.

+ Namen gebruiken in combinatie met andere regels

    Klik op de optie **MET REGELS.**
    
    Kies een regel, zoals het weergeven van kolommen van een specifiek gegevenstype.

    Klik vervolgens op afzonderlijke kolommen van dat type op naam om ze toe te voegen aan de selectielijst.

+ Een door komma's gescheiden lijst met kolomnamen typen of plakken

    Als uw gegevensset breed is, is het mogelijk eenvoudiger om indexen of gegenereerde namenlijsten te gebruiken in plaats van kolommen afzonderlijk te selecteren. Ervan uitgaande dat u de lijst van tevoren hebt voorbereid:

    1. Klik op de optie **MET REGELS.** 
    2. Selecteer **Geen kolommen**, selecteer **Opnemen**en klik vervolgens in het tekstvak met het rode uitroepteken. 
    3. Plak in of typ een door komma's gescheiden lijst met eerder gevalideerde kolomnamen. U de module niet opslaan als een kolom een ongeldige naam heeft, dus controleer de namen vooraf.
    
    U deze methode ook gebruiken om een lijst met kolommen op te geven met hun indexwaarden. 

### <a name="choose-by-type"></a>Kiezen op type

Als u de optie **MET-regels** gebruikt, u meerdere voorwaarden toepassen op de kolomselecties. U hoeft bijvoorbeeld alleen kolommen van een numeriek gegevenstype te krijgen.

De optie **BEGIN MET** bepaalt uw startpunt en is belangrijk voor het begrijpen van de resultaten. 

+ Als u de optie **ALLE kolommen** selecteert, worden alle kolommen aan de lijst toegevoegd. Vervolgens moet u de optie **Uitsluiten** gebruiken om kolommen te *verwijderen* die aan bepaalde voorwaarden voldoen. 

    U bijvoorbeeld beginnen met alle kolommen en kolommen vervolgens op naam of op type verwijderen.

+ Als u de optie **GEEN kolommen** selecteert, begint de lijst met kolommen leeg. Vervolgens geeft u voorwaarden op om kolommen aan de lijst toe te *voegen.* 

    Als u meerdere regels toepast, is elke voorwaarde **additief.** Stel dat u begint met zonder kolommen en vervolgens een regel toevoegt om alle numerieke kolommen te krijgen. In de Automobiele prijsdataset resulteert dat in 16 kolommen. Klik vervolgens op **+** het bord om een nieuwe voorwaarde toe te voegen en selecteer **Alle functies opnemen.** De resulterende gegevensset bevat alle numerieke kolommen, plus alle functiekolommen, inclusief enkele kolommen met tekenreeksfuncties.

### <a name="choose-by-column-index"></a>Kiezen op kolomindex

De kolomindex verwijst naar de volgorde van de kolom in de oorspronkelijke gegevensset.

+ Kolommen worden genummerd sequentieel vanaf 1.  
+ Als u een reeks kolommen wilt krijgen, gebruikt u een koppelteken. 
+ Open specificaties zoals `1-` of `-3` zijn niet toegestaan.
+ Dubbele indexwaarden (of kolomnamen) zijn niet toegestaan en kunnen leiden tot een fout.

Als u bijvoorbeeld van uitgaande dat uw gegevensset ten minste acht kolommen heeft, u in een van de volgende voorbeelden meerdere niet-aaneengesloten kolommen retourneren: 

+ `8,1-4,6`
+ `1,3-8`
+ `1,3-6,4` 

het laatste voorbeeld leidt niet tot een fout; Het retourneert echter één `4`exemplaar van de kolom .



### <a name="change-order-of-columns"></a>Volgorde van kolommen wijzigen

De optie **Duplicaten toestaan en kolomvolgorde in selectie behouden,** begint met een lege lijst en voegt kolommen toe die u op naam of index opgeeft. In tegenstelling tot andere opties, die altijd kolommen in hun "natuurlijke volgorde" retourneren, worden de kolommen met deze optie uitgevoerd in de volgorde waarin u ze een naam geeft of weergeeft. 

In een gegevensset met de kolommen Col1, Col2, Col3 en Col4 u bijvoorbeeld de volgorde van de kolommen omkeren en kolom 2 weglaten door een van de volgende lijsten op te geven:

+ `Col4, Col3, Col1`
+ `4,3,1`


## <a name="next-steps"></a>Volgende stappen

Bekijk de [set modules die beschikbaar zijn](module-reference.md) voor Azure Machine Learning. 