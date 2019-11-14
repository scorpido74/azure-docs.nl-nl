---
title: 'Zelf studie: een ruimtelijke grafiek implementeren-Azure Digital Apparaatdubbels | Microsoft Docs'
description: In deze zelfstudie leert u stapsgewijs hoe u uw exemplaar van Azure Digital Twins implementeert en uw ruimtelijke resources configureert.
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.custom: seodec18
ms.service: digital-twins
ms.topic: tutorial
ms.date: 11/12/2019
ms.openlocfilehash: e78a85ac590cc6c6f501774a2cdd64a4544676a1
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74039440"
---
# <a name="tutorial-deploy-azure-digital-twins-preview-and-configure-a-spatial-graph"></a>Zelf studie: Azure Digital Apparaatdubbels preview implementeren en een ruimtelijke grafiek configureren

U kunt de Azure Digital Apparaatdubbels preview-service gebruiken om mensen, plaatsen en apparaten samen te brengen in een samenhangend ruimtelijk systeem. Deze reeks zelfstudies laat zien hoe u Azure Digital Twins gebruikt voor het detecteren van ruimtebezetting met optimale omstandigheden qua temperatuur- en luchtkwaliteit. 

In deze zelfstudies leert u een .NET-consoletoepassing te maken om een scenario op te bouwen voor een kantoorgebouw. Het gebouw heeft meerdere verdiepingen en verschillende ruimten per verdieping. De ruimten bevatten apparaten waaraan bewegings-, temperatuur- en luchtkwaliteitssensoren zijn gekoppeld. 

U leert hoe u de fysieke gebieden en entiteiten in het gebouw kunt repliceren als digitale objecten met behulp van de Azure Digital Twins-service. U simuleert apparaatgebeurtenissen met een andere consoletoepassing. Vervolgens leert u hoe u de gebeurtenissen die afkomstig zijn van deze fysieke gebieden en entiteiten bijna in realtime kunt bewaken. 

Een office manager kan deze informatie gebruiken om werknemers in het gebouw te helpen bij het boeken van vergaderruimten met optimale omstandigheden. Een facilitair manager kan uw instellingen gebruiken voor het detecteren van trends in het gebruik van de ruimten en voor het bewaken van werkomstandigheden voor onderhoudswerkzaamheden.

In de eerste zelfstudie van deze reeks leert u het volgende:

> [!div class="checklist"]
> * Digital Twins implementeren.
> * Machtigingen verlenen aan uw app.
> * Een voorbeeld-app van Digital Twins wijzigen.
> * Het gebouw inrichten.

In deze zelfstudies worden dezelfde voorbeelden gebruikt en aangepast als in de [quickstart om beschikbare ruimten te vinden](quickstart-view-occupancy-dotnet.md), voor een meer gedetailleerde en uitgebreide uitleg van de concepten.

## <a name="prerequisites"></a>Vereisten

- Een Azure-abonnement. Als u nog geen Azure-account hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan.

- De .NET Core-SDK. De Azure Digital Twins-voorbeelden in deze zelfstudies zijn geschreven in C#. Installeer [.NET Core SDK-versie 2.1.403 of hoger](https://www.microsoft.com/net/download) op een ontwikkelcomputer om het voorbeeld te bouwen en uit te voeren. Controleer of de juiste versie op uw computer is geïnstalleerd door `dotnet --version` uit te voeren in een opdrachtvenster.

- [Visual Studio Code](https://code.visualstudio.com/) om de voorbeeldcode mee te verkennen. 

## <a name="deploy-digital-twins"></a>Azure Digital Twins implementeren

Maak een nieuw exemplaar van de Azure Digital Twins-service met behulp van de stappen in deze sectie. Per abonnement kan slechts één exemplaar worden gemaakt. Ga naar de volgende sectie als u al een actief exemplaar hebt. 

[!INCLUDE [create-digital-twins-portal](../../includes/digital-twins-create-portal.md)]

## <a name="grant-permissions-to-your-app"></a>Machtigingen verlenen aan uw app

Digital Twins maakt gebruik van [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) (Azure AD) voor het beheren van de [lees-/schrijftoegang](../active-directory/develop/v1-permissions-and-consent.md) tot de service. Elke toepassing die moet worden verbonden met uw exemplaar van Digital Twins, moet worden geregistreerd bij Azure AD. In de stappen in deze sectie wordt uitgelegd hoe u de voorbeeld-app registreert.

Als u al over een app-registratie beschikt, kunt u deze opnieuw gebruiken voor uw voorbeeld. Neem deze sectie echter wel door om te controleren of de registratie van uw app juist is geconfigureerd.

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-permissions.md)]

