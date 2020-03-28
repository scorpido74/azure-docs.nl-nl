---
title: 'Zelfstudie: Gegevens van gezichten in een afbeelding detecteren en weergeven met behulp van de .NET SDK'
titleSuffix: Azure Cognitive Services
description: In deze zelfstudie maakt u een Windows-app die de Face-service gebruikt om gezichten in een afbeelding te detecteren en te framen.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: tutorial
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: a5cf3c59c94134e1d0751c1467cd324a95c366eb
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "78898806"
---
# <a name="tutorial-create-a-windows-presentation-framework-wpf-app-to-display-face-data-in-an-image"></a>Zelfstudie: Een WPF-app (Windows Presentation Framework) maken om gezichtsgegevens in een afbeelding weer te geven

In deze zelfstudie leert u hoe u de Azure Face-service via de SDK .NET-client gebruiken om gezichten in een afbeelding te detecteren en vervolgens die gegevens in de gebruikersinterface weer te geven. U maakt een WPF-toepassing die gezichten detecteert, een kader rond elk gezicht tekent en een beschrijving van het gezicht in de statusbalk weergeeft. 

In deze handleiding ontdekt u hoe u:

> [!div class="checklist"]
> - Een WPF-toepassing maken
> - De Face-clientbibliotheek installeren
> - De clientbibliotheek gebruiken om gezichten in een afbeelding te detecteren
> - Een kader rond elk gedetecteerd gezicht tekenen
> - Een beschrijving van het gemarkeerde gezicht op de statusbalk weergeven

![Schermafbeelding met gedetecteerde gezichten omlijst door rechthoeken](../Images/getting-started-cs-detected.png)

De volledige voorbeeldcode is beschikbaar in de opslagplaats [Cognitive Face CSharp sample](https://github.com/Azure-Samples/Cognitive-Face-CSharp-sample) (Cognitive Face CSharp-voorbeeld) op GitHub.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint. 


## <a name="prerequisites"></a>Vereisten

- Een Face-abonnementssleutel. U kunt een abonnementssleutel voor een gratis proefversie downloaden van [Cognitive Services proberen](https://azure.microsoft.com/try/cognitive-services/?api=face-api). Of volg de instructies in [Een Cognitive Services-account maken](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) om je te abonneren op de Face-service en je sleutel te krijgen. Maak vervolgens [omgevingsvariabelen](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) voor de tekenreeks sleutel- `FACE_SUBSCRIPTION_KEY` en `FACE_ENDPOINT`serviceeindpunt, benoemd en , respectievelijk.
- Elke editie van [Visual Studio 2015 of 2017](https://www.visualstudio.com/downloads/).

## <a name="create-the-visual-studio-project"></a>Het Visual Studio-project maken

Volg deze stappen voor het maken van een nieuw WPF-toepassingsproject.

1. Open het dialoogvenster Nieuw project in Visual Studio. Vouw **Geïnstalleerd** en vervolgens **Visual C#** uit en selecteer **WPF App (.NET Framework)**.
1. Noem de toepassing **FaceTutorial** en klik vervolgens op **OK**.
1. Download de vereiste NuGet-pakketten. Klik met de rechtermuisknop op uw project in Solution Explorer en selecteer **NuGet-pakketten beheren**. Zoek en installeer vervolgens het volgende pakket:
    - [Microsoft.Azure.CognitiveServices.Vision.Face 2.5.0-preview.1](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.5.0-preview.1)

## <a name="add-the-initial-code"></a>De initiële code toevoegen

In deze sectie voegt u het basisframework van de app toe zonder de gezichtsbepalende kenmerken.

### <a name="create-the-ui"></a>Gebruikersinterface maken

Open *MainWindow.xaml* en vervang de&mdash;inhoud door de volgende code met deze code maakt het ui-venster. De `FacePhoto_MouseMove` `BrowseButton_Click` methoden en methoden zijn gebeurtenishandlers die u later zult definiëren.

[!code-xaml[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml?name=snippet_xaml)]

### <a name="create-the-main-class"></a>Hoofdklasse maken

Open *MainWindow.xaml.cs* en voeg de naamruimten van de clientbibliotheek te, samen met de overige noodzakelijke naamruimten. 

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_using)]

Voeg vervolgens de volgende code in de klasse **MainWindow** in. Met deze code wordt een **FaceClient-exemplaar** gemaakt met de abonnementssleutel en het eindpunt.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_mainwindow_fields)]

Voeg vervolgens de **MainWindow-constructor** toe. Het controleert uw URL-tekenreeks voor eindpunten en koppelt deze vervolgens aan het clientobject.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_mainwindow_constructor)]

