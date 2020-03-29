---
title: Apparaten inrichten voor multitenancy in Azure IoT Hub Device Provisioning Service
description: Apparaten inrichten voor multitenancy met uw DPS-exemplaar (Device Provisioning Service)
author: wesmc7777
ms.author: wesmc
ms.date: 04/10/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: e0dec0a67ed33186797ccec8066aaad89ceb8dcb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75434743"
---
# <a name="how-to-provision-for-multitenancy"></a>Hoe te voorzien in multitenancy 

Het toewijzingsbeleid dat door de inprovisioningservice is gedefinieerd, ondersteunt verschillende toewijzingsscenario's. Twee veelvoorkomende scenario's zijn:

* **Geolocatie / GeoLatentie:** Als een apparaat tussen locaties beweegt, wordt de netwerklatentie verbeterd door het apparaat te laten inrichten aan de IoT-hub die het dichtst bij elke locatie ligt. In dit scenario wordt een groep IoT-hubs geselecteerd die zich uitstrekken over verschillende regio's. Voor deze inschrijvingen wordt het beleid voor toewijzing met **de laagste latentie** geselecteerd. Dit beleid zorgt ervoor dat de apparaatinrichtingsservice de gebruikslaten van apparaten evalueert en de IoT-hub van de kast uit de groep IoT-hubs bepaalt. 

* **Multi-tenancy**: Apparaten die worden gebruikt in een IoT-oplossing moeten mogelijk worden toegewezen aan een specifieke IoT-hub of groep IoT-hubs. De oplossing vereist mogelijk dat alle apparaten voor een bepaalde tenant communiceren met een specifieke groep IoT-hubs. In sommige gevallen kan een tenant eigenaar zijn van IoT-hubs en vereisen dat apparaten worden toegewezen aan hun IoT-hubs.

Het is gebruikelijk om deze twee scenario's te combineren. Een IoT-oplossing met meerdere tenant's wijst bijvoorbeeld vaak tenantapparaten toe met behulp van een groep IoT-hubs die verspreid zijn over regio's. Deze tenantapparaten kunnen worden toegewezen aan de IoT-hub in die groep, die de laagste latentie heeft op basis van geografische locatie.

In dit artikel wordt een gesimuleerd apparaatvoorbeeld van de [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) gebruikt om aan te tonen hoe apparaten in een multitenant-scenario in verschillende regio's kunnen worden ingericht. U voert de volgende stappen uit in dit artikel:

* Gebruik de Azure CLI om twee regionale IoT-hubs **(West US** en **East US)** te maken.
* Een inschrijving voor meerdere tenanten maken
* Gebruik de Azure CLI om twee regionale Linux-VM's te maken om als apparaten in dezelfde regio's **(West-VS** en **Oost-VS)** op te treden.
* De ontwikkelomgeving voor de Azure IoT C SDK instellen op beide Linux VM's
* Simuleer de apparaten om te zien dat ze zijn ingericht voor dezelfde tenant in de dichtstbijzijnde regio.


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Vereisten

* Voltooiing van de Service Voor het inrichten van [IoT-hubapparaten snel aan de slag met de Azure-portal.](./quick-setup-auto-provision.md)


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]


## <a name="create-two-regional-iot-hubs"></a>Twee regionale IoT-hubs maken

In deze sectie gebruikt u de Azure Cloud Shell om twee nieuwe regionale IoT-hubs in de **regio's West-VS** en **Oost-VS** te maken voor een tenant.


