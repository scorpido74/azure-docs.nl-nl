---
title: Een nieuw type Azure IoT Edge apparaat definiëren in azure IoT Central | Microsoft Docs
description: In deze zelf studie wordt uitgelegd hoe u, als ontwerper, een nieuw Azure IoT Edge apparaat maakt in uw Azure IoT Central-toepassing. U definieert de telemetrie, de status, de eigenschappen en de opdrachten voor uw type.
author: rangv
ms.author: rangv
ms.date: 10/22/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: c205b4dd4871ed53e32dce72f12cc2dcfb3baf41
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/09/2019
ms.locfileid: "73893051"
---
# <a name="tutorial-define-a-new-azure-iot-edge-device-type-in-your-azure-iot-central-application-preview-features"></a>Zelf studie: een nieuw type Azure IoT Edge apparaat in uw Azure IoT Central-toepassing definiëren (preview-functies)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

In deze zelf studie wordt uitgelegd hoe u, als ontwerper, een apparaatprofiel kunt gebruiken om een nieuw type Azure IoT Edge apparaat te definiëren in uw Azure IoT Central-toepassing. 

[Raadpleeg dit artikel](overview-iot-central.md)voor een overzicht van Azure IOT Edge. 

Azure IoT Edge bestaat uit drie onderdelen:
* **IOT Edge modules** zijn containers waarop Azure-Services, services van derden of uw eigen code worden uitgevoerd. De modules zijn geïmplementeerd op IoT Edge-apparaten en worden lokaal uitgevoerd op deze apparaten.
* De **IOT Edge runtime** wordt op elk IOT edge apparaat uitgevoerd en beheert de modules die op elk apparaat zijn geïmplementeerd.
* Met een **Cloud interface** kunt u IOT edge apparaten op afstand bewaken en beheren. IoT Central is de Cloud interface.

Een **Azure IOT Edge** apparaat kan een gateway apparaat zijn met downstream-apparaten die verbinding maken met het Azure IOT edge apparaat. De verbindings patronen voor downstream-apparaten worden in deze zelf studie besproken.

Een **sjabloon** voor een apparaat definieert de mogelijkheden van uw apparaat & IOT Edge modules. Mogelijkheden zijn onder andere telemetrie die de module verzendt, module-eigenschappen en de opdrachten die een module beantwoordt.

In deze zelf studie maakt u een **omgevings sensor** -apparaat sjabloon. Een omgevings sensor apparaat:

* Verzendt telemetrie, zoals Tempe ratuur.
* Reageert op Beschrijf bare eigenschappen wanneer deze in de cloud worden bijgewerkt, zoals het verzend interval voor telemetrie.
* Reageert op opdrachten zoals het opnieuw instellen van de Tempe ratuur.

In deze zelf studie maakt u ook een **omgevings** sjabloon voor het gateway apparaat. Een omgeving met een milieu gateway:

* Verzendt telemetrie, zoals Tempe ratuur.
* Reageert op Beschrijf bare eigenschappen wanneer deze in de cloud worden bijgewerkt, zoals het verzend interval voor telemetrie.
* Reageert op opdrachten zoals het opnieuw instellen van de Tempe ratuur.
* Hiermee staat u relaties toe met andere hulp modellen voor apparaten


In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Maak een nieuwe Azure IoT Edge Device Device-sjabloon.
> * Upload een implementatie manifest.
> * Maak mogelijkheden, waaronder telemetrie, eigenschappen en opdrachten voor elke module
> * Definieer een visualisatie voor de module-telemetrie.
> * Relaties toevoegen aan downstream-Apparaatinstellingen
> * De sjabloon van het apparaat publiceren.

## <a name="prerequisites"></a>Vereisten

U hebt een Azure IoT Central-toepassing nodig om deze zelfstudie te voltooien. Volg deze Snelstartgids om [een Azure IOT Central-toepassing te maken](quick-deploy-iot-central.md).


## <a name="downstream-device-relationships-with-gateway--modules"></a>Downstream-relaties tussen apparaten met gateway & modules

Downstream-apparaten kunnen via de module $edgeHub verbinding maken met Azure IoT Edge gateway apparaat. Dit Azure IoT Edge apparaat wordt in dit scenario een transparante gateway

![Pagina centrale toepassing](./media/tutorial-define-edge-device-type/gateway-transparent.png)

