---
title: Quick Start een classifier-Custom Vision Service bouwen
titleSuffix: Azure Cognitive Services
description: In deze Quick Start leert u hoe u de Custom Vision-website kunt gebruiken om een installatie kopie classificatie model te maken.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: 56bdaa324420bf274e7cda8ac1c6506e4bc9ad21
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81404056"
---
# <a name="quickstart-how-to-build-a-classifier-with-custom-vision"></a>Snelstartgids: een classificatie bouwen met Custom Vision

In deze Quick Start leert u hoe u een classificatie kunt bouwen via de website van Custom Vision. Nadat u een classificatie model hebt gemaakt, kunt u de Custom Vision-service gebruiken voor het classificeren van afbeeldingen.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

- Een set installatie kopieën waarmee u uw classificatie kunt trainen. Hieronder vindt u tips voor het kiezen van installatie kopieën.

## <a name="create-custom-vision-resources-in-the-azure-portal"></a>Custom Vision resources maken in de Azure Portal

[!INCLUDE [create-resources](includes/create-resources.md)]

## <a name="create-a-new-project"></a>Een nieuw project maken

Navigeer in uw webbrowser naar de webpagina van [Custom Vision](https://customvision.ai) en selecteer __Aanmelden__. Meld u aan met hetzelfde account dat u hebt gebruikt om u aan te melden bij de Azure Portal.

![Afbeelding van de aanmeldings pagina](./media/browser-home.png)


1. Als u uw eerste project wilt maken, selecteert u **Nieuw project**. Het dialoog venster **Nieuw project maken** wordt weer gegeven.

    ![Het dialoog venster Nieuw project bevat velden voor naam, beschrijving en domeinen.](./media/getting-started-build-a-classifier/new-project.png)

1. Voer een naam en een beschrijving in voor het project. Selecteer vervolgens een resource groep. Als uw aangemelde account is gekoppeld aan een Azure-account, worden in de vervolg keuzelijst voor de resource groep al uw Azure-resource groepen weer gegeven die een Custom Vision Service Resource bevatten. 

   > [!NOTE]
   > Als er geen resource groep beschikbaar is, bevestigt u dat u bent aangemeld bij [customvision.ai](https://customvision.ai) met hetzelfde account als u hebt gebruikt om u aan te melden bij de [Azure Portal](https://portal.azure.com/). Zorg er ook voor dat u dezelfde map hebt geselecteerd in de Custom Vision portal als de map in de Azure Portal waar uw Custom Vision-resources zich bevinden. In beide sites kunt u uw map selecteren in het menu van de vervolg keuzelijst in de rechter bovenhoek van het scherm. 

1. Selecteer __classificatie__ onder __project typen__. Kies onder __classificatie typen__ofwel multi **Label** of multi **Class**, afhankelijk van uw use-case. Met de classificatie voor meerdere labels wordt een wille keurig aantal tags toegepast op een afbeelding (nul of meer), terwijl classificatie met meerdere klassen afbeeldingen in één categorie sorteert (elke afbeelding die u verzendt, wordt in de meest waarschijnlijke tag gesorteerd). Als u wilt, kunt u het classificatie type later wijzigen.

1. Selecteer vervolgens een van de beschik bare domeinen. Elk domein optimaliseert de classificatie voor specifieke typen installatie kopieën, zoals beschreven in de volgende tabel. U kunt het domein later indien gewenst wijzigen.

    |Domain|Doel|
    |---|---|
    |__Encarta__| Geoptimaliseerd voor een breed scala aan afbeeldings classificatie taken. Als geen van de andere domeinen geschikt is of als u niet zeker weet welk domein u wilt kiezen, selecteert u het algemene domein. |
    |__Middel__|Geoptimaliseerd voor foto's van vaat werk zoals u zou zien in een restaurant menu. Als u foto's van afzonderlijke vruchten of groenten wilt classificeren, gebruikt u het voedsel domein.|
    |__Oriëntatie punten__|Geoptimaliseerd voor herken bare bezienswaardigheden, zowel natuurlijke als kunst matig. Dit domein werkt het beste wanneer het oriëntatie punt duidelijk zichtbaar is in de foto. Dit domein werkt ook als het oriëntatie punt enigszins wordt belemmerd door mensen die er een voor hebben.|
    |__Retail__|Geoptimaliseerd voor installatie kopieën die worden gevonden in een winkel catalogus of een winkel website. Gebruik dit domein als u een hoge precisie wilt indelen tussen de handelingen, Pants en shirts.|
    |__Compacte domeinen__| Geoptimaliseerd voor de beperkingen van real-time classificatie op mobiele apparaten. De modellen die door compacte domeinen worden gegenereerd, kunnen worden geëxporteerd om lokaal uit te voeren.|

1. Selecteer ten slotte __project maken__.

## <a name="choose-training-images"></a>Trainings afbeeldingen kiezen

[!INCLUDE [choose training images](includes/choose-training-images.md)]

## <a name="upload-and-tag-images"></a>Afbeeldingen uploaden en coderen

In deze sectie gaat u afbeeldingen uploaden en hand matig labelen om de classificatie te trainen. 

1. Als u installatie kopieën wilt toevoegen, klikt u op de knop __afbeeldingen toevoegen__ en selecteert u __lokale bestanden zoeken__. Selecteer __openen__ om naar labels te verplaatsen. De label selectie wordt toegepast op de hele groep installatie kopieën die u hebt geselecteerd om te uploaden. het is dus eenvoudiger om afbeeldingen in afzonderlijke groepen te uploaden op basis van de gewenste labels. U kunt ook de labels voor afzonderlijke afbeeldingen wijzigen nadat ze zijn geüpload.

    ![Het besturings element afbeeldingen toevoegen wordt weer gegeven in de linkerbovenhoek en als een knop onderaan in het midden.](./media/getting-started-build-a-classifier/add-images01.png)


1. Als u een tag wilt maken, voert u tekst in het veld __My Tags__ in en drukt u op ENTER. Als de tag al bestaat, wordt deze weer gegeven in een vervolg keuzemenu. In een project met meerdere labels kunt u meer dan één tag toevoegen aan uw installatie kopieën, maar in een project met meerdere klassen kunt u slechts één label toevoegen. Als u het uploaden van de installatie kopieën wilt volt ooien, gebruikt u de knop __[aantal] bestanden uploaden__ . 

    ![Afbeelding van de tag en upload pagina](./media/getting-started-build-a-classifier/add-images03.png)

1. Selecteer __gereed__ wanneer de installatie kopieën zijn geüpload.

    ![Op de voortgangs balk worden alle voltooide taken weer gegeven.](./media/getting-started-build-a-classifier/add-images04.png)

Als u nog een aantal installatie kopieën wilt uploaden, gaat u terug naar het begin van deze sectie en herhaalt u de stappen.

## <a name="train-the-classifier"></a>De classificatie trainen

Als u de classificatie wilt trainen, selecteert u de **trein** knop. De classificatie maakt gebruik van alle huidige installatie kopieën om een model te maken waarmee de visuele kwaliteiten van elke tag worden geïdentificeerd.

![De trein knop in de rechter bovenhoek van de koptekst werkbalk van de webpagina](./media/getting-started-build-a-classifier/train01.png)

Het trainings proces kan slechts enkele minuten duren. Gedurende deze periode wordt informatie over het trainings proces weer gegeven op het tabblad **prestaties** .

![Het browser venster met een dialoog venster voor training in de hoofd sectie](./media/getting-started-build-a-classifier/train02.png)

## <a name="evaluate-the-classifier"></a>De classificatie evalueren

Nadat de training is voltooid, worden de prestaties van het model geschat en weer gegeven. De Custom Vision Service gebruikt de installatie kopieën die u hebt ingediend voor training om de nauw keurigheid te berekenen en op te halen met behulp van een proces met de [Kruis validatie met de vouw k-vouwen](https://en.wikipedia.org/wiki/Cross-validation_(statistics)). Nauw keurigheid en intrekken zijn twee verschillende metingen van de effectiviteit van een classificatie:

- **Precisie** geeft de Fractie van geïdentificeerde classificaties aan die juist zijn. Als het model bijvoorbeeld 100 installatie kopieën heeft geïdentificeerd als honden en 99 van de afbeeldingen daad werkelijk van honden, zou de precisie 99% zijn.
- Bij **intrekken** wordt het gedeelte van de werkelijke classificaties aangegeven die juist zijn geïdentificeerd. Als er bijvoorbeeld daad werkelijk 100 installatie kopieën van Apples waren en het model 80 als Apple heeft geïdentificeerd, is het intrekken 80%.

![De resultaten van de training tonen de algehele nauw keurigheid en intrekken en de nauw keurigheid en intrekken voor elke tag in de classificatie.](./media/getting-started-build-a-classifier/train03.png)

### <a name="probability-threshold"></a>Drempelwaarde waarschijnlijkheid

[!INCLUDE [probability threshold](includes/probability-threshold.md)]

## <a name="manage-training-iterations"></a>Trainings herhalingen beheren

Telkens wanneer u uw classificatie traint, maakt u een nieuwe _iteratie_ met de eigen bijgewerkte metrische gegevens voor prestaties. U kunt al uw iteraties bekijken in het linkerdeel venster van het tabblad **prestaties** . U vindt ook de knop **verwijderen** , die u kunt gebruiken om een herhaling te verwijderen als deze verouderd is. Wanneer u een herhaling verwijdert, verwijdert u alle installatie kopieën die uniek zijn gekoppeld.

Zie [uw model gebruiken met de Voorspellings-API](./use-prediction-api.md) om te leren hoe u via een programma toegang hebt tot uw getrainde modellen.

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u geleerd hoe u een installatie kopie classificatie model maakt en traint met behulp van de Custom Vision-website. Vervolgens krijgt u meer informatie over het iteratieve proces van het verbeteren van uw model.

> [!div class="nextstepaction"]
> [Een model testen en opnieuw trainen](test-your-model.md)

