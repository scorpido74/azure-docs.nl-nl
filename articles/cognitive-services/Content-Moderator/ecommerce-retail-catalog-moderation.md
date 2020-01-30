---
title: 'Zelf studie: software matige installatie kopieën van e-commerce-Content Moderator'
titleSuffix: Azure Cognitive Services
description: Deze zelf studie laat zien hoe u een toepassing kunt instellen voor het analyseren en classificeren van product afbeeldingen met opgegeven labels (met behulp van Azure Computer Vision en Custom Vision). Label ongewenste afbeeldingen die verder moeten worden gecontroleerd (met behulp van Azure Content Moderator).
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: tutorial
ms.date: 01/27/2020
ms.author: pafarley
ms.openlocfilehash: 5e74eda9e30c536c0eba4e847019344c87e10cce
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2020
ms.locfileid: "76774337"
---
# <a name="tutorial-moderate-e-commerce-product-images-with-azure-content-moderator"></a>Zelf studie: software matige installatie kopieën van e-commerce met Azure Content Moderator

In deze zelf studie leert u hoe u Azure Cognitive Services, met inbegrip van Content Moderator, kunt gebruiken om product afbeeldingen te classificeren en te vertragen voor een e-commerce scenario. U gebruikt Computer Vision en Custom Vision voor het Toep assen van tags (labels) op afbeeldingen en vervolgens maakt u een team beoordeling, waarin u de op de machine learning gebaseerde technologieën van Content Moderator combineert met mede beoordelings teams om een intelligent toezicht systeem te bieden.

In deze handleiding ontdekt u hoe u:

> [!div class="checklist"]
> * Aanmelden voor Content Moderator en een beoordelingsteam maken.
> * De Image-API van Content Moderator gebruiken om te scannen op mogelijke ongepaste inhoud of inhoud voor volwassenen.
> * De Computer Vision-service gebruiken om te scannen op beroemdheden (of andere tags die Computer Vision kan detecteren).
> * De Custom Vision service gebruiken om te scannen op de aanwezigheid van vlaggen, speelgoed en pennen (of andere aangepaste tags).
> * De gecombineerde scanresultaten presenteren voor menselijke beoordeling en uiteindelijke besluitvorming.

De volledige voorbeeldcode is beschikbaar in de opslagplaats [Samples eCommerce Catalog Moderation](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration) op GitHub.

Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