## <a name="configure-the-digital-twins-sample"></a>Het Digital Twins-voorbeeld configureren

In deze sectie ziet u hoe een Azure Digital Twins-toepassing communiceert met de [REST-API's van Digital Twins](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index). 

### <a name="download-the-sample"></a>Het voorbeeld downloaden

Als u de voorbeelden voor de [quickstart om beschikbare ruimten te vinden](quickstart-view-occupancy-dotnet.md) al hebt gedownload, kunt u deze stappen overslaan.

1. Download de [.NET-voorbeelden van Digital Twins](https://github.com/Azure-Samples/digital-twins-samples-csharp/archive/master.zip).
2. Pak de inhoud van de gecomprimeerde map uit op uw computer.

### <a name="explore-the-sample"></a>Het voorbeeld verkennen

Open het bestand **digital-twins-samples-csharp\digital-twins-samples.code-workspace** uit de uitgepakte voorbeeldmap in Visual Studio Code. Dit bestand bevat twee projecten:

* U kunt het inrichtingsvoorbeeld **occupancy-quickstart** gebruiken voor het configureren en inrichten van een [ruimtelijke informatiegrafiek](concepts-objectmodel-spatialgraph.md#digital-twins-object-models). Deze grafiek is de digitale kopie van uw fysieke ruimten en de resources in die ruimten. Hiervoor wordt een [objectmodel](concepts-objectmodel-spatialgraph.md#digital-twins-object-models) gebruikt waarin objecten voor een slim gebouw worden gedefinieerd. Ga voor een volledige lijst van objecten en REST-API's van Digital Twins naar [deze REST API-documentatie](https://docs.westcentralus.azuresmartspaces.net/management/swagger) of de URL van de Beheer API die is gemaakt voor uw [exemplaar](#deploy-digital-twins).

   Als u de voorbeeld-app wilt verkennen om te zien hoe deze communiceert met uw exemplaar van Digital Twins, kunt u beginnen met de map **src\actions**. De bestanden in deze map implementeren de opdrachten die u in deze zelfstudies gaat gebruiken:
    - Het bestand **provisionSample.cs** laat zien hoe u de ruimtelijke grafiek kunt inrichten.
    - Het bestand **getSpaces.cs** haalt informatie over de ingerichte ruimten op.
    - Het bestand **getAvailableAndFreshSpaces.cs** haalt de resultaten op van een aangepaste (door de gebruiker gedefinieerde) functie op.
    - Het bestand **createEndpoints.cs** maakt eindpunten voor interactie met andere services.

* Het simulatievoorbeeld **device-connectivity** simuleert sensorgegevens en verzendt deze naar de IoT-hub die is ingericht voor uw exemplaar van Digital Twins. U gebruikt dit voorbeeld in [de volgende zelfstudie nadat u de ruimtelijke grafiek hebt ingericht](tutorial-facilities-udf.md#simulate-sensor-data). De sensor- en apparaat-id's die u gebruikt voor het configureren van dit voorbeeld moeten gelijk zijn aan de id's die u gebruikt voor het inrichten van de grafiek.

### <a name="configure-the-provisioning-sample"></a>Het inrichtingsvoorbeeld configureren

1. Open een opdrachtvenster en ga naar het gedownloade voorbeeld. Voer de volgende opdracht uit:

    ```cmd/sh
    cd occupancy-quickstart/src
    ```

1. Herstel afhankelijkheden van het voorbeeldproject met deze opdracht:

    ```cmd/sh
    dotnet restore
    ```

1. Open in Visual Studio Code het bestand [appSettings.json](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/appSettings.json) van het project **occupancy-quickstart**. Werk de volgende waarden bij:
   * **ClientId**: voer de toepassings-id van uw Azure AD-app-registratie in. U hebt deze id genoteerd in de sectie voor het [instellen van app-machtigingen](#grant-permissions-to-your-app).
   * **Tenant**: voer de map-id van uw [Azure AD-tenant](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) in. U hebt deze id ook genoteerd in de sectie voor het [instellen van app-machtigingen](#grant-permissions-to-your-app).
   * **BaseUrl**: voer de URL van uw exemplaar van Digital Twins in. Hiertoe vervangt u de tijdelijke aanduidingen in deze URL door de waarden voor uw exemplaar: `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/api/v1.0/`. U kunt deze URL ook verkrijgen door de URL van de Beheer API uit [de implementatiesectie](#deploy-digital-twins) te wijzigen. Vervang **swagger/** door **api/v1.0/** .

1. Bekijk een lijst met functies van Digital Twins die u kunt verkennen met behulp van het voorbeeld. Voer de volgende opdracht uit:

    ```cmd/sh
    dotnet run
    ```

## <a name="understand-the-provisioning-process"></a>Inzicht in het inrichtingsproces

In deze sectie wordt beschreven hoe u met het voorbeeld een ruimtelijke grafiek van een gebouw inricht.

Ga in Visual Studio Code naar de map **occupancy-quickstart\src\actions** en open het bestand **provisionSample.cs**. Let op de volgende functie:

```csharp
public static async Task<IEnumerable<ProvisionResults.Space>> ProvisionSample(HttpClient httpClient, ILogger logger)
{
    IEnumerable<SpaceDescription> spaceCreateDescriptions;
    using (var r = new StreamReader("actions/provisionSample.yaml"))
    {
        spaceCreateDescriptions = await GetProvisionSampleTopology(r);
    }

    var results = await CreateSpaces(httpClient, logger, spaceCreateDescriptions, Guid.Empty);

    Console.WriteLine($"Completed Provisioning: {JsonConvert.SerializeObject(results, Formatting.Indented)}");

    return results;
}

```

Deze functie maakt gebruik van het bestand [provisionSample.yaml](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/provisionSample.yaml) in dezelfde map. Open dit bestand en bekijk de hiërarchie van een kantoorgebouw, bestaande uit: *locatie*, *verdieping*, *gebied* en *ruimten*. Elk van deze fysieke ruimten kan *devices* en *sensors* bevatten. Elk vermelding heeft een vooraf gedefinieerde `type`&mdash;, bijvoorbeeld Verdieping, Ruimte.

Het **yaml**-voorbeeldbestand bevat een ruimtelijke grafiek die gebruikmaakt van het Digital Twins-objectmodel `Default`. Dit model bevat algemene namen voor de meeste typen. Algemene namen zijn voldoende voor een gebouw. Voorbeelden hiervan zijn Temperatuur voor SensorDataType en Toewijzen voor SpaceBlobType. Een voorbeeld van een type ruimte is Ruimte met de subtypen Concentratieruimte, Vergaderruimte, enzovoort. 

Als u een ruimtelijke grafiek voor een ander type locatie wilt maken (zoals een fabriek) hebt u wellicht een ander objectmodel nodig. Als u wilt weten welke modellen beschikbaar zijn, voert u de opdracht `dotnet run GetOntologies` uit vanaf de opdrachtregel voor het inrichtingsvoorbeeld. 

Lees voor meer informatie over ruimtelijke grafieken en de objectmodellen [Inzicht in Digital Twins-objectmodellen en ruimtelijke informatiegrafiek](concepts-objectmodel-spatialgraph.md).

### <a name="modify-the-sample-spatial-graph"></a>Het voorbeeld van de ruimtelijke grafiek wijzigen

Het bestand **provisionSample.yaml** bevat de volgende knooppunten:

- **resources**: het knooppunt `resources` maakt een IoT Hub-resource om te communiceren met de apparaten in uw installatie. Een IoT-hub in het hoofdknooppunt van uw grafiek kan communiceren met alle apparaten en sensoren in uw grafiek.  

- **spaces**: in het Digital Twins-objectmodel worden de fysieke locaties vertegenwoordigd door `spaces`. Elke ruimte heeft een `Type`&mdash;, bijvoorbeeld een regio, locatie of klant&mdash;, en een gebruiksvriendelijke `Name`. Ruimten kunnen deel uitmaken van andere ruimten in een hiërarchische structuur. Het bestand provisionSample.yaml bevat een ruimtelijke grafiek van een denkbeeldig gebouw. Merk op dat ruimten van het type `Floor` logisch genest zijn in `Venue`, `Area` in een verdieping, en `Room`-knooppunten in een gebied. 

- **devices**: ruimten kunnen `devices` bevatten. Dat zijn fysieke of virtuele entiteiten waarmee een aantal sensoren wordt beheerd. Een apparaat kan bijvoorbeeld het telefoonnummer van een gebruiker zijn of een Raspberry Pi-sensorpod, een gateway, enzovoort. In het denkbeeldige gebouw uit het voorbeeld bevat de ruimte **Focus Room** bijvoorbeeld het apparaat **Raspberry Pi 3 A1**. Elk apparaatknooppunt wordt geïdentificeerd door een unieke `hardwareId`, die is vastgelegd in het voorbeeld. Als u dit voorbeeld wilt configureren in een productieomgeving, moet u deze waarden vervangen door de waarden van uw installatie.  

- **sensoren**: een apparaat kan meerdere `sensors` bevatten. Hiermee kunnen fysieke wijzigingen (bijvoorbeeld in temperatuur, beweging of accuniveau) worden gedetecteerd en vastgelegd. Elk sensorknooppunt wordt geïdentificeerd door een unieke `hardwareId`, die hier is vastgelegd. Voor een werkelijke toepassing moet u deze vervangen door de unieke id's van de sensoren in uw installatie. Het bestand provisionSample.yaml bevat twee sensoren: een bewegingssensor (*Motion*) en een CO2-sensor (*CarbonDioxide*). Voeg, onder de regels voor de CO2-sensor, de volgende regels toe om een temperatuursensor (*Temperature*) toe te voegen. Merk op dat deze regels in provisionSample.yaml zijn opgegeven als commentaarregels. U kunt er coderegels van maken door het teken `#` aan het begin van elke regel te verwijderen. 

    ```yaml
            - dataType: Temperature
              hardwareId: SAMPLE_SENSOR_TEMPERATURE
    ```
    > [!NOTE]
    > Zorg ervoor dat de sleutels `dataType` en `hardwareId` in overeenstemming zijn met de instructies boven dit fragment. Zorg er ook voor dat in uw editor spaties niet worden vervangen door tabs. 

Sla het bestand provisionSample.yaml op en sluit het. In de volgende zelfstudie gaat u meer gegevens aan dit bestand toevoegen en het voorbeeldgebouw inrichten in Azure Digital Twins.

> [!TIP]
> U kunt de ruimtelijke grafiek bekijken en aanpassen met de [Graph Viewer voor Azure Digital Twins](https://github.com/Azure/azure-digital-twins-graph-viewer).

## <a name="clean-up-resources"></a>Resources opschonen

Als u Azure Digital Twins niet verder wilt verkennen, kunt u de resources die in deze zelfstudie zijn gemaakt, gerust verwijderen:

1. Klik in het linkermenu in de [Azure-portal](https://portal.azure.com) op **Alle resources**, selecteer de Digital Twins-resourcegroep en selecteer **Verwijderen**.

    > [!TIP]
    > Als u problemen hebt bij het verwijderen van uw Digital Twins-exemplaar, is er een service-update met de oplossing hiervoor beschikbaar. Probeer opnieuw of u het exemplaar kunt verwijderen.

1. Verwijder zo nodig de voorbeeldtoepassing van uw werkcomputer.

## <a name="next-steps"></a>Volgende stappen

Ga voor meer informatie over het implementeren van aangepaste logica voor het bewaken van de omstandigheden in uw voorbeeldgebouw naar de volgende zelfstudie in de reeks: 
> [!div class="nextstepaction"]
> [Zelfstudie: Uw gebouw inrichten en uw werkomstandigheden controleren](tutorial-facilities-udf.md)