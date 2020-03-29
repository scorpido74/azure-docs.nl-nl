---
title: Uw classificatie verbeteren - Custom Vision Service
titleSuffix: Azure Cognitive Services
description: In dit artikel leert u hoe de hoeveelheid, kwaliteit en verscheidenheid aan gegevens de kwaliteit van uw classificatie in de Custom Vision-service kunnen verbeteren.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: pafarley
ms.openlocfilehash: c2858d5f9bca662cbbcd48b2345a7dc2c7ae48b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73718543"
---
# <a name="how-to-improve-your-classifier"></a>De classificatie verbeteren

In deze gids leert u hoe u de kwaliteit van uw Custom Vision Service classifier verbeteren. De kwaliteit van uw classificatie is afhankelijk van de hoeveelheid, kwaliteit en verscheidenheid van de gelabelde gegevens die u verstrekt en hoe evenwichtig de totale gegevensset is. Een goede classifier heeft een uitgebalanceerde trainingsdataset die representatief is voor wat zal worden voorgelegd aan de classificatie. Het proces van het bouwen van een dergelijke classificatie is iteratief; het is gebruikelijk om een paar rondes van de opleiding te nemen om de verwachte resultaten te bereiken.

Het volgende is een algemeen patroon om u te helpen een nauwkeurigerclassificatie op te bouwen:

1. Eerste ronde training
1. Voeg meer afbeeldingen toe en breng gegevens in balans; omscholen
1. Voeg afbeeldingen toe met verschillende achtergrond, belichting, objectgrootte, camerahoek en stijl; omscholen
1. Nieuwe afbeelding(en) gebruiken om voorspelling te testen
1. Bestaande trainingsgegevens wijzigen op basis van voorspellingsresultaten

## <a name="prevent-overfitting"></a>Overfitting voorkomen

Soms leert een classificatie voorspellingen te doen op basis van willekeurige kenmerken die uw afbeeldingen gemeen hebben. Als u bijvoorbeeld een classificatie maakt voor appels versus citrus, en u afbeeldingen van appels in handen en van citrus vruchten op witte platen hebt gebruikt, kan de classificatie onnodig belang geven aan handen versus platen, in plaats van appels versus citrusvruchten.

![Afbeelding van onverwachte classificatie](./media/getting-started-improving-your-classifier/unexpected.png)

Als u dit probleem wilt verhelpen, gebruikt u de volgende richtlijnen voor training met meer gevarieerde afbeeldingen: afbeeldingen met verschillende hoeken, achtergronden, objectgrootte, groepen en andere variaties.

## <a name="data-quantity"></a>Gegevenshoeveelheid

Het aantal trainingsbeelden is de belangrijkste factor. We raden aan om ten minste 50 afbeeldingen per label als uitgangspunt te nemen. Met minder afbeeldingen is er een hoger risico op overfitting en hoewel uw prestatiecijfers een goede kwaliteit kunnen suggereren, kan uw model worstelen met gegevens uit de echte wereld. 

## <a name="data-balance"></a>Gegevenssaldo

Het is ook belangrijk om rekening te houden met de relatieve hoeveelheden van uw trainingsgegevens. Bijvoorbeeld, het gebruik van 500 afbeeldingen voor een label en 50 afbeeldingen voor een ander label zorgt voor een onevenwichtige training dataset. Dit zal ertoe leiden dat het model nauwkeuriger in het voorspellen van een label dan een ander. U zult waarschijnlijk betere resultaten zien als u ten minste een 1:2-verhouding tussen het label met de minste afbeeldingen en het label met de meeste afbeeldingen behoudt. Als het label met de meeste afbeeldingen bijvoorbeeld 500 afbeeldingen heeft, moet het label met de minste afbeeldingen ten minste 250 afbeeldingen hebben voor training.

## <a name="data-variety"></a>Gegevensverscheidenheid

