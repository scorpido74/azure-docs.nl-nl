---
title: Aangepaste toewijzingsbeleidsregels met Azure IoT Hub Device Provisioning Service
description: Aangepaste toewijzingsbeleidsregels gebruiken met de DPS (Azure IoT Hub Device Provisioning Service)
author: wesmc7777
ms.author: wesmc
ms.date: 11/14/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 87ffca1957d4ec449753f1966ed05cf3948f5ca2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75453947"
---
# <a name="how-to-use-custom-allocation-policies"></a>Aangepaste toewijzingsbeleidsregels gebruiken

Een aangepast toewijzingsbeleid geeft u meer controle over de manier waarop apparaten aan een IoT-hub worden toegewezen. Dit wordt bereikt door aangepaste code in een [Azure-functie](../azure-functions/functions-overview.md) te gebruiken om apparaten toe te wijzen aan een IoT-hub. De service voor het inrichten van apparaten roept uw Azure-functiecode op met alle relevante informatie over het apparaat en de inschrijving. Uw functiecode wordt uitgevoerd en retourneert de IoT-hub-informatie die wordt gebruikt voor het inrichten van het apparaat.

Met behulp van aangepast toewijzingsbeleid bepaalt u uw eigen toewijzingsbeleid wanneer het beleid van de Apparaatinrichtingsservice niet voldoet aan de vereisten van uw scenario.

Misschien wilt u bijvoorbeeld het certificaat onderzoeken dat een apparaat gebruikt tijdens het inrichten en het apparaat toewijzen aan een IoT-hub op basis van een certificaateigenschap. Of misschien hebt u informatie die is opgeslagen in een database voor uw apparaten en moet u de database opvragen om te bepalen aan welke IoT-hub een apparaat moet worden toegewezen.

In dit artikel wordt een aangepast toewijzingsbeleid getoond met behulp van een Azure-functie die is geschreven in C#. Er worden twee nieuwe IoT-hubs gecreëerd die een *Contoso Toasters Division* en een *Contoso Heat Pumps Division vertegenwoordigen.* Hulpmiddelen die om voorzieningen vragen, moeten over een registratie-id beschikken met een van de volgende achtervoegsels die voor de voorziening moeten worden aanvaard:

* **-contoso-tstrsd-007**: Contoso Toasters Division
* **-contoso-hpsd-088**: Contoso Warmtepompen Divisie

