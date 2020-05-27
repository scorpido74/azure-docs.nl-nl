---
title: Gegevens groeperen in opslag locaties
titleSuffix: Azure Machine Learning
description: Informatie over het gebruik van de groeps gegevens in de module opslag locaties voor het groeperen van nummers of het wijzigen van de distributie van doorlopende gegevens.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/19/2020
ms.openlocfilehash: b33aa3d2ab22fc986100d6141dd03d5547ef1862
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/26/2020
ms.locfileid: "83853825"
---
# <a name="group-data-into-bins"></a>Gegevens groeperen in opslag locaties

In dit artikel wordt beschreven hoe u de [groeps gegevens](group-data-into-bins.md) in de module opslag locaties in azure machine learning Designer (preview) gebruikt om getallen te groeperen of de distributie van doorlopende gegevens te wijzigen.

De module [gegevens groeperen in opslag locaties](group-data-into-bins.md) ondersteunt meerdere opties voor binning-gegevens. U kunt aanpassen hoe de randen van de opslag locatie worden ingesteld en hoe waarden worden verdeeld in de opslag locaties. U kunt bijvoorbeeld het volgende doen:  

+ Voer hand matig een reeks waarden in die moeten fungeren als de grenzen van de opslag locatie.  
+ Waarden toewijzen aan opslag locaties met behulp van *quantiles*of percentiel posities.  
+ Een gelijkmatige verdeling van waarden in de opslag locaties afdwingen.  

### <a name="more-about-binning-and-grouping"></a>Meer informatie over binning en groeperen

*Binning* -of groepeer gegevens (ook wel *kwantisatiefouten*genoemd) is een belang rijk hulp middel bij het voorbereiden van numerieke gegevens voor machine learning en is handig in scenario's als deze:

+ Een kolom met doorlopende getallen heeft te veel unieke waarden om effectief te kunnen model leren, zodat u de waarden automatisch of hand matig toewijst aan groepen om een kleinere set discrete bereiken te maken.

+ Een kolom met getallen vervangen door categorische-waarden die specifieke bereiken vertegenwoordigen.

    Stel dat u waarden in een kolom leeftijd wilt groeperen door aangepaste bereiken op te geven, zoals 1-15, 16-22, 23-30, enzovoort voor demografische gegevens van gebruikers.

+ Een gegevensset heeft enkele extreme waarden, die allemaal goed buiten het verwachte bereik vallen, en deze waarden hebben een zeer grote invloed op het getrainde model. Als u de afwijking in het model wilt beperken, kunt u de gegevens transformeren naar een uniforme distributie met behulp van de methode quantiles.

    Met deze methode bepaalt de module [groeps gegevens in de opslag locaties](group-data-into-bins.md) de ideale opslaglocatie locaties en de breedte van de opslag locatie om ervoor te zorgen dat ongeveer hetzelfde aantal steek proeven in elke bin wordt onderverdeeld. Afhankelijk van de normalisatie methode die u kiest, worden de waarden in de opslag locaties omgezet naar percentielen of toegewezen aan een opslaglocatie nummer.

### <a name="examples-of-binning"></a>Voor beelden van binning

Het volgende diagram toont de distributie van numerieke waarden voor en na binning met de methode **quantiles** . In vergelijking met de onbewerkte gegevens zijn de gegevens binning en omgezet naar een eenheid-normale schaal.  

' Er kan een voor beeld worden gevonden vanuit het resultaat van deze pijplijn uitvoering: https://ml.azure.com/visualinterface/authoring/Normal/87270db9-4651-448e-bd28-8ef7428084dc?wsid=%2Fsubscriptions%2Fe9b2ec51-5c94-4fa8-809a-dc1e695e4896%2Fresourcegroups%2Fmodule-ws-rg%2Fworkspaces%2Fmodule-prerelease-119&flight=cm&tid=72f988bf-86f1-41af-91ab-2d7cd011db47&smtendpoint=https%3A%2F%2Fsmt-test1.azureml-test.net '

Omdat er zoveel manieren zijn om gegevens te groeperen, is het raadzaam om te experimenteren met verschillende methoden en waarden. 

## <a name="how-to-configure-group-data-into-bins"></a>Groeps gegevens in opslag locaties configureren

1. Voeg de **groeps gegevens** in de module opslag locaties toe aan uw pijp lijn in Designer (preview). U kunt deze module vinden in de categorie **gegevens transformatie**.

2. Verbind de gegevensset die numerieke gegevens op de bin heeft.  Kwantisatiefouten kan alleen worden toegepast op kolommen met numerieke gegevens. 

    Als de gegevensset niet-numerieke kolommen bevat, gebruikt u de module [select columns in dataset](select-columns-in-dataset.md) om een subset kolommen te selecteren waarmee u wilt werken.

