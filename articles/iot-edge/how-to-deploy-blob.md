---
title: De Azure Blob Storage-module implementeren op apparaten-Azure IoT Edge | Microsoft Docs
description: Een Azure Blob Storage-module implementeren op uw IoT Edge-apparaat voor het opslaan van gegevens aan de rand.
author: arduppal
ms.author: arduppal
ms.date: 08/07/2019
ms.topic: article
ms.service: iot-edge
ms.custom: seodec18
ms.reviewer: arduppal
manager: mchad
ms.openlocfilehash: e5420bbe7f65dcef4997d909b3bc4ede00dd9902
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/10/2019
ms.locfileid: "70844224"
---
# <a name="deploy-the-azure-blob-storage-on-iot-edge-module-to-your-device"></a>De Azure Blob Storage op IoT Edge module implementeren op uw apparaat

Er zijn verschillende manieren om modules op een IoT Edge apparaat te implementeren en ze werken allemaal voor Azure Blob Storage op IoT Edge modules. De twee meest eenvoudige methoden zijn de Azure portal of Visual Studio Code-sjablonen te gebruiken.

## <a name="prerequisites"></a>Vereisten

- Een [IoT-hub](../iot-hub/iot-hub-create-through-portal.md) in uw Azure-abonnement.
- Een [IoT Edge-apparaat](how-to-register-device-portal.md) met IoT Edge-runtime geïnstalleerd.
- [Visual Studio code](https://code.visualstudio.com/) en de [Azure IOT-hulpprogram ma's](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) bij het implementeren vanuit Visual Studio code.

## <a name="deploy-from-the-azure-portal"></a>Implementeren vanuit de Azure Portal

De Azure Portal begeleidt u bij het maken van een implementatie manifest en het pushen van de implementatie naar een IoT Edge-apparaat.

### <a name="select-your-device"></a>Selecteer uw apparaat

1. Aanmelden bij de [Azure-portal](https://portal.azure.com) en navigeer naar uw IoT-hub.
1. Selecteer **IoT Edge** in het menu.
1. Klik op de ID van het doelapparaat uit de lijst met apparaten.
1. Selecteer **Modules instellen**.

### <a name="configure-a-deployment-manifest"></a>Een manifest van de implementatie configureren

Het manifest voor een implementatie is een JSON-document waarin wordt beschreven welke modules te implementeren, hoe gegevens stromen tussen de modules, en de gewenste eigenschappen van de moduledubbels. De Azure Portal bevat een wizard die u helpt bij het maken van een implementatie manifest, in plaats van het JSON-document hand matig te bouwen. De oplossing bestaat uit drie stappen: **Modules toevoegen**, **routes opgeven**en de **implementatie controleren**.

#### <a name="add-modules"></a>Modules toevoegen

1. In de **implementatie modules** sectie van de pagina, selecteer **toevoegen**.

1. Selecteer **IOT Edge module**uit de typen modules in de vervolg keuzelijst.

1. Geef een naam op voor de module en geef vervolgens de container installatie kopie op:

   - **Naam** -azureblobstorageoniotedge
   - **URI van installatie kopie** -MCR.Microsoft.com/Azure-Blob-Storage:Latest

   > [!IMPORTANT]
   > Azure IoT Edge is hoofdletter gevoelig wanneer u aanroepen naar modules uitvoert en de opslag-SDK is standaard ingesteld op kleine letters. Hoewel de naam van de module in [Azure Marketplace](how-to-deploy-modules-portal.md#deploy-modules-from-azure-marketplace) **AzureBlobStorageonIoTEdge**is, is het wijzigen van de naam in kleine letters handig om ervoor te zorgen dat uw verbindingen met de Azure Blob Storage op IOT Edge module niet worden onderbroken.

1. De standaard **container maken opties** waarden definiëren de poort bindingen die uw container nodig heeft, maar u moet ook de gegevens van uw opslag account en een koppeling voor de opslag op uw apparaat toevoegen. Vervang de standaard-JSON in de portal door de volgende JSON:

   ```json
   {
     "Env":[
       "LOCAL_STORAGE_ACCOUNT_NAME=<your storage account name>",
       "LOCAL_STORAGE_ACCOUNT_KEY=<your storage account key>"
     ],
     "HostConfig":{
       "Binds":[
           "<storage mount>"
       ],
       "PortBindings":{
         "11002/tcp":[{"HostPort":"11002"}]
       }
     }
   }
   ```

1. Werk de JSON bij die u hebt gekopieerd met de volgende gegevens:

   - Vervang `<your storage account name>` door een naam die u kunt onthouden. Account namen moeten 3 tot 24 tekens lang zijn, met kleine letters en cijfers. Geen spaties.

   - Vervang `<your storage account key>` door een base64-sleutel van 64-bytes. U kunt een sleutel met de hulpprogramma's zoals genereren [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64). U gebruikt deze referenties voor toegang tot de blob-opslag van andere modules.

   - Vervangen `<storage mount>` door het besturings systeem van de container. Geef de naam van een [volume](https://docs.docker.com/storage/volumes/) of het absolute pad naar een map op uw IoT Edge-apparaat dat is waar u de blob-module voor het opslaan van de gegevens. De opslag koppeling wijst een locatie op het apparaat toe die u hebt opgegeven voor een set-locatie in de module.

     - Voor Linux-containers is  *\<de indeling een opslagpad of volume >:/blobroot*. Bijvoorbeeld
         - [volume koppeling](https://docs.docker.com/storage/volumes/)gebruiken: **mijn-volume:/blobroot** 
         - [binding koppelen](https://docs.docker.com/storage/bind-mounts/)gebruiken: **/SRV/containerdata:/blobroot**. Volg de stappen om [Directory toegang te verlenen aan de container gebruiker](how-to-store-data-blob.md#granting-directory-access-to-container-user-on-linux)
     - Voor Windows-containers is  *\<de indeling een opslagpad of volume >: C:/BlobRoot*. Bijvoorbeeld
         - [volume koppeling](https://docs.docker.com/storage/volumes/)gebruiken: **mijn-volume: C:/blobroot**. 
         - [binding koppelen](https://docs.docker.com/storage/bind-mounts/)gebruiken: **C:/ContainerData: C:/BlobRoot**.
         - In plaats van uw lokale station te gebruiken, kunt u uw SMB-netwerk locatie toewijzen voor meer informatie Raadpleeg [SMB share gebruiken als uw lokale opslag](how-to-store-data-blob.md#using-smb-share-as-your-local-storage)

     > [!IMPORTANT]
     > Wijzig de tweede helft van de opslag koppelings waarde, die verwijst naar een specifieke locatie in de module. De opslag koppeling moet altijd eindigen op **:/blobroot** for Linux-containers en **: C:/blobroot** voor Windows-containers.

1. Stel de eigenschappen [deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) en [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties) in voor uw module door de volgende JSON te kopiëren en deze te plakken in het vak **Stel de gewenste eigenschappen van de module** . Configureer elke eigenschap met een geschikte waarde, sla deze op en ga door met de implementatie. Als u de IoT Edge Simulator gebruikt, stelt u de waarden in op de verwante omgevings variabelen voor deze eigenschappen, die u kunt vinden in de sectie uitleg van [deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) en [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties).

   ```json
   {
     "properties.desired": {
       "deviceAutoDeleteProperties": {
         "deleteOn": <true, false>,
         "deleteAfterMinutes": <timeToLiveInMinutes>,
         "retainWhileUploading":<true,false>
       },
       "deviceToCloudUploadProperties": {
         "uploadOn": <true, false>,
         "uploadOrder": "<NewestFirst, OldestFirst>",
         "cloudStorageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>; EndpointSuffix=<your end point suffix>",
         "storageContainersForUpload": {
           "<source container name1>": {
             "target": "<target container name1>"
           }
         },
         "deleteAfterUpload":<true,false>
       }
     }
   }

      ```

   ![opties voor het maken van een container, de eigenschappen deviceAutoDeleteProperties en deviceToCloudUploadProperties instellen](./media/how-to-deploy-blob/iotedge-custom-module.png)

   Voor informatie over het configureren van deviceToCloudUploadProperties en deviceAutoDeleteProperties nadat de module is geïmplementeerd, raadpleegt u [de module twee bewerken](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin). Zie [gewenste eigenschappen definiëren of bijwerken](module-composition.md#define-or-update-desired-properties)voor meer informatie over gewenste eigenschappen.

1. Selecteer **Opslaan**.

1. Selecteer **volgende** om door te gaan naar de sectie routes.

#### <a name="specify-routes"></a>Routes opgeven

Behoud de standaard routes en selecteer **volgende** om door te gaan naar de sectie beoordeling.

#### <a name="review-deployment"></a>Implementatie bekijken

De revisie sectie ziet u de JSON-implementatie manifest dat is gemaakt op basis van uw selecties in de vorige twee secties. Er zijn ook twee modules gedeclareerd die u niet hebt toegevoegd: **$edgeAgent** en **$edgeHub**. Deze twee modules waaruit de [IoT Edge-runtime](iot-edge-runtime.md) en zijn vereist standaardwaarden in elke implementatie.

Lees de informatie van uw implementatie, en selecteer vervolgens **indienen**.

### <a name="verify-your-deployment"></a>Uw implementatie verifiëren

Nadat u de implementatie hebt verzonden, keert u terug naar de pagina **IOT Edge** van uw IOT-hub.

1. Selecteer het IoT Edge apparaat waarop de implementatie is gericht om de details ervan te openen.
1. Controleer in de details van het apparaat of de module Blob Storage wordt vermeld als **opgegeven in de implementatie** en wordt **gerapporteerd door het apparaat**.

Het kan even duren voordat de module op het apparaat is gestart en vervolgens weer aan IoT Hub is gemeld. Vernieuw de pagina om de bijgewerkte status weer te geven.

## <a name="deploy-from-visual-studio-code"></a>Implementeren vanuit Visual Studio code

Azure IoT Edge zorgt voor sjablonen in Visual Studio Code om u te helpen bij het edge-oplossingen te ontwikkelen. Gebruik de volgende stappen om een nieuwe IoT Edge-oplossing te maken met een Blob Storage-module en om het implementatie manifest te configureren.

1. Selecteer **weergave** > **Command Palette**.

1. Voer in het opdrachtpalet de opdracht **Azure IoT Edge: New IoT Edge solution** in en voer deze uit.

   ![Nieuwe IoT Edge oplossing uitvoeren](./media/how-to-develop-csharp-module/new-solution.png)

   Volg de aanwijzingen in het opdrachtpalet om uw oplossing te maken.

   | Veld | Value |
   | ----- | ----- |
   | Map selecteren | Kies de locatie op uw ontwikkel computer voor Visual Studio code om de oplossings bestanden te maken. |
   | Een naam opgeven voor de oplossing | Voer een beschrijvende naam voor de oplossing in of accepteer de standaardnaam **EdgeSolution**. |
   | Modulesjabloon selecteren | Kies een **bestaande module (Voer de volledige URL van de installatie kopie in)** . |
   | Een modulenaam opgeven | Voer een naam in voor alle kleine letters voor uw module, zoals **azureblobstorageoniotedge**.<br /><br />Het is belang rijk dat u een kleine naam gebruikt voor de Azure-Blob Storage op IoT Edge module. IoT Edge is hoofdletter gevoelig bij het verwijzen naar modules en de opslag-SDK wordt standaard omgezet in kleine letters. |
   | Docker-installatie kopie voor de module opgeven | Geef de afbeeldings-URI op: **MCR.Microsoft.com/Azure-Blob-Storage:Latest** |

   Visual Studio code voert de door u verstrekte informatie, maakt een IoT Edge oplossing en laadt deze vervolgens in een nieuw venster. Sjabloon van de oplossing maakt een manifest implementatiesjabloon die uw blob storage-module-installatiekopie bevat, maar u wilt configureren van de module opties maken.

1. Open *deployment.template.json* in uw nieuwe werkruimte voor de oplossing en zoek de **modules** sectie. Breng de volgende configuratie wijzigingen aan:

   1. Verwijder de **SimulatedTemperatureSensor** -module, omdat deze niet nodig is voor deze implementatie.

   1. Kopieer de volgende code en plak deze in `createOptions` het veld:

      ```json
      "Env":[
       "LOCAL_STORAGE_ACCOUNT_NAME=<your storage account name>",
       "LOCAL_STORAGE_ACCOUNT_KEY=<your storage account key>"
      ],
      "HostConfig":{
        "Binds": ["<storage mount>"],
        "PortBindings":{
          "11002/tcp": [{"HostPort":"11002"}]
        }
      }
      ```

      ![Module createOptions-Visual Studio code bijwerken](./media/how-to-deploy-blob/create-options.png)

1. Vervang `<your storage account name>` door een naam die u kunt onthouden. Account namen moeten 3 tot 24 tekens lang zijn, met kleine letters en cijfers. Geen spaties.

1. Vervang `<your storage account key>` door een base64-sleutel van 64-bytes. U kunt een sleutel met de hulpprogramma's zoals genereren [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64). U gebruikt deze referenties voor toegang tot de blob-opslag van andere modules.

1. Vervangen `<storage mount>` door het besturings systeem van de container. Geef de naam van een [volume](https://docs.docker.com/storage/volumes/) of het absolute pad naar een map op uw IoT Edge-apparaat dat is waar u de blob-module voor het opslaan van de gegevens. De opslag koppeling wijst een locatie op het apparaat toe die u hebt opgegeven voor een set-locatie in de module.  

      
     - Voor Linux-containers is  *\<de indeling een opslagpad of volume >:/blobroot*. Bijvoorbeeld
         - [volume koppeling](https://docs.docker.com/storage/volumes/)gebruiken: **mijn-volume:/blobroot** 
         - [binding koppelen](https://docs.docker.com/storage/bind-mounts/)gebruiken: **/SRV/containerdata:/blobroot**. Volg de stappen om [Directory toegang te verlenen aan de container gebruiker](how-to-store-data-blob.md#granting-directory-access-to-container-user-on-linux)
     - Voor Windows-containers is  *\<de indeling een opslagpad of volume >: C:/BlobRoot*. Bijvoorbeeld
         - [volume koppeling](https://docs.docker.com/storage/volumes/)gebruiken: **mijn-volume: C:/blobroot**. 
         - [binding koppelen](https://docs.docker.com/storage/bind-mounts/)gebruiken: **C:/ContainerData: C:/BlobRoot**.
         - In plaats van uw lokale station te gebruiken, kunt u uw SMB-netwerk locatie toewijzen voor meer informatie Raadpleeg [SMB share gebruiken als uw lokale opslag](how-to-store-data-blob.md#using-smb-share-as-your-local-storage)

     > [!IMPORTANT]
     > Wijzig de tweede helft van de opslag koppelings waarde, die verwijst naar een specifieke locatie in de module. De opslag koppeling moet altijd eindigen op **:/blobroot** for Linux-containers en **: C:/blobroot** voor Windows-containers.

1. Configureer [deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) en [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties) voor uw module door de volgende JSON toe te voegen aan het bestand *Deployment. Temp late. json* . Configureer elke eigenschap met een geschikte waarde en sla het bestand op. Als u de IoT Edge Simulator gebruikt, stelt u de waarden in op de verwante omgevings variabelen voor deze eigenschappen, die u kunt vinden in de sectie uitleg van [deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) en [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties)

   ```json
   "<your azureblobstorageoniotedge module name>":{
     "properties.desired": {
       "deviceAutoDeleteProperties": {
         "deleteOn": <true, false>,
         "deleteAfterMinutes": <timeToLiveInMinutes>,
         "retainWhileUploading": <true, false>
       },
       "deviceToCloudUploadProperties": {
         "uploadOn": <true, false>,
         "uploadOrder": "<NewestFirst, OldestFirst>",
         "cloudStorageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>",
         "storageContainersForUpload": {
           "<source container name1>": {
             "target": "<target container name1>"
           }
         },
         "deleteAfterUpload": <true, false>
       }
     }
   }
   ```

   ![gewenste eigenschappen instellen voor azureblobstorageoniotedge-Visual Studio code](./media/how-to-deploy-blob/devicetocloud-deviceautodelete.png)

   Voor informatie over het configureren van deviceToCloudUploadProperties en deviceAutoDeleteProperties nadat de module is geïmplementeerd, raadpleegt u [de module twee bewerken](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin). Zie [EdgeAgent gewenste eigenschappen](module-edgeagent-edgehub.md#edgeagent-desired-properties)voor meer informatie over de opties voor het maken van containers, het opnieuw opstarten van beleid en de gewenste status.

1. Sla het bestand *deployment.template.json* op.

1. Met de rechtermuisknop op **deployment.template.json** en selecteer **genereren IoT Edge-implementatie manifest**.

1. Visual Studio code maakt gebruik van de informatie die u hebt verstrekt in *implementatie. Temp late. json* en gebruikt deze om een nieuw implementatie manifest bestand te maken. Het manifest van de implementatie wordt gemaakt in een nieuwe **config** map in de werkruimte van uw oplossing. Zodra u dat bestand hebt, kunt u de stappen in [implementeren Azure IoT Edge-modules van Visual Studio Code](how-to-deploy-modules-vscode.md) of [implementeren Azure IoT Edge-modules met de Azure CLI 2.0](how-to-deploy-modules-cli.md).

## <a name="deploy-multiple-module-instances"></a>Meerdere module-exemplaren implementeren

Als u meerdere exemplaren van de Azure Blob Storage op IOT Edge module wilt implementeren, moet u een ander opslagpad opgeven en de `HostPort` waarde wijzigen waaraan de module is gekoppeld. De blob-opslag-modules altijd poort 11002 in de container beschikbaar, maar u kunt aangeven welke poort gebonden aan op de host.

Bewerk de **container Create-opties** (in de Azure Portal) of het veld **createOptions** (in het bestand *Deployment. sjabloon. json* in Visual Studio code) om `HostPort` de waarde te wijzigen:

```json
"PortBindings":{
  "11002/tcp": [{"HostPort":"<port number>"}]
}
```

Wanneer u verbinding met aanvullende blob storage-modules maken, wijzigt u het eindpunt om te verwijzen naar de bijgewerkte host-poort.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over [Azure Blob Storage op IOT Edge](how-to-store-data-blob.md)

Zie voor meer informatie over hoe implementatie werk manifesten en hoe ze worden gemaakt, [te begrijpen hoe IoT Edge-modules kunnen worden gebruikt, geconfigureerd en opnieuw gebruikt](module-composition.md).
