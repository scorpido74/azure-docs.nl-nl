---
title: Het kenmerk HeadPose gebruiken
titleSuffix: Azure Cognitive Services
description: Leer hoe u het kenmerk HeadPose gebruikt om automatisch de rechthoek van het gezicht te draaien of hoofdbewegingen in een videofeed te detecteren.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 05/29/2019
ms.author: pafarley
ms.openlocfilehash: 534846044770d66ec5171ad4f61de921d2d5d194
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169790"
---
# <a name="use-the-headpose-attribute"></a>Het kenmerk HeadPose gebruiken

In deze handleiding ziet u hoe u het kenmerk HeadPose van een gedetecteerd gezicht gebruiken om enkele belangrijke scenario's in te schakelen.

## <a name="rotate-the-face-rectangle"></a>De gezichtsrechthoek roteren

De rechthoek van het gezicht, geretourneerd met elk gedetecteerd gezicht, markeert de locatie en grootte van het gezicht in de afbeelding. Standaard is de rechthoek altijd uitgelijnd met de afbeelding (de zijkanten zijn verticaal en horizontaal); dit kan inefficiënt zijn voor het inlijsten van schuine gezichten. In situaties waarin u gezichten in een afbeelding programmatisch wilt bijsnijden, u de rechthoek beter roteren om bij te snijden.

De [voorbeeldapp Cognitive Services Face WPF](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF) gebruikt het kenmerk HeadPose om de gedetecteerde gezichtsrechthoeken te roteren.

### <a name="explore-the-sample-code"></a>De voorbeeldcode verkennen

U de gezichtsrechthoek programmatisch roteren met het kenmerk HeadPose. Als u dit kenmerk opgeeft bij het detecteren van gezichten (zie [Gezichten detecteren),](HowtoDetectFacesinImage.md)u het later opvragen. Met de volgende methode uit de [App Cognitive Services Face WPF](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF) wordt een lijst met **Gedetecteerde Face-objecten** gemaakt en wordt een lijst met **[gezichtsobjecten](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/app-samples/Cognitive-Services-Face-WPF/Sample-WPF/Controls/Face.cs)** geretourneerd. **Face** is hier een aangepaste klasse die gezichtsgegevens opslaat, inclusief de bijgewerkte rechthoekcoördinaten. Nieuwe waarden worden berekend voor **boven,** **links,** **breedte**en **hoogte**en een nieuw veld **FaceAngle** geeft de rotatie aan.

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

### <a name="display-the-updated-rectangle"></a>De bijgewerkte rechthoek weergeven

Vanaf hier u de geretourneerde **Gezichtsobjecten** in uw beeldscherm gebruiken. De volgende regels van [FaceDetectionPage.xaml](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/app-samples/Cognitive-Services-Face-WPF/Sample-WPF/Controls/FaceDetectionPage.xaml) laten zien hoe de nieuwe rechthoek wordt weergegeven uit deze gegevens:

```xaml
 <DataTemplate>
    <Rectangle Width="{Binding Width}" Height="{Binding Height}" Stroke="#FF26B8F4" StrokeThickness="1">
        <Rectangle.LayoutTransform>
            <RotateTransform Angle="{Binding FaceAngle}"/>
        </Rectangle.LayoutTransform>
    </Rectangle>
</DataTemplate>
```

## <a name="detect-head-gestures"></a>Hoofdgebaren detecteren

U hoofdgebaren zoals knikken en hoofdschudden door headpose-veranderingen in realtime bij te houden detecteren. U deze functie gebruiken als een aangepaste liveness detector.

Liveness detectie is de taak om te bepalen dat een onderwerp is een echte persoon en niet een beeld of video vertegenwoordiging. Een hoofd gebaar detector kan dienen als een manier om te helpen controleren levendigheid, vooral in tegenstelling tot een afbeelding vertegenwoordiging van een persoon.

> [!CAUTION]
> Als u hoofdgebaren in realtime wilt detecteren, moet u de Face API in een hoog tempo (meer dan één keer per seconde) aanroepen. Als je een gratis abonnement (f0) hebt, is dit niet mogelijk. Als u een abonnement met een betaalde laag hebt, moet u ervoor zorgen dat u de kosten hebt berekend voor het maken van snelle API-oproepen voor detectie van hoofdgebaren.

Zie het [Voorbeeld van Face HeadPose](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceAPIHeadPoseSample) op GitHub voor een werkend voorbeeld van hoofdgebarendetectie.

## <a name="next-steps"></a>Volgende stappen

Zie de [Cognitive Services Face WPF-app](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF) op GitHub voor een werkend voorbeeld van gedraaide gezichtsrechthoeken. Of zie de [Face HeadPose Sample-app,](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples) die het kenmerk HeadPose in realtime bijhoudt om hoofdbewegingen te detecteren.