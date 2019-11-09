---
title: Zelf studie voor het ontwikkelen van C-module voor Windows-Azure IoT Edge | Microsoft Docs
description: In deze zelf studie wordt uitgelegd hoe u een IoT Edge module maakt met C-code en deze implementeert op een Windows-apparaat met IoT Edge
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 05/28/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: c098b67ab2782fa3cf29b5b19aa198f899ba69c0
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/09/2019
ms.locfileid: "73890617"
---
# <a name="tutorial-develop-a-c-iot-edge-module-for-windows-devices"></a>Zelf studie: een C IoT Edge-module ontwikkelen voor Windows-apparaten

Gebruik Visual Studio om C-code te ontwikkelen en te implementeren op een Windows-apparaat met Azure IoT Edge. 

U kunt Azure IoT Edge-modules gebruiken voor het implementeren van code die uw bedrijfslogica rechtstreeks op uw IoT Edge-apparaten implementeert. In deze zelfstudie leert u een IoT Edge-module te maken die sensorgegevens filtert. In deze zelfstudie leert u het volgende:    

> [!div class="checklist"]
> * Gebruik Visual Studio om een IoT Edge module te maken die is gebaseerd op de C SDK.
> * Gebruik Visual Studio en docker om een docker-installatie kopie te maken en te publiceren naar het REGI ster.
> * De module implementeren op uw IoT Edge-apparaat.
> * Gegenereerde gegevens weergeven.

De IoT Edge-module die u maakt in deze zelfstudie filtert de temperatuurgegevens die door uw apparaat worden gegenereerd. Er worden alleen gegevens upstream gezonden als de temperatuur boven een opgegeven drempelwaarde komt. Dit soort analyse is nuttig om de hoeveelheid gegevens te reduceren die worden gecommuniceerd naar en worden opgeslagen in de cloud. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Oplossings bereik

In deze zelf studie wordt gedemonstreerd hoe u een module ontwikkelt in **C** met behulp van **Visual Studio 2019**en hoe u deze implementeert op een **Windows-apparaat**. Als u modules voor Linux-apparaten ontwikkelt, gaat u in plaats daarvan naar [een C IOT Edge-module ontwikkelen voor Linux-apparaten](tutorial-c-module.md) . 

Gebruik de volgende tabel om inzicht te krijgen in de opties voor het ontwikkelen en implementeren van C-modules op Windows-apparaten: 

| C | Visual Studio Code | Visual Studio 2017/2019 | 
| -- | ------------------ | ------------------ |
| **Windows AMD64** |  | ![C-modules ontwikkelen voor WinAMD64 in Visual Studio](./media/tutorial-c-module/green-check.png) |

## <a name="prerequisites"></a>Vereisten

Voordat u met deze zelf studie begint, moet u de vorige zelf studie hebben door lopen om uw ontwikkel omgeving in te stellen voor Windows-container ontwikkeling: [ontwikkel IOT Edge-modules voor Windows-apparaten](tutorial-develop-for-windows.md). Nadat u deze zelf studie hebt voltooid, moet u aan de volgende vereisten voldoen: 

