---
title: 'Zelfstudie: Matige e-commerce productafbeeldingen - Content Moderator'
titleSuffix: Azure Cognitive Services
description: In deze zelfstudie ziet u hoe u een toepassing instelt om productafbeeldingen te analyseren en te classificeren met opgegeven labels (met Azure Computer Vision en Custom Vision). Tag aanstootgevende afbeeldingen die verder moeten worden beoordeeld (met Azure Content Moderator).
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: tutorial
ms.date: 01/27/2020
ms.author: pafarley
ms.openlocfilehash: 5e74eda9e30c536c0eba4e847019344c87e10cce
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76774337"
---
# <a name="tutorial-moderate-e-commerce-product-images-with-azure-content-moderator"></a>Zelfstudie: E-commerceproductafbeeldingen beheren met Azure-inhoudsmoderator

In deze zelfstudie leert u hoe u Azure Cognitive Services, waaronder Content Moderator, gebruiken om productafbeeldingen te classificeren en te beheren voor een e-commercescenario. Je gebruikt Computer Vision en Custom Vision om tags (labels) toe te passen op afbeeldingen, en dan maak je een teamreview, die op inhoudsinformatie gebaseerde technologieën combineert met menselijke beoordelingsteams om een intelligent moderatiesysteem te bieden.

In deze handleiding ontdekt u hoe u:

> [!div class="checklist"]
> * Aanmelden voor Content Moderator en een beoordelingsteam maken.
> * De Image-API van Content Moderator gebruiken om te scannen op mogelijke ongepaste inhoud of inhoud voor volwassenen.
> * De Computer Vision-service gebruiken om te scannen op beroemdheden (of andere tags die Computer Vision kan detecteren).
> * De Custom Vision service gebruiken om te scannen op de aanwezigheid van vlaggen, speelgoed en pennen (of andere aangepaste tags).
> * De gecombineerde scanresultaten presenteren voor menselijke beoordeling en uiteindelijke besluitvorming.

De volledige voorbeeldcode is beschikbaar in de opslagplaats [Samples eCommerce Catalog Moderation](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration) op GitHub.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