1. Gebruik de Azure Cloud Shell om een resourcegroep te maken met de opdracht [AZ-groep maken.](/cli/azure/group#az-group-create) Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. 

    In het volgende voorbeeld wordt een resourcegroep met de naam *contoso-us-resource-group* in de *eastus-regio.* Het wordt aanbevolen deze groep te gebruiken voor alle bronnen die in dit artikel zijn gemaakt. Dit zal opruimen gemakkelijker maken nadat u klaar bent.

    ```azurecli-interactive 
    az group create --name contoso-us-resource-group --location eastus
    ```

2. Gebruik de Azure Cloud Shell om een IoT-hub in de **eastusregio** te maken met de opdracht [az iot-hub.](/cli/azure/iot/hub#az-iot-hub-create) De IoT-hub wordt toegevoegd aan de *contoso-us-resource-group.*

    In het volgende voorbeeld wordt een IoT-hub gemaakt met de naam *contoso-east-hub* op de *eastus-locatie.* U moet uw eigen unieke hubnaam gebruiken in plaats van **contoso-east-hub.**

    ```azurecli-interactive 
    az iot hub create --name contoso-east-hub --resource-group contoso-us-resource-group --location eastus --sku S1
    ```
    
    Deze opdracht kan enkele minuten in beslag nemen.

3. Gebruik de Azure Cloud Shell om een IoT-hub in de **westusregio** te maken met de opdracht [AZ Iot-hub.](/cli/azure/iot/hub#az-iot-hub-create) Deze IoT-hub wordt ook toegevoegd aan de *contoso-us-resource-group.*

    In het volgende voorbeeld wordt een IoT-hub gemaakt met de naam *contoso-west-hub* op de *westuslocatie.* U moet uw eigen unieke hubnaam gebruiken in plaats van **contoso-west-hub.**

    ```azurecli-interactive 
    az iot hub create --name contoso-west-hub --resource-group contoso-us-resource-group --location westus --sku S1
    ```

    Deze opdracht kan enkele minuten in beslag nemen.



## <a name="create-the-multitenant-enrollment"></a>De inschrijving voor meerdere tenanten maken

In deze sectie maakt u een nieuwe inschrijvingsgroep voor de tenantapparaten.  

Voor de eenvoud maakt dit artikel gebruik [van symmetrische sleutelattest](concepts-symmetric-key-attestation.md) bij de inschrijving. Voor een veiligere oplossing u overwegen [om X.509-certificaatattest te](concepts-security.md#x509-certificates) gebruiken met een vertrouwensketen.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com)en open de instantie Device Provisioning Service.

2. Selecteer het tabblad **Inschrijvingen beheren** en klik boven aan de pagina op de knop **Inschrijvingsgroep toevoegen.** 

3. Voer **bij Groep inschrijving toevoegen**de volgende gegevens in en klik op **Opslaan.**

    **Groepsnaam**: Voer **contoso-us-devices in.**

    **Attesttype**: **Symmetrische toets selecteren**.

    **Automatisch genereren sleutels:** Dit selectievakje moet al worden aangevinkt.

    **Selecteer hoe u apparaten wilt toewijzen aan hubs:** Selecteer **Laagste latentie**.

    ![Multitenant-inschrijvingsgroep toevoegen voor symmetrische sleutelattest](./media/how-to-provision-multitenant/create-multitenant-enrollment.png)


4. Klik op **Inschrijvingsgroep toevoegen**op **Een nieuwe IoT-hub koppelen** om beide regionale hubs te koppelen.

    **Abonnement**: Als u meerdere abonnementen hebt, kiest u het abonnement waarbij u de regionale IoT-hubs hebt gemaakt.

    **IoT-hub**: selecteer een van de regionale hubs die u hebt gemaakt.

    **Toegangsbeleid:** kies **iothubowner**.

    ![Koppel de regionale IoT-hubs aan de inrichtingsservice](./media/how-to-provision-multitenant/link-regional-hubs.png)


5. Zodra beide regionale IoT-hubs zijn gekoppeld, moet u ze selecteren voor de inschrijvingsgroep en op **Opslaan** klikken om de regionale IoT-hubgroep voor de inschrijving te maken.

    ![De regionale hubgroep voor de inschrijving maken](./media/how-to-provision-multitenant/enrollment-regional-hub-group.png)


6. Nadat u de inschrijving hebt opgeslagen, opent u deze opnieuw en maakt u een notitie van de **primaire sleutel.** U moet de inschrijving eerst opslaan om de sleutels te laten genereren. Deze sleutel wordt later gebruikt om unieke apparaatsleutels voor beide gesimuleerde apparaten te genereren.


## <a name="create-regional-linux-vms"></a>Regionale Linux VM's maken

In deze sectie maak je twee regionale Linux virtuele machines (VM's). Deze VM's worden vanuit elke regio een apparaatsimulatiemonster uitgevoerd om de inrichting van apparaten voor tenantapparaten uit beide regio's aan te tonen.

Om het opruimen gemakkelijker te maken, worden deze VM's toegevoegd aan dezelfde resourcegroep die de IoT-hubs bevat die zijn gemaakt, *contoso-us-resource-group.* De VM's zullen echter in afzonderlijke regio's **(West-VS** en **Oost-VS)** worden uitgevoerd.

1. Voer in de Azure Cloud Shell de volgende opdracht uit om een VM in **de Oost-VS-regio** te maken nadat u de volgende parameterwijzigingen in de opdracht hebt aangebracht:

    **--naam:** Voer een unieke naam in voor de VM van uw regionale apparaat in **de Vs.** 

    **--admin-gebruikersnaam:** Gebruik uw eigen gebruikersnaam voor beheerders.

    **--admin-wachtwoord:** Gebruik uw eigen beheerderswachtwoord.

    ```azurecli-interactive
    az vm create \
    --resource-group contoso-us-resource-group \
    --name ContosoSimDeviceEast \
    --location eastus \
    --image Canonical:UbuntuServer:18.04-LTS:18.04.201809110 \
    --admin-username contosoadmin \
    --admin-password myContosoPassword2018 \
    --authentication-type password
    ```

    Het uitvoeren van deze opdracht duurt enkele minuten. Zodra de opdracht is voltooid, noteert u de **publicIpAddress-waarde** voor de VM van uw Oost-Amerikaanse regio.

1. Voer in de Azure Cloud Shell de opdracht uit om een **WEST-AMERIKAANSE** regio VM te maken nadat u de volgende parameterwijzigingen in de opdracht hebt aangebracht:

    **--naam:** Voer een unieke naam in voor de VM van uw **west-Amerikaanse** regionale apparaat. 

    **--admin-gebruikersnaam:** Gebruik uw eigen gebruikersnaam voor beheerders.

    **--admin-wachtwoord:** Gebruik uw eigen beheerderswachtwoord.

    ```azurecli-interactive
    az vm create \
    --resource-group contoso-us-resource-group \
    --name ContosoSimDeviceWest \
    --location westus \
    --image Canonical:UbuntuServer:18.04-LTS:18.04.201809110 \
    --admin-username contosoadmin \
    --admin-password myContosoPassword2018 \
    --authentication-type password
    ```

    Het uitvoeren van deze opdracht duurt enkele minuten. Zodra de opdracht is voltooid, noteer t.a.v. de **publicIpAddress-waarde** voor de VM van uw West-VS-regio.

1. Open twee command-line shells. Maak verbinding met een van de regionale VM's in elke shell met behulp van SSH. 

    Geef uw gebruikersnaam voor beheerders door en het openbare IP-adres dat u voor de VM hebt genoteerd als parameters aan SSH. Voer het beheerderswachtwoord in wanneer daarom wordt gevraagd.

    ```bash
    ssh contosoadmin@1.2.3.4

    contosoadmin@ContosoSimDeviceEast:~$
    ```

    ```bash
    ssh contosoadmin@5.6.7.8

    contosoadmin@ContosoSimDeviceWest:~$
    ```



## <a name="prepare-the-azure-iot-c-sdk-development-environment"></a>De Azure IoT C SDK-ontwikkelomgeving voorbereiden

In deze sectie kloont u de Azure IoT C SDK op elke VM. De SDK bevat een voorbeeld dat de inrichting van het apparaat van een tenant vanuit elke regio simuleert.

1. Installeer voor elke virtuele machine **CMake**, **g++**, **gcc**en [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git) met de volgende opdrachten:

    ```bash
    sudo apt-get update
    sudo apt-get install cmake build-essential libssl-dev libcurl4-openssl-dev uuid-dev git-all
    ```

1. Zoek de tagnaam voor de [nieuwste versie](https://github.com/Azure/azure-iot-sdk-c/releases/latest) van de SDK.

1. Kloon de [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) op beide VM's.  Gebruik de tag die u in de `-b` vorige stap hebt gevonden als de waarde voor de parameter:

    ```bash
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Deze bewerking kan enkele minuten in beslag nemen.

1. Maak voor beide VM's een nieuwe **cmake-map** in de repository en wijzigt deze in die map.

    ```bash
    mkdir ~/azure-iot-sdk-c/cmake
    cd ~/azure-iot-sdk-c/cmake
    ```

1. Voer voor beide VM's de volgende opdracht uit, waarmee een versie van de SDK wordt gebouwd die specifiek is voor uw ontwikkelclientplatform. 

    ```bash
    cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    ```

    Zodra het bouwen is voltooid, zijn de laatste paar uitvoerregels vergelijkbaar met de volgende uitvoer:

    ```bash
    -- IoT Client SDK Version = 1.2.9
    -- Provisioning client ON
    -- Provisioning SDK Version = 1.2.9
    -- target architecture: x86_64
    -- Checking for module 'libcurl'
    --   Found libcurl, version 7.58.0
    -- Found CURL: curl
    -- target architecture: x86_64
    -- target architecture: x86_64
    -- target architecture: x86_64
    -- target architecture: x86_64
    -- iothub architecture: x86_64
    -- target architecture: x86_64
    -- Configuring done
    -- Generating done
    -- Build files have been written to: /home/contosoadmin/azure-iot-sdk-c/cmake
    ```    


## <a name="derive-unique-device-keys"></a>Unieke apparaatsleutels afleiden

Wanneer u symmetrische sleutelattestation met groepsinschrijvingen gebruikt, gebruikt u de inschrijvingsgroepsleutels niet rechtstreeks. In plaats daarvan maakt u een unieke afgeleide sleutel voor elk apparaat en wordt vermeld in [groepsinschrijvingen met symmetrische toetsen.](concepts-symmetric-key-attestation.md#group-enrollments)

Als u de apparaatsleutel wilt genereren, gebruikt u de hoofdsleutel van de groep om een [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) van de unieke registratie-ID voor het apparaat te berekenen en het resultaat om te zetten in Base64-indeling.

Neem de groepshoofdsleutel niet op in de apparaatcode.

Gebruik het voorbeeld van de Bash-shell om een afgeleide apparaatsleutel voor elk apparaat te maken met **openssl.**

- Vervang de waarde voor **KEY** door de **primaire sleutel die** u eerder voor uw inschrijving hebt opgemerkt.

- Vervang de waarde voor **REG_ID** door uw eigen unieke registratie-ID voor elk apparaat. Gebruik alfanumerieke en streepjestekens ('-') om beide id's te definiëren.

Voorbeeld apparaat sleutel generatie voor *contoso-simdevice-oost:*

```bash
KEY=rLuyBPpIJ+hOre2SFIP9Ajvdty3j0EwSP/WvTVH9eZAw5HpDuEmf13nziHy5RRXmuTy84FCLpOnhhBPASSbHYg==
REG_ID=contoso-simdevice-east

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
p3w2DQr9WqEGBLUSlFi1jPQ7UWQL4siAGy75HFTFbf8=
```

Voorbeeld apparaat sleutel generatie voor *contoso-simdevice-west:*

```bash
KEY=rLuyBPpIJ+hOre2SFIP9Ajvdty3j0EwSP/WvTVH9eZAw5HpDuEmf13nziHy5RRXmuTy84FCLpOnhhBPASSbHYg==
REG_ID=contoso-simdevice-west

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
J5n4NY2GiBYy7Mp4lDDa5CbEe6zDU/c62rhjCuFWxnc=
```


De tenant-apparaten gebruiken elk hun afgeleide apparaatsleutel en unieke registratie-ID om symmetrische sleutelattest uit te voeren met de inschrijvingsgroep tijdens de inrichting van de Tenant IoT-hubs.




## <a name="simulate-the-devices-from-each-region"></a>De apparaten uit elke regio simuleren


In deze sectie werkt u een inrichtingsvoorbeeld in de Azure IoT C SDK bij voor beide regionale VM's. 

De voorbeeldcode simuleert een opstartreeks voor apparaten die het inrichtingsverzoek naar het instantie-exemplaar Apparaatinrichtingsservice verzendt. De opstartreeks zorgt ervoor dat het apparaat wordt herkend en toegewezen aan de IoT-hub die het dichtst bij elkaar staat op basis van latentie.

1. Selecteer in Azure Portal het tabblad **Overzicht** voor uw Device Provisioning-service en noteer de waarde van het **_Id-bereik_**.

    ![Device Provisioning Service-eindpuntgegevens uit de portalblade extraheren](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

1. Open **\_~/azure-iot-sdk-c/provisioning client/samples/prov\_\_\_\_\_\_dev client sample/prov dev client sample.c** voor bewerking op beide VM's.

    ```bash
    vi ~/azure-iot-sdk-c/provisioning_client/samples/prov_dev_client_sample/prov_dev_client_sample.c
    ```

1. Zoek de constante `id_scope` op en vervang de waarde door uw **Id-bereik**-waarde die u eerder hebt gekopieerd. 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

1. Zoek de definitie voor de functie `main()` op in hetzelfde bestand. Zorg ervoor `hsm_type` dat de `SECURE_DEVICE_TYPE_SYMMETRIC_KEY` variabele is ingesteld op zoals hieronder wordt weergegeven, zodat deze overeenkomt met de attestmethode voor de inschrijvingsgroep. 

    Sla uw wijzigingen op in de bestanden op beide VM's.

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
    ```

1. Op beide VM's, `prov_dev_set_symmetric_key_info()` zoek de oproep naar in **prov\_dev\_client\_sample.c** die wordt becommentarieerd.

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    Maak de functieoproepen ongedaan en vervang de tijdelijke aanduidingswaarden (inclusief de hoekhaakjes) door de unieke registratie-id's en afgeleide apparaatsleutels voor elk apparaat. De onderstaande toetsen zijn bijvoorbeeld alleen voor doeleinden. Gebruik de sleutels die u eerder hebt gegenereerd.

    Oost-VS:
    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("contoso-simdevice-east", "p3w2DQr9WqEGBLUSlFi1jPQ7UWQL4siAGy75HFTFbf8=");
    ```

    West-VS:
    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("contoso-simdevice-west", "J5n4NY2GiBYy7Mp4lDDa5CbEe6zDU/c62rhjCuFWxnc=");
    ```

    Sla de bestanden op.

1. Navigeer op beide VM's naar de onderstaande voorbeeldmap en bouw het voorbeeld.

    ```bash
    cd ~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample/
    cmake --build . --target prov_dev_client_sample --config Debug
    ```

1. Zodra de build is geslaagd, voert u **prov\_dev\_client\_sample.exe** uit op beide VM's om een tenantapparaat uit elke regio te simuleren. Merk op dat elk apparaat is toegewezen aan de tenant IoT-hub die het dichtst bij de regio's van het gesimuleerde apparaat ligt.

    Voer de simulatie uit:
    ```bash
    ~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample/prov_dev_client_sample
    ```

    Voorbeeld uitvoer van de VM Oost-VS:

    ```bash
    contosoadmin@ContosoSimDeviceEast:~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample$ ./prov_dev_client_sample
    Provisioning API Version: 1.2.9

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-east-hub.azure-devices.net, deviceId: contoso-simdevice-east
    Press enter key to exit:

    ```

    Voorbeeld uitvoer van de West US VM:
    ```bash
    contosoadmin@ContosoSimDeviceWest:~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample$ ./prov_dev_client_sample
    Provisioning API Version: 1.2.9

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-west-hub.azure-devices.net, deviceId: contoso-simdevice-west
    Press enter key to exit:
    ```



## <a name="clean-up-resources"></a>Resources opschonen

Als u van plan bent om te blijven werken met resources die in dit artikel zijn gemaakt, u deze verlaten. Als u niet van plan bent de resource te blijven gebruiken, gebruikt u de volgende stappen om alle bronnen die door dit artikel zijn gemaakt te verwijderen om onnodige kosten te voorkomen.

De stappen hier aannemen dat u alle resources in dit artikel hebt gemaakt, zoals geïnstrueerd in dezelfde resourcegroep met de naam **contoso-us-resource-group**.

> [!IMPORTANT]
> Het verwijderen van een resourcegroep kan niet ongedaan worden gemaakt. De resourcegroep en alle resources daarin worden permanent verwijderd. Zorg ervoor dat u niet per ongeluk de verkeerde resourcegroep of resources verwijdert. Als u de IoT Hub in een bestaande resourcegroep hebt gemaakt met resources die u wilt behouden, moet u alleen de IoT Hub-resource zelf verwijderen in plaats van de resourcegroep te verwijderen.
>

Ga als u de brongroep op naam verwijderen:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) en klik op **Resourcegroepen**.

2. Typ in het tekstvak **Filteren op naam...** de naam van de resourcegroep met uw resources, **contoso-us-resource-group.** 

3. Klik rechts van de resourcegroep in de lijst met resultaten op **...** en vervolgens op **Resourcegroep verwijderen**.

4. U wordt gevraagd om het verwijderen van de resourcegroep te bevestigen. Typ de naam van de resourcegroep nogmaals om te bevestigen en klik op **Verwijderen**. Na enkele ogenblikken worden de resourcegroep en alle resources in de groep verwijderd.

## <a name="next-steps"></a>Volgende stappen

- Zie Concepten voor het opnieuw inrichten van IoT Hub Device voor meer informatie over het opnieuw [inrichten van iot-hubapparaten](concepts-device-reprovision.md) 
- Zie [Apparaten deprovisionen die voorheen automatisch zijn ingericht](how-to-unprovision-devices.md) voor meer deprovisioning. 