3. Geef de binning-modus op. De binning-modus bepaalt andere para meters, dus zorg ervoor dat u eerst de optie **binning modus** selecteert. De volgende typen binning worden ondersteund:

    **Quantiles**: met de methode quantile worden waarden toegewezen aan opslag locaties op basis van percentiel posities. Quantiles is ook bekend als binning met gelijke hoogte.

    **Gelijke breedte**: met deze optie moet u het totale aantal bakken opgeven. De waarden in de kolom gegevens worden in de opslag locaties geplaatst, zodat elke bin hetzelfde interval heeft tussen de begin-en eind waarden. Als gevolg hiervan kunnen sommige opslag locaties meer waarden hebben als de gegevens clumped rond een bepaald punt zijn.

    **Aangepaste randen**: u kunt de waarden opgeven die elke opslag locatie beginnen. De rand waarde is altijd de ondergrens van de lade.  Stel dat u waarden wilt groeperen in twee bakken, een met een waarde die groter is dan 0 en een met waarden die kleiner dan of gelijk aan 0 zijn. In dit geval typt u 0 in een **door komma's gescheiden lijst met bin-randen**. De uitvoer van de module zou 1 en 2 moeten zijn, wat de bin-index voor elke Rijwaarde aangeeft. De lijst met door komma's gescheiden waarden moet een oplopende volg orde hebben, zoals 1, 3, 5, 7.

4. **Aantal bakken**: als u de **Quantiles**-en binning-modus met **gelijke breedte** gebruikt, gebruikt u deze optie om op te geven hoeveel bakken of *Quantiles*u wilt maken.

5. Gebruik voor **kolommen naar bin**de kolom kiezer om de kolommen te kiezen met de waarden die u wilt gebruiken voor de opslag locatie. Kolommen moeten een numeriek gegevens type zijn.

    Dezelfde binning-regel wordt toegepast op alle toepasselijke kolommen die u kiest. Als u een andere methode wilt gebruiken om een bepaalde kolom te maken, gebruikt u daarom een afzonderlijke instantie van [groeps gegevens in de opslag locaties](group-data-into-bins.md) voor elke set kolommen.

    > [!WARNING]
    > Als u een kolom kiest die geen toegestaan type is, wordt er een runtime fout gegenereerd. De module retourneert een fout zodra een kolom van een niet-toegestaan type wordt gevonden. Als er een fout optreedt, controleert u alle geselecteerde kolommen. In de fout worden niet alle ongeldige kolommen weer geven.

6. Geef voor **uitvoer modus**op hoe u de waarden voor de afgemelde waarde wilt uitvoeren.

      + **Toevoegen**: Hiermee maakt u een nieuwe kolom met de waarden voor binning en voegt u deze toe aan de invoer tabel.

      + **InPlace**: vervangt de oorspronkelijke waarden door de nieuwe waarden in de gegevensset.

      + **ResultOnly**: retourneert alleen de resultaten kolommen.

7. Als u de **Quantiles** binning-modus selecteert, gebruikt u de optie **Quantile normalisatie** om te bepalen hoe waarden worden genormaliseerd voordat ze in Quantiles worden gesorteerd. Houd er rekening mee dat het normaliseren van waarden de waarden transformeert, maar heeft geen invloed op het uiteindelijke aantal opslag locaties.

    De volgende normalisatie typen worden ondersteund:

    + **Percentage**: de waarden worden genormaliseerd binnen het bereik [0100]

    + **PQuantile**: waarden worden genormaliseerd binnen het bereik [0, 1]

    + **QuantileIndex**: waarden worden genormaliseerd binnen het bereik [1, aantal bakken]

8. Als u kiest voor de optie **aangepaste randen** , typt u een door komma's gescheiden lijst met getallen die moeten worden gebruikt als *bin-randen* in het tekstvak **met door komma's gescheiden** waarden.  De waarden markeren het punt dat de opslag locaties deelt, dus als u één waarde voor de bak-rand typt, worden er twee opslag locaties gegenereerd. Als u twee waarden voor de breedte van de bak typt, worden er drie bakken gegenereerd, enzovoort.

    De waarden moeten worden gesorteerd om de bakken te maken, van laag naar hoog.

10. Een label maken voor **kolommen als categorische**: Selecteer deze optie om aan te geven dat de kolommen met de kolom categorische moeten worden afgehandeld.

11. Verzend de pijp lijn.

### <a name="results"></a>Resultaten

De module [gegevens groeperen in opslag locaties](group-data-into-bins.md) retourneert een gegevensset waarin elk element is binning volgens de opgegeven modus. 

Er wordt ook een **binning-trans formatie**geretourneerd. Dit is een functie die kan worden door gegeven aan de module [trans formatie Toep assen](apply-transformation.md) op nieuwe voor beelden van gegevens met dezelfde binning-modus en-para meters.  

> [!TIP]
> Houd er rekening mee dat als u binning gebruikt voor uw trainings gegevens, dezelfde binning-methode moet worden gebruikt voor gegevens die u gebruikt voor testen en voor spellingen. Dit omvat de binning-methode, bin-locaties en bin-breedten. 
> 
> Om ervoor te zorgen dat gegevens altijd worden getransformeerd met behulp van dezelfde binning-methode, wordt u aangeraden bruikbare gegevens transformaties op te slaan en deze vervolgens toe te passen op andere gegevens sets, met behulp van de module [trans formatie Toep assen](apply-transformation.md) .

## <a name="next-steps"></a>Volgende stappen

Bekijk de [set met modules die beschikbaar zijn](module-reference.md) voor Azure machine learning. 
