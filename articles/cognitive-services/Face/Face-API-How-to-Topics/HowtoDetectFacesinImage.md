---
title: Gezichten detecteren in een installatie kopie-Face-API
titleSuffix: Azure Cognitive Services
description: In deze hand leiding wordt gedemonstreerd hoe u gezichts detectie kunt gebruiken om kenmerken te extra heren, zoals geslacht, leeftijd of pose van een bepaalde afbeelding.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: sbowles
ms.openlocfilehash: 9b66231d995ffb6980ce36852115c571cd102681
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73744275"
---
# <a name="get-face-detection-data"></a>Detectie gegevens van het gezicht ophalen

In deze hand leiding wordt gedemonstreerd hoe u gezichts detectie kunt gebruiken om kenmerken te extra heren, zoals geslacht, leeftijd of pose van een bepaalde afbeelding. De code fragmenten in deze hand leiding worden geschreven in C# met behulp van de Azure Cognitive Services Face-API-client bibliotheek. Dezelfde functionaliteit is beschikbaar via de [rest API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

In deze hand leiding wordt uitgelegd hoe u:

- Haal de locaties en dimensies van gezichten op in een afbeelding.
- Haal de locaties van verschillende gezichts bezienswaardigheden, zoals leerlingen, neus en mond, op in een afbeelding.
- Het geslacht, de leeftijd, de Emotion en andere kenmerken van een gedetecteerd gezicht schatten.

## <a name="setup"></a>Instellen

In deze hand leiding wordt ervan uitgegaan dat u al een [FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet) -object met de naam `faceClient`hebt gemaakt met een face-abonnements sleutel en eind punt-URL. Hier kunt u de functie voor gezichts herkenning gebruiken door het aanroepen van [DetectWithUrlAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync?view=azure-dotnet), dat wordt gebruikt in deze hand leiding of [DetectWithStreamAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync?view=azure-dotnet). Volg een van de Quick starts voor instructies over het instellen van deze functie.

Deze hand leiding is gericht op de details van de detectie aanroep, zoals de argumenten die u kunt door geven en wat u met de geretourneerde gegevens kunt doen. U wordt aangeraden alleen een query uit te voeren voor de functies die u nodig hebt. Elke bewerking heeft meer tijd nodig om te volt ooien.

## <a name="get-basic-face-data"></a>Basis gegevens over het gezicht ophalen

Als u gezichten wilt zoeken en de locatie ervan wilt ophalen in een installatie kopie, roept u de methode aan met de para meter _returnFaceId_ ingesteld op **True**. Dit is de standaardinstelling.

```csharp
IList<DetectedFace> faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, false, null);
```

U kunt de geretourneerde [DetectedFace](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet) -objecten opvragen voor hun unieke id's en een rechthoek die de pixel coördinaten van het gezicht geeft.

```csharp
foreach (var face in faces)
{
    string id = face.FaceId.ToString();
    FaceRectangle rect = face.FaceRectangle;
}
```

Zie [FaceRectangle](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.facerectangle?view=azure-dotnet)voor informatie over het parseren van de locatie en afmetingen van het gezicht. Normaal gesp roken bevat deze rechthoek de ogen, Eyebrows, neus en mond. De bovenkant van Head, oren en Chin is niet noodzakelijkerwijs opgenomen. Als u de gezichts rechthoek wilt gebruiken om een volledige kop te bijsnijden of een mid-shot portret te krijgen, mogelijk voor een foto-ID-type afbeelding, kunt u de rechthoek in elke richting uitvouwen.

## <a name="get-face-landmarks"></a>Gezichts bezienswaardigheden ophalen

[Gezichts bezienswaardigheden](../concepts/face-detection.md#face-landmarks) zijn een verzameling gemakkelijk te vinden punten op een gezicht, zoals de pupilsen of de tip van de neus. Als u gegevens voor het gezichts punt wilt ophalen, stelt u de para meter _returnFaceLandmarks_ in op **True**.

```csharp
IList<DetectedFace> faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, true, null);
```

De volgende code laat zien hoe u de locaties van de neus en de pupillen kunt ophalen:

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

U kunt ook gegevens over gezichts bezienswaardigheden gebruiken om de richting van het gezicht nauw keurig te berekenen. U kunt bijvoorbeeld de draai hoek van het gezicht definiëren als een vector van het midden van de mond naar het midden van de ogen. Met de volgende code wordt deze vector berekend:

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

Wanneer u de richting van het gezicht kent, kunt u het rechthoekige gezichts kader draaien om het op de juiste manier af te stemmen. Als u gezichten wilt bijsnijden in een afbeelding, kunt u de afbeelding op een programmatische manier draaien, zodat de gezichten altijd rechtop worden weer gegeven.

## <a name="get-face-attributes"></a>Gezichts kenmerken ophalen

Naast gezichts rechthoeken en bezienswaardigheden, kunnen met de gezichts detectie-API verschillende conceptuele kenmerken van een gezicht worden geanalyseerd. Zie de sectie met de conceptuele [kenmerken](../concepts/face-detection.md#attributes) voor een volledige lijst.

Als u face-kenmerken wilt analyseren, stelt u de para meter _returnFaceAttributes_ in op een lijst met [FaceAttributeType Enum](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet) -waarden.

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

Vervolgens krijgt u verwijzingen naar de geretourneerde gegevens en voert u meer bewerkingen uit op basis van uw behoeften.

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

Zie de conceptuele hand leiding voor [gezichts detectie en kenmerken](../concepts/face-detection.md) voor meer informatie over elk van de kenmerken.

## <a name="next-steps"></a>Volgende stappen

In deze hand leiding hebt u geleerd hoe u de verschillende functies van gezicht detectie kunt gebruiken. Vervolgens integreert u deze functies in uw app door een diep gaande zelf studie te volgen.

- [Zelf studie: een WPF-app maken voor het weer geven van gezichts gegevens in een installatie kopie](../Tutorials/FaceAPIinCSharpTutorial.md)
- [Zelf studie: een Android-app maken voor het detecteren en plaatsen van gezichten in een installatie kopie](../Tutorials/FaceAPIinJavaForAndroidTutorial.md)

## <a name="related-topics"></a>Verwante onderwerpen

- [Referentie documentatie (REST)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
- [Referentie documentatie (.NET SDK)](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/faceapi?view=azure-dotnet)