---
title: 'Zelf studie: een nieuw type Azure IoT Edge apparaat in azure definiëren IoT Central'
description: In deze zelf studie wordt uitgelegd hoe u, als ontwerper, een nieuw Azure IoT Edge apparaat maakt in uw Azure IoT Central-toepassing. U definieert de telemetrie, de status, de eigenschappen en de opdrachten voor uw type.
author: rangv
ms.author: rangv
ms.date: 12/09/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 00ab92effbc5167d8bca3242e55d566c71209ac4
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74979084"
---
# <a name="tutorial-define-a-new-azure-iot-edge-device-type-in-your-azure-iot-central-application-preview-features"></a>Zelf studie: een nieuw type Azure IoT Edge apparaat in uw Azure IoT Central-toepassing definiëren (preview-functies)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

In deze zelf studie wordt uitgelegd hoe u, als ontwerper, een apparaatprofiel kunt gebruiken om een nieuw type Azure IoT Edge apparaat te definiëren in uw Azure IoT Central-toepassing. 

Zie [Wat is Azure IOT Central (preview-functies)?](overview-iot-central.md)voor een overzicht. 

IoT Edge bestaat uit drie onderdelen:
* **IOT Edge modules** zijn containers waarop Azure-Services, partner services of uw eigen code worden uitgevoerd. Modules worden op IoT Edge apparaten geïmplementeerd en lokaal op deze apparaten uitgevoerd.
* De **IOT Edge runtime** wordt op elk IOT edge apparaat uitgevoerd en beheert de modules die op elk apparaat zijn geïmplementeerd.
* Met een **Cloud interface** kunt u IOT edge apparaten op afstand bewaken en beheren. IoT Central is de Cloud interface.

Een **Azure IOT Edge** apparaat kan een gateway apparaat zijn, met downstream-apparaten die verbinding maken met het IOT edge apparaat. In deze zelf studie wordt meer informatie over verbindings patronen voor downstream-apparaten gedeeld.

Met een **apparaatprofiel** worden de mogelijkheden van uw apparaat en IOT Edge modules gedefinieerd. Mogelijkheden zijn onder andere telemetrie die de module verzendt, module-eigenschappen en de opdrachten die een module beantwoordt.

In deze zelf studie maakt u een omgevings sensor-apparaat sjabloon. Een omgevings sensor apparaat:

* Verzendt telemetrie, zoals de Tempe ratuur.
* Reageert op schrijf bare eigenschappen wanneer deze in de cloud worden bijgewerkt, zoals het verzend interval voor telemetrie.
* Reageert op opdrachten, zoals het opnieuw instellen van de Tempe ratuur.

In deze zelf studie maakt u ook een omgevings sjabloon voor het gateway apparaat. Een omgeving met een milieu gateway:

* Verzendt telemetrie, zoals de Tempe ratuur.
* Reageert op schrijf bare eigenschappen wanneer deze in de cloud worden bijgewerkt, zoals het verzend interval voor telemetrie.
* Reageert op opdrachten, zoals het opnieuw instellen van de Tempe ratuur.
* Hiermee kunnen relaties met andere hulp modellen voor apparaten worden ondersteund.


In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Maak een nieuwe Azure IoT Edge Device Device-sjabloon.
> * Upload een implementatie manifest.
> * Maak mogelijkheden, waaronder telemetrie, eigenschappen en opdrachten voor elke module.
> * Definieer een visualisatie voor de module-telemetrie.
> * Relaties toevoegen aan downstream-Apparaatinstellingen.
> * De sjabloon van het apparaat publiceren.

## <a name="prerequisites"></a>Vereisten

Als u deze zelf studie wilt volt ooien, moet u [een Azure IOT Central-toepassing maken](quick-deploy-iot-central.md).


## <a name="downstream-device-relationships-with-a-gateway-and-modules"></a>De relaties van downstream-apparaten met een gateway en modules

Downstream-apparaten kunnen via de module `$edgeHub` verbinding maken met een IoT Edge gateway-apparaat. Dit IoT Edge apparaat wordt in dit scenario een transparante gateway.

![Diagram van transparante gateway](./media/tutorial-define-edge-device-type/gateway-transparent.png)

