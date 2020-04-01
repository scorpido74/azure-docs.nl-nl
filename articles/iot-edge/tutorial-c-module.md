---
title: Zelfstudie ontwikkelen C-module voor Linux - Azure IoT Edge | Microsoft Documenten
description: In deze zelfstudie ziet u hoe u een IoT Edge-module met C-code maakt en implementeert op een Linux-apparaat met IoT Edge
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 11/07/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 4236b7ad3b15d1bb58a146f5905d226e0c3833d5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76760944"
---
# <a name="tutorial-develop-a-c-iot-edge-module-for-linux-devices"></a>Zelfstudie: Een C IoT Edge-module ontwikkelen voor Linux-apparaten

Gebruik Visual Studio Code om C-code te ontwikkelen en te implementeren op een Linux-apparaat met Azure IoT Edge.

U kunt IoT Edge-modules gebruiken voor het implementeren van code die uw bedrijfslogica rechtstreeks op uw IoT Edge-apparaten implementeert. In deze zelfstudie leert u een IoT Edge-module te maken die sensorgegevens filtert. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Visual Studio Code gebruiken om een IoT Edge-module in C te maken
> * Visual Studio Code en Docker gebruiken om een Docker-installatiekopie te maken en deze te publiceren in een containerregister
> * De module implementeren op uw IoT Edge-apparaat
> * Gegenereerde gegevens weergeven

De IoT Edge-module die u maakt in deze zelfstudie filtert de temperatuurgegevens die door uw apparaat worden gegenereerd. Er worden alleen gegevens upstream gezonden als de temperatuur boven een opgegeven drempelwaarde komt. Dit soort analyse is nuttig om de hoeveelheidgegevens die worden gecommuniceerd naar en opgeslagen in de cloud te reduceren.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Oplossingsbereik

Deze zelfstudie laat zien hoe je een module in **C** ontwikkelen met Behulp van **Visual Studio Code**en hoe je deze implementeert op een **Linux-apparaat.** Als u modules voor Windows-apparaten ontwikkelt, gaat u in plaats daarvan naar [Een C IoT Edge-module voor Windows-apparaten](tutorial-c-module-windows.md) ontwikkelen.

Gebruik de volgende tabel om inzicht te krijgen in uw opties voor het ontwikkelen en implementeren van C-modules op Linux:

| C | Visual Studio Code | Visual Studio |
| - | ------------------ | ------------- |
| **Linux AMD64** | ![VS Code voor C-modules gebruiken op Linux AMD64](./media/tutorial-c-module/green-check.png) | ![VS voor C-modules gebruiken op Linux AMD64](./media/tutorial-c-module/green-check.png) |
| **Linux ARM32** | ![VS-code voor C-modules gebruiken op Linux ARM32](./media/tutorial-c-module/green-check.png) | ![VS voor C-modules gebruiken op Linux ARM32](./media/tutorial-c-module/green-check.png) |

## <a name="prerequisites"></a>Vereisten

Voordat u met deze zelfstudie begint, had u de vorige zelfstudie moeten doorlopen om uw ontwikkelomgeving in te stellen voor de ontwikkeling van Linux-containers: [IoT Edge-modules ontwikkelen voor Linux-apparaten.](tutorial-develop-for-linux.md) Door het invullen van die tutorial, moet u de volgende voorwaarden op zijn plaats:

