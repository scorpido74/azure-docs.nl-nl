---
title: Fout visualiseren bij het gebruik van Azure Maps-tekening
description: In dit artikel vindt u informatie over het visualiseren van waarschuwingen en fouten die zijn geretourneerd door de API voor het converteren van ontwikkel aars.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 7e43995c4221127580333c3f013fcb209730f863
ms.sourcegitcommit: 6571e34e609785e82751f0b34f6237686470c1f3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/15/2020
ms.locfileid: "84791113"
---
# <a name="using-the-azure-maps-drawing-error-visualizer"></a>Fout visualiseren met behulp van de Azure Maps voor het tekenen van fouten

De tekening fout Visualer is een zelfstandige webtoepassing waarin de waarschuwingen voor het [teken pakket](drawing-conversion-error-codes.md) worden weer gegeven en de fouten die tijdens het conversie proces zijn gedetecteerd. De webtoepassing voor het visualiseren van fouten bestaat uit een statische pagina die u kunt gebruiken zonder verbinding met Internet te maken.  U kunt de fout Visualer gebruiken om fouten en waarschuwingen op te lossen in overeenstemming met de vereisten voor het [teken pakket](drawing-requirements.md). De [Azure Maps Conversion API](https://docs.microsoft.com/rest/api/maps/conversion) retourneert alleen een antwoord met een koppeling naar de error visualer alleen wanneer er een fout wordt gedetecteerd.

## <a name="prerequisites"></a>Vereisten

Voordat u de fout Visualer voor het tekenen van een tekening kunt downloaden, moet u het volgende doen:

1. [Een Azure Maps-account maken](quick-demo-map-app.md#create-an-account-with-azure-maps)
2. [Verkrijg een primaire abonnements sleutel](quick-demo-map-app.md#get-the-primary-key-for-your-account), ook wel de primaire sleutel of de abonnements sleutel genoemd.
3. [Een Creator-resource maken](how-to-manage-creator.md)

In deze zelf studie wordt gebruikgemaakt van de toepassing [postman](https://www.postman.com/) , maar u kunt een andere API-ontwikkel omgeving kiezen.

## <a name="download"></a>Downloaden

1. Upload uw tekening pakket naar de Azure Maps Creator-service om een `udid` voor het geüploade pakket te verkrijgen. Zie [een tekening pakket uploaden](tutorial-creator-indoor-maps.md#upload-a-drawing-package)voor stappen voor het uploaden van een pakket.

2. Nu het teken pakket is geüpload, wordt het `udid` geüploade pakket gebruikt voor het converteren van het pakket naar kaart gegevens. Zie [een tekening pakket converteren](tutorial-creator-indoor-maps.md#convert-a-drawing-package)voor stappen voor het converteren van een pakket.

    >[!NOTE]
    >Als het conversie proces is geslaagd, ontvangt u geen koppeling naar het hulp programma error Visualer.

3. Zoek op het tabblad antwoord **headers** in de toepassing postman naar de `diagnosticPackageLocation` eigenschap die wordt geretourneerd door de conversie-API. Het antwoord moet er als volgt uitzien zoals in de volgende JSON:

    ```json
    {
        "operationId": "77dc9262-d3b8-4e32-b65d-74d785b53504",
        "created": "2020-04-22T19:39:54.9518496+00:00",
        "status": "Failed",
        "resourceLocation": "https://atlas.microsoft.com/conversion/{conversionId}?api-version=1.0",
        "properties": {
            "diagnosticPackageLocation": "https://atlas.microsoft.com/mapData/ce61c3c1-faa8-75b7-349f-d863f6523748?api-version=1.0"
        }
    }
    ```

4. Down load de fout Visualer voor het tekening pakket door een `HTTP-GET` aanvraag op de URL te maken `diagnosticPackageLocation` .

## <a name="setup"></a>Instellen

In het gedownloade gezipte pakket van de `diagnosticPackageLocation` koppeling vindt u twee bestanden.

* _VisualizationTool.zip_: bevat de bron code, de media en de webpagina voor de fout visualer voor het tekenen van een tekening.
* _ConversionWarningsAndErrors.jsop_: bevat een lijst met waarschuwingen, fouten en aanvullende details die worden gebruikt door de tekening fout visualiseren.

Unzip de map _VisualizationTool.zip_ . Het bevat de volgende items:

* _assets_ map: bevat afbeeldingen en media bestanden
* _statische_ map: bron code
* _index.html_ -bestand: de webtoepassing.

Open het bestand _index.html_ met een van de volgende browsers, met het desbetreffende versie nummer. U kunt een andere versie gebruiken als de versie een even compatibel gedrag biedt als de vermelde versie.

* Micro soft Edge 80
* Safari 13
* Chrome 80
* Firefox 74

## <a name="using-the-drawing-error-visualizer-tool"></a>Het hulp programma voor het visualiseren van fouten weer geven

Nadat u het hulp programma voor het visualiseren van de teken fouten hebt gestart, wordt de upload pagina weer gegeven. De upload pagina bevat een slepen-en-neerzetten-vak. Het selectie vakje slepen & wordt ook functions als knop waarmee een Verkenner-dialoog venster wordt gestart.

:::image type="content" source="./media/drawing-errors-visualizer/start-page.png" alt-text="Fout bij het visualiseren van de app-start pagina van de tekening":::

De _ConversionWarningsAndErrors.jsin_ het bestand is geplaatst in de hoofdmap van de gedownloade map. Als u de _ConversionWarningsAndErrors.jsop_ wilt laden, kunt u slepen & het bestand neerzetten op het vak of klikken op het vak, het bestand in het dialoog venster bestanden Verkenner zoeken en het bestand vervolgens uploaden.

:::image type="content" source="./media/drawing-errors-visualizer/loading-data.gif" alt-text="Fout bij het visualiseren van fouten met de app-slepen en neerzetten om gegevens te laden":::

Zodra de _ConversionWarningsAndErrors.jsvoor_ het laden van bestanden wordt weer gegeven, ziet u een lijst met fouten en waarschuwingen in het tekening pakket. Elke fout of waarschuwing wordt opgegeven door de laag, het niveau en een gedetailleerd bericht. Als u gedetailleerde informatie over een fout of waarschuwing wilt weer geven, klikt u op de koppeling **Details** . Een sectie die kan worden tegengevoerd, wordt weer gegeven onder de lijst. U kunt nu naar elke fout navigeren voor meer informatie over het oplossen van de fout.

:::image type="content" source="./media/drawing-errors-visualizer/errors.png" alt-text="Fout bij het visualiseren van problemen met app-fouten en waarschuwingen":::

## <a name="next-steps"></a>Volgende stappen

Zodra het [teken pakket aan de vereisten voldoet](drawing-requirements.md), kunt u de [Azure Maps dataset-service](https://docs.microsoft.com/rest/api/maps/conversion) gebruiken om het teken pakket te converteren naar een gegevensset. Vervolgens kunt u de web module van de binnenste kaarten gebruiken om uw toepassing te ontwikkelen. Lees de volgende artikelen voor meer informatie:

> [!div class="nextstepaction"]
> [Fout codes voor teken conversie](drawing-conversion-error-codes.md)

> [!div class="nextstepaction"]
> [Maker voor kaarten in de binnenste](creator-indoor-maps.md)

> [!div class="nextstepaction"]
> [De module binnenste kaarten gebruiken](how-to-use-indoor-module.md)

> [!div class="nextstepaction"]
> [Dynamische stijl van de toewijzing van binnenste kaarten implementeren](indoor-map-dynamic-styling.md)