Downstream-apparaten kunnen ook via een aangepaste module verbinding maken met een IoT Edge gateway-apparaat. In het volgende scenario maken downstream-apparaten verbinding via een aangepaste Modbus-module.

![Diagram van de verbinding van de aangepaste module](./media/tutorial-define-edge-device-type/gateway-module.png)

In het volgende diagram ziet u een verbinding met een IoT Edge gateway apparaat via beide typen modules (aangepast en `$edgeHub`).  

![Diagram van verbinding maken via beide verbindings modules](./media/tutorial-define-edge-device-type/gateway-module-transparent.png)

Ten slotte kunnen downstream-apparaten via meerdere aangepaste modules verbinding maken met een IoT Edge gateway-apparaat. In het volgende diagram worden downstream-apparaten weer gegeven die verbinding maken via een aangepaste Modbus-module, een afgeleide aangepaste module en de module `$edgeHub`. 

![Diagram van verbinding maken via meerdere aangepaste modules](./media/tutorial-define-edge-device-type/gateway-module2-transparent.png)


## <a name="create-a-template"></a>Een sjabloon maken

Als ontwerper kunt u IoT Edge-apparaatklassen maken en bewerken in uw toepassing. Nadat u een apparaataccount hebt gepubliceerd, kunt u echte apparaten die de sjabloon voor het apparaat implementeren, verbinden.

### <a name="select-device-template-type"></a>Type sjabloon voor apparaat selecteren 

Als u een nieuwe apparaatprofiel wilt toevoegen aan uw toepassing, selecteert u **Apparaatbeheer** in het linkerdeel venster.

![Scherm opname van de preview-toepassing, met de geselecteerde Apparaatinstellingen](./media/tutorial-define-edge-device-type/edgedevicetemplate.png)

Selecteer **+ Nieuw** om te beginnen met het maken van een nieuwe sjabloon voor het apparaat.

![Scherm afbeelding van de pagina met Apparaatinstellingen, met nieuwe gemarkeerd](./media/tutorial-define-edge-device-type/edgedevicetemplatenew.png)

Selecteer op de pagina **sjabloon type selecteren** de optie **Azure IOT Edge**en selecteer **volgende: aanpassen**.

![Scherm afbeelding van de pagina sjabloon type selecteren](./media/tutorial-define-edge-device-type/selectiotedge.png)

### <a name="customize-device-template"></a>Sjabloon voor het apparaat aanpassen

In IoT Edge kunt u bedrijfs logica implementeren en beheren in de vorm van modules. IoT Edge modules zijn de kleinste reken eenheid die wordt beheerd door IoT Edge en kunnen Azure-Services (zoals Azure Stream Analytics) of uw eigen oplossings code bevatten. Zie [IOT Edge modules](../../iot-edge/iot-edge-modules.md)voor informatie over hoe modules worden ontwikkeld, geïmplementeerd en onderhouden.

Op hoog niveau is een manifest van de implementatie van een lijst met moduledubbels die zijn geconfigureerd met de gewenste eigenschappen. Een implementatie manifest vertelt een IoT Edge apparaat (of een groep apparaten) welke modules moeten worden geïnstalleerd en hoe ze kunnen worden geconfigureerd. Implementatie manifesten bevatten de gewenste eigenschappen voor elke module dubbele. IoT Edge-apparaten melden de gerapporteerde eigenschappen voor elke module terug.

Visual Studio code gebruiken om een implementatie manifest te maken. Zie [Azure IOT Edge voor Visual Studio code voor](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)meer informatie.

