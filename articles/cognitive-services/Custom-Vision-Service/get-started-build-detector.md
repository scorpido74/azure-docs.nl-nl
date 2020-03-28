---
title: Snelstart Een objectdetector bouwen - Custom Vision Service
titleSuffix: Azure Cognitive Services
description: In deze quickstart leert u hoe u de Custom Vision-website gebruiken om een afbeeldingsclassificatiemodel te maken.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 12/05/2019
ms.author: anroth
ms.openlocfilehash: 8aef46f0b9c3dc526f1fbed3d9bc59f97771b509
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169990"
---
# <a name="quickstart-how-to-build-an-object-detector-with-custom-vision"></a>Snelstart: Een objectdetector bouwen met Custom Vision

In deze quickstart leer je hoe je een objectdetector bouwen via de Custom Vision-website. Zodra u een detectormodel hebt gemaakt, u de Custom Vision-service gebruiken voor objectdetectie.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

- Een set afbeeldingen waarmee u uw detectormodel trainen. U de set [voorbeeldafbeeldingen](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/tree/master/samples/vision/images) op GitHub gebruiken. Of u uw eigen afbeeldingen kiezen met behulp van de onderstaande tips.

## <a name="create-custom-vision-resources-in-the-azure-portal"></a>Aangepaste vision-resources maken in de Azure-portal

[!INCLUDE [create-resources](includes/create-resources.md)]

## <a name="create-a-new-project"></a>Een nieuw project maken

