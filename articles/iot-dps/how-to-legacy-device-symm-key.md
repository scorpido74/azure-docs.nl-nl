---
title: Oudere apparaten inrichten met symmetrische sleutels - Azure IoT Hub Device Provisioning Service
description: Symmetrische sleutels gebruiken om oudere apparaten in te richten met uw DPS-exemplaar (Device Provisioning Service)
author: wesmc7777
ms.author: wesmc
ms.date: 04/10/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: philmea
ms.openlocfilehash: 4d1a92f3ebf32d2270eb77ec9c79fe860ba090e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75434719"
---
# <a name="how-to-provision-legacy-devices-using-symmetric-keys"></a>Oudere apparaten inrichten met symmetrische sleutels

Een veelvoorkomend probleem met veel oudere apparaten is dat ze vaak een identiteit hebben die bestaat uit één enkel stukje informatie. Deze identiteitsgegevens zijn meestal een MAC-adres of een serienummer. Oudere apparaten hebben mogelijk geen certificaat, TPM of andere beveiligingsfunctie die kan worden gebruikt om het apparaat veilig te identificeren. De Device Provisioning Service voor IoT-hub bevat symmetrische sleutelattest. Symmetrische sleutelattest kan worden gebruikt om een apparaat te identificeren op basis van informatie zoals het MAC-adres of een serienummer.