Voeg tot slot de methoden **BrowseButton_Click** en **FacePhoto_MouseMove** aan de klasse toe. Deze methoden komen overeen met de gebeurtenishandlers die zijn opgegeven in *MainWindow.xaml*. Met de methode **BrowseButton_Click** wordt het dialoogvenster **OpenFileDialog** gemaakt, waarmee de gebruiker een JPG-afbeelding kan selecteren. Daarna wordt de afbeelding in het hoofdvenster weergegeven. U voegt de resterende code voor **BrowseButton_Click** en **FacePhoto_MouseMove** later toe. Kijk ook naar de verwijzing `faceList`, een lijst met **DetectedFace**-objecten. Deze verwijzing is waar uw app de werkelijke gezichtsgegevens opslaat en belt.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_browsebuttonclick_start)]

<!-- [!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_browsebuttonclick_end)] -->

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_mousemove_start)]

<!-- [!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_mousemove_end)] -->

### <a name="try-the-app"></a>De app uitproberen

Druk op **Start** in het menu om uw app te testen. Als het app-venster wordt geopend, klikt u in de linkerbenedenhoek op **Bladeren**. Het dialoogvenster **Bestand openen** verschijnt. Selecteer een afbeelding in uw bestandssysteem en controleer of deze in het venster wordt weergegeven. Sluit vervolgens de app en ga door naar de volgende stap.

![Schermafbeelding met ongewijzigde afbeelding van gezichten](../Images/getting-started-cs-ui.png)

## <a name="upload-image-and-detect-faces"></a>Afbeelding uploaden en gezichten detecteren

Uw app detecteert gezichten door de methode **FaceClient.Face.DetectWithStreamAsync** aan te roepen. Deze verpakt de [Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)-REST API voor het uploaden van een lokale afbeelding.

Voeg de volgende methode in de klasse **MainWindow** in, onder de methode **FacePhoto_MouseMove**. Met deze methode wordt een lijst met gezichtskenmerken gedefinieerd om het ingediende afbeeldingsbestand op te halen en te lezen in een **stream**. Vervolgens worden beide objecten doorgegeven aan de methodeaanroep **DetectWithStreamAsync**.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_uploaddetect)]

## <a name="draw-rectangles-around-faces"></a>Rechthoeken rond gezichten tekenen

Vervolgens voegt u de code toe om een rechthoek rond elk gedetecteerd gezicht in de afbeelding te tekenen. Voeg in de klasse **MainWindow** de volgende code toe aan het eind van de methode **BrowseButton_Click**, na de regel `FacePhoto.Source = bitmapSource`. Deze code vult een lijst met gedetecteerde gezichten van de aanroep naar **UploadAndDetectFaces**. Vervolgens wordt een rechthoek rond elk gezicht getekend en de aangepaste afbeelding weergegeven in het hoofdvenster.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_browsebuttonclick_mid)]

## <a name="describe-the-faces"></a>De gezichten beschrijven

Voeg de volgende methode toe aan de klasse **MainWindow**, onder de methode **UploadAndDetectFaces**. Met deze methode worden de opgehaalde gezichtskenmerken omgezet in een tekenreeks die het gezicht beschrijft.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_facedesc)]

## <a name="display-the-face-description"></a>De beschrijving van het gezicht weergeven

Voeg de volgende code toe aan de methode **FacePhoto_MouseMove**. Deze gebeurtenis-handler geeft de tekenreeks met de beschrijving van het gezicht in `faceDescriptionStatusBar` weer als de cursor boven een gedetecteerd vierkant rond het gezicht wordt geplaatst.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_mousemove_mid)]

## <a name="run-the-app"></a>De app uitvoeren

Voer de toepassing uit en blader naar een afbeelding met een gezicht. Wacht enkele seconden, zodat de Face-service kan reageren. U zou nu een rode rechthoek rond de gezichten in de afbeelding moeten zien. Als u de muisaanwijzer boven een gezichtsrechthoek beweegt, moet de beschrijving van dat gezicht op de statusbalk worden weergegeven.

![Schermafbeelding met gedetecteerde gezichten omlijst door rechthoeken](../Images/getting-started-cs-detected.png)


## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het basisproces geleerd voor het gebruik van de .NET-SDK van de Face-service en een toepassing gemaakt om gezichten in een afbeelding te detecteren en omlijsten. Meer informatie over de details van gezichtsdetectie.

> [!div class="nextstepaction"]
> [Gezichten in afbeeldingen detecteren](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)
