---
title: 'Snelstartgids: beschik bare ruimten zoeken-Azure Digital Apparaatdubbels | Microsoft Docs'
description: In deze snelstart voert u twee .NET Core-voorbeeldtoepassingen uit om telemetriegegevens over gesimuleerde beweging en koolstofdioxide te verzenden naar een ruimte in Azure Digital Twins. Het doel is om beschikbare ruimten met frisse lucht van beheer-API's te vinden na berekende verwerking in de cloud.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.devlang: csharp
ms.topic: quickstart
ms.custom: mvc seodec18
ms.date: 01/10/2020
ms.openlocfilehash: 6c9c5df27f4a361e534bac2fe21b2c470f8d0186
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/11/2020
ms.locfileid: "75895602"
---
# <a name="quickstart-find-available-rooms-by-using-azure-digital-twins"></a>Snelstart: beschikbare ruimten zoeken met behulp van Azure Digital Twins

Met de Azure Digital Twins-service kunt u opnieuw een digitale kopie van uw fysieke omgeving maken. U kunt vervolgens een melding ontvangen van gebeurtenissen in uw omgeving en uw reacties hierop aanpassen.

In deze snelstart wordt [een paar .NET-voorbeelden](https://github.com/Azure-Samples/digital-twins-samples-csharp) gebruikt om een denkbeeldig kantoorgebouw te digitaliseren. Hiermee kunt u zien hoe u beschikbare ruimten in dat gebouw kunt vinden. Met Digital Twins kunt u vele sensoren koppelen aan uw omgeving. U kunt met behulp van een gesimuleerde sensor voor koolstofdioxide ook vaststellen of de luchtkwaliteit van de beschikbare ruimte optimaal is. Door een van de voorbeeldtoepassingen worden willekeurige sensorgegevens gegenereerd, zodat u dit scenario beter kunt visualiseren.

In de volgende video vindt u een overzicht van de Quick Start-instellingen:

>[!VIDEO https://www.youtube.com/embed/1izK266tbMI]

## <a name="prerequisites"></a>Vereisten

1. Als u geen Azure-account hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

1. De twee consoletoepassingen die u uitvoert in deze snelstart zijn geschreven in C#. Installeer [.NET Core SDK-versie 2.1.403 of hoger](https://www.microsoft.com/net/download) op uw ontwikkelcomputer. Als u de .NET Core-SDK hebt geïnstalleerd, controleert u de huidige versie van C# op uw ontwikkelmachine. Voer `dotnet --version` in een opdrachtprompt uit.

1. Download het [C#-voorbeeldproject](https://github.com/Azure-Samples/digital-twins-samples-csharp/archive/master.zip). Pak het archief digital-twins-samples-csharp-master.zip uit.

## <a name="create-a-digital-twins-instance"></a>Een Digital Twins-exemplaar maken

Maak een nieuw exemplaar van Digital Twins in de [portal](https://portal.azure.com) door de stappen in deze sectie te volgen.

[!INCLUDE [create-digital-twins-portal](../../includes/digital-twins-create-portal.md)]

## <a name="set-permissions-for-your-app"></a>Machtigingen instellen voor uw app

In deze sectie registreert u uw voorbeeldtoepassing in Azure Active Directory (Azure AD), zodat deze toegang heeft tot uw Digital Twins-exemplaar. Als u al over een app-registratie van Azure AD beschikt, kunt u deze opnieuw gebruiken voor uw voorbeeld. Controleer of deze is geconfigureerd zoals beschreven in deze sectie.

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-permissions.md)]

## <a name="build-application"></a>Toepassing bouwen

Volg deze stappen om de bezettingstoepassing te bouwen.

1. Open een opdrachtprompt. Ga naar de map waar uw `digital-twins-samples-csharp-master.zip`-bestanden zijn uitgepakt.
1. Voer `cd occupancy-quickstart/src` uit.
1. Voer `dotnet restore` uit.
1. Bewerk [appSettings.json](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/appSettings.json) om de volgende variabelen bij te werken:
    - **ClientId**: voer de toepassings-id van uw Azure AD-app-registratie in, die u in de voorgaande sectie hebt genoteerd.
    - **Tenant**: voer de map-id van uw Azure AD-tenant in, die u ook in de vorige sectie hebt genoteerd.
    - **BaseUrl**: de URL van de beheer-API van uw Digital Twins-exemplaar wordt weergegeven in de indeling `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/api/v1.0/`. Vervang de tijdelijke aanduidingen in deze URL door de waarden voor uw exemplaar uit de vorige sectie.

    Sla het bijgewerkte bestand op.

## <a name="provision-graph"></a>Grafiek inrichten

In deze stap wordt uw ruimtelijke Digital Twins-grafiek ingericht met:

- Diverse ruimten.
- Eén apparaat.
- Twee sensoren.
- Een aangepaste functie.
- Een roltoewijzing.

De ruimtelijke grafiek wordt ingericht met behulp van het bestand [provisionSample.yaml](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/provisionSample.yaml).

1. Voer `dotnet run ProvisionSample` uit.

    >[!NOTE]
    >Het programma voor apparaataanmelding van de Azure CLI wordt gebruikt om de gebruiker bij Azure AD te verifiëren. De gebruiker moet een bepaalde code invoeren om zich te verifiëren met behulp van [de Microsoft-aanmeldingspagina](https://microsoft.com/devicelogin). Nadat de code is ingevoerd, volgt u de stappen om te verifiëren. De gebruiker moet zich verifiëren als het hulpprogramma wordt uitgevoerd.

    >[!TIP]
    > Als u deze stap uitvoert, controleert u of de variabelen correct zijn gekopieerd als u dit foutbericht ziet: `EXIT: Unexpected error: The input is not a valid Base-64 string ...`

1. De inrichtingsstap kan enkele minuten duren. Er wordt ook een IoT Hub binnen uw Digital Twins-exemplaar ingericht. Het inrichten blijft doorlopen totdat de status van de IoT Hub gelijk is aan `Running`.

    [![de voor beeld-status = actief inrichten](media/quickstart-view-occupancy-dotnet/azure-digital-twins-quickstart-provision-sample.png)](media/quickstart-view-occupancy-dotnet/azure-digital-twins-quickstart-provision-sample.png#lightbox)

1. Aan het einde van de uitvoering, kopieert u de `ConnectionString` van het apparaat voor gebruik in de voorbeeldapparaatsimulator. Kopieer alleen de tekenreeks die wordt beschreven in deze afbeelding.

    [de connection string ![kopiëren](media/quickstart-view-occupancy-dotnet/azure-digital-twins-quickstart-connection-string.png)](media/quickstart-view-occupancy-dotnet/azure-digital-twins-quickstart-connection-string.png#lightbox)

    >[!TIP]
    > U kunt de ruimtelijke grafiek bekijken en aanpassen met de [Graph Viewer voor Azure Digital Twins](https://github.com/Azure/azure-digital-twins-graph-viewer).

Houd het console venster geopend voor gebruik later opnieuw.

## <a name="send-sensor-data"></a>Sensorgegevens verzenden

Bouw en voer de app voor sensor Simulator uit door de volgende stappen uit te voeren.

1. Open een nieuwe opdrachtprompt. Ga naar het project dat u hebt gedownload in de map `digital-twins-samples-csharp-master`.
1. Voer `cd device-connectivity` uit.
1. Voer `dotnet restore` uit.
1. Bewerk [appsettings.json](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/device-connectivity/appsettings.json) om **DeviceConnectionString** bij te werken met de vorige `ConnectionString`. Sla het bijgewerkte bestand op.
1. Voer `dotnet run` uit om te beginnen met het verzenden van gegevens. Het wordt verzonden naar Azure Digital Apparaatdubbels, zoals wordt weer gegeven in de volgende afbeelding.

     [Connectiviteit van ![apparaat](media/quickstart-view-occupancy-dotnet/azure-digital-twins-quickstart-device-connectivity.png)](media/quickstart-view-occupancy-dotnet/azure-digital-twins-quickstart-device-connectivity.png#lightbox)

1. Voer deze simulator uit, zodat u de resultaten naast de actie van de volgende stap kunt bekijken. In dit venster kunt u zien dat de gesimuleerde sensorgegevens naar Digital Twins zijn verzonden. In de volgende stap wordt in realtime naar beschikbare ruimte met frisse lucht gezocht.

    >[!TIP]
    > Als u deze stap uitvoert, controleert u of `DeviceConnectionString` correct is gekopieerd als u dit foutbericht ziet: `EXIT: Unexpected error: The input is not a valid Base-64 string ...`

## <a name="find-available-spaces-with-fresh-air"></a>Beschikbare ruimten met frisse lucht zoeken

Het sensorvoorbeeld simuleert willekeurige gegevenswaarden voor twee sensoren. Deze zijn beweging en koolstofdioxide. In het voorbeeld worden beschikbare ruimten met frisse lucht gedefinieerd door geen aanwezigheid in de ruimte. Ze worden ook gedefinieerd door een koolstofdioxidegehalte van minder dan 1000 ppm. Als niet aan de voorwaarde wordt voldaan, is de ruimte niet beschikbaar of is de luchtkwaliteit slecht.

1. Open de opdracht prompt die u hebt gebruikt om de inrichtings stap eerder uit te voeren.
1. Voer `dotnet run GetAvailableAndFreshSpaces` uit.
1. Bekijk deze opdrachtprompt en de opdrachtprompt voor sensorgegevens naast elkaar.

    Met de opdracht prompt voor sensor gegevens worden gesimuleerde bewegings-en kooldioxyde-gegevens naar digitale-Apparaatdubbels elke vijf seconden verzonden. Met de andere opdracht prompt wordt de grafiek in realtime gelezen om beschik bare ruimten met verse lucht te ontdekken op basis van wille keurige gesimuleerde gegevens. Een van deze voorwaarden wordt bijna in realtime weergegeven op basis van de sensorgegevens die de laatste keer zijn verzonden:
   - `Room is available and air is fresh`
   - `Room is not available or air quality is poor`

     [![beschik bare ruimten met nieuwe lucht ophalen](media/quickstart-view-occupancy-dotnet/azure-digital-twins-quickstart-get-available.png)](media/quickstart-view-occupancy-dotnet/azure-digital-twins-quickstart-get-available.png#lightbox)

Open [Visual Studio code](https://code.visualstudio.com/Download) met het code werkruimte project in `digital-twins-samples-csharp`als u wilt weten wat er is gebeurd in deze Snelstartgids en welke api's werden genoemd. Gebruik de volgende opdracht:

```cmd
<path>\occupancy-quickstart\src>code ..\..\digital-twins-samples.code-workspace
```

In de zelfstudies wordt diep op de code ingegaan. U komt te weten hoe u configuratiegegevens kunt wijzigen en welke API's worden aangeroepen. Ga voor meer informatie over beheer-API's naar de pagina van Digital Twins Swagger:

```URL
https://YOUR_INSTANCE_NAME.YOUR_LOCATION.azuresmartspaces.net/management/swagger
```

| Name | Vervangen door |
| --- | --- |
| YOUR_INSTANCE_NAME | De naam van uw Digital Twins-exemplaar |
| YOUR_LOCATION | De server regio waar uw exemplaar wordt gehost |

Of ga naar [Digital Twins Swagger](https://docs.westcentralus.azuresmartspaces.net/management/swagger).

## <a name="clean-up-resources"></a>Resources opschonen

In de zelfstudies wordt ook in detail ingegaan op:

- Het bouwen van een toepassing voor facility managers om de productiviteit van de aanwezigen te verhogen.
- Het gebouw efficiënter leiden.

Als u doorgaat naar de zelfstudies, verwijder dan niet de resources die u in deze snelstart hebt gemaakt. Als u niet doorgaat, verwijdert u alle resources die u in deze snelstart hebt gemaakt.

1. Verwijder de map die is gemaakt bij het downloaden van de opslagplaats met voorbeelden.
1. Selecteer **Alle resources** in het menu aan de linkerkant van de [Azure-portal](https://portal.azure.com). Selecteer vervolgens uw Digital Twins-resource. Selecteer boven aan het deelvenster **Alle resources** de optie **Verwijderen**.

    > [!TIP]
    > Als u eerder problemen hebt ondervonden bij het verwijderen van uw digitale Apparaatdubbels-exemplaar, is er een service-update met de oplossing geïmplementeerd. Probeer opnieuw of u het exemplaar kunt verwijderen.

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u een eenvoudige scenario en voorbeeld toepassingen gebruikt om te laten zien hoe digitale Apparaatdubbels kan worden gebruikt om te zoeken naar ruimten met goede werk omstandigheden. Lees deze zelf studie voor uitgebreide analyse van dit scenario:

>[!div class="nextstepaction"]
>[Zelfstudie: Azure Digital Twins implementeren en een ruimtelijke grafiek configureren](tutorial-facilities-setup.md)