Als u eenvoudig een [hardwarebeveiligingsmodule (HSM)](concepts-security.md#hardware-security-module) en een certificaat installeren, is dat misschien een betere aanpak voor het identificeren en inrichten van uw apparaten. Aangezien deze aanpak u in staat stelt om het bijwerken van de code die is geïmplementeerd op al uw apparaten te omzeilen, en u geen geheime sleutel in uw apparaatafbeelding hebt ingesloten.

In dit artikel wordt ervan uitgegaan dat noch een HSM, noch een certificaat een haalbare optie is. Er wordt echter van uitgegaan dat u een methode hebt om apparaatcode bij te werken om de Apparaatinrichtingsservice te gebruiken om deze apparaten in te richten. 

In dit artikel wordt ook ervan uitgegaan dat de apparaatupdate plaatsvindt in een beveiligde omgeving om onbevoegde toegang tot de hoofdgroepsleutel of de afgeleide apparaatsleutel te voorkomen.

Dit artikel is gericht op een Windows-gebaseerd werkstation. U kunt de procedures echter ook uitvoeren op Linux. Zie [Inrichten voor multitenancy](how-to-provision-multitenant.md) voor een Linux-voorbeeld.

> [!NOTE]
> Het monster dat in dit artikel wordt gebruikt, is geschreven in C. Er is ook een [C# apparaat provisioning symmetrische sleutel monster](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/device/SymmetricKeySample) beschikbaar. Download of kloon de [azure-iot-samples-csharp repository](https://github.com/Azure-Samples/azure-iot-samples-csharp) en volg de in-line instructies in de voorbeeldcode om dit voorbeeld te gebruiken. U de instructies in dit artikel volgen om een symmetrische sleutelinschrijvingsgroep te maken met behulp van de portal en om de primaire en secundaire sleutels van de id-scope en de inschrijvingsgroep te vinden die nodig zijn om het voorbeeld uit te voeren. U ook afzonderlijke inschrijvingen maken met behulp van het voorbeeld.

## <a name="overview"></a>Overzicht

Voor elk apparaat wordt een unieke registratie-id gedefinieerd op basis van informatie die dat apparaat identificeert. Bijvoorbeeld het MAC-adres of een serienummer.

Er wordt een inschrijvingsgroep gemaakt die [symmetrische sleutelattestation](concepts-symmetric-key-attestation.md) gebruikt, met de Service Apparaatinrichting. De inschrijvingsgroep bevat een groepshoofdsleutel. Die hoofdsleutel wordt gebruikt om elke unieke registratie-id te hashashen om een unieke apparaatsleutel voor elk apparaat te produceren. Het apparaat gebruikt die afgeleide apparaatsleutel met zijn unieke registratie-id om te getuigen van de Apparaatinrichtingsservice en wordt toegewezen aan een IoT-hub.

De apparaatcode die in dit artikel wordt gedemonstreerd, volgt hetzelfde patroon als de [Quickstart: Een gesimuleerd apparaat voorzien met symmetrische toetsen.](quick-create-simulated-device-symm-key.md) De code simuleert een apparaat met behulp van een voorbeeld van de [Azure IoT C SDK.](https://github.com/Azure/azure-iot-sdk-c) Het gesimuleerde apparaat getuigt met een inschrijvingsgroep in plaats van een individuele inschrijving, zoals aangetoond in de quickstart.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Vereisten

* Voltooiing van de Service Voor het inrichten van [IoT-hubapparaten snel aan de slag met de Azure-portal.](./quick-setup-auto-provision.md)

De volgende voorwaarden zijn voor een Windows-ontwikkelomgeving. Zie voor Linux of macOS de juiste sectie in [Uw ontwikkelomgeving voorbereiden](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) in de SDK-documentatie.

* [Visual Studio](https://visualstudio.microsoft.com/vs/) 2019 met de ['Desktop development with C++'](https://docs.microsoft.com/cpp/?view=vs-2019#pivot=workloads) workload enabled. Ook Visual Studio 2015 en Visual Studio 2017 worden ondersteund.

* Meest recente versie van [Git](https://git-scm.com/download/) geïnstalleerd.

## <a name="prepare-an-azure-iot-c-sdk-development-environment"></a>Een ontwikkelomgeving voorbereiden voor de Azure IoT C-SDK

In deze sectie bereidt u een ontwikkelomgeving voor die wordt gebruikt om de [Azure IoT C-SDK](https://github.com/Azure/azure-iot-sdk-c) te bouwen. 

De SDK bevat de voorbeeldcode voor het gesimuleerde apparaat. Dit gesimuleerde apparaat probeert de inrichting uit te voeren tijdens de opstartprocedure van het apparaat.

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
    
    Als `cmake` uw C++-compiler niet kan vinden, kunnen er fouten in de build optreden tijdens het uitvoeren van de bovenstaande opdracht. Als dit gebeurt, voert u deze opdracht uit bij de [Visual Studio-opdrachtprompt](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs). 

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


## <a name="create-a-symmetric-key-enrollment-group"></a>Een symmetrische sleutelinschrijvingsgroep maken

1. Meld u aan bij de [Azure-portal](https://portal.azure.com)en open de instantie Device Provisioning Service.

2. Selecteer het tabblad **Inschrijvingen beheren** en klik boven aan de pagina op de knop **Inschrijvingsgroep toevoegen.** 

3. Voer **bij Groep inschrijving toevoegen**de volgende gegevens in en klik op **Opslaan.**

   - **Groepsnaam**: Voer **mylegacydevices in.**

   - **Attesttype**: **Symmetrische toets selecteren**.

   - **Automatisch sleutels genereren**: schakel dit selectievakje in.

   - **Selecteer hoe u apparaten wilt toewijzen aan hubs:** Selecteer **Statische configuratie,** zodat u toewijzen aan een specifieke hub.

   - **Selecteer de IoT-hubs waaraan deze groep kan worden toegewezen:** Selecteer een van uw hubs.

     ![Inschrijvingsgroep toevoegen voor symmetrische sleutelattest](./media/how-to-legacy-device-symm-key/symm-key-enrollment-group.png)

4. Zodra u uw inschrijving hebt opgeslagen, worden de **primaire sleutel** en **secundaire sleutel** gegenereerd en aan de inschrijvingsvermelding toegevoegd. Uw symmetrische sleutelinschrijvingsgroep wordt weergegeven als **mylegacyapparaten** onder de kolom *Groepsnaam* op het tabblad *Inschrijvingsgroepen.* 

    Open de inschrijving en kopieer de waarde van uw gegenereerde **primaire sleutel**. Deze sleutel is uw hoofdgroepsleutel.


## <a name="choose-a-unique-registration-id-for-the-device"></a>Kies een unieke registratie-id voor het apparaat

Er moet een unieke registratie-ID worden gedefinieerd om elk apparaat te identificeren. U het MAC-adres, het serienummer of unieke informatie van het apparaat gebruiken. 

In dit voorbeeld gebruiken we een combinatie van een MAC-adres en serienummer dat de volgende tekenreeks vormt voor een registratie-id.

```
sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6
```

Maak een unieke registratie-id voor uw apparaat. Geldige tekens zijn alfanumerieke kleine letters en streepjes ('-').


## <a name="derive-a-device-key"></a>Een apparaatsleutel afleiden 

Als u de apparaatsleutel wilt genereren, gebruikt u de hoofdsleutel van de groep om een [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) van de unieke registratie-ID voor het apparaat te berekenen en het resultaat om te zetten in Base64-indeling.

Neem de groepshoofdsleutel niet op in de apparaatcode.


#### <a name="linux-workstations"></a>Linux-werkstations

Als u een Linux-werkstation gebruikt, u openssl gebruiken om uw afgeleide apparaatsleutel te genereren, zoals in het volgende voorbeeld wordt weergegeven.

Vervang de waarde van **KEY** door de **primaire sleutel die** u eerder hebt opgemerkt.

Vervang de waarde van **REG_ID** door uw registratie-ID.

```bash
KEY=8isrFI1sGsIlvvFSSFRiMfCNzv21fjbE/+ah/lSh3lF8e2YG1Te7w1KpZhJFFXJrqYKi9yegxkqIChbqOS9Egw==
REG_ID=sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```


#### <a name="windows-based-workstations"></a>Windows-werkstations

Als u een windows-werkstation gebruikt, u PowerShell gebruiken om uw afgeleide apparaatsleutel te genereren, zoals in het volgende voorbeeld wordt weergegeven.

Vervang de waarde van **KEY** door de **primaire sleutel die** u eerder hebt opgemerkt.

Vervang de waarde van **REG_ID** door uw registratie-ID.

```powershell
$KEY='8isrFI1sGsIlvvFSSFRiMfCNzv21fjbE/+ah/lSh3lF8e2YG1Te7w1KpZhJFFXJrqYKi9yegxkqIChbqOS9Egw=='
$REG_ID='sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6'

$hmacsha256 = New-Object System.Security.Cryptography.HMACSHA256
$hmacsha256.key = [Convert]::FromBase64String($KEY)
$sig = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID))
$derivedkey = [Convert]::ToBase64String($sig)
echo "`n$derivedkey`n"
```

```powershell
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```


Uw apparaat gebruikt de afgeleide apparaatsleutel met uw unieke registratie-id om symmetrische sleutelattest uit te voeren met de inschrijvingsgroep tijdens de inrichting.



## <a name="create-a-device-image-to-provision"></a>Een apparaatafbeelding maken om in te richten

In deze sectie werkt u een inprovisioningvoorbeeld met de naam **prov\_\_dev-clientvoorbeeld\_** bij in de Azure IoT C SDK die u eerder hebt ingesteld. 

Met deze voorbeeldcode wordt een opstartreeks voor apparaten gesimuleerd die het inrichtingsverzoek naar het instantieexemplaar Apparaatinrichtingsservice verzendt. De opstartreeks zorgt ervoor dat het apparaat wordt herkend en toegewezen aan de IoT-hub die u in de inschrijvingsgroep hebt geconfigureerd.

1. Selecteer in Azure Portal het tabblad **Overzicht** voor uw Device Provisioning-service en noteer de waarde van het **_Id-bereik_**.

    ![Device Provisioning Service-eindpuntgegevens uit de portalblade extraheren](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. Open in Visual Studio het **bestand met azure_iot_sdks.sln-oplossing** dat is gegenereerd door CMake eerder uit te voeren. Het oplossingsbestand bevindt zich als het goed is op de volgende locatie:

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

6. Zoek de aanroep naar `prov_dev_set_symmetric_key_info()` in **prov\_dev\_client\_sample.c**, waarbij een opmerking is geplaatst.

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    Maak de functieaanroep ongedaan en vervang de tijdelijke aanduidingswaarden (inclusief de hoekhaakjes) door de unieke registratie-id voor uw apparaat en de afgeleide apparaatsleutel die u hebt gegenereerd.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6", "Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=");
    ```
   
    Sla het bestand op.

7. Klik met de rechtermuisknop op het **prov\_dev\_client\_sample**-project en selecteer **Set as Startup Project**. 

8. Selecteer in het menu Visual Studio de optie **Foutopsporing** > **starten zonder foutopsporing** om de oplossing uit te voeren. Klik in de prompt om het project opnieuw te bouwen op **Yes** om het project opnieuw te bouwen voordat het wordt uitgevoerd.

    De volgende output is een voorbeeld waarbij het gesimuleerde apparaat met succes opstart en verbinding maakt met het inrichtingsservice-exemplaar voor toewijzing aan een IoT-hub:

    ```cmd
    Provisioning API Version: 1.2.8

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: 
    test-docs-hub.azure-devices.net, deviceId: sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6

    Press enter key to exit:
    ```

9. Navigeer in de portal naar de IoT-hub waaraan uw gesimuleerde apparaat is toegewezen en klik op het tabblad **IoT-apparaten.** Bij een succesvolle inrichting van de gesimuleerde hub wordt de apparaat-id weergegeven op het **iE-apparaatblad,** met *STATUS* als **ingeschakeld**. Mogelijk moet u bovenaan op de knop **Vernieuwen** klikken. 

    ![Apparaat wordt geregistreerd voor de IoT-hub](./media/how-to-legacy-device-symm-key/hub-registration.png) 



## <a name="security-concerns"></a>Bezorgdheid over de veiligheid

Houd er rekening mee dat dit de afgeleide apparaatsleutel als onderdeel van de afbeelding achterlaat, wat geen aanbevolen aanbevolen beveiligingsaanbevolen praktijk is. Dit is een van de redenen waarom veiligheid en gebruiksgemak afwegingen zijn. 





## <a name="next-steps"></a>Volgende stappen

* Zie Concepten voor het opnieuw inrichten van IoT Hub Device voor meer informatie over het opnieuw [inrichten van iot-hubapparaten](concepts-device-reprovision.md) 
* [Snelstartgids: een gesimuleerd apparaat inrichten met symmetrische sleutels](quick-create-simulated-device-symm-key.md)
* Zie [Apparaten deprovisionen die voorheen automatisch zijn ingericht](how-to-unprovision-devices.md) voor meer deprovisioning. 











