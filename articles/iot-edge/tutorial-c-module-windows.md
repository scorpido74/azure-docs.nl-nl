---
title: Zelfstudie ontwikkelen C-module voor Windows - Azure IoT Edge | Microsoft Documenten
description: In deze zelfstudie ziet u hoe u een IoT Edge-module met C-code maakt en implementeert op een Windows-apparaat met IoT Edge
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 05/28/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 09d039801107a44df4f3bf3745a1e074e6d708b8
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76760961"
---
# <a name="tutorial-develop-a-c-iot-edge-module-for-windows-devices"></a>Zelfstudie: Een C IoT Edge-module voor Windows-apparaten ontwikkelen

Gebruik Visual Studio om C-code te ontwikkelen en te implementeren op een Windows-apparaat met Azure IoT Edge.

U kunt Azure IoT Edge-modules gebruiken voor het implementeren van code die uw bedrijfslogica rechtstreeks op uw IoT Edge-apparaten implementeert. In deze zelfstudie leert u een IoT Edge-module te maken die sensorgegevens filtert. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Gebruik Visual Studio om een IoT Edge-module te maken die is gebaseerd op de C SDK.
> * Gebruik Visual Studio en Docker om een Docker-afbeelding te maken en deze te publiceren in uw register.
> * De module implementeren op uw IoT Edge-apparaat.
> * Gegenereerde gegevens weergeven.

De IoT Edge-module die u maakt in deze zelfstudie filtert de temperatuurgegevens die door uw apparaat worden gegenereerd. Er worden alleen gegevens upstream gezonden als de temperatuur boven een opgegeven drempelwaarde komt. Dit soort analyse is nuttig om de hoeveelheid gegevens te reduceren die worden gecommuniceerd naar en worden opgeslagen in de cloud.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Oplossingsbereik

Deze zelfstudie laat zien hoe je een module in **C** ontwikkelen met **Visual Studio 2019** en deze implementeren op een **Windows-apparaat.** Als u modules voor Linux-apparaten ontwikkelt, gaat u in plaats daarvan [naar Het ontwikkelen van een C IoT Edge-module voor Linux-apparaten.](tutorial-c-module.md)

Gebruik de volgende tabel om inzicht te krijgen in uw opties voor het ontwikkelen en implementeren van C-modules op Windows-apparaten:

| C | Visual Studio Code | Visual Studio 2017/2019 |
| -- | ------------------ | ------------------ |
| **Windows AMD64** |  | ![C-modules ontwikkelen voor WinAMD64 in Visual Studio](./media/tutorial-c-module/green-check.png) |

## <a name="prerequisites"></a>Vereisten

Voordat u met deze zelfstudie begint, moet u de vorige zelfstudie hebben doorlopen om uw ontwikkelomgeving in te stellen voor de ontwikkeling van Windows-containers: [IoT Edge-modules ontwikkelen voor Windows-apparaten.](tutorial-develop-for-windows.md) Na het voltooien van die zelfstudie, moet u de volgende voorwaarden op zijn plaats:

