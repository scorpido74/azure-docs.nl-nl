---
title: Aangepaste toewijzings beleidsregels gebruiken met de Azure-IoT Hub Device Provisioning Service | Microsoft Docs
description: Aangepaste toewijzings beleid gebruiken met de Azure-IoT Hub Device Provisioning Service
author: wesmc7777
ms.author: wesmc
ms.date: 04/10/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: philmea
ms.openlocfilehash: 11872f8efcebf39edef2f97cd30c225edbe74bb4
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/10/2019
ms.locfileid: "73903575"
---
# <a name="how-to-use-custom-allocation-policies"></a>Aangepaste toewijzings beleid gebruiken

Een aangepast toewijzings beleid geeft u meer controle over de manier waarop apparaten worden toegewezen aan een IoT-hub. Dit wordt bereikt door gebruik te maken van aangepaste code in een [Azure-functie](../azure-functions/functions-overview.md) om apparaten toe te wijzen aan een IOT-hub. De Device Provisioning Service roept uw Azure-functie code aan die alle relevante informatie over het apparaat en de inschrijving verschaft. De functie code wordt uitgevoerd en retourneert de IoT hub-gegevens die worden gebruikt om het apparaat in te richten.

Door gebruik te maken van aangepaste toewijzings beleid, definieert u uw eigen toewijzings beleid wanneer de beleids regels die door de Device Provisioning Service worden geleverd niet voldoen aan de vereisten van uw scenario.

Misschien wilt u bijvoorbeeld het certificaat dat een apparaat gebruikt tijdens het inrichten, bekijken en het apparaat toewijzen aan een IoT-hub op basis van een certificaat eigenschap. Of misschien hebt u gegevens opgeslagen in een Data Base voor uw apparaten en moet u de data base opvragen om te bepalen aan welke IoT-hub een apparaat moet worden toegewezen.

In dit artikel wordt een aangepast toewijzings beleid gedemonstreerd met behulp van een Azure-functie die is geschreven in C#. Er worden twee nieuwe IoT-hubs gemaakt die een deel van *Contoso-pop-upoperators* en een *divisie van Contoso-hitte pompen*vertegenwoordigen. Apparaten die inrichting aanvragen, moeten een registratie-ID hebben met een van de volgende achtervoegsels om te worden geaccepteerd voor het inrichten:

* **-Contoso-tstrsd-007**: contoso-pop-uptaaks
* **-Contoso-hpsd-088**: contoso hitte pompen delen