Downstream-apparaten kunnen via een aangepaste module verbinding maken met Azure IoT Edge gateway apparaat. In het volgende scenario maken downstream-apparaten verbinding via een aangepaste Modbus-module en kunnen downstream-apparaten verbinding maken met Azure IoT Edge gateway apparaat via de module $edgeHub.

![Pagina centrale toepassing](./media/tutorial-define-edge-device-type/gateway-module.png)

Downstream-apparaten kunnen via een aangepaste module verbinding maken met Azure IoT Edge gateway apparaat. In het volgende scenario maken downstream-apparaten verbinding via een aangepaste Modbus-module. 

![Pagina centrale toepassing](./media/tutorial-define-edge-device-type/gateway-module-transparent.png)

Downstream-apparaten kunnen via meerdere aangepaste modules verbinding maken met Azure IoT Edge gateway apparaat. In het volgende scenario kunnen downstream-apparaten verbinding maken via een aangepaste Modbus-module, de aangepaste uitsluitings module en downstream-apparaten met behulp van de module $edgeHub om verbinding te maken met Azure IoT Edge gateway apparaat. 

![Pagina centrale toepassing](./media/tutorial-define-edge-device-type/gateway-module2-transparent.png)


## <a name="create-a-template"></a>Een sjabloon maken

Als ontwerper kunt u Azure IoT Edge-apparaatklassen maken en bewerken in uw toepassing. Nadat u een apparaataccount hebt gepubliceerd, kunt u echte apparaten die de sjabloon voor het apparaat implementeren, verbinden.

### <a name="select-device-template-type"></a>Type sjabloon voor apparaat selecteren 

Als u een nieuwe apparaatprofiel wilt toevoegen aan uw toepassing, gaat u naar de pagina met **Apparaatinstellingen** . Hiertoe selecteert u het tabblad **device templates** in het linkerdeel venster.

![Pagina centrale toepassing](./media/tutorial-define-edge-device-type/edgedevicetemplate.png)

Klik op **+ Nieuw** om te beginnen met het maken van een nieuwe sjabloon voor het apparaat.

![Apparaatinstellingen-nieuw](./media/tutorial-define-edge-device-type/edgedevicetemplatenew.png)

U gaat op de selectie pagina voor het type apparaat sjabloon land. Selecteer **Azure IOT Edge** tegel en klik op **volgende:** knop aan de onderkant aanpassen

![Selectie van Device-sjablonen-Azure IoT Edge](./media/tutorial-define-edge-device-type/selectiotedge.png)

### <a name="customize-device-template"></a>Sjabloon voor het apparaat aanpassen

Met Azure IoT Edge kunt u bedrijfs logica implementeren en beheren in de vorm van modules. **Azure IOT Edge modules** zijn de kleinste reken eenheid die wordt beheerd door IOT Edge en kunnen Azure-Services (zoals Azure stream Analytics) of uw eigen oplossings code bevatten. Om te begrijpen hoe modules worden ontwikkeld, geïmplementeerd en onderhouden [IOT Edge modules](../../iot-edge/iot-edge-modules.md).

Op hoog niveau is een implementatie manifest een lijst met module apparaatdubbels die zijn geconfigureerd met de gewenste eigenschappen. Een implementatie manifest vertelt een IoT Edge apparaat (of een groep apparaten) welke modules moeten worden geïnstalleerd en hoe ze moeten worden geconfigureerd. Implementatie manifesten bevatten de gewenste eigenschappen voor elke module dubbele. IoT Edge-apparaten melden de gerapporteerde eigenschappen voor elke module terug.

Visual Studio code gebruiken om een implementatie manifest te maken. Volg de documentatie voor het maken van een [implementatie manifest](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge).

Hier volgt een basis implementatie manifest met één module als voor beeld die moet worden gebruikt voor deze zelf studie. Kopieer de onderstaande JSON en sla deze op als JSON-bestand. 

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

**Een Azure IoT Edge implementatie manifest uploaden**

Klik op de knop **Bladeren** 

![Apparaatprofiel-Azure IoT Edge](./media/tutorial-define-edge-device-type/edgedevicetemplateuploadmanifest.png)

Als u van plan bent een sjabloon voor een Azure IoT Edge gateway-apparaat te maken, schakelt u het selectie vakje **gateway apparaat met downstream-apparaten** in

![Apparaatprofiel-Azure IoT Edge](./media/tutorial-define-edge-device-type/gateway-upload.png)

U krijgt een dialoog venster voor bestands selectie. Selecteer het manifest bestand voor implementatie en klik op de knop **openen** .

