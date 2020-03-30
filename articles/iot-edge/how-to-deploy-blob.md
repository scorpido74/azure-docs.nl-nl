---
title: Blob-opslag op module implementeren op uw apparaat - Azure IoT Edge
description: Implementeer een Azure Blob Storage-module op uw IoT Edge-apparaat om gegevens aan de rand op te slaan.
author: kgremban
ms.author: kgremban
ms.date: 3/10/2020
ms.topic: conceptual
ms.service: iot-edge
ms.reviewer: arduppal
ms.openlocfilehash: 04b145622a1a4237b576a1bb512b5f749f9c3823
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80133293"
---
# <a name="deploy-the-azure-blob-storage-on-iot-edge-module-to-your-device"></a>De Azure Blob Storage op de IoT Edge-module implementeren naar uw apparaat

Er zijn verschillende manieren om modules te implementeren op een IoT Edge-apparaat en ze werken allemaal voor Azure Blob Storage op IoT Edge-modules. De twee eenvoudigste methoden zijn het gebruik van de Azure-portal of Visual Studio Code-sjablonen.

## <a name="prerequisites"></a>Vereisten

- Een [IoT-hub](../iot-hub/iot-hub-create-through-portal.md) in uw Azure-abonnement.
- Een [IoT Edge-apparaat](how-to-register-device.md) met de IoT Edge-runtime geïnstalleerd.
- [Visual Studio Code](https://code.visualstudio.com/) en de [Azure IoT-hulpprogramma's](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) als deze worden geïmplementeerd vanuit Visual Studio Code.

## <a name="deploy-from-the-azure-portal"></a>Implementeren vanuit de Azure-portal

De Azure-portal begeleidt u bij het maken van een implementatiemanifest en het pushen van de implementatie naar een IoT Edge-apparaat.

### <a name="select-your-device"></a>Uw apparaat selecteren

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) en navigeer naar uw IoT-hub.
1. Selecteer **IoT Edge** in het menu.
1. Klik op de id van het doelapparaat in de lijst met apparaten.
1. Selecteer **Modules instellen**.

### <a name="configure-a-deployment-manifest"></a>Een implementatiemanifest configureren

Een implementatiemanifest is een JSON-document dat beschrijft welke modules moeten worden geïmplementeerd, hoe gegevens tussen de modules stromen en de gewenste eigenschappen van de moduletweeling. De Azure-portal heeft een wizard die u door het maken van een implementatiemanifest leidt. Het heeft drie stappen georganiseerd in tabbladen: **Modules,** **Routes**en **Review + Create**.

#### <a name="add-modules"></a>Modules toevoegen

1. Klik in de sectie **IoT Edge Modules** van de pagina op de vervolgkeuzelijst **Toevoegen** en selecteer **IoT Edge Module** om de pagina **IoT Edge Module toevoegen** weer te geven.

