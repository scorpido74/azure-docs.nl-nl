---
title: Quick Start een object detector-Custom Vision Service bouwen
titleSuffix: Azure Cognitive Services
description: In deze Quick Start leert u hoe u de Custom Vision-website kunt gebruiken om een installatie kopie classificatie model te maken.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 12/05/2019
ms.author: anroth
ms.openlocfilehash: 0d9c175db1370fe07b3278a46d910c59d81df860
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74969918"
---
# <a name="quickstart-how-to-build-an-object-detector-with-custom-vision"></a>Quick Start: een object detector bouwen met Custom Vision

In deze Quick Start leert u hoe u een object detector kunt bouwen via de Custom Vision-website. Wanneer u een detector model bouwt, kunt u de Custom Vision-service gebruiken voor object detectie.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

- Een set installatie kopieën waarmee uw detector model kan worden getraind. U kunt de set [voorbeeld afbeeldingen](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/tree/master/samples/vision/images) gebruiken op github. U kunt ook uw eigen installatie kopieën kiezen met behulp van de onderstaande tips.

## <a name="create-custom-vision-resources-in-the-azure-portal"></a>Custom Vision resources maken in de Azure Portal

[!INCLUDE [create-resources](includes/create-resources.md)]

## <a name="create-a-new-project"></a>Een nieuw project maken