Zorg ervoor dat u afbeeldingen gebruikt die representatief zijn voor wat tijdens normaal gebruik aan de classificatie wordt voorgelegd. Anders kan uw classificatie leren voorspellingen te doen op basis van willekeurige kenmerken die uw afbeeldingen gemeen hebben. Als u bijvoorbeeld een classificatie maakt voor appels versus citrus, en u afbeeldingen van appels in handen en van citrus vruchten op witte platen hebt gebruikt, kan de classificatie onnodig belang geven aan handen versus platen, in plaats van appels versus citrusvruchten.

![Afbeelding van onverwachte classificatie](./media/getting-started-improving-your-classifier/unexpected.png)

Om dit probleem te verhelpen, voeg een verscheidenheid aan afbeeldingen om ervoor te zorgen dat uw classifier goed kan generaliseren. Hieronder vindt u enkele manieren waarop u uw trainingsset diverser maken:

* __Achtergrond:__ Geef afbeeldingen van uw object op voor verschillende achtergronden. Foto's in natuurlijke contexten zijn beter dan foto's voor neutrale achtergronden omdat ze meer informatie bieden voor de classificatie.

    ![Afbeelding van achtergrondvoorbeelden](./media/getting-started-improving-your-classifier/background.png)

* __Verlichting:__ Bieden beelden met gevarieerde verlichting (dat wil zeggen, genomen met flitser, hoge belichting, enzovoort), vooral als de beelden die worden gebruikt voor voorspelling hebben verschillende verlichting. Het is ook handig om afbeeldingen met verschillende verzadiging, tint en helderheid te gebruiken.

    ![Afbeelding van verlichtingsmonsters](./media/getting-started-improving-your-classifier/lighting.png)

* __Objectgrootte:__ Geef afbeeldingen op waarin de objecten variëren in grootte en aantal (bijvoorbeeld een foto van trossen bananen en een close-up van een enkele banaan). Verschillende maten helpt de classifier generaliseren beter.

    ![Afbeelding van groottevoorbeelden](./media/getting-started-improving-your-classifier/size.png)

* __Camerahoek:__ Geef foto's die zijn gemaakt met verschillende camerahoeken. Als u ook al uw foto's moet maken met vaste camera's (zoals bewakingscamera's), moet u&mdash;een ander label toewijzen aan elk regelmatig voorkomend object om te voorkomen dat overfitting niet-gerelateerde objecten (zoals lantaarnpalen) als de belangrijkste functie wordt geïnterpreteerd.

    ![Afbeelding van hoekmonsters](./media/getting-started-improving-your-classifier/angle.png)

* __Stijl:__ Geef afbeeldingen van verschillende stijlen van dezelfde klasse (bijvoorbeeld verschillende variëteiten van dezelfde vrucht). Als u echter objecten van drastisch verschillende stijlen hebt (zoals Mickey Mouse vs. een echte muis), raden we u aan ze te labelen als afzonderlijke klassen om hun verschillende kenmerken beter weer te geven.

    ![Afbeelding van stijlvoorbeelden](./media/getting-started-improving-your-classifier/style.png)

## <a name="negative-images"></a>Negatieve afbeeldingen

Op een bepaald punt in uw project moet u mogelijk _negatieve voorbeelden_ toevoegen om uw classificatie nauwkeuriger te maken. Negatieve monsters zijn die welke niet overeenkomen met een van de andere tags. Wanneer u deze afbeeldingen uploadt, past u het speciale **negatieve** label op deze afbeeldingen toe.

> [!NOTE]
> De Custom Vision Service ondersteunt een automatische negatieve beeldverwerking. Als u bijvoorbeeld een druif vs. bananenclassificatie bouwt en een afbeelding van een schoen indient voor voorspelling, moet de classificatie dat beeld zo dicht mogelijk bij 0% scoren voor zowel druif als banaan.
> 
> Aan de andere kant, in gevallen waar de negatieve beelden zijn slechts een variatie van de beelden die worden gebruikt in de opleiding, is het waarschijnlijk dat het model zal de negatieve beelden te classificeren als een gelabelde klasse als gevolg van de grote gelijkenissen. Bijvoorbeeld, als u een sinaasappel vs grapefruit classifier, en je voeden in een afbeelding van een clementine, kan het scoren van de clementine als een sinaasappel, omdat veel kenmerken van de clementine lijken op die van sinaasappelen. Als uw negatieve afbeeldingen van deze aard zijn, raden we u aan een of meer extra tags (zoals **Andere)** te maken en de negatieve afbeeldingen met deze tag tijdens de training te labelen, zodat het model beter onderscheid kan maken tussen deze klassen.

