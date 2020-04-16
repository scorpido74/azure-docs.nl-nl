---
title: Snelstart Een classificatie bouwen - Custom Vision Service
titleSuffix: Azure Cognitive Services
description: In deze quickstart leert u hoe u de Custom Vision-website gebruiken om een afbeeldingsclassificatiemodel te maken.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: 56bdaa324420bf274e7cda8ac1c6506e4bc9ad21
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81404056"
---
# <a name="quickstart-how-to-build-a-classifier-with-custom-vision"></a>Snelstart: Hoe maak je een classificatie te bouwen met Custom Vision

In deze quickstart leer je hoe je een classificatie bouwen via de Custom Vision-website. Zodra u een classificatiemodel hebt gemaakt, u de aangepaste vision-service gebruiken voor afbeeldingsclassificatie.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

- Een set van beelden waarmee je je classifier trainen. Zie hieronder voor tips over het kiezen van afbeeldingen.

## <a name="create-custom-vision-resources-in-the-azure-portal"></a>Aangepaste vision-resources maken in de Azure-portal

[!INCLUDE [create-resources](includes/create-resources.md)]

## <a name="create-a-new-project"></a>Een nieuw project maken

Navigeer in uw webbrowser naar de [webpagina Aangepast zicht](https://customvision.ai) en selecteer __Aanmelden.__ Meld u aan met hetzelfde account dat u hebt gebruikt om u aan te melden bij de Azure-portal.

![Afbeelding van de aanmeldingspagina](./media/browser-home.png)


1. Als u uw eerste project wilt maken, selecteert u **Nieuw project**. Het dialoogvenster **Nieuw project maken** wordt weergegeven.

    ![Het dialoogvenster Nieuw project heeft velden voor naam, beschrijving en domeinen.](./media/getting-started-build-a-classifier/new-project.png)

1. Voer een naam en een beschrijving voor het project in. Selecteer vervolgens een resourcegroep. Als uw aangemelde account is gekoppeld aan een Azure-account, worden in de vervolgkeuzelijst Resourcegroep al uw Azure ResourceGroepen weergegeven die een Aangepaste Vision-servicebron bevatten. 

   > [!NOTE]
   > Als er geen resourcegroep beschikbaar is, bevestig dan dat u zich hebt aangemeld bij [customvision.ai](https://customvision.ai) met hetzelfde account als u hebt gebruikt om u aan te melden bij de [Azure-portal.](https://portal.azure.com/) Bevestig ook dat u dezelfde 'Directory' hebt geselecteerd in de Portal voor Aangepaste Visie als de map in de Azure-portal waar uw Aangepaste Visie-bronnen zich bevinden. Op beide sites u uw map selecteren in het vervolgkeuzemenu in de rechterbovenhoek van het scherm. 

1. Selecteer __Classificatie__ onder __Projecttypen__. Kies vervolgens onder __Classificatietypen__ **multilabel** of **multiclass,** afhankelijk van uw use case. Multilabelclassificatie past een willekeurig aantal van uw tags toe op een afbeelding (nul of meer), terwijl classificatie van meerdere klassen afbeeldingen sorteert in afzonderlijke categorieën (elke afbeelding die u indient, wordt gesorteerd in de meest waarschijnlijke tag). U het classificatietype later wijzigen als u dat wilt.

1. Selecteer vervolgens een van de beschikbare domeinen. Elk domein optimaliseert de classificatie voor specifieke typen afbeeldingen, zoals beschreven in de volgende tabel. U het domein later wijzigen als u dat wenst.

    |Domain|Doel|
    |---|---|
    |__Generieke__| Geoptimaliseerd voor een breed scala aan taken voor beeldclassificatie. Als geen van de andere domeinen geschikt is of als u niet zeker weet welk domein u moet kiezen, selecteert u het algemene domein. |
    |__Voedsel__|Geoptimaliseerd voor foto's van gerechten zoals je ze zou zien op een restaurant menu. Als u foto's van individuele groenten of fruit wilt classificeren, gebruikt u het domein Voedsel.|
    |__Bezienswaardigheden__|Geoptimaliseerd voor herkenbare oriëntatiepunten, zowel natuurlijk als kunstmatig. Dit domein werkt het beste wanneer het oriëntatiepunt duidelijk zichtbaar is op de foto. Dit domein werkt zelfs als het oriëntatiepunt lichtjes door mensen voor het wordt belemmerd.|
    |__Retail__|Geoptimaliseerd voor afbeeldingen die worden gevonden in een winkelcatalogus of winkelwebsite. Als u wilt hoge precisie classificeren tussen jurken, broeken en shirts, gebruik dan dit domein.|
    |__Compacte domeinen__| Geoptimaliseerd voor de beperkingen van real-time classificatie op mobiele apparaten. De modellen die worden gegenereerd door compacte domeinen kunnen worden geëxporteerd om lokaal te worden uitgevoerd.|

1. Selecteer tot slot __Project maken__.

## <a name="choose-training-images"></a>Trainingsafbeeldingen kiezen

[!INCLUDE [choose training images](includes/choose-training-images.md)]

## <a name="upload-and-tag-images"></a>Afbeeldingen uploaden en coderen

In deze sectie upload en tag je afbeeldingen handmatig om de classificatie te trainen. 

1. Als u afbeeldingen wilt toevoegen, klikt u op de knop __Afbeeldingen toevoegen__ en selecteert u Bladeren door __lokale bestanden__. Selecteer __Openen__ om naar tags te gaan. Je tagselectie wordt toegepast op de hele groep afbeeldingen die je hebt geselecteerd om te uploaden, dus het is makkelijker om afbeeldingen in afzonderlijke groepen te uploaden op basis van de gewenste tags. U de tags voor afzonderlijke afbeeldingen ook wijzigen nadat ze zijn geüpload.

    ![Het besturingselement Afbeeldingen toevoegen wordt linksboven en als knop onder in het midden weergegeven.](./media/getting-started-build-a-classifier/add-images01.png)


1. Als u een tag wilt maken, voert u tekst in het veld __Mijn tags in__ en drukt u op Enter. Als de tag al bestaat, wordt deze weergegeven in een vervolgkeuzemenu. In een multilabelproject u meer dan één tag aan uw afbeeldingen toevoegen, maar in een project met meerdere klassen u er slechts één toevoegen. Als u het uploaden van de afbeeldingen wilt voltooien, gebruikt u de knop __[nummer] bestanden uploaden.__ 

    ![Afbeelding van de tag- en uploadpagina](./media/getting-started-build-a-classifier/add-images03.png)

1. Selecteer __Gereed__ zodra de afbeeldingen zijn geüpload.

    ![Op de voortgangsbalk worden alle voltooide taken weergegeven.](./media/getting-started-build-a-classifier/add-images04.png)

Als u een andere set afbeeldingen wilt uploaden, gaat u terug naar de bovenkant van deze sectie en herhaalt u de stappen.

## <a name="train-the-classifier"></a>De classificatie trainen

Als u de classificatie wilt trainen, selecteert u de knop **Trein.** De classificatie gebruikt alle huidige afbeeldingen om een model te maken dat de visuele kwaliteiten van elke tag identificeert.

![De treinknop rechtsboven op de werkbalk van de webpagina](./media/getting-started-build-a-classifier/train01.png)

Het trainingsproces duurt slechts enkele minuten. Gedurende deze periode wordt informatie over het trainingsproces weergegeven op het tabblad **Prestaties.**

![Het browservenster met een trainingsdialoogvenster in het hoofdgedeelte](./media/getting-started-build-a-classifier/train02.png)

## <a name="evaluate-the-classifier"></a>De classificatie evalueren

Nadat de training is voltooid, worden de prestaties van het model geschat en weergegeven. De Custom Vision Service gebruikt de afbeeldingen die u hebt ingediend voor training om precisie en terugroeping te berekenen, met behulp van een proces dat [k-fold cross validatie](https://en.wikipedia.org/wiki/Cross-validation_(statistics))wordt genoemd. Precisie en terugroeping zijn twee verschillende metingen van de effectiviteit van een classificatie:

- **Precisie** geeft de fractie van geïdentificeerde classificaties aan die juist waren. Bijvoorbeeld, als het model geïdentificeerd 100 beelden als honden, en 99 van hen waren eigenlijk van honden, dan is de precisie zou 99%.
- **Recall** geeft de fractie aan van de werkelijke classificaties die correct zijn geïdentificeerd. Bijvoorbeeld, als er eigenlijk 100 beelden van appels, en het model geïdentificeerd 80 als appels, zou de recall 80%.

![De trainingsresultaten tonen de algehele precisie en recall, en de precisie en recall voor elke tag in de classificatie.](./media/getting-started-build-a-classifier/train03.png)

### <a name="probability-threshold"></a>Drempelwaarde waarschijnlijkheid

[!INCLUDE [probability threshold](includes/probability-threshold.md)]

## <a name="manage-training-iterations"></a>Trainingsiteraties beheren

Elke keer dat u uw classificatie traint, maakt u een nieuwe _iteratie_ met zijn eigen bijgewerkte prestatiestatistieken. U al uw iteraties bekijken in het linkerdeelvenster van het tabblad **Prestaties.** U vindt ook de knop **Verwijderen,** die u gebruiken om een iteratie te verwijderen als deze verouderd is. Wanneer u een iteratie verwijdert, verwijdert u afbeeldingen die er uniek aan zijn gekoppeld.

Zie [Uw model gebruiken met de voorspellings-API](./use-prediction-api.md) om te leren hoe u programmatisch toegang krijgt tot uw getrainde modellen.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart heb je met behulp van de Custom Vision-website geleerd hoe je een beeldclassificatiemodel maken en trainen. Vervolgens krijgt u meer informatie over het iteratieve proces van het verbeteren van uw model.

> [!div class="nextstepaction"]
> [Een model testen en opnieuw trainen](test-your-model.md)