Navigeer in uw webbrowser naar de webpagina van [Custom Vision](https://customvision.ai) en selecteer __Aanmelden__. Meld u aan met hetzelfde account dat u hebt gebruikt om u aan te melden bij de Azure Portal.

![Afbeelding van de aanmeldings pagina](./media/browser-home.png)


1. Als u uw eerste project wilt maken, selecteert u **Nieuw project**. Het dialoog venster **Nieuw project maken** wordt weer gegeven.

    ![Het dialoog venster Nieuw project bevat velden voor naam, beschrijving en domeinen.](./media/get-started-build-detector/new-project.png)

1. Voer een naam en een beschrijving in voor het project. Selecteer vervolgens een resource groep. Als uw aangemelde account is gekoppeld aan een Azure-account, worden in de vervolg keuzelijst voor de resource groep al uw Azure-resource groepen weer gegeven die een Custom Vision Service Resource bevatten. 

   > [!NOTE]
   > Als er geen resource groep beschikbaar is, bevestigt u dat u bent aangemeld bij [customvision.ai](https://customvision.ai) met hetzelfde account als u hebt gebruikt om u aan te melden bij de [Azure Portal](https://portal.azure.com/). Zorg er ook voor dat u dezelfde map hebt geselecteerd in de Custom Vision portal als de map in de Azure Portal waar uw Custom Vision-resources zich bevinden. In beide sites kunt u uw map selecteren in het menu van de vervolg keuzelijst in de rechter bovenhoek van het scherm. 

1. Selecteer __object detectie__ onder __project typen__.

1. Selecteer vervolgens een van de beschik bare domeinen. Elk domein optimaliseert de detector voor specifieke typen installatie kopieën, zoals beschreven in de volgende tabel. U kunt het domein later indien gewenst wijzigen.

    |Domain|Doel|
    |---|---|
    |__Algemeen__| Geoptimaliseerd voor een breed scala aan object detectie taken. Als geen van de andere domeinen geschikt is of als u niet zeker weet welk domein u wilt kiezen, selecteert u het algemene domein. |
    |__Logo__|Geoptimaliseerd voor het vinden van merk logo's in afbeeldingen.|
    |__Compacte domeinen__| Geoptimaliseerd voor de beperkingen van real-time object detectie op mobiele apparaten. De modellen die door compacte domeinen worden gegenereerd, kunnen worden geëxporteerd om lokaal uit te voeren.|

1. Selecteer ten slotte __project maken__.

## <a name="choose-training-images"></a>Trainings afbeeldingen kiezen

[!INCLUDE [choose training images](includes/choose-training-images.md)]

## <a name="upload-and-tag-images"></a>Afbeeldingen uploaden en labelen

In deze sectie gaat u afbeeldingen uploaden en hand matig labelen om de detector te helpen trainen. 

1. Als u installatie kopieën wilt toevoegen, klikt u op de knop __afbeeldingen toevoegen__ en selecteert u __lokale bestanden zoeken__. Selecteer __openen__ om de installatie kopieën te uploaden.

    ![Het besturings element afbeeldingen toevoegen wordt weer gegeven in de linkerbovenhoek en als een knop onderaan in het midden.](./media/get-started-build-detector/add-images.png)

1. U ziet de geüploade installatie kopieën in de sectie niet- **gelabeld** van de gebruikers interface. De volgende stap is het hand matig labelen van de objecten die u door de detector wilt laten herkennen. Klik op de eerste afbeelding om het dialoog venster labelen te openen. 

    ![Afbeeldingen die zijn geüpload, in de sectie zonder Tags](./media/get-started-build-detector/images-untagged.png)

1. Klik en sleep een rechthoek rond het object in uw afbeelding. Voer vervolgens een nieuwe label naam in met de knop **+** of selecteer een bestaande tag in de vervolg keuzelijst. Het is belang rijk dat u alle exemplaren van de object (en) die u wilt detecteren, labelt, omdat de detector het ongecodeerde achtergrond gebied als een negatief voor beeld in de training gebruikt. Wanneer u klaar bent met Tags maken, klikt u op de pijl aan de rechter kant om uw tags op te slaan en naar de volgende afbeelding te gaan.

    ![Een object met een rechthoekige selectie labelen](./media/get-started-build-detector/image-tagging.png)

Als u nog een aantal installatie kopieën wilt uploaden, gaat u terug naar het begin van deze sectie en herhaalt u de stappen.

## <a name="train-the-detector"></a>De detector trainen

Selecteer de **trein** knop om het detector model te trainen. De detector gebruikt alle huidige installatie kopieën en de bijbehorende tags om een model te maken dat elk gelabeld object identificeert.

![De trein knop in de rechter bovenhoek van de koptekst werkbalk van de webpagina](./media/getting-started-build-a-classifier/train01.png)

Het trainings proces kan slechts enkele minuten duren. Gedurende deze periode wordt informatie over het trainings proces weer gegeven op het tabblad **prestaties** .

![Het browser venster met een dialoog venster voor training in de hoofd sectie](./media/get-started-build-detector/training.png)

## <a name="evaluate-the-detector"></a>De detector evalueren

Nadat de training is voltooid, worden de prestaties van het model berekend en weer gegeven. De Custom Vision-service gebruikt de installatie kopieën die u hebt ingediend voor training om de precisie, het terughalen en gemiddelde precisie te berekenen. Nauw keurigheid en intrekken zijn twee verschillende metingen van de effectiviteit van een detector:

- **Precisie** geeft de Fractie van geïdentificeerde classificaties aan die juist zijn. Als het model bijvoorbeeld 100 installatie kopieën heeft geïdentificeerd als honden en 99 van de afbeeldingen daad werkelijk van honden, zou de precisie 99% zijn.
- Bij **intrekken** wordt het gedeelte van de werkelijke classificaties aangegeven die juist zijn geïdentificeerd. Als er bijvoorbeeld daad werkelijk 100 installatie kopieën van Apples waren en het model 80 als Apple heeft geïdentificeerd, is het intrekken 80%.

![De resultaten van de training tonen de algemene precisie en terugroeping en gemiddelde nauw keurigheid.](./media/get-started-build-detector/trained-performance.png)

### <a name="probability-threshold"></a>Drempelwaarde waarschijnlijkheid

[!INCLUDE [probability threshold](includes/probability-threshold.md)]

## <a name="manage-training-iterations"></a>Trainings herhalingen beheren

Telkens wanneer u uw detector traint, maakt u een nieuwe _iteratie_ met de eigen bijgewerkte metrische gegevens voor prestaties. U kunt al uw iteraties bekijken in het linkerdeel venster van het tabblad **prestaties** . In het linkerdeel venster ziet u ook de knop **verwijderen** , die u kunt gebruiken om een herhaling te verwijderen als deze verouderd is. Wanneer u een herhaling verwijdert, verwijdert u alle installatie kopieën die uniek zijn gekoppeld.

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u geleerd hoe u een object detector model maakt en traint met behulp van de Custom Vision-website. Vervolgens krijgt u meer informatie over het iteratieve proces van het verbeteren van uw model.

> [!div class="nextstepaction"]
> [Een model testen en opnieuw trainen](test-your-model.md)