## <a name="use-prediction-images-for-further-training"></a>Gebruik voorspellingsafbeeldingen voor verdere training

Wanneer u de classificatie van de afbeelding gebruikt of test door afbeeldingen in te dienen bij het voorspellingseindpunt, slaat de aangepaste vision-service deze afbeeldingen op. U ze vervolgens gebruiken om het model te verbeteren.

1. Als u afbeeldingen wilt weergeven die zijn ingediend bij de classificatie, opent u de [webpagina Aangepaste visie,](https://customvision.ai)gaat u naar uw project en selecteert u het tabblad __Voorspellingen.__ De standaardweergave toont afbeeldingen van de huidige iteratie. U het vervolgkeuzemenu __Iteratie__ gebruiken om afbeeldingen weer te geven die tijdens eerdere iteraties zijn ingediend.

    ![schermafbeelding van het tabblad Voorspellingen, met afbeeldingen in beeld](./media/getting-started-improving-your-classifier/predictions.png)

2. Plaats de plaats in de boventoon van een afbeelding om de tags te zien die door de classificatie zijn voorspeld. Afbeeldingen worden zo gesorteerd dat de afbeeldingen die de meeste verbeteringen aan de classificatie kunnen brengen, bovenaan staan. Als u een andere sorteermethode wilt gebruiken, maakt u een selectie in de sectie __Sorteren.__ 

    Als u een afbeelding wilt toevoegen aan uw bestaande trainingsgegevens, selecteert u de afbeelding, stelt u de juiste tag(s) in en klikt u op __Opslaan en sluiten__. De afbeelding wordt verwijderd uit __Voorspellingen__ en toegevoegd aan de set trainingsafbeeldingen. U het bekijken door het tabblad __Trainingsafbeeldingen te__ selecteren.

    ![Afbeelding van de tagpagina](./media/getting-started-improving-your-classifier/tag.png)

3. Gebruik vervolgens de __knop Trein__ om de classificatie om te scholen.

## <a name="visually-inspect-predictions"></a>Voorspellingen visueel inspecteren

Als u afbeeldingsvoorspellingen wilt inspecteren, gaat u naar het tabblad __Trainingsafbeeldingen,__ selecteert u uw vorige trainingsiteratie in het vervolgkeuzemenu **Iteratie** en controleert u een of meer tags onder de sectie **Labels.** De weergave moet nu een rood vak tonen rond elk van de afbeeldingen waarvoor het model de opgegeven tag niet correct kon voorspellen.

![Afbeelding van de iteratiegeschiedenis](./media/getting-started-improving-your-classifier/iteration.png)

Soms kan een visuele inspectie patronen identificeren die u vervolgens corrigeren door meer trainingsgegevens toe te voegen of bestaande trainingsgegevens te wijzigen. Een classificatie voor appels versus limoenen kan bijvoorbeeld alle groene appels verkeerd labelen als limoenen. U dit probleem vervolgens verhelpen door trainingsgegevens toe te voegen en te verstrekken die gelabelde afbeeldingen van groene appels bevatten.

## <a name="next-steps"></a>Volgende stappen

In deze handleiding hebt u verschillende technieken geleerd om uw aangepaste beeldclassificatiemodel nauwkeuriger te maken. Leer vervolgens hoe u afbeeldingen programmatisch testen door ze in te dienen bij de Voorspellings-API.

> [!div class="nextstepaction"]
> [De voorspellings-API gebruiken](use-prediction-api.md)