Navigeer in uw webbrowser naar de [webpagina Aangepast zicht](https://customvision.ai) en selecteer __Aanmelden.__ Meld u aan met hetzelfde account dat u hebt gebruikt om u aan te melden bij de Azure-portal.

![Afbeelding van de aanmeldingspagina](./media/browser-home.png)


1. Als u uw eerste project wilt maken, selecteert u **Nieuw project**. Het dialoogvenster **Nieuw project maken** wordt weergegeven.

    ![Het dialoogvenster Nieuw project heeft velden voor naam, beschrijving en domeinen.](./media/get-started-build-detector/new-project.png)

1. Voer een naam en een beschrijving voor het project in. Selecteer vervolgens een resourcegroep. Als uw aangemelde account is gekoppeld aan een Azure-account, worden in de vervolgkeuzelijst Resourcegroep al uw Azure ResourceGroepen weergegeven die een Aangepaste Vision-servicebron bevatten. 

   > [!NOTE]
   > Als er geen resourcegroep beschikbaar is, bevestig dan dat u zich hebt aangemeld bij [customvision.ai](https://customvision.ai) met hetzelfde account als u hebt gebruikt om u aan te melden bij de [Azure-portal.](https://portal.azure.com/) Bevestig ook dat u dezelfde 'Directory' hebt geselecteerd in de Portal voor Aangepaste Visie als de map in de Azure-portal waar uw Aangepaste Visie-bronnen zich bevinden. Op beide sites u uw map selecteren in het vervolgkeuzemenu in de rechterbovenhoek van het scherm. 

1. Selecteer __Objectdetectie__ onder __Projecttypen__.

1. Selecteer vervolgens een van de beschikbare domeinen. Elk domein optimaliseert de detector voor specifieke typen afbeeldingen, zoals beschreven in de volgende tabel. U het domein later wijzigen als u dat wenst.

    |Domain|Doel|
    |---|---|
    |__Algemeen__| Geoptimaliseerd voor een breed scala aan objectdetectietaken. Als geen van de andere domeinen geschikt is of als u niet zeker weet welk domein u moet kiezen, selecteert u het algemene domein. |
    |__Logo__|Geoptimaliseerd voor het vinden van merklogo's in afbeeldingen.|
    |__Compacte domeinen__| Geoptimaliseerd voor de beperkingen van real-time objectdetectie op mobiele apparaten. De modellen die worden gegenereerd door compacte domeinen kunnen worden geëxporteerd om lokaal te worden uitgevoerd.|

1. Selecteer tot slot __Project maken__.

## <a name="choose-training-images"></a>Trainingsafbeeldingen kiezen

[!INCLUDE [choose training images](includes/choose-training-images.md)]

## <a name="upload-and-tag-images"></a>Afbeeldingen uploaden en coderen

In deze sectie upload je afbeeldingen en tag je handmatig om de detector te trainen. 

1. Als u afbeeldingen wilt toevoegen, klikt u op de knop __Afbeeldingen toevoegen__ en selecteert u Bladeren door __lokale bestanden__. Selecteer __Openen__ om de afbeeldingen te uploaden.

    ![Het besturingselement Afbeeldingen toevoegen wordt linksboven en als knop onder in het midden weergegeven.](./media/get-started-build-detector/add-images.png)

1. Je ziet je geüploade afbeeldingen in het gedeelte **Niet-gelabelde** gebruikersinterface. De volgende stap is om handmatig tag de objecten die u wilt dat de detector te leren herkennen. Klik op de eerste afbeelding om het dialoogvenster tagging te openen. 

    ![Afbeeldingen geüpload in de sectie Niet-gelabeld](./media/get-started-build-detector/images-untagged.png)

1. Klik en sleep een rechthoek rond het object in de afbeelding. Voer vervolgens een nieuwe tagnaam in met de **+** knop of selecteer een bestaande tag in de vervolgkeuzelijst. Het is erg belangrijk om elke instantie van het object(en) dat u wilt detecteren te taggen, omdat de detector het niet-gelabelde achtergrondgebied als negatief voorbeeld gebruikt in de training. Wanneer u klaar bent met taggen, klikt u op de pijl rechts om uw tags op te slaan en door te gaan naar de volgende afbeelding.

    ![Een object taggen met een rechthoekige selectie](./media/get-started-build-detector/image-tagging.png)

Als u een andere set afbeeldingen wilt uploaden, gaat u terug naar de bovenkant van deze sectie en herhaalt u de stappen.

## <a name="train-the-detector"></a>Train de detector

Als u het detectormodel wilt trainen, selecteert u de **knop Trein.** De detector gebruikt alle huidige afbeeldingen en hun tags om een model te maken dat elk gelabeld object identificeert.

![De treinknop rechtsboven op de werkbalk van de webpagina](./media/getting-started-build-a-classifier/train01.png)

Het trainingsproces duurt slechts enkele minuten. Gedurende deze periode wordt informatie over het trainingsproces weergegeven op het tabblad **Prestaties.**

![Het browservenster met een trainingsdialoogvenster in het hoofdgedeelte](./media/get-started-build-detector/training.png)

## <a name="evaluate-the-detector"></a>Evalueer de detector

Nadat de training is voltooid, worden de prestaties van het model berekend en weergegeven. De aangepaste vision-service gebruikt de afbeeldingen die u hebt ingediend voor training om precisie, terugroepinformatie en gemiddelde gemiddelde precisie te berekenen. Precisie en terugroeping zijn twee verschillende metingen van de effectiviteit van een detector:

- **Precisie** geeft de fractie van geïdentificeerde classificaties aan die juist waren. Bijvoorbeeld, als het model geïdentificeerd 100 beelden als honden, en 99 van hen waren eigenlijk van honden, dan is de precisie zou 99%.
- **Recall** geeft de fractie aan van de werkelijke classificaties die correct zijn geïdentificeerd. Bijvoorbeeld, als er eigenlijk 100 beelden van appels, en het model geïdentificeerd 80 als appels, zou de recall 80%.

![De trainingsresultaten tonen de algehele precisie en terugroep, en betekenen gemiddelde gemiddelde precisie.](./media/get-started-build-detector/trained-performance.png)

### <a name="probability-threshold"></a>Drempelwaarde waarschijnlijkheid

[!INCLUDE [probability threshold](includes/probability-threshold.md)]

## <a name="manage-training-iterations"></a>Trainingsiteraties beheren

Elke keer dat u uw detector traint, maakt u een nieuwe _iteratie_ met zijn eigen bijgewerkte prestatiestatistieken. U al uw iteraties bekijken in het linkerdeelvenster van het tabblad **Prestaties.** In het linkerdeelvenster vindt u ook de knop **Verwijderen,** die u gebruiken om een iteratie te verwijderen als deze verouderd is. Wanneer u een iteratie verwijdert, verwijdert u afbeeldingen die er uniek aan zijn gekoppeld.

Zie [Uw model gebruiken met de voorspellings-API](./use-prediction-api.md) om te leren hoe u programmatisch toegang krijgt tot uw getrainde modellen.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart heb je met behulp van de Custom Vision-website geleerd hoe je een objectdetectormodel maken en trainen. Vervolgens krijgt u meer informatie over het iteratieve proces van het verbeteren van uw model.

> [!div class="nextstepaction"]
> [Een model testen en opnieuw trainen](test-your-model.md)