- Een abonnementssleutel voor Content Moderator. Volg de instructies in [Een Cognitive Services-account maken](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) om u te abonneren op Content Moderator en een sleutel op te halen.
- Een abonnementssleutel voor Computer Vision (dezelfde instructies als hierboven).
- Een versie van [Visual Studio 2015 of 2017](https://www.visualstudio.com/downloads/).
- Een reeks afbeeldingen voor elk label dat door de Custom Vision-classificatie wordt gebruikt (in dit geval van speelgoed, pennen en vlaggen).

## <a name="create-a-review-team"></a>Een beoordelingsteam maken

Raadpleeg de Snelstartgids [Content moderator op het web](quick-start.md) voor instructies over hoe u zich kunt registreren voor het [hulp programma content moderator beoordeling](https://contentmoderator.cognitive.microsoft.com/) en maak een beoordelings team. Noteer de waarde voor **Team Id** op de pagina **Create review team**.

## <a name="create-custom-moderation-tags"></a>Aangepaste controletags maken

Maak vervolgens aangepaste labels in het hulp programma voor beoordeling (Zie het artikel [Tags](https://docs.microsoft.com/azure/cognitive-services/content-moderator/review-tool-user-guide/tags) als u hulp nodig hebt bij dit proces). In dit geval voegen we deze tags toe: **celebrity**, **USA**, **flag**, **toy** en **pen**. Niet alle labels moeten Detecteer bare categorieën zijn in Computer Vision (zoals **beroemdheden**). u kunt uw eigen aangepaste Tags toevoegen zolang u de classificatie van de Custom Vision traint om ze later te detecteren.

![Aangepaste tags configureren](images/tutorial-ecommerce-tags2.PNG)

## <a name="create-visual-studio-project"></a>Een Visual Studio-project maken

1. Open het dialoogvenster Nieuw project in Visual Studio. Vouw **Geïnstalleerd** en vervolgens **Visual C#** uit en selecteer **Console App (.NET Framework)** .
1. Geef de toepassing de naam **EcommerceModeration** en klik op **OK**.
1. Als u dit project aan een bestaande oplossing toevoegt, selecteert u dit project als één opstart project.

In deze zelf studie wordt de code voor het project gemarkeerd, maar niet elke regel code. Kopieer de volledige inhoud van _Program.cs_ uit het voorbeeldproject ([Samples eCommerce Catalog Moderation](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration)) naar het bestand _Program.cs_ van het nieuwe project. Neem vervolgens de onderstaande gedeelten door voor meer informatie over de werking van het project en hoe u dit zelf kunt gebruiken.

## <a name="define-api-keys-and-endpoints"></a>API-sleutels en -eindpunten definiëren

In deze zelf studie worden drie cognitieve Services gebruikt. Daarom zijn drie overeenkomstige sleutels en API-eind punten vereist. Bekijk de volgende velden in de klasse **Program**:

[!code-csharp[define API keys and endpoint URIs](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=21-29)]

U moet de `___Key` velden bijwerken met de waarden van uw abonnements sleutels en u moet de `___Uri` velden wijzigen in de juiste eindpunt-Url's (u krijgt de Custom Vision sleutel en het eind punt later op). U kunt deze waarden vinden op de tabbladen **Quick Start** van elke Azure-resource. Voer in het gedeelte `YOURTEAMID` van het veld `ReviewUri` de id in van het beoordelingsteam dat u eerder hebt gemaakt. U vult het laatste deel van het `CustomVisionUri` veld later in.

[!INCLUDE [subdomains note](../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="primary-method-calls"></a>Aanroepen van primaire methode

Bekijk de volgende code in de methode **Main**, waarmee een lijst met afbeeldings-URL's wordt doorlopen. Elke installatie kopie wordt geanalyseerd met de drie verschillende services, registreert de toegepaste Tags in de **ReviewTags** -matrix en maakt vervolgens een beoordeling voor menselijke moderators door de installatie kopieën naar het hulp programma content moderator beoordeling te verzenden. Deze methoden gaan we in de volgende gedeelten bespreken. Als u wilt, kunt u bepalen welke installatie kopieën worden verzonden om te bekijken, met behulp van de **ReviewTags** -matrix in een voorwaardelijke instructie om te controleren welke tags zijn toegepast.

[!code-csharp[Main: evaluate each image and create review](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=53-70)]

## <a name="evaluateadultracy-method"></a>EvaluateAdultRacy-methode

Bekijk de methode **EvaluateAdultRacy** in de klasse **Program**. Deze methode accepteert een afbeeldings-URL en een matrix van sleutel-waardeparen als parameters. De methode roept de Image API van Content Moderator aan (met behulp van REST) om de waarden voor AdultScore en RacyScore op te vragen voor de afbeelding. Als de score voor een groter is dan 0,4 (het bereik ligt tussen 0 en 1), wordt de overeenkomstige waarde in de **ReviewTags** -matrix ingesteld op **True**.

[!code-csharp[define EvaluateAdultRacy method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=73-113)]

## <a name="evaluatecomputervisiontags-method"></a>Methode EvaluateComputerVisionTags

De volgende methode verwerkt een afbeeldings-URL en de gegevens van uw Computer Vision-abonnement en analyseert de afbeelding op de aanwezigheid van beroemdheden. Als er een of meer beroemdheden worden gevonden in de afbeelding, wordt de bijbehorende waarde in de matrix **ReviewTags** ingesteld op **True**.

[!code-csharp[define EvaluateCustomVisionTags method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=115-146)]

## <a name="evaluatecustomvisiontags-method"></a>EvaluateComputerVisionTags-methode

Bekijk nu de methode **EvaluateCustomVisionTags**, waarmee de daadwerkelijke producten worden geclassificeerd&mdash;in dit geval vlaggen, speelgoed en pennen. Volg de instructies in de hand leiding [een classificatie maken](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) om uw eigen aangepaste afbeeldings classificatie te bouwen en vlaggen, speel goed en pennen te detecteren (of wat u hebt gekozen als uw aangepaste Tags) in afbeeldingen. U kunt de installatie kopieën in de map voor **beeld-installatie kopieën** van de [github-opslag plaats](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration) gebruiken om snel enkele categorieën in dit voor beeld te trainen.

![Webpagina van Custom Vision met trainingsafbeeldingen van pennen, speelgoed en vlaggen](images/tutorial-ecommerce-custom-vision.PNG)

Zodra u de classificatie hebt getraind, haalt u de Voorspellings sleutel en de eind punt-URL voor de prediction op (Zie [de URL en de Voorspellings sleutel ophalen](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/use-prediction-api#get-the-url-and-prediction-key) als u hulp nodig hebt bij het ophalen van deze waarden), en u kunt ze aan uw `CustomVisionKey` en `CustomVisionUri` velden toewijzen. De methode gebruikt deze waarden om query's uit te voeren op de classificatie. Als de classificatie een of meer van de aangepaste tags vindt in de afbeelding, worden de bijbehorende waarden in de matrix **ReviewTags** ingesteld op **True**.

[!code-csharp[define EvaluateCustomVisionTags method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=148-171)]

## <a name="create-reviews-for-review-tool"></a>Beoordelingen maken voor het beoordelingsprogramma

In de vorige gedeelten hebt u bekeken hoe de app binnenkomende installatie kopieën voor volwassenen en ongepaste-inhoud (Content Moderator), beroemdheden (Computer Vision) en diverse andere objecten (Custom Vision) scant. Bekijk vervolgens de methode **CreateReview** , die de afbeeldingen uploadt met alle toegepaste labels (door gegeven als _meta gegevens_) naar het content moderator controle programma.

[!code-csharp[define CreateReview method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=173-196)]

De afbeeldingen worden weergegeven op het tabblad Review van het [beoordelingsprogramma Content Moderator](https://contentmoderator.cognitive.microsoft.com/).

![Schermafbeelding van het beoordelingsprogramma Content Moderator met verschillende afbeeldingen en de bijbehorende gemarkeerde tags](images/tutorial-ecommerce-content-moderator.PNG)

## <a name="submit-a-list-of-test-images"></a>Een lijst met testafbeeldingen aanbieden

Zoals u kunt zien in de methode **Main**, zoekt dit programma naar een map 'C:Test' met daarin een bestand _Urls.txt_ dat een lijst met afbeeldings-URL's bevat. Maak dit bestand en deze map, of wijzig het pad zodat het naar uw tekst bestand verwijst. Vul dit bestand vervolgens in met de Url's van de installatie kopieën die u wilt testen.

[!code-csharp[Main: set up test directory, read lines](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=38-51)]

## <a name="run-the-program"></a>Het programma uitvoeren

Als u alle bovenstaande stappen hebt gevolgd, moet het programma elke installatie kopie verwerken (query's uitvoeren op alle drie de services voor hun relevante tags) en vervolgens de installatie kopieën uploaden met label gegevens naar het hulp programma Content Moderator beoordeling.

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie stelt u een programma in om product afbeeldingen te analyseren, ze te labelen op product type en een beoordelings team toe te staan om weloverwogen beslissingen te nemen over de toezicht op inhoud. In de volgende zelfstudie wordt meer aandacht besteed aan het beoordelen van gecontroleerde afbeeldingen.

> [!div class="nextstepaction"]
> [Gecontroleerde afbeeldingen beoordelen](./review-tool-user-guide/review-moderated-images.md)