Het manifest bestand voor implementatie wordt gecontroleerd op basis van een schema. Klik op de knop **controleren** nadat de validatie is voltooid

![Apparaatprofiel-Azure IoT Edge](./media/tutorial-define-edge-device-type/deploymentmanifestvalidate.png)

Hieronder volgt de stroom van een levens cyclus in het implementatie manifest in IoT Central.

![Apparaatprofiel-Azure IoT Edge](./media/tutorial-define-edge-device-type/dmflow.png)

De pagina controleren wordt weer gegeven met details van het implementatie manifest. De lijst met modules uit het implementatie manifest wordt weer gegeven op de pagina controleren. In deze zelf studie ziet u de SimulatedTemperatureSensor-module die wordt weer gegeven. Klik op de knop **maken** .

![Apparaatprofiel-Azure IoT Edge](./media/tutorial-define-edge-device-type/edgedevicetemplatereview.png)

Als u een gateway apparaat hebt geselecteerd, wordt deze controle pagina weer geven

![Apparaatprofiel-Azure IoT Edge](./media/tutorial-define-edge-device-type/gateway-review.png)


Het maken van een nieuwe apparaatprofiel-kring veld wordt weer gegeven waarin de sjabloon voor het apparaat wordt gemaakt in IoT Central.

Er wordt een apparaatprofiel gemaakt met module Capability-modellen. In deze zelf studie ziet u SimulatedTemperatureSensor module Capability model Create. 

Wijzig de titel van de sjabloon apparaat sjabloon in omgevings sensor.

![Apparaatprofiel-Azure IoT Edge](./media/tutorial-define-edge-device-type/edgedevicetemplatelanding.png)

Azure IoT Edge apparaat Plug n Play-modellen worden als volgt uitgevoerd
* Elke Azure IoT Edge-apparaatprofiel heeft een **mogelijkheidsprofiel**
* Er wordt een **module mogelijkheidsprofiel** gegenereerd voor elke aangepaste module die wordt vermeld in het implementatie manifest.
* Er wordt een **relatie** tot stand gebracht tussen elk module mogelijkheidsprofiel en een model voor het maken van een apparaat.
* Module-functionaliteits model implementeert **module interfaces**
* Elke module interface bevat
   - Telemetrie
   - Eigenschappen
   - Opdrachten

![Apparaatprofiel-Azure IoT Edge](./media/tutorial-define-edge-device-type/edgemodelling.png)

**Mogelijkheden toevoegen aan module-functionaliteits model**

Hier volgt een voor beeld van een uitvoer van de module SimulatedTemperatureSensor
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

Voeg mogelijkheden toe aan de SimulatedTemperatureSensor-module, die overeenkomt met de bovenstaande JSON. 

* Klik op een interface van SimulatedTemperatureSensor module-functionaliteits model **beheren** . Klik op **mogelijkheid toevoegen**. 

    ![Apparaatprofiel-Azure IoT Edge](./media/tutorial-define-edge-device-type/edgetemplateaddcapability.png)
  
* Machine toevoegen als object type omdat het een complex type is
  
    ![Apparaatprofiel-Azure IoT Edge](./media/tutorial-define-edge-device-type/edgetemplatemachineobject.png)

    Klik op **definiëren**. Wijzig in het pop-upvenster object naam in de modale computer en maak eigenschappen temperatuur, druk en klik op **Toep assen**
  
    ![Apparaatprofiel-Azure IoT Edge](./media/tutorial-define-edge-device-type/edgetemplatemachineattributes.png)
  
* Een omgeving als object type toevoegen omdat het een complex type is

    Klik op **definiëren**. In de pop-up object naam wijzigen in omgeving en eigenschappen temperatuur, vochtigheid maken en klik op **Toep assen**
  
    ![Apparaatprofiel-Azure IoT Edge](./media/tutorial-define-edge-device-type/edgetemplateambientattributes.png)

  
* Voeg timeCreated toe als DateTime-type en klik op **Opslaan**
  
    ![Apparaatprofiel-Azure IoT Edge](./media/tutorial-define-edge-device-type/edgetemplateallattributes.png)


### <a name="add-relationships"></a>Relaties toevoegen

Als u Azure IoT Edge apparaat hebt geselecteerd als gateway apparaat, kunt u downstream-relaties toevoegen aan apparaatfuncties voor apparaten die u wilt verbinden met het gateway apparaat.
  
  ![Apparaatprofiel-Azure IoT Edge](./media/tutorial-define-edge-device-type/gateway-add-relationship.png)