2. Geef op het tabblad **Module-instellingen** een naam voor de module op en geef vervolgens de containerafbeelding URI op:

   Voorbeelden:
  
   - **Naam IoT Edge-module**:`azureblobstorageoniotedge`
   - **Afbeelding URI**:`mcr.microsoft.com/azure-blob-storage:latest`

   ![Module Twee instellingen](./media/how-to-deploy-blob/addmodule-tab1.png)

   Selecteer niet **toevoegen** totdat u waarden hebt opgegeven in de tabbladen **Module-instellingen,** **Opties voor containermaken**en **Tweeinstellingen module,** zoals beschreven in deze procedure.

   > [!IMPORTANT]
   > Azure IoT Edge is hoofdlettergevoelig wanneer u naar modules belt en de Storage SDK is ook standaard in kleine letters. Hoewel de naam van de module in de [Azure Marketplace](how-to-deploy-modules-portal.md#deploy-modules-from-azure-marketplace) **AzureBlobStorageonIoTEdge**is, helpt het wijzigen van de naam in kleine letters ervoor te zorgen dat uw verbindingen met de Azure Blob Storage op IoT Edge-module niet worden onderbroken.

3. Open het tabblad **Opties voor containermaken.**

   ![Module Twee instellingen](./media/how-to-deploy-blob/addmodule-tab3.png)

   Kopieer en plak de volgende JSON in het vak, om opslagaccountgegevens en een mount voor de opslag op uw apparaat te verstrekken.
  
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

4. Werk de JSON die u hebt gekopieerd naar **opties voor containermaken** bij met de volgende informatie:

   - Vervang `<your storage account name>` door een naam die u zich herinneren. Accountnamen moeten 3 tot 24 tekens lang zijn, met kleine letters en cijfers. Geen spaties.

   - Vervang `<your storage account key>` door een 64-byte base64-toets. U een sleutel genereren met tools zoals [GeneratePlus.](https://generate.plus/en/base64) U gebruikt deze referenties om toegang te krijgen tot de blob-opslag van andere modules.

   - Vervang `<storage mount>` volgens uw containerbesturingssysteem. Geef de naam van een [volume](https://docs.docker.com/storage/volumes/) of het absolute pad op naar een bestaande map op uw IoT Edge-apparaat waar de blobmodule de gegevens opslaat. De opslagberg brengt een locatie op uw apparaat in kaart die u aan een ingestelde locatie in de module verstrekt.

     - Voor Linux-containers is * \<de indeling opslagpad of volume>:/blobroot*. Bijvoorbeeld
         - gebruik [volume mount](https://docs.docker.com/storage/volumes/): **my-volume:/blobroot**
         - gebruik [binding mount](https://docs.docker.com/storage/bind-mounts/): **/srv/containerdata:/blobroot**. Zorg ervoor dat u de stappen volgt om [de containergebruiker toegang te verlenen](how-to-store-data-blob.md#granting-directory-access-to-container-user-on-linux)
     - Voor Windows-containers is * \<de indeling opslagpad of volume>:C:/BlobRoot*. Bijvoorbeeld
         - gebruik [volume mount](https://docs.docker.com/storage/volumes/): **my-volume:C:/blobroot**.
         - [bindingshouder gebruiken:](https://docs.docker.com/storage/bind-mounts/) **C:/ContainerData:C:/BlobRoot**.
         - In plaats van uw lokale schijf te gebruiken, u uw smb-netwerklocatie in kaart brengen, voor meer informatie, zie [het gebruik van SMB-share als uw lokale opslag](how-to-store-data-blob.md#using-smb-share-as-your-local-storage)

     > [!IMPORTANT]
     > Wijzig de tweede helft van de opslagbergwaarde niet, wat wijst op een specifieke locatie in de module. De opslagmount moet altijd eindigen met **:/blobroot** voor Linux-containers en **:C:/BlobRoot** voor Windows-containers.

5. Kopieer op het tabblad **Twee instellingen voor modules** het volgende JSON en plak deze in het vak.

   ![Module Twee instellingen](./media/how-to-deploy-blob/addmodule-tab4.png)

   Configureer elke eigenschap met een geschikte waarde, zoals aangegeven door de tijdelijke aanduidingen. Als u de IoT Edge-simulator gebruikt, stelt u de waarden in op de gerelateerde omgevingsvariabelen voor deze eigenschappen, zoals beschreven door [deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) en [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties).

   ```json
   {
     "deviceAutoDeleteProperties": {
       "deleteOn": <true, false>,
       "deleteAfterMinutes": <timeToLiveInMinutes>,
       "retainWhileUploading": <true,false>
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
       "deleteAfterUpload": <true,false>
     }
   }
   ```

   Zie Module [Twin](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin)bewerken voor informatie over het configureren van deviceToCloudUploadProperties en deviceAutoDeleteProperties nadat de module is geïmplementeerd. Zie Gewenste eigenschappen definiëren of bijwerken voor meer informatie over de [gewenste eigenschappen.](module-composition.md#define-or-update-desired-properties)

6. Selecteer **Toevoegen**.

7. Selecteer **Volgende: Routes** om door te gaan naar de sectie routes.

#### <a name="specify-routes"></a>Routes opgeven

Bewaar de standaardroutes en selecteer **Volgende: Controleren + maken** om door te gaan naar de beoordelingssectie.

#### <a name="review-deployment"></a>Implementatie controleren

In de sectie beoordeling ziet u het JSON-implementatiemanifest dat is gemaakt op basis van uw selecties in de vorige twee secties. Er zijn ook twee modules aangegeven dat je niet hebt toegevoegd: **$edgeAgent** en **$edgeHub**. Deze twee modules vormen de [IoT Edge-runtime](iot-edge-runtime.md) en zijn vereist standaardinstellingen voor elke implementatie.

Controleer uw implementatiegegevens en selecteer **Vervolgens Maken**.

### <a name="verify-your-deployment"></a>Uw implementatie verifiëren

Nadat u de implementatie hebt gemaakt, keert u terug naar de **IoT Edge-pagina** van uw IoT-hub.

1. Selecteer het IoT Edge-apparaat dat u met de implementatie hebt getarget om de details te openen.
1. Controleer in de apparaatgegevens of de blob-opslagmodule wordt vermeld als **opgegeven in implementatie** en gerapporteerd per **apparaat.**

Het kan even duren voordat de module op het apparaat is gestart en vervolgens wordt gerapporteerd aan IoT Hub. Vernieuw de pagina om een bijgewerkte status te zien.

## <a name="deploy-from-visual-studio-code"></a>Implementeren vanuit Visual Studio Code

Azure IoT Edge biedt sjablonen in Visual Studio Code om u te helpen randoplossingen te ontwikkelen. Gebruik de volgende stappen om een nieuwe IoT Edge-oplossing te maken met een blob-opslagmodule en om het implementatiemanifest te configureren.

1. Selecteer**Opdrachtpalet** **weergeven** > .

1. Voer in het opdrachtpalet de opdracht **Azure IoT Edge: New IoT Edge solution** in en voer deze uit.

   ![Nieuwe IoT Edge-oplossing uitvoeren](./media/how-to-develop-csharp-module/new-solution.png)

   Volg de aanwijzingen in het opdrachtpalet om uw oplossing te maken.

   | Veld | Waarde |
   | ----- | ----- |
   | Map selecteren | Kies de locatie op uw ontwikkelmachine voor Visual Studio Code om de oplossingsbestanden te maken. |
   | Een naam opgeven voor de oplossing | Voer een beschrijvende naam voor de oplossing in of accepteer de standaardnaam **EdgeSolution**. |
   | Modulesjabloon selecteren | Kies **Bestaande module (Voer de URL van volledige afbeelding in).** |
   | Een modulenaam opgeven | Voer een all-kleine naam in voor uw module, zoals **azureblobstorageoniotedge.**<br/><br/>Het is belangrijk om een kleine naam te gebruiken voor de Azure Blob Storage op IoT Edge-module. IoT Edge is hoofdlettergevoelig wanneer u naar modules verwijst en de Storage SDK standaard in kleine letters. |
   | Docker-afbeelding voor de module bieden | Geef de afbeelding URI: **mcr.microsoft.com/azure-blob-storage:latest** |

   Visual Studio Code neemt de door u verstrekte informatie, maakt een IoT Edge-oplossing en laadt deze vervolgens in een nieuw venster. De oplossingssjabloon maakt een sjabloon voor implementatiemanifesten die de afbeelding van de blob-opslagmodule bevat, maar u moet de opties voor het maken van de module configureren.

1. Open *deployment.template.json* in uw nieuwe oplossingswerkruimte en zoek de **sectie modules.** Breng de volgende configuratiewijzigingen aan:

   1. Verwijder de **module SimulatedTemperatureSensor,** omdat deze implementatie niet nodig is.

   1. Kopieer en plak de `createOptions` volgende code in het veld:

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

      ![Module-createOptions bijwerken - Visual Studio Code](./media/how-to-deploy-blob/create-options.png)

1. Vervang `<your storage account name>` door een naam die u zich herinneren. Accountnamen moeten 3 tot 24 tekens lang zijn, met kleine letters en cijfers. Geen spaties.

1. Vervang `<your storage account key>` door een 64-byte base64-toets. U een sleutel genereren met tools zoals [GeneratePlus.](https://generate.plus/en/base64) U gebruikt deze referenties om toegang te krijgen tot de blob-opslag van andere modules.

1. Vervang `<storage mount>` volgens uw containerbesturingssysteem. Geef de naam van een [volume](https://docs.docker.com/storage/volumes/) of het absolute pad op naar een map op uw IoT Edge-apparaat waar u wilt dat de blobmodule de gegevens opslaat. De opslagberg brengt een locatie op uw apparaat in kaart die u aan een ingestelde locatie in de module verstrekt.  

     - Voor Linux-containers is * \<de indeling opslagpad of volume>:/blobroot*. Bijvoorbeeld
         - gebruik [volume mount](https://docs.docker.com/storage/volumes/): **my-volume:/blobroot**
         - gebruik [binding mount](https://docs.docker.com/storage/bind-mounts/): **/srv/containerdata:/blobroot**. Zorg ervoor dat u de stappen volgt om [de containergebruiker toegang te verlenen](how-to-store-data-blob.md#granting-directory-access-to-container-user-on-linux)
     - Voor Windows-containers is * \<de indeling opslagpad of volume>:C:/BlobRoot*. Bijvoorbeeld
         - gebruik [volume mount](https://docs.docker.com/storage/volumes/): **my-volume:C:/blobroot**.
         - [bindingshouder gebruiken:](https://docs.docker.com/storage/bind-mounts/) **C:/ContainerData:C:/BlobRoot**.
         - In plaats van uw lokale schijf te gebruiken, u uw smb-netwerklocatie in kaart brengen, voor meer informatie zie [het gebruik van SMB-share als uw lokale opslag](how-to-store-data-blob.md#using-smb-share-as-your-local-storage)

     > [!IMPORTANT]
     > Wijzig de tweede helft van de opslagbergwaarde niet, wat wijst op een specifieke locatie in de module. De opslagmount moet altijd eindigen met **:/blobroot** voor Linux-containers en **:C:/BlobRoot** voor Windows-containers.

1. Configureer [deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) en [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties) voor uw module door de volgende JSON toe te voegen aan het bestand *deployment.template.json.* Configureer elke eigenschap met de juiste waarde en sla het bestand op. Als u de IoT Edge-simulator gebruikt, stelt u de waarden in op de gerelateerde omgevingsvariabelen voor deze eigenschappen, die u vinden in de uitlegsectie van [deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) en [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties)

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

   ![de gewenste eigenschappen instellen voor azureblobstorageoniotedge - Visual Studio Code](./media/how-to-deploy-blob/devicetocloud-deviceautodelete.png)

   Zie Module [Twin](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin)bewerken voor informatie over het configureren van deviceToCloudUploadProperties en deviceAutoDeleteProperties nadat de module is geïmplementeerd. Zie [EdgeAgent gewenste eigenschappen](module-edgeagent-edgehub.md#edgeagent-desired-properties)voor meer informatie over opties voor het maken van containers, het beleid opnieuw starten en de gewenste status.

1. Sla het bestand *deployment.template.json* op.

1. Klik met de rechtermuisknop op **deployment.template.json** en selecteer **IoT Edge-implementatiemanifest genereren**.

1. Visual Studio Code neemt de informatie die u hebt opgegeven in *deployment.template.json* en gebruikt deze om een nieuw implementatiemanifestbestand te maken. Het implementatiemanifest wordt gemaakt in een nieuwe **config-map** in uw oplossingswerkruimte. Zodra u dat bestand hebt, u de stappen volgen in [Azure IoT Edge-modules implementeren vanuit Visual Studio Code](how-to-deploy-modules-vscode.md) of Azure [IoT Edge-modules implementeren met Azure CLI 2.0.](how-to-deploy-modules-cli.md)

## <a name="deploy-multiple-module-instances"></a>Meerdere module-exemplaren implementeren

Als u meerdere exemplaren van de Azure Blob Storage op IoT Edge-module wilt `HostPort` implementeren, moet u een ander opslagpad bieden en de waarde wijzigen waaraan de module zich bindt. De blob-opslagmodules stellen poort 11002 altijd bloot in de container, maar u aangeven aan welke poort het is gebonden aan de host.

Opties **voor containermaken bewerken** (in de Azure-portal) of het veld **createOptions** (in het bestand `HostPort` *deployment.template.json* in Visual Studio Code) om de waarde te wijzigen:

```json
"PortBindings":{
  "11002/tcp": [{"HostPort":"<port number>"}]
}
```

Wanneer u verbinding maakt met extra blobopslagmodules, wijzigt u het eindpunt om naar de bijgewerkte hostpoort te wijzen.

## <a name="configure-proxy-support"></a>Proxyondersteuning configureren

Als uw organisatie een proxyserver gebruikt, moet u proxy-ondersteuning configureren voor de edgeAgent- en edgeHub-runtime-modules. Dit proces omvat twee taken:

- Configureer de runtime daemons en de IoT Edge-agent op het apparaat.
- Stel de HTTPS_PROXY omgevingsvariabele in voor modules in het JSON-bestand van het implementatiemanifest.

Dit proces wordt beschreven in [Een IoT Edge-apparaat configureren om te communiceren via een proxyserver.](how-to-configure-proxy-support.md)

Daarnaast vereist een blob-opslagmodule ook de HTTPS_PROXY-instelling in het manifestimplementatiebestand. U het implementatiemanifestbestand direct bewerken of de Azure-portal gebruiken.

1. Navigeer naar uw Iot-hub in de Azure-portal en selecteer **Iot Edge** in het menu van het linkerdeelvenster.

1. Selecteer het apparaat met de module om te configureren.

1. Selecteer **Modules instellen**.

1. Selecteer in het gedeelte **IoT Edge Modules** van de pagina de blob-opslagmodule.

1. Selecteer op de pagina **IoT Edge Module** bijwerken het tabblad **Omgevingsvariabelen.**

1. Toevoegen `HTTPS_PROXY` voor de **url van** de naam en uw proxy voor de **waarde**.

      ![HTTPS_PROXY omgevingsvariabele instellen](./media/how-to-deploy-blob/https-proxy-config.png)

1. Klik **op Bijwerken**en vervolgens controleren + **Maken**.

1. Houd er rekening mee dat de proxy wordt toegevoegd aan de module in het implementatiemanifest en selecteer **Maken**.

1. Controleer de instelling door de module te selecteren op de pagina apparaatdetails en selecteer op het onderste gedeelte van de pagina Details van **iot-randmodules** het tabblad **Omgevingsvariabelen.**

      ![HTTPS_PROXY omgevingsvariabele instellen](./media/how-to-deploy-blob/verify-proxy-config.png)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [Azure Blob Storage op IoT Edge](how-to-store-data-blob.md).

Zie [Begrijpen hoe IoT Edge-modules kunnen worden gebruikt, geconfigureerd en hergebruikt](module-composition.md)voor meer informatie over hoe implementatiemanifesten werken en hoe u deze maken.
