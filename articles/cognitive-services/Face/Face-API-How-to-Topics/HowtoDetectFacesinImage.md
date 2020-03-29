---
title: Gezichten in een afbeelding detecteren - Gezicht
titleSuffix: Azure Cognitive Services
description: Deze handleiding laat zien hoe gezichtsherkenning kan worden gebruikt om kenmerken zoals geslacht, leeftijd of pose uit een bepaalde afbeelding te extraheren.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: sbowles
ms.openlocfilehash: 7070cb3bcd1b519828a750cf4ba6caf7ecb34bbb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "76169876"
---
# <a name="get-face-detection-data"></a>Gegevens voor gezichtsherkenning opvragen

Deze handleiding laat zien hoe gezichtsherkenning kan worden gebruikt om kenmerken zoals geslacht, leeftijd of pose uit een bepaalde afbeelding te extraheren. De codefragmenten in deze handleiding worden in C# geschreven met behulp van de Azure Cognitive Services Face-clientbibliotheek. Dezelfde functionaliteit is beschikbaar via de [REST API.](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)

Deze gids laat u zien hoe u:

- De locaties en afmetingen van gezichten in een afbeelding weergeven.
- Krijg de locaties van verschillende gezichtsoriëntatiepunten, zoals pupillen, neus en mond, in een afbeelding.
- Denk dat het geslacht, leeftijd, emotie en andere attributen van een gedetecteerd gezicht.

## <a name="setup"></a>Instellen

In deze handleiding wordt ervan uitgegaan dat u `faceClient`al een [FaceClient-object](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet) hebt gemaakt met de naam Face-abonnementssleutel en de URL van het eindpunt. Vanaf hier u de gezichtsherkenningsfunctie gebruiken door [detectwithurlasync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync?view=azure-dotnet)te bellen, die in deze handleiding wordt gebruikt, of [DetectWithStreamAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync?view=azure-dotnet). Volg een van de snelstarts voor instructies over het instellen van deze functie.

Deze handleiding richt zich op de specifieke kenmerken van de detecteeroproep, zoals welke argumenten u doorgeven en wat u doen met de geretourneerde gegevens. We raden u aan alleen de functies te zoeken die u nodig hebt. Elke bewerking neemt extra tijd in beslag.

## <a name="get-basic-face-data"></a>Basisgegevens voor het gezicht opvragen

Als u gezichten wilt vinden en hun locaties in een afbeelding wilt plaatsen, roept u de methode aan met de parameter _returnFaceId_ die is ingesteld op **true.** Dit is de standaardinstelling.

```csharp
IList<DetectedFace> faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, false, null);
```

U de geretourneerde [Gedetecteerde Face-objecten](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet) opvragen voor hun unieke id's en een rechthoek die de pixelcoördinaten van het gezicht geeft.

```csharp
foreach (var face in faces)
{
    string id = face.FaceId.ToString();
    FaceRectangle rect = face.FaceRectangle;
}
```

Zie [FaceRectangle](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.facerectangle?view=azure-dotnet)voor informatie over het ontsteken van de locatie en afmetingen van het gezicht. Meestal bevat deze rechthoek de ogen, wenkbrauwen, neus en mond. De bovenkant van hoofd, oren, en kin zijn niet noodzakelijk inbegrepen. Als u de rechthoek wilt gebruiken om een compleet hoofd bij te snijden of een portret in het middenbeeld te krijgen, u de rechthoek in elke richting uitbreiden voor een afbeelding van het foto-ID-type.

## <a name="get-face-landmarks"></a>Krijg gezicht oriëntatiepunten