De relatie kan worden toegevoegd op een apparaat of in een module.
  
  ![Apparaatprofiel-Azure IoT Edge](./media/tutorial-define-edge-device-type/gateway-relationship-types.png)


U kunt een functionaliteits model voor het downstream-apparaat selecteren of Asterix selecteren. 
  
  ![Apparaatprofiel-Azure IoT Edge](./media/tutorial-define-edge-device-type/gateway-downstream-rel.png)

  Voor deze zelf studie selecteren we Asterix. Dit betekent dat een stroomafwaartse relatie wordt toegestaan. Klik op **Opslaan**.

  ![Apparaatprofiel-Azure IoT Edge](./media/tutorial-define-edge-device-type/gateway-add-relationship-asterix.png)


### <a name="add-cloud-properties"></a>Cloud eigenschappen toevoegen

Een sjabloon voor een apparaat kan Cloud eigenschappen bevatten. Cloud eigenschappen bestaan alleen in de IoT Central-toepassing en worden nooit verzonden naar of ontvangen van een apparaat.

1. Selecteer **Cloud eigenschappen** en vervolgens **+ eigenschap Cloud toevoegen**. Gebruik de informatie in de volgende tabel om een Cloud eigenschap toe te voegen aan de sjabloon van uw apparaat.

    | Weergavenaam      | Semantisch type | Schema |
    | ----------------- | ------------- | ------ |
    | Laatste servicedatum | Geen          | Date   |
    | Klant naam     | Geen          | Tekenreeks |

2. Selecteer **Opslaan** om uw wijzigingen op te slaan:

  
    ![Cloud eigenschappen-Azure IoT Edge](./media/tutorial-define-edge-device-type/edgetemplatecloudproperties.png)

### <a name="add-customizations"></a>Aanpassingen toevoegen

Gebruik aanpassingen wanneer u een interface moet wijzigen of IoT Central-specifieke functies wilt toevoegen aan een mogelijkheid waarvoor u geen versie hoeft te hebben van uw apparaat. U kunt velden aanpassen wanneer het capaciteits model zich in een concept of gepubliceerde status bevindt. U kunt alleen velden aanpassen die de interface compatibiliteit niet verstoren. U kunt bijvoorbeeld:

- Pas de weergave naam en de eenheden van een mogelijkheid aan.
- Een standaard kleur toevoegen die moet worden gebruikt wanneer de waarde in een grafiek wordt weer gegeven.
- Geef de initiële, minimale en maximale waarde voor een eigenschap op.

U kunt de naam of het type van de mogelijkheid niet aanpassen. Klik op **Opslaan**.
  
![Aanpassingen-Azure IoT Edge](./media/tutorial-define-edge-device-type/edgetemplatecustomize.png)


### <a name="create-views"></a>Weer gaven maken

Als opbouw functie kunt u de toepassing aanpassen om relevante informatie over het omgevings sensor apparaat weer te geven voor een operator. Met uw aanpassingen kan de operator de omgevings sensor apparaten beheren die zijn verbonden met de toepassing. U kunt twee soorten weer gaven maken voor een operator die u kunt gebruiken om te communiceren met apparaten:

* Formulieren voor het weer geven en bewerken van apparaat-en Cloud eigenschappen.
* Dash boards om apparaten te visualiseren.

### <a name="configure-a-view-to-visualize-devices"></a>Een weer gave configureren om apparaten te visualiseren

Met een dash board van een apparaat kan een operator een apparaat visualiseren met behulp van grafieken en metrische gegevens. Als systeembouwer kunt u bepalen welke informatie in een apparaatdashboard wordt weergegeven. U kunt meerdere Dash boards voor apparaten definiëren. Als u een dash board wilt maken om de telemetrie van de omgevings sensor te visualiseren, selecteert u **weer gaven** en vervolgens **het apparaat visualiseren**:

  
![Weer gaven-Azure IoT Edge](./media/tutorial-define-edge-device-type/visualizingthedevice.png)


De telemetrie van de omgeving en telemetrie van de machine zijn complexe objecten. Als u grafieken wilt maken, doet u het volgende

Sleep omgevings-telemetrie en selecteer lijn diagram. 
  
![Weer gaven-Azure IoT Edge](./media/tutorial-define-edge-device-type/sensorambientchart.png)