De apparaten worden ingericht op basis van een van deze vereiste achtervoegsels op de registratie-ID. Deze apparaten worden gesimuleerd met behulp van een indiseringsvoorbeeld dat is opgenomen in de [Azure IoT C SDK.](https://github.com/Azure/azure-iot-sdk-c)

U voert de volgende stappen uit in dit artikel:

* Gebruik de Azure CLI om twee IoT-hubs van de Contoso-divisie **(Contoso Toasters Division** en **Contoso Heat Pumps Division)** te maken.
* Een nieuwe groepsinschrijving maken met behulp van een Azure-functie voor het aangepaste toewijzingsbeleid
* Maak apparaatsleutels voor twee apparaatsimulaties.
* De ontwikkelomgeving instellen voor de Azure IoT C SDK
* De apparaten simuleren en controleren of ze zijn ingericht volgens de voorbeeldcode in het aangepaste toewijzingsbeleid

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

De volgende voorwaarden zijn voor een Windows-ontwikkelomgeving. Zie voor Linux of macOS de juiste sectie in [Uw ontwikkelomgeving voorbereiden](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) in de SDK-documentatie.

* [Visual Studio](https://visualstudio.microsoft.com/vs/) 2019 met de ['Desktop development with C++'](https://docs.microsoft.com/cpp/?view=vs-2019#pivot=workloads) workload enabled. Ook Visual Studio 2015 en Visual Studio 2017 worden ondersteund.

* Meest recente versie van [Git](https://git-scm.com/download/) geïnstalleerd.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-the-provisioning-service-and-two-divisional-iot-hubs"></a>De inrichtingsservice en twee divisieiot-hubs maken

In deze sectie gebruikt u de Azure Cloud Shell om een inrichtingsservice en twee IoT-hubs te maken die de **Divisie Contoso-broodroosters** en de **divisie Contoso-warmtepompen**vertegenwoordigen.

> [!TIP]
> De opdrachten die in dit artikel worden gebruikt, maken de inrichtingsservice en andere resources op de locatie West-VS. We raden u aan uw resources te maken in de regio die bij u in de buurt ligt en die de service voor apparaatinrichting ondersteunt. U kunt een lijst met beschikbare locaties weergeven door de opdracht `az provider show --namespace Microsoft.Devices --query "resourceTypes[?resourceType=='ProvisioningServices'].locations | [0]" --out table` uit te voeren of door naar de pagina [Status van Azure](https://azure.microsoft.com/status/) te gaan en te zoeken naar 'device provisioning service'. In opdrachten kunnen locaties worden opgegeven in één woord- of meerdere woordindeling. bijvoorbeeld: westus, West US, WEST US, enz. De waarde is niet hoofdlettergevoelig. Als u meerdere woorden gebruikt om een locatie op te geven, voeg dan de waarde toe tussen aanhalingstekens; bijvoorbeeld: `-- location "West US"`.
>

1. Gebruik de Azure Cloud Shell om een resourcegroep te maken met de opdracht [AZ-groep maken.](/cli/azure/group#az-group-create) Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd.

    In het volgende voorbeeld wordt een resourcegroep met de naam *contoso-us-resource-group* in de *westusregio.* Het wordt aanbevolen deze groep te gebruiken voor alle bronnen die in dit artikel zijn gemaakt. Deze aanpak zal het opruimen gemakkelijker maken nadat u klaar bent.

    ```azurecli-interactive 
    az group create --name contoso-us-resource-group --location westus
    ```

2. Gebruik de Azure Cloud Shell om een apparaatinrichtingsservice te maken met de opdracht [az iot dps maken.](/cli/azure/iot/dps#az-iot-dps-create) De inrichtingsservice wordt toegevoegd aan *contoso-us-resource-group.*

    In het volgende voorbeeld wordt een inrichtingsservice met de naam *contoso-provisioning-service-1098* op de *locatie Westus.* U moet een unieke servicenaam gebruiken. Maak uw eigen achtervoegsel in de servicenaam in plaats van **1098.**

    ```azurecli-interactive 
    az iot dps create --name contoso-provisioning-service-1098 --resource-group contoso-us-resource-group --location westus
    ```

    Deze opdracht kan enkele minuten in beslag nemen.

3. Gebruik de Azure Cloud Shell om de **IoT-hub van De** Divisie Voor 200-nl te maken met de opdracht [AZ Iot-hub.](/cli/azure/iot/hub#az-iot-hub-create) De IoT-hub wordt toegevoegd aan *de contoso-us-resource-group.*

    In het volgende voorbeeld wordt een IoT-hub gemaakt met de naam *contoso-broodroosters-hub-1098* op de *westuslocatie.* U moet een unieke hubnaam gebruiken. Maak je eigen achtervoegsel in de hubnaam in plaats van **1098.** De voorbeeldcode voor het `-toasters-` aangepaste toewijzingsbeleid vereist in de hubnaam.

    ```azurecli-interactive 
    az iot hub create --name contoso-toasters-hub-1098 --resource-group contoso-us-resource-group --location westus --sku S1
    ```

    Deze opdracht kan enkele minuten in beslag nemen.

4. Gebruik de Azure Cloud Shell om de **IoT-hub van De Divisie** IoT van De Warmtedivisie te maken met de opdracht [AZ Iot-hub.](/cli/azure/iot/hub#az-iot-hub-create) Deze IoT-hub wordt ook toegevoegd aan *de contoso-us-resource-group.*

    In het volgende voorbeeld wordt een IoT-hub gemaakt met de naam *contoso-heatpumps-hub-1098* op de *locatie Westus.* U moet een unieke hubnaam gebruiken. Maak je eigen achtervoegsel in de hubnaam in plaats van **1098.** De voorbeeldcode voor het `-heatpumps-` aangepaste toewijzingsbeleid vereist in de hubnaam.

    ```azurecli-interactive 
    az iot hub create --name contoso-heatpumps-hub-1098 --resource-group contoso-us-resource-group --location westus --sku S1
    ```

    Deze opdracht kan enkele minuten in beslag nemen.

## <a name="create-the-custom-allocation-function"></a>De aangepaste toewijzingsfunctie maken

In deze sectie maakt u een Azure-functie die uw aangepaste toewijzingsbeleid implementeert. Deze functie bepaalt aan welke divisie-IoT-hub een apparaat moet worden geregistreerd op basis van de vraag of de registratie-ID de tekenreeks **-contoso-tstrsd-007** of **-contoso-hpsd-088**bevat . Het stelt ook de initiële toestand van het apparaat twin op basis van de vraag of het apparaat is een broodrooster of een warmtepomp.

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Selecteer op uw startpagina **+ Een resource maken**.

2. Typ 'Functie-app' in het zoekvak *Marketplace.* Selecteer **functie-app**in de vervolgkeuzelijst Functie-app en selecteer **Vervolgens Maken**.

3. Voer op de pagina **Functie-app** maken onder het tabblad **Basisbeginselen** de volgende instellingen voor uw nieuwe functie-app in en selecteer **Controleren + maken:**

    **Resourcegroep**: Selecteer de **contoso-us-resource-groep** om alle bronnen die in dit artikel zijn gemaakt bij elkaar te houden.

    **Functie-app-naam**: Voer een unieke functie-appnaam in. In dit voorbeeld wordt gebruik gebruikt **voor contoso-functie-app-1098**.

    **Publiceren:** controleer of **code** is geselecteerd.

    **Runtime Stack:** Selecteer **.NET Core** in de vervolgkeuzelijst.

    **Regio**: Selecteer hetzelfde gebied als uw resourcegroep. In dit voorbeeld wordt West **US gebruikt.**

    > [!NOTE]
    > Standaard is Application Insights ingeschakeld. Application Insights is niet nodig voor dit artikel, maar het kan u helpen eventuele problemen die u tegenkomt met de aangepaste toewijzing te begrijpen en te onderzoeken. U desgevraagd toepassingsinzichten uitschakelen door het tabblad **Controle te** selecteren en vervolgens **Nee** voor **Toepassingsinzichten inschakelen**te selecteren.

    ![Een Azure-functie-app maken om de aangepaste toewijzingsfunctie te hosten](./media/how-to-use-custom-allocation-policies/create-function-app.png)

4. Selecteer **op** de pagina Overzicht de optie **Maken** om de functie-app te maken. De implementatie kan enkele minuten duren. Wanneer deze is voltooid, selecteert u **Ga naar resource**.

5. Selecteer **+** in het linkerdeelvenster van de pagina **Overzicht** van de functie-app naast **Functies** om een nieuwe functie toe te voegen.

    ![Een functie toevoegen aan de functie-app](./media/how-to-use-custom-allocation-policies/create-function.png)

6. Selecteer op de pagina **Azure-functies voor .NET - aan de slag** voor de stap EEN **IMPLEMENTATIE-omgeving KIEZEN** de tegel **In-portal** en selecteer **Doorgaan**.

    ![De poort voor de ontwikkeling van portalen selecteren](./media/how-to-use-custom-allocation-policies/function-choose-environment.png)

7. Selecteer op de volgende pagina voor de stap **EEN FUNCTIE MAKEN** de tegel **Webhook + API** en selecteer Vervolgens **Maken**. Er wordt een functie met de naam **HttpTrigger1** gemaakt en de portal geeft de inhoud van het **codebestand run.csx** weer.

8. Referentie vereiste Nuget pakketten. Om de eerste apparaattweeling te maken, gebruikt de aangepaste toewijzingsfunctie klassen die zijn gedefinieerd in twee Nuget-pakketten die in de hostingomgeving moeten worden geladen. Met Azure-functies wordt naar Nuget-pakketten verwezen met behulp van een *function.host-bestand.* In deze stap slaat u een *function.host-bestand* op en uploadt u deze.

    1. Kopieer de volgende regels naar uw favoriete editor en sla het bestand op uw computer op als *function.host*.

        ```xml
        <Project Sdk="Microsoft.NET.Sdk">  
            <PropertyGroup>  
                <TargetFramework>netstandard2.0</TargetFramework>  
            </PropertyGroup>  
            <ItemGroup>  
                <PackageReference Include="Microsoft.Azure.Devices.Provisioning.Service" Version="1.5.0" />  
                <PackageReference Include="Microsoft.Azure.Devices.Shared" Version="1.16.0" />  
            </ItemGroup>  
        </Project>
        ```

    2. Vouw in de functie **HttpTrigger1** het tabblad **Bestanden weergeven** aan de rechterkant van het venster uit.

        ![Weergavebestanden openen](./media/how-to-use-custom-allocation-policies/function-open-view-files.png)

    3. Selecteer **Uploaden**, blader naar het **bestand function.proj** en selecteer **Openen** om het bestand te uploaden.

        ![Uploadbestand selecteren](./media/how-to-use-custom-allocation-policies/function-choose-upload-file.png)

9. Vervang de code voor de functie **HttpTrigger1** door de volgende code en selecteer **Opslaan:**

    ```csharp
    #r "Newtonsoft.Json"

    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;

    using Microsoft.Azure.Devices.Shared;               // For TwinCollection
    using Microsoft.Azure.Devices.Provisioning.Service; // For TwinState

    public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        // Get request body
        string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
        dynamic data = JsonConvert.DeserializeObject(requestBody);

        log.LogInformation("Request.Body:...");
        log.LogInformation(requestBody);

        // Get registration ID of the device
        string regId = data?.deviceRuntimeContext?.registrationId;

        string message = "Uncaught error";
        bool fail = false;
        ResponseObj obj = new ResponseObj();

        if (regId == null)
        {
            message = "Registration ID not provided for the device.";
            log.LogInformation("Registration ID : NULL");
            fail = true;
        }
        else
        {
            string[] hubs = data?.linkedHubs.ToObject<string[]>();

            // Must have hubs selected on the enrollment
            if (hubs == null)
            {
                message = "No hub group defined for the enrollment.";
                log.LogInformation("linkedHubs : NULL");
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
                        log.LogInformation(message);
                        fail = true;
                    }
                    else
                    {
                        // Specify the initial tags for the device.
                        TwinCollection tags = new TwinCollection();
                        tags["deviceType"] = "toaster";

                        // Specify the initial desired properties for the device.
                        TwinCollection properties = new TwinCollection();
                        properties["state"] = "ready";
                        properties["darknessSetting"] = "medium";

                        // Add the initial twin state to the response.
                        TwinState twinState = new TwinState(tags, properties);
                        obj.initialTwin = twinState;
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
                        log.LogInformation(message);
                        fail = true;
                    }
                    else
                    {
                        // Specify the initial tags for the device.
                        TwinCollection tags = new TwinCollection();
                        tags["deviceType"] = "heatpump";

                        // Specify the initial desired properties for the device.
                        TwinCollection properties = new TwinCollection();
                        properties["state"] = "on";
                        properties["temperatureSetting"] = "65";

                        // Add the initial twin state to the response.
                        TwinState twinState = new TwinState(tags, properties);
                        obj.initialTwin = twinState;
                    }
                }
                // Unrecognized device.
                else
                {
                    fail = true;
                    message = "Unrecognized device registration.";
                    log.LogInformation("Unknown device registration");
                }
            }
        }

        log.LogInformation("\nResponse");
        log.LogInformation((obj.iotHubHostName != null) ? JsonConvert.SerializeObject(obj) : message);

        return (fail)
            ? new BadRequestObjectResult(message) 
            : (ActionResult)new OkObjectResult(obj);
    }

    public class ResponseObj
    {
        public string iotHubHostName {get; set;}
        public TwinState initialTwin {get; set;}
    }
    ```

## <a name="create-the-enrollment"></a>De inschrijving maken

In deze sectie maakt u een nieuwe inschrijvingsgroep die het aangepaste toewijzingsbeleid gebruikt. Voor de eenvoud maakt dit artikel gebruik [van symmetrische sleutelattest](concepts-symmetric-key-attestation.md) bij de inschrijving. Voor een veiligere oplossing u overwegen [om X.509-certificaatattest te](concepts-security.md#x509-certificates) gebruiken met een vertrouwensketen.

1. Nog steeds op de [Azure-portal,](https://portal.azure.com)open uw inrichtingsservice.

2. Selecteer **Inschrijvingen beheren** in het linkerdeelvenster en selecteer de knop **Inschrijvingsgroep toevoegen** boven aan de pagina.

3. Voer **bij Groep inschrijving toevoegen**de volgende gegevens in en selecteer de knop **Opslaan.**

    **Groepsnaam**: Voer apparaten in die **op maat**zijn toegewezen .

    **Attesttype**: **Symmetrische toets selecteren**.

    **Automatisch genereren sleutels:** Dit selectievakje moet al worden aangevinkt.

    **Selecteer hoe u apparaten wilt toewijzen aan hubs:** Selecteer **Aangepast (Azure-functie gebruiken)**.

    ![Aangepaste toewijzingsinschrijvingsgroep toevoegen voor symmetrische sleutelattest](./media/how-to-use-custom-allocation-policies/create-custom-allocation-enrollment.png)

4. Selecteer **bij Inschrijvingsgroep toevoegen**de optie Een nieuwe **IoT-hub koppelen** om beide nieuwe divisieiot-hubs te koppelen.

    Voer deze stap uit voor beide divisieiot-hubs.

    **Abonnement:** Als u meerdere abonnementen hebt, kiest u het abonnement waarbij u de divisieal IoT-hubs hebt gemaakt.

    **IoT-hub**: selecteer een van de divisiehubs die u hebt gemaakt.

    **Toegangsbeleid:** kies **iothubowner**.

    ![De divisiele IoT-hubs koppelen aan de inrichtingsservice](./media/how-to-use-custom-allocation-policies/link-divisional-hubs.png)

5. In **De inschrijvingsgroep toevoegen**, moet u deze selecteren als De IoT Hub-groep voor de inschrijvingsgroep zoals hieronder weergegeven:

    ![De divisiehubgroep voor de inschrijving maken](./media/how-to-use-custom-allocation-policies/enrollment-divisional-hub-group.png)

6. Schuif **bij Inschrijvingsgroep toevoegen**naar de sectie **Azure-functie selecteren** de functie-app die u in de vorige sectie hebt gemaakt. Selecteer vervolgens de functie die u hebt gemaakt en selecteer Opslaan om de inschrijvingsgroep op te slaan.

    ![De functie selecteren en de inschrijvingsgroep opslaan](./media/how-to-use-custom-allocation-policies/save-enrollment.png)

7. Nadat u de inschrijving hebt opgeslagen, opent u deze opnieuw en maakt u een notitie van de **primaire sleutel.** U moet de inschrijving eerst opslaan om de sleutels te laten genereren. Deze sleutel wordt later gebruikt om unieke apparaatsleutels voor gesimuleerde apparaten te genereren.

## <a name="derive-unique-device-keys"></a>Unieke apparaatsleutels afleiden

In deze sectie maakt u twee unieke apparaattoetsen. Eén sleutel wordt gebruikt voor een gesimuleerd broodroosterapparaat. De andere sleutel wordt gebruikt voor een gesimuleerde warmtepomp.

Als u de apparaatsleutel wilt genereren, gebruikt u de **primaire sleutel** die u eerder hebt opgemerkt om de [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) van de apparaatregistratie-ID voor elk apparaat te berekenen en het resultaat om te zetten in Base64-indeling. Zie de sectie groepsinschrijvingen van [symmetrisch sleutelattest](concepts-symmetric-key-attestation.md)voor meer informatie over het maken van afgeleide apparaatsleutels met inschrijvingsgroepen.

Gebruik voor het voorbeeld in dit artikel de volgende twee apparaatregistratie-id's en bereken een apparaatsleutel voor beide apparaten. Beide registratie-id's hebben een geldig achtervoegsel om te werken met de voorbeeldcode voor het aangepaste toewijzingsbeleid:

* **breakroom499-contoso-tstrsd-007**
* **mainbuilding167-contoso-hpsd-088**

### <a name="linux-workstations"></a>Linux-werkstations

Als u een Linux-werkstation gebruikt, u openssl gebruiken om uw afgeleide apparaatsleutels te genereren, zoals in het volgende voorbeeld wordt weergegeven.

1. Vervang de waarde van **KEY** door de **primaire sleutel die** u eerder hebt opgemerkt.

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

### <a name="windows-based-workstations"></a>Windows-werkstations

Als u een werkstation op basis van Windows gebruikt, u PowerShell gebruiken om uw afgeleide apparaatsleutel te genereren, zoals in het volgende voorbeeld wordt weergegeven.

1. Vervang de waarde van **KEY** door de **primaire sleutel die** u eerder hebt opgemerkt.

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

De gesimuleerde apparaten gebruiken de afgeleide apparaatsleutels bij elke registratie-ID om symmetrische sleutelattest uit te voeren.

## <a name="prepare-an-azure-iot-c-sdk-development-environment"></a>Een ontwikkelomgeving voorbereiden voor de Azure IoT C-SDK

In deze sectie bereidt u de ontwikkelomgeving voor die wordt gebruikt om de [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c)te bouwen. De SDK bevat de voorbeeldcode voor het gesimuleerde apparaat. Dit gesimuleerde apparaat probeert de inrichting uit te voeren tijdens de opstartprocedure van het apparaat.

Deze sectie is gericht op een Windows-werkstation. Zie voor een Voorbeeld van Linux de set-up van de VM's in [Hoe u multitenancy inrichten.](how-to-provision-multitenant.md)

1. Download het [CMake-buildsysteem.](https://cmake.org/download/)

    Het is belangrijk dat de vereisten voor Visual Studio met (Visual Studio en de workload Desktopontwikkeling met C++) op uw computer zijn geïnstalleerd **voordat** de `CMake`-installatie wordt gestart. Zodra aan de vereisten is voldaan en de download is geverifieerd, installeert u het CMake-bouwsysteem.

2. Zoek de tagnaam voor de [nieuwste versie](https://github.com/Azure/azure-iot-sdk-c/releases/latest) van de SDK.

3. Open een opdrachtprompt of Git Bash-shell. Voer de volgende opdrachten uit om de nieuwste versie van de [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub-repository te klonen. Gebruik de tag die u in de `-b` vorige stap hebt gevonden als de waarde voor de parameter:

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Deze bewerking kan enkele minuten in beslag nemen.

4. Maak de submap `cmake` in de hoofdmap van de Git-opslagplaats en navigeer naar die map. Voer de volgende opdrachten `azure-iot-sdk-c` uit de map uit:

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

5. Voer de volgende opdracht uit om een versie van de SDK te bouwen die specifiek is voor uw clientplatform voor ontwikkeling. Er wordt een Visual Studio-oplossing voor het gesimuleerde apparaat gegenereerd in de map `cmake`. 

    ```cmd
    cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    ```

    Als `cmake` u uw C++ compiler niet vindt, u buildfouten krijgen tijdens het uitvoeren van de opdracht. Als dat gebeurt, probeert u de opdracht uit te voeren in de [opdrachtprompt visual studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs).

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

In deze sectie werkt u een voorbeeld van **prov dev-client\_\_\_** met de naam prov dev-client bij in de Azure IoT C SDK die u eerder hebt ingesteld.

Met deze voorbeeldcode wordt een opstartreeks voor apparaten gesimuleerd die het inrichtingsverzoek naar het instantieexemplaar Apparaatinrichtingsservice verzendt. De opstartreeks zorgt ervoor dat het broodroosterapparaat wordt herkend en toegewezen aan de IoT-hub met behulp van het aangepaste toewijzingsbeleid.

1. Selecteer in Azure Portal het tabblad **Overzicht** voor uw Device Provisioning-service en noteer de waarde van het **_Id-bereik_**.

    ![Device Provisioning Service-eindpuntgegevens uit de portalblade extraheren](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. Open in Visual Studio het **bestand met azure_iot_sdks.sln-oplossing** dat is gegenereerd door CMake eerder uit te voeren. Het oplossingsbestand bevindt zich als het goed is op de volgende locatie:

    ```
    azure-iot-sdk-c\cmake\azure_iot_sdks.sln
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

### <a name="simulate-the-contoso-toaster-device"></a>Het Contoso-broodroosterapparaat simuleren

1. Als u het broodroosterapparaat wilt `prov_dev_set_symmetric_key_info()` simuleren, zoekt u de oproep naar **prov\_dev\_client\_sample.c** die wordt becommentarieerd.

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    Maak de functieaanroep ongedaan en vervang de tijdelijke aanduidingswaarden (inclusief de hoekhaakjes) door de registratie-id van de broodrooster en de afgeleide apparaatsleutel die u eerder hebt gegenereerd. De kernwaarde **JC8F96eayuQwwz+PkE7IzjH2lIAjCA61tDigBnSs=** hieronder wordt slechts als voorbeeld gegeven.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("breakroom499-contoso-tstrsd-007", "JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=");
    ```

    Sla het bestand op.

2. Selecteer in het menu Visual Studio de optie **Foutopsporing** > **starten zonder foutopsporing** om de oplossing uit te voeren. Selecteer **Ja**, om het project opnieuw op te bouwen voordat u het project uitvoert.

    De volgende uitvoer is een voorbeeld van het gesimuleerde roosterapparaat dat met succes wordt opgestart en verbinding maakt met de inrichtingsserviceinstantie die door het aangepaste toewijzingsbeleid aan de IoT-hub voor broodroosters moet worden toegewezen:

    ```cmd
    Provisioning API Version: 1.3.6

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-toasters-hub-1098.azure-devices.net, deviceId: breakroom499-contoso-tstrsd-007

    Press enter key to exit:
    ```

### <a name="simulate-the-contoso-heat-pump-device"></a>Simuleer het Contoso-warmtepompapparaat

1. Als u het warmtepompapparaat wilt `prov_dev_set_symmetric_key_info()` simuleren, werkt u de oproep opnieuw bij naar **prov\_dev\_client\_sample.c** met de registratie-ID van de warmtepomp en de afgeleide apparaatsleutel die u eerder hebt gegenereerd. De kernwaarde **6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=** hieronder wordt ook slechts als voorbeeld gegeven.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("mainbuilding167-contoso-hpsd-088", "6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=");
    ```

    Sla het bestand op.

2. Selecteer in het menu Visual Studio de optie **Foutopsporing** > **starten zonder foutopsporing** om de oplossing uit te voeren. Selecteer **Ja** om het project opnieuw op te bouwen voordat u het project wilt herbouwen voordat u het project wilt herbouwen.

    De volgende uitvoer is een voorbeeld van het gesimuleerde warmtepompapparaat dat met succes wordt opgestart en verbinding maakt met de inrichtingsserviceinstantie die moet worden toegewezen aan de IoT-hub van Contoso-warmtepompen door het aangepaste toewijzingsbeleid:

    ```cmd
    Provisioning API Version: 1.3.6

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-heatpumps-hub-1098.azure-devices.net, deviceId: mainbuilding167-contoso-hpsd-088

    Press enter key to exit:
    ```

## <a name="troubleshooting-custom-allocation-policies"></a>Problemen met aangepaste toewijzingsbeleid oplossen

In de volgende tabel worden verwachte scenario's en de foutcodes van de resultaten weergegeven die u mogelijk ontvangt. Gebruik deze tabel om aangepaste toewijzingsbeleidsfouten op te lossen met uw Azure-functies.

| Scenario | Registratieresultaat van Provisioning Service | SDK-resultaten inrichten |
| -------- | --------------------------------------------- | ------------------------ |
| De webhook retourneert 200 OK met 'iotHubHostName' ingesteld op een geldige IoT hub host naam | Resultaatstatus: Toegewezen  | SDK retourneert PROV_DEVICE_RESULT_OK samen met hub-informatie |
| De webhook retourneert 200 OK met 'iotHubHostName' aanwezig in het antwoord, maar ingesteld op een lege tekenreeks of null | Resultaatstatus: mislukt<br><br> Foutcode: CustomAllocationIotHubNotSpecified (400208) | SDK retourneert PROV_DEVICE_RESULT_HUB_NOT_SPECIFIED |
| De webhook retourneert 401 Ongeautoriseerde | Resultaatstatus: mislukt<br><br>Foutcode: CustomAllocationUnauthorizedAccess (400209) | SDK retourneert PROV_DEVICE_RESULT_UNAUTHORIZED |
| Er is een individuele inschrijving gemaakt om het apparaat uit te schakelen | Resultaatstatus: uitgeschakeld | SDK retourneert PROV_DEVICE_RESULT_DISABLED |
| De webhook retourneert foutcode >= 429 | Dps' orchestration zal een aantal keren opnieuw proberen. Het beleid voor nieuwe try is momenteel:<br><br>&nbsp;&nbsp;- Opnieuw proberen tellen: 10<br>&nbsp;&nbsp;- Initiële interval: 1s<br>&nbsp;&nbsp;- Toename: 9s | SDK negeert fout en verzendt een ander bericht over de status van bericht voor berichten in de opgegeven tijd |
| De webhook retourneert een andere statuscode | Resultaatstatus: mislukt<br><br>Foutcode: CustomAllocationFailed (400207) | SDK retourneert PROV_DEVICE_RESULT_DEV_AUTH_ERROR |

## <a name="clean-up-resources"></a>Resources opschonen

Als u van plan bent door te werken met de bronnen die in dit artikel zijn gemaakt, u deze verlaten. Als u niet van plan bent de resources te blijven gebruiken, gebruikt u de volgende stappen om alle bronnen die in dit artikel zijn gemaakt te verwijderen om onnodige kosten te voorkomen.

De stappen hier aannemen dat u alle resources in dit artikel hebt gemaakt, zoals geïnstrueerd in dezelfde resourcegroep met de naam **contoso-us-resource-group**.

> [!IMPORTANT]
> Het verwijderen van een resourcegroep kan niet ongedaan worden gemaakt. De resourcegroep en alle resources daarin worden permanent verwijderd. Zorg ervoor dat u niet per ongeluk de verkeerde resourcegroep of resources verwijdert. Als u de IoT Hub in een bestaande resourcegroep hebt gemaakt met resources die u wilt behouden, moet u alleen de IoT Hub-resource zelf verwijderen in plaats van de resourcegroep te verwijderen.
>

Ga als u de brongroep op naam verwijderen:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) en selecteer **Resourcegroepen**.

2. Typ in het tekstvak **Filteren op naam...** de naam van de resourcegroep met uw resources, **contoso-us-resource-group.** 

3. Selecteer rechts van uw resourcegroep in de resultatenlijst **...** selecteer **dan Brongroep verwijderen**.

4. U wordt gevraagd de verwijdering van de brongroep te bevestigen. Typ de naam van uw resourcegroep opnieuw om te bevestigen en selecteer **Verwijderen**. Na enkele ogenblikken worden de resourcegroep en alle resources in de groep verwijderd.

## <a name="next-steps"></a>Volgende stappen

* Zie Concepten voor het opnieuw inrichten van IoT Hub Device voor meer informatie over het opnieuw [inrichten van iot-hubapparaten](concepts-device-reprovision.md) 
* Zie [Apparaten deprovisionen die voorheen automatisch zijn ingericht](how-to-unprovision-devices.md) voor meer deprovisioning. 