[Gezicht oriëntatiepunten](../concepts/face-detection.md#face-landmarks) zijn een set van gemakkelijk te vinden punten op een gezicht, zoals de leerlingen of het puntje van de neus. Als u gegevens met betrekking tot oriëntatiepunten voor het gezicht wilt krijgen, stelt u de parameter _returnFaceLandmarks_ in **op true.**

```csharp
IList<DetectedFace> faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, true, null);
```

De volgende code laat zien hoe u de locaties van de neus en pupillen ophalen:

```csharp
foreach (var face in faces)
{
    var landmarks = face.FaceLandmarks;

    double noseX = landmarks.NoseTip.X;
    double noseY = landmarks.NoseTip.Y;

    double leftPupilX = landmarks.PupilLeft.X;
    double leftPupilY = landmarks.PupilLeft.Y;

    double rightPupilX = landmarks.PupilRight.X;
    double rightPupilY = landmarks.PupilRight.Y;
}
```

U ook gegevens over gezichtsherkenning gebruiken om de richting van het gezicht nauwkeurig te berekenen. U bijvoorbeeld de rotatie van het gezicht definiëren als een vector van het midden van de mond naar het midden van de ogen. De volgende code berekent deze vector:

```csharp
var upperLipBottom = landmarks.UpperLipBottom;
var underLipTop = landmarks.UnderLipTop;

var centerOfMouth = new Point(
    (upperLipBottom.X + underLipTop.X) / 2,
    (upperLipBottom.Y + underLipTop.Y) / 2);

var eyeLeftInner = landmarks.EyeLeftInner;
var eyeRightInner = landmarks.EyeRightInner;

var centerOfTwoEyes = new Point(
    (eyeLeftInner.X + eyeRightInner.X) / 2,
    (eyeLeftInner.Y + eyeRightInner.Y) / 2);

Vector faceDirection = new Vector(
    centerOfTwoEyes.X - centerOfMouth.X,
    centerOfTwoEyes.Y - centerOfMouth.Y);
```

Wanneer u de richting van het gezicht kent, u het rechthoekige gezichtskader roteren om het beter uit te lijnen. Als u gezichten in een afbeelding wilt bijsnijden, u de afbeelding programmatisch roteren, zodat de vlakken altijd rechtop worden weergegeven.

## <a name="get-face-attributes"></a>Gezichtskenmerken ophalen

Naast gezichtsrechthoeken en oriëntatiepunten kan de gezichtsherkennings-API verschillende conceptuele kenmerken van een gezicht analyseren. Zie de conceptuele sectie [Gezichtskenmerken](../concepts/face-detection.md#attributes) voor een volledige lijst.

Als u gezichtskenmerken wilt analyseren, stelt u de parameter _returnFaceAttributes_ in op een lijst met [FaceAttributeType-enumwaarden.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet)

```csharp
var requiredFaceAttributes = new FaceAttributeType[] {
    FaceAttributeType.Age,
    FaceAttributeType.Gender,
    FaceAttributeType.Smile,
    FaceAttributeType.FacialHair,
    FaceAttributeType.HeadPose,
    FaceAttributeType.Glasses,
    FaceAttributeType.Emotion
};
var faces = await faceClient.DetectWithUrlAsync(imageUrl, true, false, requiredFaceAttributes);
```

Ontvang vervolgens verwijzingen naar de geretourneerde gegevens en voer meer bewerkingen uit op basis van uw behoeften.

```csharp
foreach (var face in faces)
{
    var attributes = face.FaceAttributes;
    var age = attributes.Age;
    var gender = attributes.Gender;
    var smile = attributes.Smile;
    var facialHair = attributes.FacialHair;
    var headPose = attributes.HeadPose;
    var glasses = attributes.Glasses;
    var emotion = attributes.Emotion;
}
```

Zie de conceptuele handleiding [Voor gezichtsherkenning en kenmerken](../concepts/face-detection.md) voor meer informatie over elk van de kenmerken.

## <a name="next-steps"></a>Volgende stappen

In deze gids heb je geleerd hoe je de verschillende functionaliteiten van gezichtsherkenning gebruiken. Integreer deze functies vervolgens in uw app door een uitgebreide zelfstudie te volgen.

- [Zelfstudie: een WPF-app maken om gegevens van gezichten in een afbeelding weer te geven](../Tutorials/FaceAPIinCSharpTutorial.md)
- [Zelfstudie: een Android-app maken om gezichten in een afbeelding te herkennen en te omlijsten](../Tutorials/FaceAPIinJavaForAndroidTutorial.md)

## <a name="related-topics"></a>Verwante onderwerpen

- [Referentiedocumentatie (REST)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
- [Referentiedocumentatie (.NET SDK)](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/faceapi?view=azure-dotnet)