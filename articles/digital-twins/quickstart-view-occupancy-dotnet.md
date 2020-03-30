---
title: 'Snelstart: Beschikbare kamers zoeken - Azure Digital Twins | Microsoft Documenten'
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
ms.openlocfilehash: de8611e53339d38a03836bc9272ad42ff88a59b8
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79371423"
---
# <a name="quickstart-find-available-rooms-by-using-azure-digital-twins"></a>Snelstart: beschikbare ruimten zoeken met behulp van Azure Digital Twins

[!INCLUDE [digital-twins-preview-limit-alert](../../includes/digital-twins-preview-limit-alert.md)]

Met de Azure Digital Twins-service kunt u opnieuw een digitale kopie van uw fysieke omgeving maken. U kunt vervolgens een melding ontvangen van gebeurtenissen in uw omgeving en uw reacties hierop aanpassen.

In deze snelstart wordt [een paar .NET-voorbeelden](https://github.com/Azure-Samples/digital-twins-samples-csharp) gebruikt om een denkbeeldig kantoorgebouw te digitaliseren. Hiermee kunt u zien hoe u beschikbare ruimten in dat gebouw kunt vinden. Met Digital Twins kunt u vele sensoren koppelen aan uw omgeving. U kunt met behulp van een gesimuleerde sensor voor koolstofdioxide ook vaststellen of de luchtkwaliteit van de beschikbare ruimte optimaal is. Door een van de voorbeeldtoepassingen worden willekeurige sensorgegevens gegenereerd, zodat u dit scenario beter kunt visualiseren.

In de volgende video wordt de instelling snel aande/

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
    >Het programma voor apparaataanmelding van de Azure CLI wordt gebruikt om de gebruiker bij Azure AD te verifiëren. De gebruiker moet een bepaalde code invoeren om zich te verifiëren met behulp van [de Microsoft-aanmeldingspagina](https://microsoft.com/devicelogin). Volg de stappen die u wilt verifiëren nadat de code is ingevoerd. De gebruiker moet zich verifiëren als het hulpprogramma wordt uitgevoerd.

    >[!TIP]
    > Als u deze stap uitvoert, controleert u of de variabelen correct zijn gekopieerd als u dit foutbericht ziet: `EXIT: Unexpected error: The input is not a valid Base-64 string ...`

1. De inrichtingsstap kan enkele minuten duren. Er wordt ook een IoT Hub binnen uw Digital Twins-exemplaar ingericht. Het inrichten blijft doorlopen totdat de status van de IoT Hub gelijk is aan `Running`.

    [![Het voorbeeld inrichten - Status=Actief](media/quickstart-view-occupancy-dotnet/azure-digital-twins-quickstart-provision-sample.png)](media/quickstart-view-occupancy-dotnet/azure-digital-twins-quickstart-provision-sample.png#lightbox)

1. Aan het einde van de uitvoering, kopieert u de `ConnectionString` van het apparaat voor gebruik in de voorbeeldapparaatsimulator. Kopieer alleen de tekenreeks die wordt beschreven in deze afbeelding.

    [![De verbindingsreeks kopiëren](media/quickstart-view-occupancy-dotnet/azure-digital-twins-quickstart-connection-string.png)](media/quickstart-view-occupancy-dotnet/azure-digital-twins-quickstart-connection-string.png#lightbox)

    >[!TIP]
    > U kunt de ruimtelijke grafiek bekijken en aanpassen met de [Graph Viewer voor Azure Digital Twins](https://github.com/Azure/azure-digital-twins-graph-viewer).

Houd het consolevenster later weer open voor gebruik.

## <a name="send-sensor-data"></a>Sensorgegevens verzenden

Bouw en voer de toepassing van het sensorsimulatorapparaat uit door deze stappen te volgen.

1. Open een nieuwe opdrachtprompt. Ga naar het project dat `digital-twins-samples-csharp-master` u hebt gedownload in de map.
1. Voer `cd device-connectivity` uit.
1. Voer `dotnet restore` uit.
1. Bewerk [appsettings.json](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/device-connectivity/appsettings.json) om **DeviceConnectionString** bij te werken met de vorige `ConnectionString`. Sla het bijgewerkte bestand op.
1. Voer `dotnet run` uit om te beginnen met het verzenden van gegevens. Het wordt verzonden naar Azure Digital Twins, zoals weergegeven in de volgende afbeelding.

     [![Apparaatconnectiviteit](media/quickstart-view-occupancy-dotnet/azure-digital-twins-quickstart-device-connectivity.png)](media/quickstart-view-occupancy-dotnet/azure-digital-twins-quickstart-device-connectivity.png#lightbox)

1. Voer deze simulator uit, zodat u de resultaten naast de actie van de volgende stap kunt bekijken. In dit venster kunt u zien dat de gesimuleerde sensorgegevens naar Digital Twins zijn verzonden. In de volgende stap wordt in realtime naar beschikbare ruimte met frisse lucht gezocht.

    >[!TIP]
    > Als u deze stap uitvoert, controleert u of `DeviceConnectionString` correct is gekopieerd als u dit foutbericht ziet: `EXIT: Unexpected error: The input is not a valid Base-64 string ...`

## <a name="find-available-spaces-with-fresh-air"></a>Beschikbare ruimten met frisse lucht zoeken

Het sensorvoorbeeld simuleert willekeurige gegevenswaarden voor twee sensoren. Deze zijn beweging en koolstofdioxide. In het voorbeeld worden beschikbare ruimten met frisse lucht gedefinieerd door geen aanwezigheid in de ruimte. Ze worden ook gedefinieerd door een koolstofdioxidegehalte van minder dan 1000 ppm. Als niet aan de voorwaarde wordt voldaan, is de ruimte niet beschikbaar of is de luchtkwaliteit slecht.

1. Open de opdrachtprompt die u hebt gebruikt om de inprovisioningstap eerder uit te voeren.
1. Voer `dotnet run GetAvailableAndFreshSpaces` uit.
1. Bekijk deze opdrachtprompt en de opdrachtprompt voor sensorgegevens naast elkaar.

    De opdrachtprompt voor sensorgegevens stuurt elke vijf seconden gesimuleerde bewegings- en kooldioxidegegevens naar Digital Twins. De andere opdrachtprompt leest de grafiek in real-time om beschikbare kamers met frisse lucht te vinden op basis van willekeurige gesimuleerde gegevens. Een van deze voorwaarden wordt bijna in realtime weergegeven op basis van de sensorgegevens die de laatste keer zijn verzonden:
   - `Room is available and air is fresh`
   - `Room is not available or air quality is poor`

     [![Beschikbare ruimten met frisse lucht ophalen](media/quickstart-view-occupancy-dotnet/azure-digital-twins-quickstart-get-available.png)](media/quickstart-view-occupancy-dotnet/azure-digital-twins-quickstart-get-available.png#lightbox)

Als u wilt begrijpen wat er in deze quickstart is gebeurd en hoe `digital-twins-samples-csharp`API's werden genoemd, opent u Visual Studio [Code](https://code.visualstudio.com/Download) met het codewerkruimteproject in . Gebruik de volgende opdracht:

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
| YOUR_LOCATION | Het servergebied waar uw instantie wordt gehost |

Of ga naar [Digital Twins Swagger](https://docs.westcentralus.azuresmartspaces.net/management/swagger).

## <a name="clean-up-resources"></a>Resources opschonen

In de zelfstudies wordt ook in detail ingegaan op:

- Het bouwen van een toepassing voor facility managers om de productiviteit van de aanwezigen te verhogen.
- Het gebouw efficiënter leiden.

Als u doorgaat naar de zelfstudies, verwijder dan niet de resources die u in deze snelstart hebt gemaakt. Als u niet doorgaat, verwijdert u alle resources die u in deze snelstart hebt gemaakt.

1. Verwijder de map die is gemaakt bij het downloaden van de opslagplaats met voorbeelden.
1. Selecteer **Alle resources** in het menu aan de linkerkant van de [Azure-portal](https://portal.azure.com). Selecteer vervolgens uw Digital Twins-resource. Selecteer boven aan het deelvenster **Alle resources** de optie **Verwijderen**.

    > [!TIP]
    > Als u eerder problemen ondervond bij het verwijderen van uw Digital Twins-exemplaar, is er een service-update met de oplossing uitgerold. Probeer opnieuw of u het exemplaar kunt verwijderen.

## <a name="next-steps"></a>Volgende stappen

Deze quickstart gebruikte een eenvoudig scenario en voorbeeldtoepassingen om te laten zien hoe Digital Twins kan worden gebruikt om kamers te vinden met goede werkomstandigheden. Lees deze zelfstudie voor een diepgaande analyse van dit scenario:

>[!div class="nextstepaction"]
>[Zelfstudie: Azure Digital Twins implementeren en een ruimtelijke grafiek configureren](tutorial-facilities-setup.md)