- Een abonnementssleutel voor Content Moderator. Volg de instructies in [Een Cognitive Services-account maken](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) om u te abonneren op Content Moderator en een sleutel op te halen.
- Een abonnementssleutel voor Computer Vision (dezelfde instructies als hierboven).
- Elke editie van [Visual Studio 2015 of 2017](https://www.visualstudio.com/downloads/).
- Een reeks afbeeldingen voor elk label dat door de Custom Vision-classificatie wordt gebruikt (in dit geval van speelgoed, pennen en vlaggen).

## <a name="create-a-review-team"></a>Een beoordelingsteam maken

Raadpleeg de [Moderator van inhoud uitproberen op het web](quick-start.md) snelstart voor instructies over hoe u zich aanmeldt voor de tool [Inhoudsmoderator en](https://contentmoderator.cognitive.microsoft.com/) maak een beoordelingsteam. Noteer de waarde voor **Team Id** op de pagina **Create review team**.

## <a name="create-custom-moderation-tags"></a>Aangepaste controletags maken

Maak vervolgens aangepaste tags in het gereedschap Controleren (zie het artikel [Labels](https://docs.microsoft.com/azure/cognitive-services/content-moderator/review-tool-user-guide/tags) als u hulp nodig hebt bij dit proces). In dit geval voegen we deze tags toe: **celebrity**, **USA**, **flag**, **toy** en **pen**. Niet alle tags moeten detecteerbare categorieën in Computer Vision (zoals **beroemdheid);** u uw eigen aangepaste tags toevoegen zolang u de classificatie van Custom Vision traint om ze later te detecteren.

![Aangepaste tags configureren](images/tutorial-ecommerce-tags2.PNG)

## <a name="create-visual-studio-project"></a>Een Visual Studio-project maken

1. Open het dialoogvenster Nieuw project in Visual Studio. Vouw **Geïnstalleerd** en vervolgens **Visual C#** uit en selecteer **Console App (.NET Framework)**.
1. Geef de toepassing de naam **EcommerceModeration** en klik op **OK**.
1. Als u dit project toevoegt aan een bestaande oplossing, selecteert u dit project als het project voor één opstart.

Deze zelfstudie markeert de code die centraal staat in het project, maar dekt niet elke regel code. Kopieer de volledige inhoud van _Program.cs_ uit het voorbeeldproject ([Samples eCommerce Catalog Moderation](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration)) naar het bestand _Program.cs_ van het nieuwe project. Neem vervolgens de onderstaande gedeelten door voor meer informatie over de werking van het project en hoe u dit zelf kunt gebruiken.

## <a name="define-api-keys-and-endpoints"></a>API-sleutels en -eindpunten definiëren

Deze zelfstudie maakt gebruik van drie cognitieve services; daarom zijn er drie bijbehorende sleutels en API-eindpunten nodig. Bekijk de volgende velden in de klasse **Program**:

[!code-csharp[define API keys and endpoint URIs](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=21-29)]

U moet de `___Key` velden bijwerken met de waarden van uw abonnementssleutels `___Uri` en u moet de velden wijzigen in de juiste URL's voor eindpunten (u krijgt later de toets Aangepaste visie en eindpunt). U deze waarden vinden in de tabbladen **Snel starten** van elke Azure-bron. Voer in het gedeelte `YOURTEAMID` van het veld `ReviewUri` de id in van het beoordelingsteam dat u eerder hebt gemaakt. Je vult het laatste deel `CustomVisionUri` van het veld later in.

[!INCLUDE [subdomains note](../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="primary-method-calls"></a>Aanroepen van primaire methode

Bekijk de volgende code in de methode **Main**, waarmee een lijst met afbeeldings-URL's wordt doorlopen. Het analyseert elke afbeelding met de drie verschillende services, registreert de toegepaste tags in de array **ReviewTags** en maakt vervolgens een beoordeling voor menselijke moderators door de afbeeldingen naar het Content Moderator Review Tool te sturen. Deze methoden gaan we in de volgende gedeelten bespreken. Als u dat wenst, u bepalen welke afbeeldingen worden verzonden om te controleren, met behulp van de array **ReviewTags** in een voorwaardelijke instructie om te controleren welke tags zijn toegepast.

[!code-csharp[Main: evaluate each image and create review](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=53-70)]

## <a name="evaluateadultracy-method"></a>EvaluateAdultRacy-methode

Bekijk de methode **EvaluateAdultRacy** in de klasse **Program**. Deze methode accepteert een afbeeldings-URL en een matrix van sleutel-waardeparen als parameters. De methode roept de Image API van Content Moderator aan (met behulp van REST) om de waarden voor AdultScore en RacyScore op te vragen voor de afbeelding. Als de score voor een van beide groter is dan 0,4 (het bereik ligt tussen 0 en 1), stelt deze de overeenkomstige waarde in de array **ReviewTags** in op **True**.

[!code-csharp[define EvaluateAdultRacy method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=73-113)]

## <a name="evaluatecomputervisiontags-method"></a>ComputerVisionTags-methode evalueren

De volgende methode verwerkt een afbeeldings-URL en de gegevens van uw Computer Vision-abonnement en analyseert de afbeelding op de aanwezigheid van beroemdheden. Als er een of meer beroemdheden worden gevonden in de afbeelding, wordt de bijbehorende waarde in de matrix **ReviewTags** ingesteld op **True**.

[!code-csharp[define EvaluateCustomVisionTags method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=115-146)]

## <a name="evaluatecustomvisiontags-method"></a>EvaluateComputerVisionTags-methode

Bekijk nu de methode **EvaluateCustomVisionTags**, waarmee de daadwerkelijke producten worden geclassificeerd&mdash;in dit geval vlaggen, speelgoed en pennen. Volg de instructies in de handleiding Hoe maak je [een classificatiegids](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) om je eigen aangepaste afbeeldingsclassificatie te bouwen en vlaggen, speelgoed en pennen (of wat je ook hebt gekozen als je aangepaste tags) in afbeeldingen te detecteren. U de afbeeldingen in de **voorbeeldafbeeldingenmap** van de [GitHub-repo](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration) gebruiken om snel een aantal categorieën in dit voorbeeld te trainen.

![Webpagina van Custom Vision met trainingsafbeeldingen van pennen, speelgoed en vlaggen](images/tutorial-ecommerce-custom-vision.PNG)

Zodra u uw classificatie hebt getraind, krijgt u de URL van de voorspellingssleutel en het voorspellingseindpunt (zie De URL `CustomVisionKey` `CustomVisionUri` en [de voorspellingssleutel ophalen](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/use-prediction-api#get-the-url-and-prediction-key) als u hulp nodig hebt bij het ophalen ervan) en wijst u deze waarden toe aan respectievelijk uw en velden. De methode gebruikt deze waarden om query's uit te voeren op de classificatie. Als de classificatie een of meer van de aangepaste tags vindt in de afbeelding, worden de bijbehorende waarden in de matrix **ReviewTags** ingesteld op **True**.

[!code-csharp[define EvaluateCustomVisionTags method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=148-171)]

## <a name="create-reviews-for-review-tool"></a>Beoordelingen maken voor het beoordelingsprogramma

In de vorige secties heb je onderzocht hoe de app binnenkomende afbeeldingen scant op inhoud voor volwassenen en racy (Content Moderator), beroemdheden (Computer Vision) en diverse andere objecten (Custom Vision). Zie vervolgens de **methode CreateReview,** die de afbeeldingen met al hun toegepaste tags (doorgegeven als _metagegevens)_ uploadt naar het Inhoudsmoderator-revisieprogramma.

[!code-csharp[define CreateReview method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=173-196)]

De afbeeldingen worden weergegeven op het tabblad Review van het [beoordelingsprogramma Content Moderator](https://contentmoderator.cognitive.microsoft.com/).

![Schermafbeelding van het beoordelingsprogramma Content Moderator met verschillende afbeeldingen en de bijbehorende gemarkeerde tags](images/tutorial-ecommerce-content-moderator.PNG)

## <a name="submit-a-list-of-test-images"></a>Een lijst met testafbeeldingen aanbieden

Zoals u kunt zien in de methode **Main**, zoekt dit programma naar een map 'C:Test' met daarin een bestand _Urls.txt_ dat een lijst met afbeeldings-URL's bevat. Maak dit bestand en de map of wijzig het pad om naar uw tekstbestand te wijzen. Vul dit bestand vervolgens in met de URL's van afbeeldingen die u wilt testen.

[!code-csharp[Main: set up test directory, read lines](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=38-51)]

## <a name="run-the-program"></a>Het programma uitvoeren

Als u alle bovenstaande stappen hebt gevolgd, moet het programma elke afbeelding verwerken (alle drie de services voor hun relevante tags opvragen) en de afbeeldingen met taginformatie uploaden naar het Inhoudsmoderatorcontrolehulpprogramma.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie stelt u een programma in om productafbeeldingen te analyseren, deze te taggen op producttype en een beoordelingsteam weloverwogen beslissingen te laten nemen over inhoudsmatiging. In de volgende zelfstudie wordt meer aandacht besteed aan het beoordelen van gecontroleerde afbeeldingen.

> [!div class="nextstepaction"]
> [Gecontroleerde afbeeldingen beoordelen](./review-tool-user-guide/review-moderated-images.md)