Hier volgt een basis implementatie manifest, met een module als voor beeld die moet worden gebruikt voor deze zelf studie. Kopieer de volgende JSON en sla deze op als een JSON-bestand. 

   ```JSON
   {
     "modulesContent": {
       "$edgeAgent": {
         "properties.desired": {
           "schemaVersion": "1.0",
           "runtime": {
             "type": "docker",
             "settings": {
               "minDockerVersion": "v1.25",
               "loggingOptions": "",
               "registryCredentials": {}
             }
           },
           "systemModules": {
             "edgeAgent": {
               "type": "docker",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
                 "createOptions": "{}"
               }
             },
             "edgeHub": {
               "type": "docker",
               "status": "running",
               "restartPolicy": "always",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
                 "createOptions": "{}"
               }
             }
           },
           "modules": {
             "SimulatedTemperatureSensor": {
               "version": "1.0",
               "type": "docker",
               "status": "running",
               "restartPolicy": "always",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
                 "createOptions": "{}"
               }
             }
           }
         }
       },
       "$edgeHub": {
         "properties.desired": {
           "schemaVersion": "1.0",
           "routes": {
               "route": "FROM /* INTO $upstream"
           },
           "storeAndForwardConfiguration": {
             "timeToLiveSecs": 7200
           }
         }
       },
       "SimulatedTemperatureSensor": {
         "properties.desired": {
              "SendData": true,
              "SendInterval": 10
         }
       }
     }
   }
   ```

#### <a name="upload-an-iot-edge-deployment-manifest"></a>Een IoT Edge implementatie manifest uploaden

Selecteer op de pagina **apparaat aanpassen** onder **een Azure IOT Edge implementatie manifest uploaden de**optie **Bladeren**. 

![Scherm afbeelding van de pagina apparaat aanpassen, met gemarkeerde Browse](./media/tutorial-define-edge-device-type/edgedevicetemplateuploadmanifest.png)

Als u van plan bent een sjabloon voor een IoT Edge gateway-apparaat te maken, moet u **gateway apparaat selecteren met downstream-apparaten**.

![Scherm afbeelding van de pagina apparaat aanpassen, met gateway apparaat met downstream-apparaten gemarkeerd](./media/tutorial-define-edge-device-type/gateway-upload.png)

Selecteer in het dialoog venster bestands selectie het manifest bestand van de implementatie en selecteer **openen**.

IoT Edge valideert het manifest bestand van de implementatie op basis van een schema. Als de validatie is geslaagd, selecteert u **controleren**.

![Scherm afbeelding van de pagina apparaat aanpassen met implementatie manifest en gemarkeerde beoordeling](./media/tutorial-define-edge-device-type/deploymentmanifestvalidate.png)

In het volgende stroom diagram ziet u de levens cyclus van het implementatie manifest in IoT Central.

![De levens cyclus van het stroom diagram van het implementatie manifest](./media/tutorial-define-edge-device-type/dmflow.png)

Vervolgens ziet u een controle pagina met details van het implementatie manifest. Op deze pagina wordt een lijst met modules uit het implementatie manifest weer gegeven. In deze zelf studie ziet u dat de module `SimulatedTemperatureSensor` wordt weer gegeven. Selecteer **Maken**.

![Scherm afbeelding van de pagina controleren, met module en gemarkeerd maken](./media/tutorial-define-edge-device-type/edgedevicetemplatereview.png)

Als u een gateway apparaat hebt geselecteerd, ziet u de volgende controle pagina.

![Scherm afbeelding van de pagina controleren, met Azure IoT Edge gateway gemarkeerd](./media/tutorial-define-edge-device-type/gateway-review.png)


U maakt een sjabloon voor het maken van een apparaat met module Capability-modellen. In deze zelf studie maakt u een sjabloon met het model van de `SimulatedTemperatureSensor`-module. 

Wijzig de titel van de sjabloon apparaat sjabloon in **omgevings sensor**.

![Scherm afbeelding van een apparaatprofiel, met de bijgewerkte titel gemarkeerd](./media/tutorial-define-edge-device-type/edgedevicetemplatelanding.png)

Model IoT Plug en Play op IoT Edge apparaat als volgt:
* Elke IoT Edge-apparaatprofiel heeft een hulp model voor het apparaat.
* Voor elke aangepaste module die wordt vermeld in het implementatie manifest, wordt een module mogelijkheidsprofiel gegenereerd.
* Er wordt een relatie tot stand gebracht tussen elk module mogelijkheidsprofiel en een mogelijkheidsprofiel.
* Een module-functionaliteits model implementeert module interfaces.
* Elke module interface bevat telemetrie, eigenschappen en opdrachten.

![Diagram van IoT Edge modellen](./media/tutorial-define-edge-device-type/edgemodelling.png)

