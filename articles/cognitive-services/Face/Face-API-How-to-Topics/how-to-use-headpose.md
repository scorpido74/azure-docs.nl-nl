---
title: Het kenmerk HeadPose gebruiken
titleSuffix: Azure Cognitive Services
description: Meer informatie over het gebruik van het kenmerk HeadPose om automatisch de gezichts rechthoek te draaien of Head-gebaren te detecteren in een video-feed.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 05/29/2019
ms.author: pafarley
ms.openlocfilehash: 534846044770d66ec5171ad4f61de921d2d5d194
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76169790"
---
# <a name="use-the-headpose-attribute"></a>Het kenmerk HeadPose gebruiken

In deze hand leiding ziet u hoe u het kenmerk HeadPose van een gedetecteerd gezicht kunt gebruiken om enkele belang rijke scenario's mogelijk te maken.

## <a name="rotate-the-face-rectangle"></a>De gezichts rechthoek draaien

De rechthoek met het gezicht, geretourneerd met elk gedetecteerd gezicht, markeert de locatie en grootte van het gezicht in de afbeelding. De rechthoek wordt standaard altijd uitgelijnd met de afbeelding (de zijden zijn verticaal en horizon taal); Dit kan inefficiënt zijn voor gehoekte gezichten met framing. In situaties waar u op een programmatische manier wilt bijsnijden in een afbeelding, is het beter om de rechthoek te draaien om bij te snijden.

De [Cognitive Services Face WPF](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF) -voor beeld-app gebruikt het kenmerk HeadPose om de gevonden gezichts rechthoeken te roteren.

### <a name="explore-the-sample-code"></a>De voorbeeldcode verkennen

U kunt de gezichts rechthoek via een programma draaien met het kenmerk HeadPose. Als u dit kenmerk opgeeft tijdens het detecteren van gezichten (Zie [gezichten detecteren](HowtoDetectFacesinImage.md)), kunt u het later alsnog een query uitvoeren. Met de volgende methode van de WPF-app [Cognitive Services gezicht](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF) wordt een lijst met **DetectedFace** -objecten gebruikt en wordt een lijst met **[gezichts](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/app-samples/Cognitive-Services-Face-WPF/Sample-WPF/Controls/Face.cs)** objecten geretourneerd. **Hier volgt** een aangepaste klasse waarin gezichts gegevens worden opgeslagen, met inbegrip van de bijgewerkte rechthoek coördinaten. Nieuwe waarden worden berekend voor **boven**, **links**, **breedte**en **hoogte**en met een nieuw veld **FaceAngle** wordt de draai hoek opgegeven.