* Een gratis of reguliere [IoT Hub](../iot-hub/iot-hub-create-through-portal.md)-laag in Azure.
* Een [Linux-apparaat met Azure IoT Edge](quickstart-linux.md)
* Een containerregister, zoals [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio Code](https://code.visualstudio.com/) geconfigureerd met de [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* [Docker CE](https://docs.docker.com/install/) geconfigureerd om Linux-containers uit te voeren.

Als u een IoT Edge-module in C wilt ontwikkelen, installeert u de volgende aanvullende vereisten op uw ontwikkelingsmachine:

* [C/C++-extensie](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools) voor Visual Studio Code (Engelstalig).

## <a name="create-a-module-project"></a>Een moduleproject maken

Met de volgende stappen wordt een IoT Edge-moduleproject voor C gemaakt met behulp van Visual Studio Code en de azure IoT-hulpprogramma's. Zodra u een projectsjabloon hebt gemaakt, voegt u nieuwe code toe zodat de module berichten filtert op basis van hun gerapporteerde eigenschappen.

### <a name="create-a-new-project"></a>Een nieuw project maken

Maak een C-oplossingssjabloon die u met uw eigen code kunt aanpassen.

1. Selecteer**Opdrachtpalet** **weergeven** > om het opdrachtpalet VS-code te openen.

2. Typ in het opdrachtpalet de opdracht **Azure: Sign in** en voer deze uit. Volg vervolgens de instructies om u aan te melden bij uw Azure-account. Als u zich al hebt aangemeld, kunt u deze stap overslaan.

3. Typ in het opdrachtpalet de opdracht **Azure IoT Edge: New IoT Edge solution** en voer deze uit. Volg de aanwijzingen in het opdrachtpalet om uw oplossing te maken.

   | Veld | Waarde |
   | ----- | ----- |
   | Map selecteren | Kies de locatie op uw ontwikkelcomputer waar VS Code de oplossingsbestanden moet maken. |
   | Een naam opgeven voor de oplossing | Voer een beschrijvende naam voor de oplossing in of accepteer de standaardnaam **EdgeSolution**. |
   | Modulesjabloon selecteren | Kies **C-module**. |
   | Een modulenaam opgeven | Geef de module de naam **CModule**. |
   | Opslagplaats voor Docker-afbeeldingen voor de module opgeven | Een opslagplaats voor afbeeldingen bevat de naam van het containerregister en de naam van uw containerafbeelding. De containerafbeelding wordt vooraf gevuld vanuit de naam die u in de laatste stap hebt opgegeven. Vervang **localhost:5000** door de waarde van de aanmeldingsserver uit uw Azure-containerregister. U vindt de aanmeldingsserver op de overzichtspagina van het containerregister in de Azure-portal. <br><br> De uiteindelijke opslagplaats voor de installatiekopie ziet er ongeveer als volgt uit: \<registernaam\>.azurecr.io/cmodule. |

   ![Opslagplaats voor Docker-installatiekopieën opgeven](./media/tutorial-c-module/repository.png)

### <a name="add-your-registry-credentials"></a>Uw registerreferenties toevoegen

In het omgevingsbestand worden de referenties voor het containerregister opgeslagen. Deze referenties worden gedeeld met de IoT Edge-runtime. De runtime heeft deze referenties nodig om uw persoonlijke installatiekopieën naar het IoT Edge-apparaat te halen.

1. Open in VS Code Explorer het .env-bestand.
2. Werk de velden **gebruikersnaam** en **wachtwoord** bij met de waarden die u hebt gekopieerd uit het Azure-containerregister.
3. Sla dit bestand op.

### <a name="select-your-target-architecture"></a>Selecteer uw doelarchitectuur

Momenteel kan Visual Studio Code C-modules ontwikkelen voor Linux AMD64- en Linux ARM32v7-apparaten. U moet selecteren welke architectuur u bij elke oplossing target, omdat de container voor elk architectuurtype anders is gebouwd en deze wordt uitgevoerd. De standaard is Linux AMD64.

1. Open het opdrachtpalet en zoek naar **Azure IoT Edge: Stel standaarddoelplatform in voor edge-oplossing**of selecteer het sneltoetspictogram in de zijbalk onder aan het venster.

2. Selecteer in het opdrachtpalet de doelarchitectuur in de lijst met opties. Voor deze tutorial gebruiken we een Virtuele Ubuntu-machine als het IoT Edge-apparaat, dus houdt de standaard **amd64**.

### <a name="update-the-module-with-custom-code"></a>De module bijwerken met aangepaste code

De standaardmodulecode ontvangt berichten in een invoerwachtrij en geeft deze door een uitvoerwachtrij. Laten we wat extra code toevoegen, zodat de module de berichten aan de rand verwerkt voordat u ze doorstuurt naar IoT Hub. Werk de module zo bij dat de temperatuurgegevens in elk bericht worden geanalyseerd en wordt het bericht alleen naar IoT Hub verzonden als de temperatuur een bepaalde drempel overschrijdt.

1. De gegevens van de sensor in dit scenario worden in JSON-indeling aangeleverd. Als u berichten wilt filteren in een JSON-indeling, moet u een JSON-bibliotheek voor C importeren. In deze zelfstudie wordt Parson gebruikt.

   1. Download de [Parson GitHub repository.](https://github.com/kgabis/parson) Kopieer de bestanden **parson.c** en **parson.h** naar de map **CModule**.

   2. Open **modules** > **CModule** > **CMakeLists.txt**. Importeer boven in het bestand de Parson-bestanden als een bibliotheek met de naam **my_parson**.

      ```txt
      add_library(my_parson
          parson.c
          parson.h
      )
      ```

   3. Toevoegen `my_parson` aan de lijst met bibliotheken in de **target_link_libraries** functie van CMakeLists.txt.

   4. Sla het bestand **CMakeLists.txt** op.

   5. Open **modules** > **CModule** > **main.c**. Voeg onderaan de lijst met include-instructies een `parson.h` nieuwe toe die u wilt opnemen voor JSON-ondersteuning:

      ```c
      #include "parson.h"
      ```

1. Voeg in het bestand **main.c** na de sectie include een globale variabele toe met de naam `temperatureThreshold`. Deze variabele bepaalt de waarde die de gemeten temperatuur moet overschrijden voordat de gegevens naar IoT Hub worden verzonden.

    ```c
    static double temperatureThreshold = 25;
    ```

1. Zoek `CreateMessageInstance` de functie in main.c. Vervang de innerlijke if-else-instructie door de volgende code die een paar regels functionaliteit toevoegt:

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

1. Vervang de volledige functie `InputQueue1Callback` door de volgende code. Deze functie implementeert het feitelijke berichtenfilter. Wanneer een bericht wordt ontvangen, wordt gecontroleerd of de gerapporteerde temperatuur de drempelwaarde overschrijdt. Zo ja, dan wordt het bericht doorgestuurd via de uitvoerwachtrij. Zo niet, dan negeert het bericht.

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
        else
        {
            printf("Not sending message (%zu) to the next stage in pipeline.\r\n", messagesReceivedByInput1Queue);
            result = IOTHUBMESSAGE_ACCEPTED;
        }

        messagesReceivedByInput1Queue++;
        return result;
    }
    ```

1. Voeg een `moduleTwinCallback`-functie toe. Deze methode ontvangt updates van de gewenste eigenschappen van de moduledubbel en updatet de variabele **temperatureThreshold** naar dezelfde waarde. Alle modules hebben hun eigen moduledubbel, waardoor u rechtstreeks in de cloud de code kunt configureren die in een module wordt uitgevoerd.

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

1. Zoek `SetupCallbacksForModule` de functie. Vervang de functie door de volgende code die een **andere als** instructie toevoegt om te controleren of de moduletwin is bijgewerkt.

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

1. Sla het bestand main.c op.

1. Open in VS Code Explorer het bestand **deployment.template.json** in de werkruimte van de IoT Edge-oplossing.

1. Voeg de moduledubbel CModule toe aan het distributiemanifest. Voeg de volgende JSON-inhoud onder aan de sectie `moduleContent` in, na de moduledubbel `$edgeHub`:

   ```json
   "CModule": {
       "properties.desired":{
           "TemperatureThreshold":25
       }
   }
   ```

   ![Dubbele CModule toevoegen aan implementatiesjabloon](./media/tutorial-c-module/module-twin.png)

1. Sla het bestand **deployment.template.json** op.

## <a name="build-and-push-your-module"></a>Bouw en duw je module

In de vorige sectie hebt u een IoT Edge-oplossing gemaakt en code toegevoegd aan de CModule die berichten uitfiltert waarin de gerapporteerde machinetemperatuur binnen de aanvaardbare limieten ligt. Nu moet u de oplossing bouwen als een containerinstallatiekopie en deze naar het containerregister pushen.

1. Open de VS Code-terminal door **View** > **Terminal te**selecteren .

1. Meld u aan bij Docker door de volgende opdracht in de terminal in te voeren. Meld u aan met de gebruikersnaam, het wachtwoord en de aanmeldingsserver vanuit uw Azure-containerregister. U deze waarden ophalen uit de sectie **Toegangssleutels** van uw register in de Azure-portal.

   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Mogelijk ontvangt u een beveiligingswaarschuwing `--password-stdin`waarin het gebruik van . Hoewel die aanbevolen procedures worden aanbevolen voor productiescenario's, valt deze buiten het bereik van deze zelfstudie. Zie de [inlogreferentie voor docker](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) voor meer informatie.

1. Klik in de VS-codeverkenner met de rechtermuisknop op het bestand **deployment.template.json** en selecteer **de Oplossing IoT Edge bouwen en pushen**.

   De opdracht bouwen en pushen start drie bewerkingen. Ten eerste maakt het een nieuwe map in de oplossing genaamd **config** die het volledige implementatiemanifest bevat, opgebouwd uit informatie in de implementatiesjabloon en andere oplossingsbestanden. Ten tweede `docker build` wordt het uitgevoerd om de containerafbeelding te bouwen op basis van het juiste dockerbestand voor uw doelarchitectuur. Vervolgens wordt `docker push` het uitgevoerd om de afbeeldingsopslagplaats naar uw containerregister te duwen.

## <a name="deploy-modules-to-device"></a>Modules implementeren op apparaat

Gebruik de Visual Studio Code explorer en de Azure IoT Tools-extensie om het moduleproject te implementeren op uw IoT Edge-apparaat. U hebt al een implementatiemanifest voorbereid op uw scenario, het **bestand deployment.json** in de config-map. U hoeft nu alleen nog maar een apparaat te selecteren dat de implementatie moet ontvangen.

Zorg ervoor dat uw IoT Edge-apparaat actief is.

1. Vouw in de visual studiocodeverkenner de sectie **Azure IoT Hub-apparaten** uit om uw lijst met IoT-apparaten weer te geven.

2. Klik met de rechtermuisknop op de naam van het IoT Edge-apparaat en selecteer **Implementatie voor één apparaat maken**.

3. Selecteer het bestand **deployment.json** in de **configuratiemap** en klik vervolgens op **Edge-distributiemanifest selecteren**. Gebruik niet het bestand deployment.template.json.

4. Klik op de knop Vernieuwen. U ziet de nieuwe **CModule** lopen samen met de **Module SimulatedTemperatureSensor** en de **$edgeAgent** en **$edgeHub.**

## <a name="view-generated-data"></a>Gegenereerde gegevens weergeven

Als u het implementatiemanifest op uw IoT Edge-apparaat toepast, verzamelt de IoT Edge-runtime op het apparaat de informatie over de nieuwe implementatie en wordt deze uitgevoerd. Modules die worden uitgevoerd op het apparaat en die niet zijn opgenomen in het implementatiemanifest, worden gestopt. Alle modules die ontbreken op het apparaat worden gestart.

U kunt de status van uw IoT Edge-apparaat bekijken via de sectie **Azure IoT Hub Devices** van de Visual Studio Code explorer. Vouw de details van uw apparaat uit voor een overzicht van de modules die worden geïmplementeerd en uitgevoerd.

1. Klik in de verkenner voor Visual Studio Code met de rechtermuisknop op de naam van uw IoT Edge-apparaat en selecteer **Ingebouwde gebeurteniseindpunt starten.**

2. Bekijk de berichten die aankomen op uw IoT Hub. Het kan even duren voordat de berichten binnenkomen, omdat het IoT Edge-apparaat zijn nieuwe implementatie moet ontvangen en alle modules moet starten. Vervolgens wachten de wijzigingen die we in de CModule-code hebben aangebracht tot de temperatuur van de machine 25 graden is voordat we berichten verzenden. Het voegt ook het berichttype **Alert** toe aan berichten die die temperatuurdrempel bereiken.

   ![Berichten bekijken die aankomen op IoT Hub](./media/tutorial-c-module/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>De moduletwee bewerken

We gebruikten de CModule module twin in het implementatiemanifest om de temperatuurdrempel op 25 graden in te stellen. U de moduletwee gebruiken om de functionaliteit te wijzigen zonder dat u de modulecode hoeft bij te werken.

1. Vouw in Visual Studio Code de details onder uw IoT Edge-apparaat uit om de lopende modules te bekijken.

2. Klik met de rechtermuisknop op **CModule** en selecteer **Moduletwin bewerken**.

3. Zoek **Temperatuurdrempel** in de gewenste eigenschappen. Verander de waarde in een nieuwe temperatuur 5 graden tot 10 graden hoger dan de laatst gemelde temperatuur.

4. Sla het dubbele modulebestand op.

5. Klik met de rechtermuisknop ergens in het dubbele bewerkingsvenster van de module en selecteer **Moduletwin bijwerken**.

6. Monitor de binnenkomende device-to-cloud berichten. U ziet de berichten stoppen totdat de nieuwe temperatuurdrempel is bereikt.

## <a name="clean-up-resources"></a>Resources opschonen

Als u van plan bent door te gaan met het volgende aanbevolen artikel, kunt u de resources en configuraties die u hebt gemaakt behouden en opnieuw gebruiken. U kunt ook hetzelfde IoT Edge-apparaat blijven gebruiken als een testapparaat.

Anders u de lokale configuraties en de Azure-resources verwijderen die u in dit artikel hebt gebruikt om kosten te voorkomen.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een IoT Edge-module gemaakt die code bevat voor het filteren van onbewerkte gegevens die worden gegenereerd door uw IoT Edge-apparaat. Wanneer u klaar bent om uw eigen modules te bouwen, u meer te weten komen over [het ontwikkelen van uw eigen IoT Edge-modules](module-development.md) of hoe u modules ontwikkelen met Visual Studio [Code.](how-to-vs-code-develop-module.md) Zie [IoT Edge-modulevoorbeelden](https://github.com/Azure/iotedge/tree/master/edge-modules) en [IoT C SDK-samples](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples)voor voorbeelden van IoT Edge- modules, waaronder de gesimuleerde temperatuurmodule.

U doorgaan naar de volgende zelfstudies om te leren hoe Azure IoT Edge u kan helpen Azure-cloudservices te implementeren om gegevens aan de rand te verwerken en te analyseren.

> [!div class="nextstepaction"]
> [Functies](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)
> [Machine Learning](tutorial-deploy-machine-learning.md)
> [Custom Vision Service](tutorial-deploy-custom-vision.md)