* Een gratis of reguliere [IoT Hub](../iot-hub/iot-hub-create-through-portal.md)-laag in Azure.
* Een [Windows-apparaat met Azure IoT Edge](quickstart.md).
* Een containerregister, zoals [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio 2019](https://docs.microsoft.com/visualstudio/install/install-visual-studio) geconfigureerd met de Azure [IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) Tools-extensie.
* [Docker-bureaublad](https://docs.docker.com/docker-for-windows/install/) geconfigureerd voor het uitvoeren van Windows-containers.
* Installeer de Azure IoT C SDK voor Windows x64 via vcpkg:

   ```powershell
   git clone https://github.com/Microsoft/vcpkg
   cd vcpkg
   .\bootstrap-vcpkg.bat
   .\vcpkg install azure-iot-sdk-c:x64-windows
   .\vcpkg --triplet x64-windows integrate install
   ```

> [!TIP]
> Als u Visual Studio 2017 (versie 15.7 of hoger) gebruikt, download en installeer dan [Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) voor VS 2017 vanuit de Visual Studio-marktplaats

## <a name="create-a-module-project"></a>Een moduleproject maken

Met de volgende stappen wordt een IoT Edge-moduleproject gemaakt dat is gebaseerd op de C SDK met behulp van Visual Studio en de azure IoT Edge Tools-extensie. Zodra u een projectsjabloon hebt gemaakt, voegt u nieuwe code toe zodat de module berichten filtert op basis van hun gerapporteerde eigenschappen.

### <a name="create-a-new-project"></a>Een nieuw project maken

Maak een C-oplossingssjabloon die u met uw eigen code kunt aanpassen.

1. Start Visual Studio 2019 en selecteer **Nieuw project maken**.

2. Zoek naar **IoT Edge** en kies het **Azure IoT Edge -project (Windows AMD64).** Klik op **Volgende**.

   ![Een nieuw Azure IoT Edge-project maken](./media/tutorial-c-module-windows/new-project.png)

3. Wijzig de naam van het project en de oplossing voor iets beschrijvends zoals **CTutorialApp**. Klik **op Maken** om het project te maken.

   ![Een nieuw Azure IoT Edge-project configureren](./media/tutorial-c-module-windows/configure-project.png)

4. Configureer uw project met de volgende waarden:

   | Veld | Waarde |
   | ----- | ----- |
   | Een sjabloon selecteren | Selecteer **C-module**. |
   | Moduleprojectnaam | Geef de module de naam **CModule**. |
   | Docker-afbeeldingsopslagplaats | Een opslagplaats voor afbeeldingen bevat de naam van het containerregister en de naam van uw containerafbeelding. De containerafbeelding wordt vooraf ingevuld vanaf de waarde van de projectnaam module. Vervang **localhost:5000** door de waarde van de aanmeldingsserver uit uw Azure-containerregister. U vindt de aanmeldingsserver op de overzichtspagina van het containerregister in de Azure-portal. <br><br> De uiteindelijke opslagplaats voor de installatiekopie ziet er ongeveer als volgt uit: \<registernaam\>.azurecr.io/cmodule. |

   ![Uw project configureren op doelapparaat, moduletype en containerregister](./media/tutorial-c-module-windows/add-application-and-module.png)

5. Selecteer **Toevoegen** om het project te maken.

### <a name="add-your-registry-credentials"></a>Uw registerreferenties toevoegen

Het implementatiemanifest deelt de referenties voor uw containerregister met de runtime van IoT Edge. De runtime heeft deze referenties nodig om uw persoonlijke installatiekopieën naar het IoT Edge-apparaat te halen. Gebruik de referenties uit de sectie **Toegangssleutels** van uw Azure-containerregister.

1. Open het bestand **deployment.template.json** in de oplossingsverkenner van Visual Studio.

2. Zoek de eigenschap **registryCredentials** in de gewenste $edgeAgent gewenste eigenschappen. Het moet uw registeradres automatisch laten invullen van de informatie die u hebt verstrekt bij het maken van het project. De gebruikersnaam- en wachtwoordvelden moeten variabele namen bevatten. Bijvoorbeeld:

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "$CONTAINER_REGISTRY_USERNAME_<registry name>",
       "password": "$CONTAINER_REGISTRY_PASSWORD_<registry name>",
       "address": "<registry name>.azurecr.io"
     }
   }
   ```

3. Open het **.env-bestand** in uw moduleoplossing. (Het is standaard verborgen in de Oplossingsverkenner, dus het kan nodig zijn om de knop **Alle bestanden weergeven** te selecteren om het weer te geven.) Het .env-bestand moet dezelfde gebruikersnaam- en wachtwoordvariabelen bevatten die u in het bestand deployment.template.json hebt gezien.

4. Voeg de **waarden gebruikersnaam** en **wachtwoord** toe uit uw Azure-containerregister.

5. Sla uw wijzigingen op in het .env-bestand.

### <a name="update-the-module-with-custom-code"></a>De module bijwerken met aangepaste code

De standaardmodulecode ontvangt berichten in een invoerwachtrij en geeft deze door een uitvoerwachtrij. Laten we wat extra code toevoegen, zodat de module de berichten aan de rand verwerkt voordat u ze doorstuurt naar IoT Hub. Werk de module zo bij dat de temperatuurgegevens in elk bericht worden geanalyseerd en wordt het bericht alleen naar IoT Hub verzonden als de temperatuur een bepaalde drempel overschrijdt.

1. De gegevens van de sensor in dit scenario worden in JSON-indeling aangeleverd. Als u berichten wilt filteren in een JSON-indeling, moet u een JSON-bibliotheek voor C importeren. In deze zelfstudie wordt Parson gebruikt.

   1. Download de [Parson GitHub repository.](https://github.com/kgabis/parson) Kopieer de **parson.c-** en **parson.h-bestanden** naar het **CModule-project.**

   2. Open in Visual Studio het **cmakelists.txt-bestand** vanuit de projectmap CModule. Importeer boven in het bestand de Parson-bestanden als een bibliotheek met de naam **my_parson**.

      ```txt
      add_library(my_parson
          parson.c
          parson.h
      )
      ```

   3. Toevoegen `my_parson` aan de lijst met bibliotheken in het **target_link_libraries** gedeelte van het CMakeLists.txt-bestand.

   4. Sla het bestand **CMakeLists.txt** op.

   5. Open **CModule** > **main.c**. Voeg onderaan de lijst met include-instructies een `parson.h` nieuwe toe die u wilt opnemen voor JSON-ondersteuning:

      ```c
      #include "parson.h"
      ```

2. Voeg in het **main.c-bestand** `temperatureThreshold` een globale variabele toe die wordt genoemd naast de variabele MessagesReceivedByInput1Queue. Deze variabele bepaalt de waarde die de gemeten temperatuur moet overschrijden voordat de gegevens naar IoT Hub worden verzonden.

    ```c
    static double temperatureThreshold = 25;
    ```

3. Zoek `CreateMessageInstance` de functie in main.c. Vervang de innerlijke if-else-instructie door de volgende code die een paar regels functionaliteit toevoegt:

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

   De nieuwe regels code in de andere instructie voegen een nieuwe eigenschap toe aan het bericht, waarin het bericht als een waarschuwing wordt labels. Met deze code worden alle berichten als waarschuwingen labels, omdat we functionaliteit toevoegen die alleen berichten naar IoT Hub verzendt als ze hoge temperaturen melden.

4. Zoek `InputQueue1Callback` de functie en vervang de hele functie door de volgende code. Deze functie implementeert het feitelijke berichtenfilter. Wanneer een bericht wordt ontvangen, wordt gecontroleerd of de gerapporteerde temperatuur de drempelwaarde overschrijdt. Zo ja, dan wordt het bericht doorgestuurd via de uitvoerwachtrij. Zo niet, dan negeert het bericht.

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

6. Zoek `SetupCallbacksForModule` de functie. Vervang de functie door de volgende code die een **andere als** instructie toevoegt om te controleren of de moduletwin is bijgewerkt.

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

8. Open het bestand **deployment.template.json.**

9. Voeg de moduledubbel CModule toe aan het distributiemanifest. Voeg de volgende JSON-inhoud onder aan de sectie `moduleContent` in, na de moduledubbel `$edgeHub`:

   ```json
   "CModule": {
       "properties.desired":{
           "TemperatureThreshold":25
       }
   }
   ```

   ![Dubbele CModule toevoegen aan implementatiesjabloon](./media/tutorial-c-module-windows/module-twin.png)

10. Sla het bestand **deployment.template.json** op.

## <a name="build-and-push-your-module"></a>Bouw en duw je module

In de vorige sectie hebt u een IoT Edge-oplossing gemaakt en code toegevoegd aan de **CModule** om berichten uit te filteren waarbij de gerapporteerde machinetemperatuur onder de aanvaardbare drempelwaarde ligt. Nu moet u de oplossing bouwen als een containerinstallatiekopie en deze naar het containerregister pushen.

1. Gebruik de volgende opdracht om je aan te melden bij Docker op je ontwikkelmachine. Meld u aan met de gebruikersnaam, het wachtwoord en de aanmeldingsserver vanuit uw Azure-containerregister. U deze waarden ophalen uit de sectie **Toegangssleutels** van uw register in de Azure-portal.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Mogelijk ontvangt u een beveiligingswaarschuwing `--password-stdin`waarin het gebruik van . Hoewel die aanbevolen procedures worden aanbevolen voor productiescenario's, valt deze buiten het bereik van deze zelfstudie. Zie de [inlogreferentie voor docker](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) voor meer informatie.

2. Klik in de Visual Studio-oplossingsverkenner met de rechtermuisknop op de projectnaam die u wilt bouwen. De standaardnaam is **AzureIotEdgeApp1** en aangezien u een Windows-module bouwt, moet de extensie **Windows.Amd64**zijn.

3. Selecteer **IoT-randmodules bouwen en pushen**.

   De opdracht bouwen en pushen start drie bewerkingen. Ten eerste maakt het een nieuwe map in de oplossing genaamd **config** die het volledige implementatiemanifest bevat, opgebouwd uit informatie in de implementatiesjabloon en andere oplossingsbestanden. Ten tweede `docker build` wordt het uitgevoerd om de containerafbeelding te bouwen op basis van het juiste dockerbestand voor uw doelarchitectuur. Vervolgens wordt `docker push` het uitgevoerd om de afbeeldingsopslagplaats naar uw containerregister te duwen.

## <a name="deploy-modules-to-device"></a>Modules implementeren op apparaat

Gebruik de Visual Studio cloud explorer en de Azure IoT Edge Tools-extensie om het moduleproject te implementeren op uw IoT Edge-apparaat. U hebt al een implementatiemanifest voorbereid op uw scenario, het **bestand deployment.json** in de config-map. U hoeft nu alleen nog maar een apparaat te selecteren dat de implementatie moet ontvangen.

Zorg ervoor dat uw IoT Edge-apparaat actief is.

1. Vouw in de Visual Studio-cloudexplorer de resources uit om uw lijst met IoT-apparaten weer te geven.

2. Klik met de rechtermuisknop op de naam van het IoT Edge-apparaat dat u wilt ontvangen.

3. Selecteer **Implementatie maken**.

4. Selecteer in de verkenner het **bestand deployment.windows-amd64** in de config-map van uw oplossing.

5. Vernieuw de cloudexplorer om de geïmplementeerde modules onder uw apparaat weer te geven.

## <a name="view-generated-data"></a>Gegenereerde gegevens weergeven

Als u het implementatiemanifest op uw IoT Edge-apparaat toepast, verzamelt de IoT Edge-runtime op het apparaat de informatie over de nieuwe implementatie en wordt deze uitgevoerd. Modules die worden uitgevoerd op het apparaat en die niet zijn opgenomen in het implementatiemanifest, worden gestopt. Alle modules die ontbreken op het apparaat worden gestart.

U de extensie IoT Edge Tools gebruiken om berichten te bekijken wanneer ze bij uw IoT Hub aankomen.

1. Selecteer in de Visual Studio-cloudexplorer de naam van uw IoT Edge-apparaat.

2. Selecteer in de lijst **Acties** **het eindpunt van ingebouwde gebeurtenis**met ingebouwde gebeurtenis starten .

3. Bekijk de berichten die aankomen op uw IoT Hub. Het kan even duren voordat de berichten binnenkomen, omdat het IoT Edge-apparaat zijn nieuwe implementatie moet ontvangen en alle modules moet starten. Vervolgens wachten de wijzigingen die we in de CModule-code hebben aangebracht tot de temperatuur van de machine 25 graden is voordat we berichten verzenden. Het voegt ook het berichttype **Alert** toe aan berichten die die temperatuurdrempel bereiken.

   ![Berichten bekijken die aankomen op IoT Hub](./media/tutorial-c-module-windows/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>De moduletwee bewerken

We gebruikten de CModule module twin om de temperatuurdrempel op 25 graden te zetten. U de moduletwee gebruiken om de functionaliteit te wijzigen zonder dat u de modulecode hoeft bij te werken.

1. Open in Visual Studio het **bestand deployment.windows-amd64.json.** (Niet het bestand deployment.template. Als u het implementatiemanifest niet ziet in het config-bestand in de oplossingsverkenner, selecteert u het pictogram **Alle bestanden weergeven** op de werkbalk van de verkenner.)

2. Zoek de CModule twin en verander de waarde van de **parameter temperatureThreshold** naar een nieuwe temperatuur 5 graden tot 10 graden hoger dan de laatst gerapporteerde temperatuur.

3. Sla het **bestand deployment.windows-amd64.json op.**

4. Volg de implementatiestappen opnieuw om het bijgewerkte implementatiemanifest op uw apparaat toe te passen.

5. Monitor de binnenkomende device-to-cloud berichten. U ziet de berichten stoppen totdat de nieuwe temperatuurdrempel is bereikt.

## <a name="clean-up-resources"></a>Resources opschonen

Als u van plan bent door te gaan met het volgende aanbevolen artikel, kunt u de resources en configuraties die u hebt gemaakt behouden en opnieuw gebruiken. U kunt ook hetzelfde IoT Edge-apparaat blijven gebruiken als een testapparaat.

Anders u de lokale configuraties en de Azure-resources verwijderen die u in dit artikel hebt gebruikt om kosten te voorkomen.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een IoT Edge-module gemaakt met code voor het filteren van onbewerkte gegevens die worden gegenereerd door uw IoT Edge-apparaat. Wanneer u klaar bent om uw eigen modules te bouwen, u meer te weten komen over [het ontwikkelen van uw eigen IoT Edge-modules](module-development.md) of hoe u modules ontwikkelen met Visual [Studio.](how-to-visual-studio-develop-module.md) Zie [IoT Edge-modulevoorbeelden](https://github.com/Azure/iotedge/tree/master/edge-modules) en [IoT C SDK-samples](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples)voor voorbeelden van IoT Edge- modules, waaronder de gesimuleerde temperatuurmodule.

U doorgaan naar de volgende zelfstudies om te leren hoe Azure IoT Edge u kan helpen Azure-cloudservices te implementeren om gegevens aan de rand te verwerken en te analyseren.

> [!div class="nextstepaction"]
> [Functies](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)
> [Machine Learning](tutorial-deploy-machine-learning.md)
> [Custom Vision Service](tutorial-deploy-custom-vision.md)