#### <a name="add-capabilities-to-a-module-capability-model"></a>Mogelijkheden toevoegen aan een module-functionaliteits model

Hier volgt een voor beeld van de uitvoer van de module `SimulatedTemperatureSensor`:
```json
{

    "machine": {

        "temperature": 75.0,
        "pressure": 40.2
    },
    "ambient": {
        "temperature": 23.0,
        "humidity": 30.0
    },
    "timeCreated": ""
}
```

U kunt mogelijkheden toevoegen aan de module `SimulatedTemperatureSensor`, die de voor gaande uitvoer weergeeft. 

1. Als u een interface van het model van de `SimulatedTemperatureSensor` module wilt beheren, selecteert u de **functie > toevoegen** **beheren** . 

    ![Scherm afbeelding van omgevings sensor sjabloon, met de functie toevoegen gemarkeerd](./media/tutorial-define-edge-device-type/edgetemplateaddcapability.png)
  
1. Voeg een machine toe als object type.
  
    ![Scherm afbeelding van de pagina sjabloon mogelijkheden van omgevings sensor, met schema gemarkeerd](./media/tutorial-define-edge-device-type/edgetemplatemachineobject.png)

1. Selecteer **definiëren**. In het dialoog venster dat wordt weer gegeven, wijzigt u de object naam in **computer**. Maak de eigenschappen voor de Tempe ratuur en druk en selecteer **Toep assen**.
  
    ![Scherm afbeelding van kenmerken in het dialoog venster, waarbij verschillende opties zijn gemarkeerd](./media/tutorial-define-edge-device-type/edgetemplatemachineattributes.png)
  
1. Voeg een **omgeving** toe als object type.

1. Selecteer **definiëren**. In het dialoog venster dat wordt weer gegeven, wijzigt u de naam van het object in **Ambient**. Maak eigenschappen van de Tempe ratuur en vochtigheid en selecteer **Toep assen**.
  
    ![Scherm afbeelding van kenmerken in het dialoog venster, waarbij verschillende opties zijn gemarkeerd](./media/tutorial-define-edge-device-type/edgetemplateambientattributes.png)

  
1. Voeg `timeCreated` toe als een `DateTime` type en selecteer **Opslaan**.
  
    ![Scherm afbeelding van omgevings sensor sjabloon, met gemarkeerd voor opslaan](./media/tutorial-define-edge-device-type/edgetemplateallattributes.png)


### <a name="add-relationships"></a>Relaties toevoegen

Als u een IoT Edge apparaat als gateway apparaat hebt geselecteerd, kunt u downstream-relaties toevoegen aan de apparaatfuncties voor apparaten die u wilt verbinden met het gateway apparaat.
  
  ![Scherm afbeelding van de sjabloon voor de omgevings gateway, met de relatie toevoegen gemarkeerd](./media/tutorial-define-edge-device-type/gateway-add-relationship.png)

U kunt een relatie toevoegen op een apparaat of in een module.
  
  ![Scherm afbeelding van de sjabloon voor de omgevings gateway, waarbij relaties op apparaat en module niveau zijn gemarkeerd](./media/tutorial-define-edge-device-type/gateway-relationship-types.png)


U kunt een functionaliteits model voor het downstream-apparaat selecteren, maar u kunt ook het asterisk-symbool selecteren. 
  
  ![Scherm opname van sjabloon voor omgevings gateway, met gemarkeerd doel](./media/tutorial-define-edge-device-type/gateway-downstream-rel.png)

  Voor deze zelf studie selecteert u het sterretje. Met deze optie wordt een downstream-relatie toegestaan. Selecteer vervolgens **Opslaan**.

  ![Scherm opname van sjabloon voor omgevings gateway, met gemarkeerd doel](./media/tutorial-define-edge-device-type/gateway-add-relationship-asterix.png)


### <a name="add-cloud-properties"></a>Cloud eigenschappen toevoegen

Een sjabloon voor een apparaat kan Cloud eigenschappen bevatten. Cloud eigenschappen bestaan alleen in de IoT Central-toepassing en worden nooit verzonden naar of ontvangen van een apparaat.