De apparaten worden ingericht op basis van een van deze vereiste achtervoegsels in de registratie-ID. Deze apparaten worden gesimuleerd met behulp van een inrichtings voorbeeld dat is opgenomen in de [Azure IOT C-SDK](https://github.com/Azure/azure-iot-sdk-c).

U voert de volgende stappen uit in dit artikel:

* Gebruik de Azure CLI om twee contoso-divisie IoT-hubs te maken (delen van**Contoso-pop-upoperators** en **divisies van Contoso-verwarming**)
* Een nieuwe groeps registratie maken met behulp van een Azure-functie voor het aangepaste toewijzings beleid
* Sleutels voor twee simulaties van apparaten maken.
* De ontwikkel omgeving voor de Azure IoT C-SDK instellen
* De apparaten simuleren en controleren of ze zijn ingericht volgens de voorbeeld code in het aangepaste toewijzings beleid

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

* Volt ooien van het [instellen van IOT hub Device Provisioning Service met de Azure Portal](./quick-setup-auto-provision.md) Snelstartgids.
* [Visual Studio](https://visualstudio.microsoft.com/vs/) 2015 of hoger met de [' Desktop C++Development '](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) -werk belasting ingeschakeld.
* Meest recente versie van [Git](https://git-scm.com/download/) geïnstalleerd.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-two-divisional-iot-hubs"></a>Twee divisie IoT-hubs maken

In deze sectie gebruikt u de Azure Cloud Shell om twee nieuwe IoT-hubs te maken die de **Divisie contoso-pop-upoperators** en de divisie van de **Contoso-hitte pompen**vertegenwoordigen.

1. Gebruik de Azure Cloud Shell om een resource groep te maken met de opdracht [AZ Group Create](/cli/azure/group#az-group-create) . Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd.

    In het volgende voor beeld wordt een resource groep met de naam *Contoso-US-Resource-Group* in de regio *eastus* gemaakt. Het wordt aanbevolen deze groep te gebruiken voor alle resources die in dit artikel zijn gemaakt. Deze aanpak maakt het opschonen van de oplossing eenvoudiger nadat u klaar bent.

    ```azurecli-interactive 
    az group create --name contoso-us-resource-group --location eastus
    ```

2. Gebruik de Azure Cloud Shell om de **Contoso-pop-Uptaaks te delen** IOT hub met de opdracht [AZ IOT hub Create](/cli/azure/iot/hub#az-iot-hub-create) . De IoT-hub wordt toegevoegd aan *Contoso-US-Resource-Group*.

    In het volgende voor beeld wordt een IoT-hub gemaakt met de naam *Contoso--pop-upmodules-hub-1098* in de locatie *oostus* . U moet een unieke naam voor de hub gebruiken. Maak uw eigen achtervoegsel in de naam van de hub in plaats van **1098**. De voorbeeld code voor het aangepaste toewijzings beleid vereist `-toasters-` in de naam van de hub.

    ```azurecli-interactive 
    az iot hub create --name contoso-toasters-hub-1098 --resource-group contoso-us-resource-group --location eastus --sku S1
    ```

    Het kan enkele minuten duren voordat deze opdracht is voltooid.

3. Gebruik de Azure Cloud Shell voor het maken van de IOT-hub van de **Contoso hitte pompen** met de opdracht [AZ IOT hub Create](/cli/azure/iot/hub#az-iot-hub-create) . Deze IoT-hub wordt ook toegevoegd aan *Contoso-US-Resource-Group*.

    In het volgende voor beeld wordt een IoT-hub gemaakt met de naam *Contoso-heatpumps-hub-1098* in de locatie *oostus* . U moet een unieke naam voor de hub gebruiken. Maak uw eigen achtervoegsel in de naam van de hub in plaats van **1098**. De voorbeeld code voor het aangepaste toewijzings beleid vereist `-heatpumps-` in de naam van de hub.

    ```azurecli-interactive 
    az iot hub create --name contoso-heatpumps-hub-1098 --resource-group contoso-us-resource-group --location eastus --sku S1
    ```

    Het kan enkele minuten duren voordat deze opdracht is voltooid.

## <a name="create-the-enrollment"></a>De inschrijving maken

In deze sectie maakt u een nieuwe registratie groep die gebruikmaakt van het aangepaste toewijzings beleid. Ter vereenvoudiging maakt dit artikel gebruik van [symmetrische sleutel attest](concepts-symmetric-key-attestation.md) met de inschrijving. Voor een veiligere oplossing kunt u het gebruik van [X. 509-certificaat attest](concepts-security.md#x509-certificates) met een vertrouwens keten gebruiken.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com)en open uw Device Provisioning service-exemplaar.

2. Selecteer het tabblad **inschrijvingen beheren** en klik vervolgens op de knop **registratie groep toevoegen** boven aan de pagina. 

3. Voer in **registratie groep toevoegen**de volgende gegevens in en klik op de knop **Opslaan** .

    **Groeps naam**: Voer **Contoso-Custom-allocated-devices**in.

    **Attestation-type**: Selecteer **symmetrische sleutel**.

    **Sleutels automatisch genereren**: dit selectie vakje moet al zijn ingeschakeld.

    **Selecteer de manier waarop u apparaten aan hubs wilt toewijzen**: Selecteer **aangepast (gebruik Azure function)** .

    ![Aangepaste toewijzings registratie groep voor symmetrische sleutel attest toevoegen](./media/how-to-use-custom-allocation-policies/create-custom-allocation-enrollment.png)

4. Klik in de **groep inschrijving toevoegen**op **een nieuwe IOT-hub koppelen** om beide nieuwe onderdelen van IOT-hubs te koppelen. 

    Voer deze stap uit voor zowel uw divisie IoT-hubs.

    **Abonnement**: als u meerdere abonnementen hebt, kiest u het abonnement waarin u de uitgebrachte IOT-hubs hebt gemaakt.

    **IOT hub**: Selecteer een van de divisie hubs die u hebt gemaakt.

    **Toegangs beleid**: Kies **iothubowner**.

    ![De afdeling IoT-hubs koppelen met de inrichtings service](./media/how-to-use-custom-allocation-policies/link-divisional-hubs.png)

5. Wanneer beide onderdelen van IoT-hubs zijn gekoppeld aan de **registratie groep**, moet u deze als de IOT hub groep voor de registratie groep selecteren, zoals hieronder wordt weer gegeven:

    ![De divisie groep voor de inschrijving maken](./media/how-to-use-custom-allocation-policies/enrollment-divisional-hub-group.png)

6. Ga in de **groep inschrijving toevoegen**naar het gedeelte **Azure function selecteren** en klik op **een nieuwe functie-app maken**.

7. Voer op **functie-app** pagina maken die wordt geopend de volgende instellingen voor de nieuwe functie in en klik op **maken**:

    **App-naam**: Voer een unieke naam in voor de functie-app. **Contoso-function-app-1098** wordt als voor beeld weer gegeven.

    **Resource groep**: Selecteer **bestaande gebruiken** en de **groep contoso-US-Resource-Group** om alle resources die in dit artikel zijn gemaakt, samen te laten.

    **Application Insights**: voor deze oefening kunt u dit uitschakelen.

    ![De functie-app maken](./media/how-to-use-custom-allocation-policies/function-app-create.png)

8. Ga terug naar de pagina **registratie groep toevoegen** en zorg ervoor dat de nieuwe functie-app is geselecteerd. Mogelijk moet u het abonnement opnieuw selecteren om de lijst met functie-apps te vernieuwen.

    Zodra de nieuwe functie-app is geselecteerd, klikt u op **een nieuwe functie maken**.

    ![De functie-app maken](./media/how-to-use-custom-allocation-policies/click-create-new-function.png)

    de nieuwe functie-app wordt geopend.

9. Klik in uw functie-app op **+** om een nieuwe functie te maken

    ![De functie-app maken](./media/how-to-use-custom-allocation-policies/new-function.png)

    Voor de nieuwe functie gebruikt u de standaard instellingen om een nieuwe **webhook + API** te maken met behulp van de **csharp** -taal. Klik op **deze functie maken**.

    Hiermee maakt u een C# nieuwe functie met de naam **HttpTriggerCSharp1**.

10. Vervang de code voor de nieuwe C# functie door de volgende code en klik op **Opslaan**:

    ```csharp
    #r "Newtonsoft.Json"
    using System.Net;
    using System.Text;
    using Newtonsoft.Json;

    public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
    {
        // Just some diagnostic logging
        log.Info("C# HTTP trigger function processed a request.");
        log.Info("Request.Content:...");
        log.Info(req.Content.ReadAsStringAsync().Result);

        // Get request body
        dynamic data = await req.Content.ReadAsAsync<object>();

        // Get registration ID of the device
        string regId = data?.deviceRuntimeContext?.registrationId;

        string message = "Uncaught error";
        bool fail = false;
        ResponseObj obj = new ResponseObj();

        if (regId == null)
        {
            message = "Registration ID not provided for the device.";
            log.Info("Registration ID : NULL");
            fail = true;
        }
        else
        {
            string[] hubs = data?.linkedHubs.ToObject<string[]>();

            // Must have hubs selected on the enrollment
            if (hubs == null)
            {
                message = "No hub group defined for the enrollment.";
                log.Info("linkedHubs : NULL");
                fail = true;
            }
            else
            {
                // This is a Contoso Toaster Model 007
                if (regId.Contains("-contoso-tstrsd-007"))
                {
                    //Find the "-toasters-" IoT hub configured on the enrollment
                    foreach(string hubString in hubs)
                    {
                        if (hubString.Contains("-toasters-"))
                            obj.iotHubHostName = hubString;
                    }

                    if (obj.iotHubHostName == null)
                    {
                        message = "No toasters hub found for the enrollment.";
                        log.Info(message);
                        fail = true;
                    }
                }
                // This is a Contoso Heat pump Model 008
                else if (regId.Contains("-contoso-hpsd-088"))
                {
                    //Find the "-heatpumps-" IoT hub configured on the enrollment
                    foreach(string hubString in hubs)
                    {
                        if (hubString.Contains("-heatpumps-"))
                            obj.iotHubHostName = hubString;
                    }

                    if (obj.iotHubHostName == null)
                    {
                        message = "No heat pumps hub found for the enrollment.";
                        log.Info(message);
                        fail = true;
                    }
                }
                // Unrecognized device.
                else
                {
                    fail = true;
                    message = "Unrecognized device registration.";
                    log.Info("Unknown device registration");
                }
            }
        }

        return (fail)
            ? req.CreateResponse(HttpStatusCode.BadRequest, message)
            : new HttpResponseMessage(HttpStatusCode.OK)
            {
                Content = new StringContent(JsonConvert.SerializeObject(obj, Formatting.Indented), Encoding.UTF8, "application/json")
            };
    }

    public class DeviceTwinObj
    {
        public string deviceId {get; set;}
    }

    public class ResponseObj
    {
        public string iotHubHostName {get; set;}
        public string IoTHub {get; set;}
        public DeviceTwinObj initialTwin {get; set;}
        public string[] linkedHubs {get; set;}
        public string enrollment {get; set;}
    }
    ```

11. Ga terug naar de pagina **registratie groep toevoegen** en controleer of de nieuwe functie is geselecteerd. Mogelijk moet u de functie-app opnieuw selecteren om de lijst met functies te vernieuwen.

    Zodra de nieuwe functie is geselecteerd, klikt u op **Opslaan** om de registratie groep op te slaan.

    ![Sla de registratie groep vervolgens op](./media/how-to-use-custom-allocation-policies/save-enrollment.png)

12. Nadat u de inschrijving hebt opgeslagen, opent u deze opnieuw en noteert u de **primaire sleutel**. U moet de inschrijving eerst opslaan om de sleutels te genereren. Deze sleutel wordt gebruikt voor het later genereren van unieke apparaatinstellingen voor gesimuleerde apparaten.

## <a name="derive-unique-device-keys"></a>Unieke Apparaatinstellingen afleiden

In deze sectie maakt u twee unieke Apparaatinstellingen. Er wordt één sleutel gebruikt voor een gesimuleerd apparaat voor een rooster. De andere sleutel wordt gebruikt voor een gesimuleerd hitte pomp-apparaat.

Als u de apparaatcode wilt genereren, gebruikt u de **primaire sleutel** die u eerder hebt genoteerd voor het berekenen van de [HMAC-sha256](https://wikipedia.org/wiki/HMAC) van de apparaat registratie-id voor elk apparaat en zet u het resultaat om in Base64-indeling. Voor meer informatie over het maken van afgeleide Apparaatinstellingen met registratie groepen, zie de sectie registraties van [symmetrische sleutel Attestation](concepts-symmetric-key-attestation.md).

Voor het voor beeld in dit artikel gebruikt u de volgende twee registratie-Id's van apparaten en berekent u een apparaatcode voor beide apparaten. Beide registratie-Id's hebben een geldig achtervoegsel dat kan worden gebruikt met de voorbeeld code voor het aangepaste toewijzings beleid:

* **breakroom499-contoso-tstrsd-007**
* **mainbuilding167-contoso-hpsd-088**

### <a name="linux-workstations"></a>Linux-werk stations

Als u een Linux-werk station gebruikt, kunt u openssl gebruiken om uw afgeleide apparaatinstellingen te genereren, zoals wordt weer gegeven in het volgende voor beeld.

1. Vervang de waarde van **Key** door de **primaire sleutel** die u eerder hebt genoteerd.

    ```bash
    KEY=oiK77Oy7rBw8YB6IS6ukRChAw+Yq6GC61RMrPLSTiOOtdI+XDu0LmLuNm11p+qv2I+adqGUdZHm46zXAQdZoOA==

    REG_ID1=breakroom499-contoso-tstrsd-007
    REG_ID2=mainbuilding167-contoso-hpsd-088

    keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
    devkey1=$(echo -n $REG_ID1 | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64)
    devkey2=$(echo -n $REG_ID2 | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64)

    echo -e $"\n\n$REG_ID1 : $devkey1\n$REG_ID2 : $devkey2\n\n"
    ```

    ```bash
    breakroom499-contoso-tstrsd-007 : JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=
    mainbuilding167-contoso-hpsd-088 : 6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=
    ```

### <a name="windows-based-workstations"></a>Windows-werk stations

Als u een Windows-werk station gebruikt, kunt u Power shell gebruiken om uw afgeleide apparaatwachtwoord te genereren, zoals wordt weer gegeven in het volgende voor beeld.

1. Vervang de waarde van **Key** door de **primaire sleutel** die u eerder hebt genoteerd.

    ```powershell
    $KEY='oiK77Oy7rBw8YB6IS6ukRChAw+Yq6GC61RMrPLSTiOOtdI+XDu0LmLuNm11p+qv2I+adqGUdZHm46zXAQdZoOA=='

    $REG_ID1='breakroom499-contoso-tstrsd-007'
    $REG_ID2='mainbuilding167-contoso-hpsd-088'

    $hmacsha256 = New-Object System.Security.Cryptography.HMACSHA256
    $hmacsha256.key = [Convert]::FromBase64String($key)
    $sig1 = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID1))
    $sig2 = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID2))
    $derivedkey1 = [Convert]::ToBase64String($sig1)
    $derivedkey2 = [Convert]::ToBase64String($sig2)

    echo "`n`n$REG_ID1 : $derivedkey1`n$REG_ID2 : $derivedkey2`n`n"
    ```

    ```powershell
    breakroom499-contoso-tstrsd-007 : JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=
    mainbuilding167-contoso-hpsd-088 : 6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=
    ```

De gesimuleerde apparaten gebruiken de afgeleide apparaatklassen met elke registratie-ID voor het uitvoeren van de symmetrische sleutel attest.

## <a name="prepare-an-azure-iot-c-sdk-development-environment"></a>Een ontwikkelomgeving voorbereiden voor de Azure IoT C-SDK

In deze sectie bereidt u de ontwikkelings omgeving voor die wordt gebruikt voor het bouwen van de [Azure IOT C-SDK](https://github.com/Azure/azure-iot-sdk-c). De SDK bevat de voorbeeld code voor het gesimuleerde apparaat. Dit gesimuleerde apparaat probeert de inrichting uit te voeren tijdens de opstartprocedure van het apparaat.

Deze sectie is gericht op een Windows-werk station. Zie voor een Linux-voor beeld de installatie van de virtuele machines in het [inrichten van multitenancy](how-to-provision-multitenant.md).

1. Down load het [cmake build-systeem](https://cmake.org/download/).

    Het is belang rijk dat de Visual Studio-vereisten (Visual Studio en de ' Desktop Development with C++'-werk belasting) op uw computer zijn geïnstalleerd **voordat** u de `CMake`-installatie start. Zodra de vereisten aanwezig zijn en de down load is gecontroleerd, installeert u het CMake build-systeem.

2. Open een opdrachtprompt of Git Bash-shell. Voer de volgende opdracht uit voor het klonen van de GitHub-opslagplaats voor de Azure IoT C-SDK:

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
    ```

    Deze bewerking kan enkele minuten in beslag nemen.

3. Maak de submap `cmake` in de hoofdmap van de Git-opslagplaats en navigeer naar die map. 

    ```cmd/sh
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

4. Voer de volgende opdracht uit om een versie van de SDK te bouwen die specifiek is voor uw clientplatform voor ontwikkeling. Er wordt een Visual Studio-oplossing voor het gesimuleerde apparaat gegenereerd in de map `cmake`. 

    ```cmd
    cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    ```

    Als `cmake` uw C++ compiler niet vindt, kunt u tijdens het uitvoeren van de opdracht build-fouten krijgen. Als dat gebeurt, voert u de opdracht uit in de [Visual Studio-opdracht prompt](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs).

    Zodra het bouwen is voltooid, zijn de laatste paar uitvoerregels vergelijkbaar met de volgende uitvoer:

    ```cmd/sh
    $ cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    -- Building for: Visual Studio 15 2017
    -- Selecting Windows SDK version 10.0.16299.0 to target Windows 10.0.17134.
    -- The C compiler identification is MSVC 19.12.25835.0
    -- The CXX compiler identification is MSVC 19.12.25835.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
    ```

## <a name="simulate-the-devices"></a>De apparaten simuleren

In deze sectie werkt u een inrichtings voorbeeld bij met de naam **prov\_dev\_client\_voor beeld** dat zich bevindt in de Azure IOT C-SDK die u eerder hebt ingesteld.

Met deze voorbeeld code wordt een opstart volgorde voor apparaten gesimuleerd die de inrichtings aanvraag naar uw Device Provisioning service-exemplaar verzendt. De opstart procedure zorgt ervoor dat het apparaat voor de pop-upmodus wordt herkend en toegewezen aan de IoT-hub met behulp van het aangepaste toewijzings beleid.

1. Selecteer in Azure Portal het tabblad **Overzicht** voor uw Device Provisioning-service en noteer de waarde van het **_Id-bereik_** .

    ![Device Provisioning Service-eindpuntgegevens uit de portalblade extraheren](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. Open in Visual Studio het oplossings bestand **azure_iot_sdks. SLN** dat is gegenereerd door cmake eerder uit te voeren. Het oplossingsbestand bevindt zich als het goed is op de volgende locatie:

    ```
    \azure-iot-sdk-c\cmake\azure_iot_sdks.sln
    ```

3. Navigeer in het deelvenster *Solution Explorer* van Visual Studio naar de map **Provision\_Samples**. Vouw het voorbeeldproject met de naam **prov\_dev\_client\_sample** uit. Vouw **Source Files** uit en open **prov\_dev\_client\_sample.c**.

4. Zoek de constante `id_scope` op en vervang de waarde door uw **Id-bereik**-waarde die u eerder hebt gekopieerd. 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

5. Zoek de definitie voor de functie `main()` op in hetzelfde bestand. Zorg ervoor dat de variabele `hsm_type` is ingesteld op `SECURE_DEVICE_TYPE_SYMMETRIC_KEY`, zoals hieronder wordt weergegeven:

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
    ```

6. Klik met de rechtermuisknop op het **prov\_dev\_client\_sample**-project en selecteer **Set as Startup Project**. 

### <a name="simulate-the-contoso-toaster-device"></a>Het contoso-pop-upapparaat simuleren

1. Als u het apparaat voor de pop-uptaak wilt simuleren, vindt u de aanroep van `prov_dev_set_symmetric_key_info()` in **prov\_dev\_client\_sample. c** .

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    Verwijder de opmerking over de functie aanroep en vervang de waarden van de tijdelijke aanduiding (inclusief de punt haken) door de registratie-ID van de pop-up en de afgeleide apparaatcode die u eerder hebt gemaakt. De sleutel waarde **JC8F96eayuQwwz + PkE7IzjH2lIAjCUnAa61tDigBnSs =** hieronder wordt alleen weer gegeven als voor beeld.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("breakroom499-contoso-tstrsd-007", "JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=");
    ```

    Sla het bestand op.

2. Selecteer in het menu van Visual Studio de optie **Debug** > **Start without debugging** om de oplossing uit te voeren. Klik in de prompt om het project opnieuw te bouwen op **Yes** om het project opnieuw te bouwen voordat het wordt uitgevoerd.

    De volgende uitvoer is een voor beeld van het gesimuleerde computer apparaat dat wordt opgestart en verbinding maakt met het exemplaar van de inrichtings service dat moet worden toegewezen aan de opties voor de IoT-hub van de pop-up via het aangepaste toewijzings beleid:

    ```cmd
    Provisioning API Version: 1.2.9

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-toasters-hub-1098.azure-devices.net, deviceId: breakroom499-contoso-tstrsd-007

    Press enter key to exit:
    ```

### <a name="simulate-the-contoso-heat-pump-device"></a>Het apparaat van de contoso hitte pomp simuleren

1. Voor het simuleren van het apparaat voor warmte pomp moet u de aanroep naar `prov_dev_set_symmetric_key_info()` in **prov\_dev\_client\_sample. c** opnieuw uitvoeren met de warmte pomp registratie-id en de afgeleide apparaatcode die u eerder hebt gegenereerd. De sleutel waarde **6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg =** hieronder weer gegeven, wordt ook alleen als voor beeld gegeven.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("mainbuilding167-contoso-hpsd-088", "6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=");
    ```

    Sla het bestand op.

2. Selecteer in het menu van Visual Studio de optie **Debug** > **Start without debugging** om de oplossing uit te voeren. In de prompt om het project opnieuw op te bouwen, klikt u op **Ja** om het project opnieuw te bouwen voordat u het uitvoert.

    De volgende uitvoer is een voor beeld van het gesimuleerde hitte pomp-apparaat dat wordt opgestart en er verbinding mee wordt gemaakt met het exemplaar van de inrichtings service dat moet worden toegewezen aan de groep van de aangepaste toewijzings beleid van de contoso hitte pompen IoT-hub:

    ```cmd
    Provisioning API Version: 1.2.9

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-heatpumps-hub-1098.azure-devices.net, deviceId: mainbuilding167-contoso-hpsd-088

    Press enter key to exit:
    ```

## <a name="troubleshooting-custom-allocation-policies"></a>Problemen met aangepaste toewijzings beleid oplossen

De volgende tabel toont de verwachte scenario's en de resultaten van de fout codes die u kunt ontvangen. Gebruik deze tabel voor hulp bij het oplossen van fouten met aangepaste toewijzings beleid met uw Azure Functions.

| Scenario | Registratie resultaat van de inrichtings service | SDK-resultaten inrichten |
| -------- | --------------------------------------------- | ------------------------ |
| De webhook retourneert 200 OK waarbij ' iotHubHostName ' is ingesteld op een geldige IoT hub-hostnaam | Resultaat status: toegewezen  | SDK retourneert PROV_DEVICE_RESULT_OK samen met informatie over de hub |
| De webhook retourneert 200 OK met ' iotHubHostName ' die in het antwoord aanwezig is, maar ingesteld op een lege teken reeks of Null | Resultaat status: mislukt<br><br> Fout code: CustomAllocationIotHubNotSpecified (400208) | SDK retourneert PROV_DEVICE_RESULT_HUB_NOT_SPECIFIED |
| De webhook retourneert 401 niet-geautoriseerd | Resultaat status: mislukt<br><br>Fout code: CustomAllocationUnauthorizedAccess (400209) | SDK retourneert PROV_DEVICE_RESULT_UNAUTHORIZED |
| Er is een afzonderlijke registratie gemaakt om het apparaat uit te scha kelen | Resultaat status: uitgeschakeld | SDK retourneert PROV_DEVICE_RESULT_DISABLED |
| De webhook retourneert fout code > = 429 | In de indeling van DPS wordt een aantal keren opnieuw geprobeerd. Het beleid voor opnieuw proberen is momenteel:<br><br>&nbsp;&nbsp;-aantal nieuwe pogingen: 10<br>&nbsp;&nbsp;-eerste interval: 1S<br>&nbsp;&nbsp;-toename: 9 's | De SDK negeert fout en verzendt een nieuw status bericht voor ophalen in de opgegeven tijd |
| De webhook retourneert een andere status code | Resultaat status: mislukt<br><br>Fout code: CustomAllocationFailed (400207) | SDK retourneert PROV_DEVICE_RESULT_DEV_AUTH_ERROR |

## <a name="clean-up-resources"></a>Resources opschonen

Als u van plan bent om verder te gaan met de resources die in dit artikel zijn gemaakt, kunt u ze blijven gebruiken. Als u niet van plan bent om de resources te blijven gebruiken, gebruikt u de volgende stappen om alle resources te verwijderen die in dit artikel zijn gemaakt om onnodige kosten te voor komen.

In de volgende stappen wordt ervan uitgegaan dat u alle resources in dit artikel hebt gemaakt, zoals u hebt opgegeven in dezelfde resource groep met de naam **Contoso-US-Resource-Group**.

> [!IMPORTANT]
> Het verwijderen van een resourcegroep kan niet ongedaan worden gemaakt. De resourcegroep en alle resources daarin worden permanent verwijderd. Zorg ervoor dat u niet per ongeluk de verkeerde resourcegroep of resources verwijdert. Als u de IoT Hub in een bestaande resourcegroep hebt gemaakt met resources die u wilt behouden, moet u alleen de IoT Hub-resource zelf verwijderen in plaats van de resourcegroep te verwijderen.
>

De resource groep op naam verwijderen:

1. Meld u aan bij [Azure Portal](https://portal.azure.com) en klik op **Resourcegroepen**.

2. Typ in het tekstvak **filteren op naam...** de naam van de resource groep met uw resources, **Contoso-US-Resource-Group**. 

3. Klik rechts van de resourcegroep in de lijst met resultaten op **...** en vervolgens op **Resourcegroep verwijderen**.

4. U wordt gevraagd om het verwijderen van de resource groep te bevestigen. Typ de naam van de resourcegroep nogmaals om te bevestigen en klik op **Verwijderen**. Na enkele ogenblikken worden de resourcegroep en alle resources in de groep verwijderd.

## <a name="next-steps"></a>Volgende stappen

* Zie [IOT hub-concepten](concepts-device-reprovision.md) voor het opnieuw inrichten van apparaten voor meer informatie. 
* Zie voor meer informatie over het ongedaan maken van de inrichting van [apparaten die eerder zijn ingericht](how-to-unprovision-devices.md) , ongedaan maken 