* Een gratis of reguliere [IoT Hub](../iot-hub/iot-hub-create-through-portal.md)-laag in Azure.
* Een [Windows-apparaat met Azure IOT Edge](quickstart.md).
* Een container register, zoals [Azure container Registry](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio 2019](https://docs.microsoft.com/visualstudio/install/install-visual-studio) geconfigureerd met de extensie [Azure IOT Edge-hulpprogram ma's](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) .
* [Docker Desktop](https://docs.docker.com/docker-for-windows/install/) is geconfigureerd om Windows-containers uit te voeren.
* Installeer de Azure IoT C-SDK voor Windows x64 via vcpkg:

   ```powershell
   git clone https://github.com/Microsoft/vcpkg
   cd vcpkg
   .\bootstrap-vcpkg.bat
   .\vcpkg install azure-iot-sdk-c:x64-windows
   .\vcpkg --triplet x64-windows integrate install
   ```
   
> [!TIP]
> Als u Visual Studio 2017 (versie 15,7 of hoger) gebruikt, downloadt en installeert u [Azure IOT Edge-Hulpprogram ma's](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) voor VS 2017 van de Visual Studio Marketplace

## <a name="create-a-module-project"></a>Een module project maken

Met de volgende stappen maakt u een IoT Edge module project dat is gebaseerd op de C SDK met behulp van Visual Studio en de uitbrei ding Azure IoT Edge tools. Zodra u een project sjabloon hebt gemaakt, voegt u nieuwe code toe zodat de module berichten filtert op basis van de gerapporteerde eigenschappen. 

### <a name="create-a-new-project"></a>Een nieuw project maken

Maak een C-oplossingssjabloon die u met uw eigen code kunt aanpassen.

1. Start Visual Studio 2019 en selecteer **Nieuw project maken**.

2. Zoek in het venster Nieuw project **IOT Edge** project en kies het **Azure IOT Edge (Windows amd64)-** project. Klik op **Volgende**. 

   ![Een nieuw Azure IoT Edge project maken](./media/tutorial-c-module-windows/new-project.png)

3. In het venster uw nieuwe project configureren wijzigt u de naam van het project en de oplossing in een beschrijvende soort, zoals **CTutorialApp**. Klik op **maken** om het project te maken. 

   ![Een nieuw Azure IoT Edge project configureren](./media/tutorial-c-module-windows/configure-project.png)

4. Configureer in het venster IoT Edge toepassing en module het project met de volgende waarden: 

   | Veld | Waarde |
   | ----- | ----- |
   | Een sjabloon selecteren | Selecteer **C-module**. | 
   | Naam van module Project | Geef de module de naam **CModule**. | 
   | Docker-afbeeldings opslagplaats | Een opslagplaats voor afbeeldingen bevat de naam van het containerregister en de naam van uw containerafbeelding. De container installatie kopie wordt vooraf ingevuld op basis van de waarde van de module Project naam. Vervang **localhost:5000** door de waarde van de aanmeldingsserver uit uw Azure-containerregister. U vindt de aanmeldingsserver op de overzichtspagina van het containerregister in de Azure-portal. <br><br> De uiteindelijke opslagplaats voor de installatiekopie ziet er ongeveer als volgt uit: \<registernaam\>.azurecr.io/cmodule. |

   ![Uw project configureren voor doel apparaat, module type en container register](./media/tutorial-c-module-windows/add-application-and-module.png)

5. Selecteer **toevoegen** om het project te maken. 

### <a name="add-your-registry-credentials"></a>Uw registerreferenties toevoegen

Het implementatie manifest deelt de referenties voor uw container register met de IoT Edge runtime. De runtime heeft deze referenties nodig om uw persoonlijke installatiekopieën naar het IoT Edge-apparaat te halen. Gebruik de referenties uit de sectie **toegangs sleutels** van uw Azure container Registry. 

1. Open in Visual Studio Solution Explorer het bestand **Deployment. sjabloon. json** . 

2. Zoek de eigenschap **registryCredentials** in de gewenste eigenschappen van de $edgeAgent. 

3. Werk de eigenschap bij met uw referenties, met de volgende indeling: 

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "<username>",
       "password": "<password>",
       "address": "<registry name>.azurecr.io"
     }
   }
   ```

4. Sla het bestand deployment.template.json op. 

### <a name="update-the-module-with-custom-code"></a>De module bijwerken met aangepaste code

Met de standaard module code worden berichten ontvangen in een invoer wachtrij en door gegeven via een uitvoer wachtrij. We gaan enkele extra code toevoegen, zodat de module de berichten aan de rand verwerkt voordat deze naar IoT Hub worden doorgestuurd. Werk de module bij zodat deze de temperatuur gegevens in elk bericht analyseert en alleen het bericht verzendt naar IoT Hub als de Tempe ratuur een bepaalde drempel waarde overschrijdt. 


1. De gegevens van de sensor in dit scenario worden in JSON-indeling aangeleverd. Als u berichten wilt filteren in een JSON-indeling, moet u een JSON-bibliotheek voor C importeren. In deze zelfstudie wordt Parson gebruikt.

   1. Download de [Parson GitHub-opslagplaats](https://github.com/kgabis/parson). Kopieer de bestanden **Parson. c** en **Parson. h** naar het **CModule** -project.

   2. Open in Visual Studio het bestand **CMakeLists. txt** in de projectmap CModule. Importeer boven in het bestand de Parson-bestanden als een bibliotheek met de naam **my_parson**.

      ```
      add_library(my_parson
          parson.c
          parson.h
      )
      ```

   3. Voeg `my_parson` toe aan de lijst met bibliotheken in de sectie **target_link_libraries** van het bestand CMakeLists. txt.

   4. Sla het bestand **CMakeLists.txt** op.

   5. Open **CModule** > **Main. c**. Voeg aan de onderkant van de lijst met include-instructies een nieuwe toe om `parson.h` voor JSON-ondersteuning op te geven:

      ```c
      #include "parson.h"
      ```

2.  Voeg in het bestand **Main. c** een globale variabele toe met de naam `temperatureThreshold` naast de variabele messagesReceivedByInput1Queue. Deze variabele bepaalt de waarde die de gemeten temperatuur moet overschrijden voordat de gegevens naar IoT Hub worden verzonden.

    ```c
    static double temperatureThreshold = 25;
    ```

3. Zoek de functie `CreateMessageInstance` in Main. c. Vervang de interne instructie if-else door de volgende code waarmee een aantal regels van functionaliteit wordt toegevoegd: 

   ```c
   if ((messageInstance->messageHandle = IoTHubMessage_Clone(message)) == NULL)
   {
       free(messageInstance);
       messageInstance = NULL;
   }
   else
   {
       messageInstance->messageTrackingId = messagesReceivedByInput1Queue;
       MAP_HANDLE propMap = IoTHubMessage_Properties(messageInstance->messageHandle);
       if (Map_AddOrUpdate(propMap, "MessageType", "Alert") != MAP_OK)
       {
          printf("ERROR: Map_AddOrUpdate Failed!\r\n");
       }
   }
   ```

   De nieuwe code regels in de instructie else voegen een nieuwe eigenschap toe aan het bericht. Hiermee wordt het bericht als een waarschuwing labelen. Met deze code worden alle berichten als waarschuwingen gelabeld, omdat er functionaliteit wordt toegevoegd waarmee alleen berichten worden verzonden naar IoT Hub als er hoge Tempe raturen worden gerapporteerd. 

4. Zoek de functie `InputQueue1Callback` en vervang de volledige functie door de volgende code. Deze functie implementeert het feitelijke berichtenfilter. Wanneer een bericht wordt ontvangen, wordt gecontroleerd of de gerapporteerde Tempe ratuur de drempel waarde overschrijdt. Zo ja, dan wordt het bericht doorgestuurd via de uitvoer wachtrij. Als dat niet het geval is, wordt het bericht genegeerd. 

    ```c
    static unsigned char *bytearray_to_str(const unsigned char *buffer, size_t len)
    {
        unsigned char *ret = (unsigned char *)malloc(len + 1);
        memcpy(ret, buffer, len);
        ret[len] = '\0';
        return ret;
    }

    static IOTHUBMESSAGE_DISPOSITION_RESULT InputQueue1Callback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
    {
        IOTHUBMESSAGE_DISPOSITION_RESULT result;
        IOTHUB_CLIENT_RESULT clientResult;
        IOTHUB_MODULE_CLIENT_LL_HANDLE iotHubModuleClientHandle = (IOTHUB_MODULE_CLIENT_LL_HANDLE)userContextCallback;

        unsigned const char* messageBody;
        size_t contentSize;

        if (IoTHubMessage_GetByteArray(message, &messageBody, &contentSize) == IOTHUB_MESSAGE_OK)
        {
            messageBody = bytearray_to_str(messageBody, contentSize);
        } else
        {
            messageBody = "<null>";
        }

        printf("Received Message [%zu]\r\n Data: [%s]\r\n",
                messagesReceivedByInput1Queue, messageBody);

        // Check if the message reports temperatures higher than the threshold
        JSON_Value *root_value = json_parse_string(messageBody);
        JSON_Object *root_object = json_value_get_object(root_value);
        double temperature;

        // If temperature exceeds threshold, send to output1
        if (json_object_dotget_value(root_object, "machine.temperature") != NULL && (temperature = json_object_dotget_number(root_object, "machine.temperature")) > temperatureThreshold)
        {
            printf("Machine temperature %f exceeds threshold %f\r\n", temperature, temperatureThreshold);
            // This message should be sent to next stop in the pipeline, namely "output1".  What happens at "outpu1" is determined
            // by the configuration of the Edge routing table setup.
            MESSAGE_INSTANCE *messageInstance = CreateMessageInstance(message);
            if (NULL == messageInstance)
            {
                result = IOTHUBMESSAGE_ABANDONED;
            }
            else
            {
                printf("Sending message (%zu) to the next stage in pipeline\n", messagesReceivedByInput1Queue);

                clientResult = IoTHubModuleClient_LL_SendEventToOutputAsync(iotHubModuleClientHandle, messageInstance->messageHandle, "output1", SendConfirmationCallback, (void *)messageInstance);
                if (clientResult != IOTHUB_CLIENT_OK)
                {
                    IoTHubMessage_Destroy(messageInstance->messageHandle);
                    free(messageInstance);
                    printf("IoTHubModuleClient_LL_SendEventToOutputAsync failed on sending msg#=%zu, err=%d\n", messagesReceivedByInput1Queue, clientResult);
                    result = IOTHUBMESSAGE_ABANDONED;
                }
                else
                {
                    result = IOTHUBMESSAGE_ACCEPTED;
                }
            }
        }
        // If message does not exceed threshold, do not forward
        else
        {
            printf("Not sending message (%zu) to the next stage in pipeline.\r\n", messagesReceivedByInput1Queue);
            result = IOTHUBMESSAGE_ACCEPTED;
        }

        messagesReceivedByInput1Queue++;
        return result;
    }
    ```

5. Voeg een `moduleTwinCallback`-functie toe. Deze methode ontvangt updates van de gewenste eigenschappen van de moduledubbel en updatet de variabele **temperatureThreshold** naar dezelfde waarde. Alle modules hebben hun eigen moduledubbel, waardoor u rechtstreeks in de cloud de code kunt configureren die in een module wordt uitgevoerd.

    ```c
    static void moduleTwinCallback(DEVICE_TWIN_UPDATE_STATE update_state, const unsigned char* payLoad, size_t size, void* userContextCallback)
    {
        printf("\r\nTwin callback called with (state=%s, size=%zu):\r\n%s\r\n",
            MU_ENUM_TO_STRING(DEVICE_TWIN_UPDATE_STATE, update_state), size, payLoad);
        JSON_Value *root_value = json_parse_string(payLoad);
        JSON_Object *root_object = json_value_get_object(root_value);
        if (json_object_dotget_value(root_object, "desired.TemperatureThreshold") != NULL) {
            temperatureThreshold = json_object_dotget_number(root_object, "desired.TemperatureThreshold");
        }
        if (json_object_get_value(root_object, "TemperatureThreshold") != NULL) {
            temperatureThreshold = json_object_get_number(root_object, "TemperatureThreshold");
        }
    }
    ```

6. Zoek de functie `SetupCallbacksForModule`. Vervang de functie door de volgende code waarmee een **else if** -instructie wordt toegevoegd om te controleren of de module twee is bijgewerkt. 

   ```c
   static int SetupCallbacksForModule(IOTHUB_MODULE_CLIENT_LL_HANDLE iotHubModuleClientHandle)
   {
       int ret;

       if (IoTHubModuleClient_LL_SetInputMessageCallback(iotHubModuleClientHandle, "input1", InputQueue1Callback, (void*)iotHubModuleClientHandle) != IOTHUB_CLIENT_OK)
       {
           printf("ERROR: IoTHubModuleClient_LL_SetInputMessageCallback(\"input1\")..........FAILED!\r\n");
           ret = MU_FAILURE;
       }
       else if (IoTHubModuleClient_LL_SetModuleTwinCallback(iotHubModuleClientHandle, moduleTwinCallback, (void*)iotHubModuleClientHandle) != IOTHUB_CLIENT_OK)
       {
           printf("ERROR: IoTHubModuleClient_LL_SetModuleTwinCallback(default)..........FAILED!\r\n");
           ret = MU_FAILURE;
       }
       else
       {
           ret = 0;
       }

       return ret;
   }
   ```

7. Sla het bestand main.c op.

8. Open het bestand **Deployment. Temp late. json** . 

9. Voeg de moduledubbel CModule toe aan het distributiemanifest. Voeg de volgende JSON-inhoud onder aan de sectie `moduleContent` in, na de moduledubbel `$edgeHub`:

   ```json
   "CModule": {
       "properties.desired":{
           "TemperatureThreshold":25
       }
   }
   ```

   ![Dubbele CModule toevoegen aan implementatiesjabloon](./media/tutorial-c-module-windows/module-twin.png)

1. Sla het bestand **deployment.template.json** op.

## <a name="build-and-push-your-module"></a>Uw module bouwen en pushen

In de vorige sectie hebt u een IoT Edge oplossing gemaakt en code toegevoegd aan de **CModule** om berichten te filteren waarbij de gerapporteerde machine temperatuur onder de aanvaard bare drempel waarde ligt. Nu moet u de oplossing bouwen als een containerinstallatiekopie en deze naar het containerregister pushen. 

1. Gebruik de volgende opdracht om u aan te melden bij docker op uw ontwikkel computer. Meld u aan met de gebruikers naam, het wacht woord en de aanmeldings server vanuit uw Azure container Registry. U kunt deze waarden ophalen uit de sectie **toegangs sleutels** van het REGI ster in de Azure Portal.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Mogelijk wordt een beveiligings waarschuwing weer gegeven waarin wordt aanbevolen het gebruik van `--password-stdin`. Hoewel dat best practice wordt aanbevolen voor productie scenario's, valt het buiten het bereik van deze zelf studie. Zie voor meer informatie de [koppeling naar docker-aanmelding](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) .

2. Klik in de Solution Explorer van Visual Studio met de rechter muisknop op de naam van het project dat u wilt bouwen. De standaard naam is **AzureIotEdgeApp1** en omdat u een Windows-module bouwt, moet de extensie **Windows. amd64**zijn. 

3. Selecteer **Build-en Push IOT Edge-modules**. 

   Met de opdracht build en push worden drie bewerkingen gestart. Eerst wordt een nieuwe map gemaakt in de oplossing configuratie met de naam **config** met het volledige implementatie manifest, met de informatie in de implementatie sjabloon en andere oplossings bestanden. Ten tweede wordt `docker build` uitgevoerd om de container installatie kopie te bouwen op basis van de juiste dockerfile voor uw doel architectuur. Vervolgens wordt `docker push` uitgevoerd om de opslag plaats van de installatie kopie naar het container register te pushen. 

## <a name="deploy-modules-to-device"></a>Modules implementeren op het apparaat

Gebruik de Visual Studio-Cloud Verkenner en de uitbrei ding van de Azure IoT Edge-Hulpprogram Ma's om het module project te implementeren op uw IoT Edge-apparaat. U hebt al een implementatie manifest voor bereid voor uw scenario, het bestand **Deployment. json** in de map Config. U hoeft nu alleen nog maar een apparaat te selecteren dat de implementatie moet ontvangen.

Zorg ervoor dat uw IoT Edge-apparaat actief is. 

1. Vouw in de Visual Studio-Cloud Verkenner de resources uit om de lijst met IoT-apparaten weer te geven. 

2. Klik met de rechter muisknop op de naam van het IoT Edge apparaat waarvan u de implementatie wilt ontvangen. 

3. Selecteer **implementatie maken**.

4. Selecteer in de Verkenner het bestand **Deployment. Windows-amd64** in de map config van uw oplossing. 

5. Vernieuw de Cloud Verkenner om de geïmplementeerde modules onder uw apparaat weer te geven. 


## <a name="view-generated-data"></a>Gegenereerde gegevens weergeven

Als u het implementatiemanifest op uw IoT Edge-apparaat toepast, verzamelt de IoT Edge-runtime op het apparaat de informatie over de nieuwe implementatie en wordt deze uitgevoerd. Modules die worden uitgevoerd op het apparaat en die niet zijn opgenomen in het implementatiemanifest, worden gestopt. Alle modules die ontbreken op het apparaat worden gestart. 

U kunt de extensie IoT Edge-Hulpprogram Ma's gebruiken om berichten weer te geven die op uw IoT Hub arriveren. 

1. Selecteer in de Visual Studio Cloud Explorer de naam van uw IoT Edge-apparaat. 

2. Selecteer in de lijst **acties** de optie **controle van ingebouwd gebeurtenis-eind punt starten**. 

3. Bekijk de berichten die op uw IoT Hub arriveren. Het kan enige tijd duren voordat de berichten binnenkomen, omdat het IoT Edge apparaat de nieuwe implementatie moet ontvangen en alle modules kan starten. Vervolgens worden de wijzigingen die we hebben aangebracht in de CModule-code gewacht totdat de computer temperatuur 25 graden bereikt voordat berichten worden verzonden. Het bericht type **waarschuwing** wordt ook toegevoegd aan berichten die de drempel waarde voor de Tempe ratuur bereiken. 

   ![Berichten weer geven die binnenkomen op IoT Hub](./media/tutorial-c-module-windows/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>De module twee bewerken

We hebben de CModule-module gebruikt om de drempel waarde voor de Tempe ratuur op 25 graden in te stellen. U kunt de module twee gebruiken om de functionaliteit te wijzigen zonder dat u de module code hoeft bij te werken.

1. Open in Visual Studio het bestand **Deployment. Windows-amd64. json** . (Niet het bestand Deployment. Temp late. Als u het implementatie manifest niet in het configuratie bestand in het Solution Explorer ziet, selecteert u het pictogram **alle bestanden weer geven** in de Explorer-werk balk.)

2. Zoek de CModule-dubbele en wijzig de waarde van de para meter **temperatureThreshold** in een nieuwe Tempe ratuur van vijf graden tot 10 graden hoger dan de laatste gerapporteerde Tempe ratuur. 

3. Sla de **implementatie. Windows-amd64. json** -bestand op.

4. Volg de implementaties tappen opnieuw om het bijgewerkte implementatie manifest op uw apparaat toe te passen. 

5. Bewaak de binnenkomende apparaat-naar-Cloud-berichten. U ziet dat de berichten stoppen totdat de nieuwe temperatuur drempel is bereikt. 

## <a name="clean-up-resources"></a>Resources opschonen 

Als u van plan bent door te gaan met het volgende aanbevolen artikel, kunt u de resources en configuraties die u hebt gemaakt behouden en opnieuw gebruiken. U kunt ook hetzelfde IoT Edge-apparaat blijven gebruiken als een testapparaat. 

Anders kunt u de lokale configuraties en de Azure-resources die u in dit artikel hebt gebruikt, verwijderen om kosten te voor komen. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een IoT Edge-module gemaakt met code voor het filteren van onbewerkte gegevens die worden gegenereerd door uw IoT Edge-apparaat. Wanneer u klaar bent om uw eigen modules te bouwen, kunt u meer te weten komen over [het ontwikkelen van uw eigen IOT Edge modules](module-development.md) of het [ontwikkelen van modules met Visual Studio](how-to-visual-studio-develop-module.md). U kunt door gaan met de volgende zelf studies om te leren hoe Azure IoT Edge u kan helpen bij de implementatie van Azure Cloud Services voor het verwerken en analyseren van gegevens aan de rand.

> [!div class="nextstepaction"]
> [Functions](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)
> [Machine Learning](tutorial-deploy-machine-learning.md)
> [Custom Vision Service](tutorial-deploy-custom-vision.md)