1. Selecteer **Cloud eigenschappen** >  **+ Cloud eigenschap toevoegen**. Gebruik de informatie in de volgende tabel om een Cloud eigenschap toe te voegen aan de sjabloon van uw apparaat.

    | Weergavenaam      | Semantisch type | Schema |
    | ----------------- | ------------- | ------ |
    | Laatste servicedatum | Geen          | Datum   |
    | Klantnaam     | Geen          | Tekenreeks |

2. Selecteer **Opslaan**.

  
    ![Scherm afbeelding van omgevings sensor sjabloon, met gemarkeerd voor opslaan](./media/tutorial-define-edge-device-type/edgetemplatecloudproperties.png)

### <a name="add-customizations"></a>Aanpassingen toevoegen

Gebruik aanpassingen om een interface te wijzigen of om IoT Central-specifieke functies toe te voegen aan een mogelijkheid waarvoor u geen versie hoeft te hebben van uw apparaat. U kunt velden aanpassen wanneer het capaciteits model zich in een concept of gepubliceerde status bevindt. U kunt alleen velden aanpassen die de interface compatibiliteit niet verstoren. U kunt bijvoorbeeld:

- Pas de weergave naam en de eenheden van een mogelijkheid aan.
- Een standaard kleur toevoegen die moet worden gebruikt wanneer de waarde in een grafiek wordt weer gegeven.
- Geef de initiële, minimale en maximale waarde voor een eigenschap op.

U kunt de naam of het type van de mogelijkheid niet aanpassen.

Wanneer u klaar bent met aanpassen, selecteert u **Opslaan**.
  
![Scherm afbeelding van sjabloon voor omgevings sensor pagina aanpassen](./media/tutorial-define-edge-device-type/edgetemplatecustomize.png)


### <a name="create-views"></a>Weergaven aanmaken

Als opbouw functie kunt u de toepassing aanpassen om relevante informatie over het omgevings sensor apparaat weer te geven voor een operator. Met uw aanpassingen kan de operator de omgevings sensor apparaten beheren die zijn verbonden met de toepassing. U kunt twee soorten weer gaven maken voor een operator die u kunt gebruiken om te communiceren met apparaten:

* Formulieren voor het weer geven en bewerken van apparaat-en Cloud eigenschappen.
* Dash boards om apparaten te visualiseren.

### <a name="configure-a-view-to-visualize-devices"></a>Een weer gave configureren om apparaten te visualiseren

Met een dash board van een apparaat kan een operator een apparaat visualiseren met behulp van grafieken en metrische gegevens. Als opbouw functie kunt u definiëren welke informatie wordt weer gegeven op een dash board van een apparaat. U kunt meerdere Dash boards voor apparaten definiëren. Als u een dash board wilt maken om de telemetrie van de omgevings sensor te visualiseren, selecteert u **weer gaven** > **het apparaat te visualiseren**:

  
![Scherm afbeelding van sjabloon weergaven pagina van omgevings sensor, met het visualiseren van het apparaat gemarkeerd](./media/tutorial-define-edge-device-type/visualizingthedevice.png)


De telemetrie van de omgeving en telemetrie van de machine zijn complexe objecten. Grafieken maken:

1. Sleep **omgevings-telemetrie**en selecteer **lijn diagram**. 
  
   ![Scherm afbeelding van omgevings sensor sjabloon, met omgevings-telemetrie en lijn diagram gemarkeerd](./media/tutorial-define-edge-device-type/sensorambientchart.png)

1. Selecteer het pictogram configureren. Selecteer **Tempe ratuur** en **vochtigheid** om de gegevens te visualiseren en selecteer **update configuratie**. 
  
   ![Scherm afbeelding van omgevings sensor sjabloon, waarbij verschillende opties zijn gemarkeerd](./media/tutorial-define-edge-device-type/sensorambienttelemetrychart.png)

1. Selecteer **Opslaan**.

U kunt meer tegels toevoegen waarin andere eigenschappen of telemetrie-waarden worden weer gegeven. U kunt ook statische tekst, koppelingen en afbeeldingen toevoegen. Als u een tegel op het dash board wilt verplaatsen of verg Roten of verkleinen, verplaatst u de muis aanwijzer over de tegel en sleept u de tegel naar een nieuwe locatie of wijzigt u het formaat ervan.
  
