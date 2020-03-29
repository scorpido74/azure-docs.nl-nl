---
title: Wat is training en model? - Aangepaste vertaler
titleSuffix: Azure Cognitive Services
description: Een model is het systeem, dat vertaling voor een specifiek taalpaar verstrekt. Het resultaat van een succesvolle training is een model. Bij het trainen van een model zijn drie wederzijds exclusieve gegevenssets vereist trainingsgegevensset, tuning dataset, en testen dataset.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: swmachan
ms.openlocfilehash: 71f1e3f460fa58b999af0a60c8cffa90c8ac8cd4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219454"
---
# <a name="what-are-trainings-and-models"></a>Wat zijn trainingen en modellen?

Een model is het systeem, dat vertaling voor een specifiek taalpaar verstrekt.
Het resultaat van een succesvolle training is een model. Bij het trainen van een model zijn drie wederzijds exclusieve documenttypen vereist: training, tuning en testen. Woordenboekdocumenttype kan ook worden verstrekt. Raadpleeg de [uitlijning van zinnen.](https://docs.microsoft.com/azure/cognitive-services/translator/custom-translator/sentence-alignment#suggested-minimum-number-of-sentences)

Als alleen trainingsgegevens worden verstrekt bij het in de rij zetten van een training, zal Custom Translator automatisch tuning- en testgegevens verzamelen. Het zal gebruik maken van een willekeurige subset van zinnen uit uw trainingsdocumenten, en deze zinnen uit te sluiten van de training gegevens zelf.

## <a name="training-document-type-for-custom-translator"></a>Documenttype trainen voor aangepaste vertaler

Documenten in de trainingsset worden door de aangepaste vertaler gebruikt als basis voor het bouwen van uw model. Tijdens de uitvoering van de training worden zinnen die in deze documenten aanwezig zijn uitgelijnd (of gekoppeld). U vrijheden nemen in het samenstellen van uw set van trainingsdocumenten. U documenten die volgens u van grote relevantie zijn, in één model opnemen. Weer uit te sluiten in een ander om de impact te zien in [BLEU (Tweetalige Evaluatie Understudy) score](what-is-bleu-score.md). Zolang u de tuningset en testset constant houdt, u experimenteren met de samenstelling van de trainingsset. Deze aanpak is een effectieve manier om de kwaliteit van uw vertaalsysteem te wijzigen.

U meerdere trainingen uitvoeren binnen een project en de [BLEU-scores](what-is-bleu-score.md) vergelijken voor alle trainingsruns. Wanneer u meerdere trainingen uitvoert ter vergelijking, moet u ervoor zorgen dat elke keer dezelfde tuning- en testgegevens worden opgegeven. Zorg er ook voor dat u de resultaten ook handmatig in het tabblad ["Testen"](how-to-view-system-test-results.md) inspecteert.

## <a name="tuning-document-type-for-custom-translator"></a>Documenttype afstemmen voor aangepaste vertaler

Parallelle documenten in deze set worden door de aangepaste vertaler gebruikt om het vertaalsysteem af te stemmen op optimale resultaten.

De tuninggegevens worden tijdens de training gebruikt om alle parameters en gewichten van het vertaalsysteem aan te passen aan de optimale waarden. Kies uw tuninggegevens zorgvuldig: de tuninggegevens moeten representatief zijn voor de inhoud van de documenten die u in de toekomst wilt vertalen. De tuninggegevens hebben een grote invloed op de kwaliteit van de geproduceerde vertalingen. Met tuning kan het vertaalsysteem vertalingen leveren die het dichtst bij de samples liggen die u in de tuninggegevens verstrekt. U hebt niet meer dan 2500 zinnen nodig in uw stemgegevens. Voor een optimale vertaalkwaliteit is het raadzaam om de stemset handmatig te selecteren door de meest representatieve selectie van zinnen te kiezen.

Wanneer u uw stemset maakt, kiest u zinnen die een zinvolle en representatieve lengte zijn van de toekomstige zinnen die u verwacht te vertalen. U moet ook zinnen kiezen met woorden en zinnen die u wilt vertalen in de geschatte verdeling die u verwacht in uw toekomstige vertalingen. In de praktijk zal een zinslengte van 7 tot 10 woorden de beste resultaten opleveren, omdat deze zinnen voldoende context bevatten om verbuiging te tonen en een zinslengte te bieden die aanzienlijk is, zonder al te complex te zijn.

Een goede beschrijving van het type zinnen te gebruiken in de tuning set is proza: werkelijke vloeiende zinnen. Niet tafelcellen, geen gedichten, geen lijsten van dingen, niet alleen interpunctie, of getallen in een zin - gewone taal.

Als u uw stemgegevens handmatig selecteert, mag deze niet dezelfde zinnen hebben als uw trainings- en testgegevens. De tuning gegevens heeft een aanzienlijke invloed op de kwaliteit van de vertalingen - kies de zinnen zorgvuldig.

Als u niet zeker weet wat u moet kiezen voor uw tuninggegevens, selecteert u gewoon de trainingsgegevens en laat Custom Translator de tuninggegevens voor u selecteren. Wanneer u de aangepaste vertaler automatisch de stemgegevens laat kiezen, gebruikt deze een willekeurige subset van zinnen uit uw tweetalige trainingsdocumenten en sluit deze zinnen uit van het trainingsmateriaal zelf.

## <a name="testing-dataset-for-custom-translator"></a>Testgegevensset voor aangepaste vertaler

Parallelle documenten in de testset worden gebruikt om de BLEU -score (Tweetalige Evaluatie Understudy) te berekenen. Deze score geeft de kwaliteit van uw vertaalsysteem aan. Deze score vertelt u eigenlijk hoe nauw de vertalingen die door het vertaalsysteem worden gedaan dat uit deze opleiding voortvloeit overeenkomen met de verwijzingszinnen in de testgegevensset.

De BLEU-score is een meting van de delta tussen de automatische vertaling en de referentievertaling. De waarde varieert van 0 tot 100. Een score van 0 geeft aan dat er geen enkel woord van de verwijzing in de vertaling wordt weergegeven. Een score van 100 geeft aan dat de automatische vertaling precies overeenkomt met de referentie: hetzelfde woord bevindt zich in exact dezelfde positie. De score die u ontvangt is het BLEU-scoregemiddelde voor alle zinnen van de testgegevens.

De testgegevens moeten parallelle documenten bevatten waarin de doeltaalzinnen de meest wenselijke vertalingen zijn van de overeenkomstige brontaalzinnen in het brondoelpaar. U dezelfde criteria gebruiken die u hebt gebruikt om de stemgegevens samen te stellen. De testgegevens hebben echter geen invloed op de kwaliteit van het vertaalsysteem. Het wordt uitsluitend gebruikt om de BLEU score voor u te genereren.

U hebt niet meer dan 2.500 zinnen nodig als testgegevens. Wanneer u het systeem automatisch de testset laat kiezen, gebruikt het een willekeurige subset van zinnen uit uw tweetalige trainingsdocumenten en sluit het deze zinnen uit van het trainingsmateriaal zelf.

U de aangepaste vertalingen van de testset bekijken en deze vergelijken met de vertalingen in uw testset door naar het testtabblad in een model te navigeren.