Klik op pictogram configureren en selecteer Tempe ratuur en vochtigheid om de gegevens te visualiseren en klik op de knop **configuratie bijwerken** . 
  
![Weer gaven-Azure IoT Edge](./media/tutorial-define-edge-device-type/sensorambienttelemetrychart.png)

Selecteer **Opslaan** om uw weer gave op te slaan:

U kunt meer tegels toevoegen waarin andere eigenschappen of telemetrie-waarden worden weer gegeven. U kunt ook statische tekst, koppelingen en afbeeldingen toevoegen. Als u een tegel op het dash board wilt verplaatsen of verg Roten of verkleinen, verplaatst u de muis aanwijzer over de tegel en sleept u de tegel naar een nieuwe locatie of wijzigt u het formaat ervan.
  
![Weer gaven-Azure IoT Edge](./media/tutorial-define-edge-device-type/viewsdashboard.png)

### <a name="add-a-device-form"></a>Een formulier van een apparaat toevoegen

Met een formulier van een apparaat kan een operator Beschrijf bare apparaateigenschappen en eigenschappen van Clouds bewerken. Als opbouw functie kunt u meerdere formulieren definiëren en kiezen welk apparaat en welke Cloud eigenschappen op elk formulier worden weer gegeven. U kunt ook eigenschappen voor alleen-lezen apparaten weer geven in een formulier.

Een formulier maken voor het weer geven en bewerken van eigenschappen van de omgevings sensor:

Navigeer naar **weer gaven** in de sjabloon voor de **omgevings sensor** . Selecteer de tegel **apparaat en Cloud gegevens bewerken** om een nieuwe weer gave toe te voegen.
  
![Weer gaven-Azure IoT Edge](./media/tutorial-define-edge-device-type/editingdeviceandclouddata.png)

Voer de naam van de **omgevings sensor**van het formulier in.

Sleep de **naam** van de klant en de **laatste service datum** -Cloud naar de bestaande sectie op het formulier.
  
![Weer gaven-Azure IoT Edge](./media/tutorial-define-edge-device-type/views-properties.png)

Selecteer **Opslaan** om de weer gave op te slaan.

### <a name="generate-default-views"></a>Standaard weergaven genereren

De functie standaard weergaven genereren wordt niet ondersteund voor Azure IoT Edge sjablonen 

## <a name="publish-device-template"></a>Device-sjabloon publiceren

Voordat u een gesimuleerde omgevings sensor kunt maken, of als u een echte omgevings sensor wilt verbinden, moet u de sjabloon voor het apparaat publiceren.

Een sjabloon voor een apparaat publiceren:

1. Ga op de pagina **Apparaatinstellingen** naar uw sjabloon.

2. Selecteer **Publiceren**.
  
    ![Weer gaven-publiceren](./media/tutorial-define-edge-device-type/edgetemplatepublish.png)

1. Kies in het dialoog venster **een sjabloon voor het publiceren van een apparaat** de optie **publiceren**:
  
    ![Weer gaven-publiceren](./media/tutorial-define-edge-device-type/edgepublishtemplate.png)

Nadat een apparaat sjabloon is gepubliceerd, wordt deze weer gegeven op de pagina **apparaten** en aan de operator. U kunt in een sjabloon voor een gepubliceerd apparaat geen mogelijkheidsprofiel bewerken zonder een nieuwe versie te maken. U kunt echter wel updates maken voor de eigenschappen, aanpassingen en weer gaven van de cloud in een sjabloon voor gepubliceerde apparaten zonder versie beheer. Nadat u wijzigingen hebt aangebracht, selecteert u **publiceren** om deze wijzigingen naar uw operator te pushen.
  
![Weer gaven-publiceren](./media/tutorial-define-edge-device-type/publishedtemplate.png)

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

* Een nieuwe rand maken als een Leaf-sjabloon
* Modules genereren op basis van een geüpload implementatie manifest
* Een telemetrie en eigenschappen voor een complex type toevoegen
* Cloud eigenschappen maken.
* Aanpassingen maken.
* Definieer een visualisatie voor de telemetrie van het apparaat.
* De sjabloon voor uw edge-apparaat publiceren.

Nu u een sjabloon voor een apparaat in uw Azure IoT Central-toepassing hebt gemaakt, is dit de voorgestelde volgende stap:

> [!div class="nextstepaction"]
> [Apparaat verbinden](./tutorial-connect-pnp-device.md)