![Scherm afbeelding van Dashboard weergave van omgevings sensor sjablonen](./media/tutorial-define-edge-device-type/viewsdashboard.png)

### <a name="add-a-device-form"></a>Een formulier van een apparaat toevoegen

Met een formulier van een apparaat kan een operator Beschrijf bare apparaateigenschappen en eigenschappen van Clouds bewerken. Als opbouw functie kunt u meerdere formulieren definiëren en kiezen welk apparaat en welke Cloud eigenschappen op elk formulier worden weer gegeven. U kunt ook eigenschappen voor alleen-lezen apparaten weer geven in een formulier.

Een formulier maken voor het weer geven en bewerken van eigenschappen van de omgevings sensor:

1. Ga in de **sjabloon omgevings sensor**naar **weer gaven**. Selecteer de tegel **apparaat en Cloud gegevens bewerken** om een nieuwe weer gave toe te voegen.
  
   ![Scherm afbeelding van sjabloon weergaven van omgevings sensors, met het bewerken van het apparaat en de Cloud gegevens gemarkeerd](./media/tutorial-define-edge-device-type/editingdeviceandclouddata.png)

1. Voer de naam van de **omgevings sensor**van het formulier in.

1. Sleep de **naam** van de klant en de **laatste service datum** -Cloud naar de bestaande sectie op het formulier.
  
   ![Scherm afbeelding van de pagina sjabloon weergaven van omgevings sensor, waarbij verschillende opties zijn gemarkeerd](./media/tutorial-define-edge-device-type/views-properties.png)

1. Selecteer **Opslaan**.

## <a name="publish-a-device-template"></a>Een apparaatsjabloon publiceren

Voordat u een gesimuleerde omgevings sensor kunt maken, of als u een echte omgevings sensor wilt verbinden, moet u de sjabloon voor het apparaat publiceren.

Een sjabloon voor een apparaat publiceren:

1. Ga op de pagina **Apparaatinstellingen** naar uw sjabloon.

2. Selecteer **Publiceren**.
  
    ![Scherm afbeelding van de sjabloon voor de omgevings sensor, met gemarkeerd voor publiceren](./media/tutorial-define-edge-device-type/edgetemplatepublish.png)

1. Kies in het dialoog venster **een sjabloon voor het publiceren van een apparaat** de optie **publiceren**.
  
    ![Scherm afbeelding van het dialoog venster een sjabloon voor een apparaat publiceren, met de markering publiceren gemarkeerd](./media/tutorial-define-edge-device-type/edgepublishtemplate.png)

Nadat een apparaat sjabloon is gepubliceerd, wordt deze weer gegeven op de pagina **apparaten** en aan de operator. U kunt in een sjabloon voor een gepubliceerd apparaat geen mogelijkheidsprofiel bewerken zonder een nieuwe versie te maken. U kunt echter updates voor de Cloud eigenschappen, aanpassingen en weer gaven maken in een sjabloon voor een gepubliceerd apparaat. Deze updates zorgen er niet voor dat er een nieuwe versie wordt gemaakt. Nadat u wijzigingen hebt aangebracht, selecteert u **publiceren** om deze wijzigingen naar uw operator te pushen.
  
![Scherm afbeelding van de lijst met sjablonen van gepubliceerde sjablonen](./media/tutorial-define-edge-device-type/publishedtemplate.png)

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

* Een nieuwe rand maken als een sjabloon voor een Leaf-apparaat.
* Modules genereren op basis van een geüpload implementatie manifest.
* Voeg complex type telemetrie en eigenschappen toe.
* Cloud eigenschappen maken.
* Aanpassingen maken.
* Definieer een visualisatie voor de telemetrie van het apparaat.
* De sjabloon voor uw edge-apparaat publiceren.

Nu u een sjabloon voor een apparaat in uw Azure IoT Central-toepassing hebt gemaakt, kunt u dit op de volgende manieren doen:

> [!div class="nextstepaction"]
> [Apparaat verbinden](./tutorial-connect-pnp-device.md)
