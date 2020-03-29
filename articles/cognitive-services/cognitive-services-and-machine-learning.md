---
title: Cognitieve diensten en machine learning
titleSuffix: Azure Cognitive Services
description: Ontdek hoe Azure Cognitive Services past bij andere Azure-aanbiedingen voor machine learning.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: diberry
ms.openlocfilehash: cde505e4c95de9b9693a0e9d260d7fa84f3e905b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "75531476"
---
# <a name="cognitive-services-and-machine-learning"></a>Cognitive Services en machine learning

Cognitive Services biedt machine learning-mogelijkheden om algemene problemen op te lossen, zoals het analyseren van tekst voor emotioneel sentiment of het analyseren van afbeeldingen om objecten of gezichten te herkennen. U hebt geen speciale machine learning- of data science-kennis nodig om deze services te gebruiken. 

[Cognitive Services](welcome.md) is een groep services die elk verschillende, algemene voorspellingsmogelijkheden ondersteunen. De diensten zijn onderverdeeld in verschillende categorieën om u te helpen de juiste service te vinden. 

|Servicecategorie|Doel|
|--|--|
|[Besluit](https://azure.microsoft.com/services/cognitive-services/directory/decision/)|Bouw apps die aanbevelingen opleveren voor geïnformeerde en efficiënte besluitvorming.|
|[Language](https://azure.microsoft.com/services/cognitive-services/directory/lang/)|Laat uw apps natuurlijke taal verwerken met vooraf gemaakte scripts, sentiment evalueren en leren herkennen wat gebruikers willen.|
|[Zoeken](https://azure.microsoft.com/services/cognitive-services/directory/search/)|Voeg Bing Search-API's toe aan uw apps en maak het mogelijk om miljarden webpagina's, afbeeldingen, video's en nieuwsartikelen met één API-aanroep te doorzoeken.|
|[Speech](https://azure.microsoft.com/services/cognitive-services/directory/speech/)|Converteer spraak naar tekst en tekst naar spraak met natuurlijke uitspraak. Vertaal teksten van de ene naar de andere taal en schakel sprekercontrole en -herkenning in.|
|[Vision](https://azure.microsoft.com/services/cognitive-services/directory/vision/)|Herken, identificeer, indexeer en wijzig uw afbeeldingen, video's en digitale inkt en voorzie deze van ondertitels.|
||||

Gebruik cognitieve services wanneer u:

* Kan een algemene oplossing gebruiken.
* Toegang tot oplossing vanuit een programmeer-REST API of SDK. 

Gebruik een andere machine learning-oplossing wanneer u:

* Noodzaak om het algoritme te kiezen en moeten trainen op zeer specifieke gegevens.

## <a name="what-is-machine-learning"></a>Wat is machine learning?

Machine learning is een concept waarbij je data en een algoritme bij elkaar brengt om een specifieke behoefte op te lossen. Zodra de gegevens en het algoritme zijn getraind, is de uitvoer een model dat u opnieuw gebruiken met verschillende gegevens. Het getrainde model biedt inzichten op basis van de nieuwe gegevens. 

Het proces van het bouwen van een machine learning-systeem vereist enige kennis van machine learning of data science.

Machine learning wordt geleverd met Behulp van [Azure Machine Learning (AML) producten en services.](https://docs.microsoft.com/azure/architecture/data-guide/technology-choices/data-science-and-machine-learning?context=azure/machine-learning/studio/context/ml-context)

## <a name="what-is-a-cognitive-service"></a>Wat is een cognitieve service?

Een Cognitive Service biedt een deel of alle componenten in een machine learning-oplossing: gegevens, algoritme en getraind model. Deze services zijn bedoeld om algemene kennis over uw gegevens te vereisen zonder ervaring met machine learning of data science. Deze diensten bieden zowel REST API(s) als op taal gebaseerde SDK's. Als gevolg hiervan moet u programmeertaal kennis hebben om de diensten te gebruiken.

## <a name="how-are-cognitive-services-and-azure-machine-learning-aml-similar"></a>Hoe zijn Cognitive Services en Azure Machine Learning (AML) vergelijkbaar?

Beide hebben als einddoel om kunstmatige intelligentie (AI) toe te passen om de bedrijfsvoering te verbeteren, maar hoe elk dit in het betreffende aanbod biedt, is anders. 

Over het algemeen is het publiek anders:

* Cognitive Services zijn voor ontwikkelaars zonder machine learning-ervaring.
* Azure Machine Learning is afgestemd op gegevenswetenschappers. 

## <a name="how-is-a-cognitive-service-different-from-machine-learning"></a>Hoe verschilt een cognitieve service van machine learning?

Een Cognitive Service biedt een getraind model voor u. Dit brengt gegevens en een algoritme samen, beschikbaar vanuit een REST API(s) of SDK. U deze service binnen enkele minuten implementeren, afhankelijk van uw scenario.  Een cognitieve service biedt antwoorden op algemene problemen, zoals sleutelzinnen in tekst- of artikelidentificatie in afbeeldingen. 

Machine learning is een proces dat over het algemeen een langere periode nodig heeft om succesvol te implementeren. Deze tijd wordt besteed aan het verzamelen, schoonmaken, transformeren, algoritmeselectie, modeltraining en implementatie om tot hetzelfde niveau van functionaliteit te komen dat wordt geleverd door een cognitieve service. Met machine learning is het mogelijk om antwoorden te geven op zeer gespecialiseerde en/of specifieke problemen. Machine learning-problemen vereisen vertrouwdheid met het specifieke onderwerp en de gegevens van het betrokken probleem, evenals expertise in data science.

## <a name="what-kind-of-data-do-you-have"></a>Wat voor gegevens heb je?

Cognitieve services, als een groep services, kunnen geen, sommige of alle aangepaste gegevens vereisen voor het getrainde model. 

### <a name="no-additional-training-data-required"></a>Geen aanvullende trainingsgegevens vereist

Diensten die een volledig getraind model bieden, kunnen worden behandeld als een _zwarte doos._ U hoeft niet te weten hoe ze werken of welke gegevens zijn gebruikt om ze te trainen. U brengt uw gegevens naar een volledig opgeleid model om een voorspelling te krijgen. 

### <a name="some-or-all-training-data-required"></a>Sommige of alle benodigde trainingsgegevens

Met sommige services u uw eigen gegevens meenemen en vervolgens een model trainen. Hiermee u het model uitbreiden met behulp van de gegevens en het algoritme van de Service met uw eigen gegevens. De output komt overeen met uw behoeften. Wanneer u uw eigen gegevens meeneemt, moet u de gegevens mogelijk taggen op een manier die specifiek is voor de service. Als u bijvoorbeeld een model traint om bloemen te identificeren, u een catalogus met bloemenafbeeldingen, samen met de locatie van de bloem in elke afbeelding, aanbieden om het model te trainen. 

Met een service _kunt_ u gegevens verstrekken om de eigen gegevens te verbeteren. Voor een service _moet_ u mogelijk gegevens verstrekken. 

### <a name="real-time-or-near-real-time-data-required"></a>Real-time of bijna real-time gegevens vereist

Een service heeft mogelijk realtime of bijna realtime gegevens nodig om een effectief model te bouwen. Deze services verwerken aanzienlijke hoeveelheden modelgegevens. 

## <a name="service-requirements-for-the-data-model"></a>Servicevereisten voor het gegevensmodel

De volgende gegevens categoriseren elke service op welke soort gegevens deze toestaat of vereist.

|Cognitieve service|Geen trainingsgegevens vereist|U verstrekt een aantal of alle trainingsgegevens|Real-time of bijna real-time gegevensverzameling|
|--|--|--|--|
|[Anomaly Detector](./Anomaly-Detector/overview.md)|x|x|x|
|Bing Zoeken |x|||
|[Computer Visie](./Computer-vision/Home.md)|x|||
|[Content Moderator](./Content-Moderator/overview.md)|x||x|
|[Custom Vision](./Custom-Vision-Service/home.md)||x||
|[Face](./Face/Overview.md)|x|x||
|[Form Recognizer](./form-recognizer/overview.md)||x||
|[Insluitende lezer](./immersive-reader/overview.md)|x|||
|[Ink Recognizer](./Ink-recognizer/overview.md)|x|x||
|[Language Understanding (LUIS)](./LUIS/what-is-luis.md)||x||
|[Personalizer](./personalizer/what-is-personalizer.md)|x*|x*|x|
|[QnA Maker](./QnAMaker/Overview/overview.md)||x||
|[Luidsprekerherkenning](./speaker-recognition/home.md)||x||
|[Spraaktekst-naar-spraak (TTS)](speech-service/text-to-speech.md)|x|x||
|[Spraak-naar-tekst (STT)](speech-service/speech-to-text.md)|x|x||
|[Spraakomzetting](speech-service/speech-translation.md)|x|||
|[Tekstanalyse](./text-analytics/overview.md)|x|||
|[Translator Text](./translator/translator-info-overview.md)|x|||
|[Vertaler Tekst - aangepaste vertaler](./translator/custom-translator/overview.md)||x||

*Personalizer heeft alleen trainingsgegevens nodig die door de service worden verzameld (omdat deze in realtime werkt) om uw beleid en gegevens te evalueren. Personalizer heeft geen grote historische datasets nodig voor up-front- of batchtraining. 

## <a name="where-can-you-use-cognitive-services"></a>Waar u gebruik maken van Cognitive Services?
 
De services worden gebruikt in elke toepassing die REST API(s) of SDK-aanroepen kan voeren. Voorbeelden van toepassingen zijn websites, bots, virtuele of mixed reality, desktop en mobiele applicaties. 

## <a name="how-is-azure-cognitive-search-related-to-cognitive-services"></a>Hoe is Azure Cognitive Search gerelateerd aan Cognitive Services?

[Azure Cognitive Search](../search/search-what-is-azure-search.md) is een afzonderlijke cloudzoekservice die optioneel cognitieve services gebruikt om beeld- en natuurlijke taalverwerking toe te voegen aan het indexeren van workloads. Cognitive Services wordt blootgesteld in Azure Cognitive Search door middel [van ingebouwde vaardigheden](../search/cognitive-search-predefined-skills.md) die individuele API's omwikkelen. U een gratis resource gebruiken voor walkthroughs, maar van plan zijn een [factureerbare bron](../search/cognitive-search-attach-cognitive-services.md) voor grotere volumes te maken en toe te voegen.

## <a name="how-can-you-use-cognitive-services"></a>Hoe u cognitieve services gebruiken?

Elke service geeft informatie over uw gegevens. U services combineren tot ketenoplossingen zoals het omzetten van spraak (audio) naar tekst, het vertalen van de tekst in vele talen en vervolgens het gebruik van de vertaalde talen om antwoorden te krijgen uit een kennisbank. Hoewel Cognitive Services kunnen worden gebruikt om zelf intelligente oplossingen te creëren, kunnen ze ook worden gecombineerd met traditionele machine learning-projecten om modellen aan te vullen of het ontwikkelingsproces te versnellen. 

Cognitieve services die geëxporteerde modellen bieden voor andere machine learning-tools:

|Cognitieve service|Modelinformatie|
|--|--|
|[Custom Vision](./custom-vision-service/home.md)|[Exporteren](./Custom-Vision-Service/export-model-python.md) voor Tensorflow voor Android, CoreML voor iOS11, ONNX voor Windows ML|

## <a name="learn-more"></a>Meer informatie

* [Architectuurgids - Wat zijn de machine learning-producten bij Microsoft?](https://docs.microsoft.com/azure/architecture/data-guide/technology-choices/data-science-and-machine-learning)
* [Machine learning - Inleiding tot deep learning versus machine learning](../machine-learning/concept-deep-learning-vs-machine-learning.md)

## <a name="next-steps"></a>Volgende stappen

* Maak uw Cognitive Service-account in de [Azure-portal](cognitive-services-apis-create-account.md) of met [Azure CLI.](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli)
* Meer informatie over hoe [u zich verifiëren bij](authentication.md) een cognitieve service.
* Gebruik [diagnostische logboekregistratie](diagnostic-logging.md) voor het identificeren en debuggen van problemen. 
* Een cognitieve service implementeren in een [Docker-container.](cognitive-services-container-support.md)
* Blijf op de hoogte van [service-updates.](https://azure.microsoft.com/updates/?product=cognitive-services)