```csharp
/// <summary>
/// Calculate the rendering face rectangle
/// </summary>
/// <param name="faces">Detected face from service</param>
/// <param name="maxSize">Image rendering size</param>
/// <param name="imageInfo">Image width and height</param>
/// <returns>Face structure for rendering</returns>
public static IEnumerable<Face> CalculateFaceRectangleForRendering(IList<DetectedFace> faces, int maxSize, Tuple<int, int> imageInfo)
{
    var imageWidth = imageInfo.Item1;
    var imageHeight = imageInfo.Item2;
    var ratio = (float)imageWidth / imageHeight;
    int uiWidth = 0;
    int uiHeight = 0;
    if (ratio > 1.0)
    {
        uiWidth = maxSize;
        uiHeight = (int)(maxSize / ratio);
    }
    else
    {
        uiHeight = maxSize;
        uiWidth = (int)(ratio * uiHeight);
    }

    var uiXOffset = (maxSize - uiWidth) / 2;
    var uiYOffset = (maxSize - uiHeight) / 2;
    var scale = (float)uiWidth / imageWidth;

    foreach (var face in faces)
    {
        var left = (int)(face.FaceRectangle.Left * scale + uiXOffset);
        var top = (int)(face.FaceRectangle.Top * scale + uiYOffset);

        // Angle of face rectangles, default value is 0 (not rotated).
        double faceAngle = 0;

        // If head pose attributes have been obtained, re-calculate the left & top (X & Y) positions.
        if (face.FaceAttributes?.HeadPose != null)
        {
            // Head pose's roll value acts directly as the face angle.
            faceAngle = face.FaceAttributes.HeadPose.Roll;
            var angleToPi = Math.Abs((faceAngle / 180) * Math.PI);

            // _____       | / \ |
            // |____|  =>  |/   /|
            //             | \ / |
            // Re-calculate the face rectangle's left & top (X & Y) positions.
            var newLeft = face.FaceRectangle.Left +
                face.FaceRectangle.Width / 2 -
                (face.FaceRectangle.Width * Math.Sin(angleToPi) + face.FaceRectangle.Height * Math.Cos(angleToPi)) / 2;

            var newTop = face.FaceRectangle.Top +
                face.FaceRectangle.Height / 2 -
                (face.FaceRectangle.Height * Math.Sin(angleToPi) + face.FaceRectangle.Width * Math.Cos(angleToPi)) / 2;

            left = (int)(newLeft * scale + uiXOffset);
            top = (int)(newTop * scale + uiYOffset);
        }

        yield return new Face()
        {
            FaceId = face.FaceId?.ToString(),
            Left = left,
            Top = top,
            OriginalLeft = (int)(face.FaceRectangle.Left * scale + uiXOffset),
            OriginalTop = (int)(face.FaceRectangle.Top * scale + uiYOffset),
            Height = (int)(face.FaceRectangle.Height * scale),
            Width = (int)(face.FaceRectangle.Width * scale),
            FaceAngle = faceAngle,
        };
    }
}
```

### <a name="display-the-updated-rectangle"></a>De bijgewerkte rechthoek weer geven

Hier kunt u de geretourneerde **gezichts** objecten in uw weer gave gebruiken. In de volgende regels van [FaceDetectionPage. xaml](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/app-samples/Cognitive-Services-Face-WPF/Sample-WPF/Controls/FaceDetectionPage.xaml) ziet u hoe de nieuwe rechthoek wordt weer gegeven op basis van deze gegevens:

```xaml
 <DataTemplate>
    <Rectangle Width="{Binding Width}" Height="{Binding Height}" Stroke="#FF26B8F4" StrokeThickness="1">
        <Rectangle.LayoutTransform>
            <RotateTransform Angle="{Binding FaceAngle}"/>
        </Rectangle.LayoutTransform>
    </Rectangle>
</DataTemplate>
```

## <a name="detect-head-gestures"></a>Head-gebaren detecteren

U kunt Head-gebaren, zoals Nodding en HeadPose, detecteren door wijzigingen in realtime bij te houden. U kunt deze functie als een aangepaste detectorshim gebruiken.

Detectie op basis van de aanwezigheid is de taak om te bepalen of een onderwerp een echte persoon is en geen afbeeldings-of video weergave. Een functie voor het aanwijzen van de printerkop kan worden gebruikt als een manier om de levens duur te controleren, met name in plaats van een afbeeldings representatie van een persoon.

> [!CAUTION]
> Als u hoofd bewegingen in realtime wilt detecteren, moet u de Face-API op een hoog tempo aanroepen (meer dan één keer per seconde). Dit is niet mogelijk als u een gratis-laag abonnement hebt. Als u een abonnement op een betaalde laag hebt, moet u ervoor zorgen dat u de kosten hebt berekend voor het opsporen van snelle API-aanroepen voor de detectie van hoofd bewegingen.

Bekijk het voor beeld van [Face HeadPose](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceAPIHeadPoseSample) op github voor een werk voorbeeld van de detectie van hoofd bewegingen.

## <a name="next-steps"></a>Volgende stappen

Zie de [Cognitive Services gezicht WPF](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF) -app op github voor een werkend voor beeld van geroteerde gezichts rechthoeken. Of Zie de voor [beeld-app face HeadPose](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples) , die het kenmerk HeadPose in realtime bijhoudt om hoofd bewegingen te detecteren.