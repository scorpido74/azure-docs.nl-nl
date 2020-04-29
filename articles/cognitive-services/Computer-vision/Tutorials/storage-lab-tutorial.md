---
title: 'Zelf studie: meta gegevens voor Azure-installatie kopieën genereren'
titleSuffix: Azure Cognitive Services
description: In deze zelf studie leert u hoe u de Azure Computer Vision-service integreert in een web-app voor het genereren van meta gegevens voor installatie kopieën.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: tutorial
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: 43172cb08bb1e31c8cff891628ca6ef85cb8c864
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81404414"
---
# <a name="tutorial-use-computer-vision-to-generate-image-metadata-in-azure-storage"></a>Zelf studie: Computer Vision gebruiken voor het genereren van meta gegevens voor afbeeldingen in Azure Storage

In deze zelf studie leert u hoe u de Azure Computer Vision-service integreert in een web-app om meta gegevens te genereren voor geüploade installatie kopieën. Dit is nuttig voor de [dam-scenario's (Digital Asset Management)](../Home.md#computer-vision-for-digital-asset-management) , bijvoorbeeld als een bedrijf snel beschrijvende bijschriften of Doorzoek bare tref woorden wil genereren voor alle installatie kopieën.

De volledige app-handleiding vindt u in het [Azure Storage- en Cognitive Services-lab](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md) op GitHub. In deze zelfstudie wordt alleen oefening 5 uit het lab behandeld. U kunt de volledige toepassing maken door elke stap te volgen, maar als u alleen wilt weten hoe u Computer Vision integreert in een bestaande web-app, leest u hier.

In deze handleiding ontdekt u hoe u:

> [!div class="checklist"]
> * Een Computer Vision-resource maken in Azure
> * Afbeeldingsanalyses uitvoeren op Azure Storage-afbeeldingen
> * Metagegevens koppelen aan Azure Storage-afbeeldingen
> * Afbeeldingsmetagegevens controleren met Azure Storage Explorer

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint. 

## <a name="prerequisites"></a>Vereisten

- [Visual Studio 2017 Community Edition](https://www.visualstudio.com/products/visual-studio-community-vs.aspx) of hoger, met de workloads 'ASP.NET and web development' en 'Azure development' geïnstalleerd.
- Een Azure Storage-account met een BLOB-container die is ingesteld voor installatie kopie opslag (Volg [de oefeningen 1 van het Azure Storage Lab](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise1) als u hulp nodig hebt bij deze stap).
- Het Azure Storage Explorer-hulpprogramma (volg [oefening 2 van het Azure Storage-lab](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise2) als u hulp nodig hebt met deze stap).
- Een ASP.NET-webtoepassing met toegang tot Azure Storage (volg [oefening 3 van het Azure Storage-lab](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise3) om snel een dergelijke app te maken).

## <a name="create-a-computer-vision-resource"></a>Een Computer Vision-resource maken

U moet een Computer Vision resource maken voor uw Azure-account. met deze resource wordt uw toegang tot de Computer Vision-service van Azure beheerd. 

1. Volg de instructies in [een Azure Cognitive Services-resource maken](../../cognitive-services-apis-create-account.md) om een computer vision resource te maken.

1. Ga vervolgens naar het menu voor de resource groep en klik op het Computer Vision-API abonnement dat u zojuist hebt gemaakt. Kopieer de URL onder **Eindpunt** naar een plaats waar u de URL eenvoudig en snel weer kunt ophalen. Klik vervolgens op **Toegangssleutels weergeven**.

    ![Azure Portal pagina met de koppeling naar de eind punt-URL en toegangs sleutels](../Images/copy-vision-endpoint.png)
    
    [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]


1. In het volgende venster kopieert u de waarde van **sleutel 1** naar het klembord.

    ![Het dialoog venster sleutels beheren met de knop kopiëren](../Images/copy-vision-key.png)

## <a name="add-computer-vision-credentials"></a>Computer Vision-referenties toevoegen

Vervolgens voegt u de vereiste referenties toe aan uw app zodat deze toegang kan krijgen tot Computer Vision-resources

Open de ASP.NET-webtoepassing in Visual Studio en ga naar het bestand **Web.config** in de hoofdmap van het project. Voeg de volgende instructies toe aan `<appSettings>` de sectie van het bestand, `VISION_KEY` waarbij u vervangt door de sleutel die u in de vorige `VISION_ENDPOINT` stap hebt gekopieerd, en met de URL die u in de stap hebt opgeslagen.

```xml
<add key="SubscriptionKey" value="VISION_KEY" />
<add key="VisionEndpoint" value="VISION_ENDPOINT" />
```

Klik vervolgens in Solution Explorer met de rechtermuisknop op het project en gebruik de opdracht **NuGet-pakketten beheren** om het pakket **Microsoft.Azure.CognitiveServices.Vision.ComputerVision** te installeren. Dit pakket bevat de typen die nodig zijn voor het aanroepen van de Computer Vision-API.

## <a name="add-metadata-generation-code"></a>Code voor het genereren van metagegevens toevoegen

Vervolgens voegt u de code toe die de Computer Vision-service gebruikt om meta gegevens voor installatie kopieën te maken. Deze stappen zijn van toepassing op de ASP.NET-app in het lab, maar u kunt ze ook aanpassen aan uw eigen app. Het is op dit punt belangrijk dat u over een ASP.NET-webtoepassing beschikt waarmee u afbeeldingen kunt uploaden naar een Azure Storage-container, waarmee u er afbeeldingen uit kunt lezen en waarmee u deze kunt weergeven. Als u niet zeker weet wat deze stap is, kunt u het beste [de oefening 3 van de Azure Storage Lab](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise3)volgen. 

1. Open het bestand *HomeController.cs* in de map **Controllers** van het project en voeg de volgende `using` instructies toe aan de bovenzijde van het bestand:

    ```csharp
    using Microsoft.Azure.CognitiveServices.Vision.ComputerVision;
    using Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models;
    ```

1. Ga vervolgens naar de methode **Uploaden**. Met deze methode worden afbeeldingen geconverteerd en naar de blob-opslag geüpload. Voeg de volgende code in achter het blok dat begint met `// Generate a thumbnail` (of na afloop van het aanmaakproces voor de afbeeldingsblob). Met deze code wordt de blob met de afbeelding (`photo`) gezocht en wordt Computer Vision gebruikt voor het genereren van een beschrijving voor de betreffende afbeelding. De Computer Vision-API maakt ook een lijst trefwoorden die van toepassing zijn op de afbeelding. De gegenereerde beschrijving en de trefwoorden worden opgeslagen in de metagegevens van de blob zodat ze later kunnen worden opgehaald.

    ```csharp
    // Submit the image to Azure's Computer Vision API
    ComputerVisionClient vision = new ComputerVisionClient(
        new ApiKeyServiceClientCredentials(ConfigurationManager.AppSettings["SubscriptionKey"]),
        new System.Net.Http.DelegatingHandler[] { });
    vision.Endpoint = ConfigurationManager.AppSettings["VisionEndpoint"];

    VisualFeatureTypes[] features = new VisualFeatureTypes[] { VisualFeatureTypes.Description };
    var result = await vision.AnalyzeImageAsync(photo.Uri.ToString(), features);

    // Record the image description and tags in blob metadata
    photo.Metadata.Add("Caption", result.Description.Captions[0].Text);

    for (int i = 0; i < result.Description.Tags.Count; i++)
    {
        string key = String.Format("Tag{0}", i);
        photo.Metadata.Add(key, result.Description.Tags[i]);
    }

    await photo.SetMetadataAsync();
    ```

1. Ga vervolgens naar de **index** -methode in hetzelfde bestand. Met deze methode worden de opgeslagen afbeeldings-blobs in de doel-BLOB-container (als **IListBlobItem** -instanties) opgesomd en door gegeven aan de weer gave van de toepassing. Vervang het blok `foreach` in de methode door de volgende code. Deze code roept **CloudBlockBlob.FetchAttributes** aan om de metagegevens die aan elke blob zijn gekoppeld op te halen. De door de computer gegenereerde beschrijving (`caption`) wordt opgehaald uit de metagegevens en toegevoegd aan het object **BlobInfo**; dit wordt vervolgens doorgegeven aan de weergave.
    
    ```csharp
    foreach (IListBlobItem item in container.ListBlobs())
    {
        var blob = item as CloudBlockBlob;
    
        if (blob != null)
        {
            blob.FetchAttributes(); // Get blob metadata
            var caption = blob.Metadata.ContainsKey("Caption") ? blob.Metadata["Caption"] : blob.Name;
    
            blobs.Add(new BlobInfo()
            {
                ImageUri = blob.Uri.ToString(),
                ThumbnailUri = blob.Uri.ToString().Replace("/photos/", "/thumbnails/"),
                Caption = caption
            });
        }
    }
    ```

## <a name="test-the-app"></a>De app testen

Sla de wijzigingen in Visual Studio op en druk op **Ctrl+F5** om de toepassing in uw browser te starten. Gebruik de app om enkele afbeeldingen te uploaden, ofwel vanuit de map 'photos' in de labresources ofwel vanuit uw eigen map. Wanneer u de muisaanwijzer op een van de afbeeldingen in de weergave houdt, wordt er een knopinfovenster weergegeven. Hierin ziet u het door de computer gegenereerde bijschrift van de afbeelding.

![Het door de computer gegenereerde bijschrift](../Images/thumbnail-with-tooltip.png)

Als u alle gekoppelde metagegevens wilt bekijken, gebruikt u Azure Storage Explorer om de opslagcontainer te gebruiken die u voor afbeeldingen gebruikt. Klik met de rechtermuisknop op een van de blobs in de container en selecteer **Eigenschappen**. In het dialoogvenster ziet u een lijst met sleutel-waardeparen. De door de computer gegenereerde afbeeldingsbeschrijving wordt opgeslagen in het item Bijschrift en de zoektrefwoorden worden opgeslagen in Tag0, Tag1, enzovoort. Wanneer u klaar bent, klikt u op **Annuleren** om het dialoogvenster te sluiten.

![Dialoog venster Eigenschappen van afbeelding, met de opgegeven labels voor meta gegevens](../Images/blob-metadata.png)

## <a name="clean-up-resources"></a>Resources opschonen

Als u aan de web-app wilt blijven werken, ziet u het gedeelte [Volgende stappen](#next-steps). Als u deze toepassing niet wilt blijven gebruiken, verwijdert u alle app-specifieke resources. Als u resources wilt verwijderen, kunt u de resource groep met uw Azure Storage-abonnement en Computer Vision resource verwijderen. Hiermee worden het opslagaccount, de ernaar geüploade blobs en de App Service-resource die nodig is om verbinding te maken met de ASP.NET-web-app verwijderd. 

Als u de resource groep wilt verwijderen, opent u het tabblad **resource groepen** in de portal, navigeert u naar de resource groep die u hebt gebruikt voor dit project en klikt u op **resource groep verwijderen** boven aan de weer gave. U wordt gevraagd de naam van de resource groep te typen om te bevestigen dat u deze wilt verwijderen omdat een resource groep niet meer kan worden hersteld.

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie stelt u de Computer Vision-service van Azure in een bestaande web-app in voor het automatisch genereren van bijschriften en tref woorden voor BLOB-installatie kopieën die worden geüpload. Zie nu oefening 6 uit het Azure Storage-lab om te ontdekken hoe u zoekfunctionaliteit toevoegt aan uw web-app. Hierbij wordt gebruikgemaakt van de zoektrefwoorden die de Computer Vision-service genereert.

> [!div class="nextstepaction"]
> [Zoekfunctionaliteit toevoegen aan uw app](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise6)
