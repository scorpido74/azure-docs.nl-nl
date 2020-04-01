---
title: Zelfstudie - Apparaat instellen voor de Azure IoT Hub Device Provisioning Service
description: In deze zelfstudie ziet u hoe u apparaat instellen voor inlevering via de IoT Hub Device Provisioning Service (DPS) tijdens het productieproces van het apparaat
author: wesmc7777
ms.author: wesmc
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 6ff732888e416fcd51216070b3b30ed37b79e92c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239488"
---
# <a name="tutorial-set-up-a-device-to-provision-using-the-azure-iot-hub-device-provisioning-service"></a>Zelfstudie: Een apparaat instellen voor inrichten met de Azure IoT Hub Device Provisioning Service

In de vorige zelfstudie hebt u geleerd hoe u Azure IoT Hub Device Provisioning Service kunt instellen om apparaten automatisch in te richten in uw IoT-hub. In deze zelfstudie ziet u hoe u uw apparaat kunt instellen tijdens het fabriceren van het apparaat, en ervoor zorgt dat het automatisch kan worden ingericht met IoT Hub. Uw apparaat wordt ingericht op basis van het bijbehorende [Attestation-mechanisme](concepts-device.md#attestation-mechanism) na de eerste keer opstarten en verbinden met de inrichtingsservice. Deze zelfstudie bestaat uit de volgende taken:

> [!div class="checklist"]
> * Platformspecifieke SDK van de Device Provisioning Service-client bouwen
> * De beveiligingsartefacten extraheren
> * De software voor apparaatregistratie maken

Voor deze zelfstudie wordt ervan uitgegaan dat u al een Device Provisioning Service-instantie en een IoT-hub hebt gemaakt met behulp van de instructies in de vorige zelfstudie [Cloudresources instellen](tutorial-set-up-cloud.md).

In deze zelfstudie wordt gebruikgemaakt van de [Azure IoT SDKs and libraries for C](https://github.com/Azure/azure-iot-sdk-c)-opslagplaats (Azure IoT SDK’s en bibliotheken voor C), die de SDK van de Device Provisioning Service-client voor C bevat. De SDK biedt momenteel ondersteuning voor TPM en X.509 voor apparaten die worden uitgevoerd in Windows- of Ubuntu-implementaties. Deze zelfstudie is gebaseerd op het gebruik van een Windows-ontwikkelclient, die ook een basisvaardigheid met Visual Studio aanneemt. 

Als u niet bekend bent met het proces van automatische inrichting, bekijkt u de [Concepten voor automatische inrichting](concepts-auto-provisioning.md) voordat u verdergaat. 


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

De volgende voorwaarden zijn voor een Windows-ontwikkelomgeving. Zie voor Linux of macOS de juiste sectie in [Uw ontwikkelomgeving voorbereiden](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) in de SDK-documentatie.

* [Visual Studio](https://visualstudio.microsoft.com/vs/) 2019 met de ['Desktop development with C++'](https://docs.microsoft.com/cpp/?view=vs-2019#pivot=workloads) workload enabled. Ook Visual Studio 2015 en Visual Studio 2017 worden ondersteund.

* Meest recente versie van [Git](https://git-scm.com/download/) geïnstalleerd.

## <a name="build-a-platform-specific-version-of-the-sdk"></a>Een platformspecifieke versie van de SDK bouwen

De SDK van de Device Provisioning Service-client helpt u bij het implementeren van de software voor apparaatregistratie. Maar voordat u deze kunt gebruiken, moet u een versie van de SDK bouwen die specifiek is voor uw ontwikkelclientplatform en attestation- mechanisme. In deze zelfstudie bouwt u een SDK die Visual Studio gebruikt op een Windows-ontwikkelingsplatform, voor een ondersteund type attest:

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

5. Bouw de SDK voor uw ontwikkelplatform op basis van de attestation-mechanismen die u gaat gebruiken. Gebruik een van de volgende opdrachten (let ook op de twee puntjes waarop elke opdracht eindigt). Na het voltooien wordt de `/cmake`-submap met CMake uitgebreid met inhoud die specifiek is voor uw apparaat:
 
    - Voor apparaten die gebruikmaken van de TPM-simulator voor attestation:

        ```cmd/sh
        cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON ..
        ```

    - Voor elk ander apparaat (een fysiek TPM/HSM/X.509- of een gesimuleerd X.509-certificaat):

        ```cmd/sh
        cmake -Duse_prov_client:BOOL=ON ..
        ```


Nu bent u klaar om de SDK te gebruiken om de registratiecode voor het apparaat te bouwen. 
 
<a id="extractsecurity"></a> 

## <a name="extract-the-security-artifacts"></a>De beveiligingsartefacten extraheren 

De volgende stap is het extraheren van de beveiligingsartefacten voor het attestation-mechanisme dat wordt gebruikt op het apparaat. 

### <a name="physical-devices"></a>Fysieke apparaten 

Afhankelijk van of u de SDK bouwt om gebruik te maken van attestation voor een fysieke TPM/HSM of dat u gebruikmaakt van X.509-certificaten, gaat het verzamelen van de beveiligingsartefacten als volgt:

- Voor een TPM-apparaat moet u bij de TPM-chipfabrikant de bijbehorende **Goedkeuringssleutel** achterhalen. U kunt een unieke **Registratie-id** voor het TPM-apparaat afleiden door de goedkeuringssleutel te hashen.  

- Voor een X.509-apparaat moet u de certificaten verkrijgen die aan uw apparaten zijn verleend. Door de inrichtingsservice worden twee typen inschrijvingsvermeldingen beschikbaar gemaakt die de toegang voor apparaten beheren met behulp van het X.509-attestation-mechanisme. Welke certificaten u nodig hebt, is afhankelijk van het type inschrijving dat u gaat gebruiken.

    - Afzonderlijke inschrijvingen: inschrijving voor één specifiek apparaat. Dit type inschrijvingsvermelding vereist [leaf-certificaten voor eindentiteiten](concepts-security.md#end-entity-leaf-certificate).
    
    - Registratiegroepen: voor dit type inschrijvingsvermelding zijn tussenliggende certificaten of basiscertificaten vereist. Zie [Apparaattoegang tot de inrichtingsservice beheren met X.509-certificaten](concepts-security.md#controlling-device-access-to-the-provisioning-service-with-x509-certificates) voor meer informatie.

### <a name="simulated-devices"></a>Gesimuleerde apparaten

Afhankelijk van of u de SDK bouwt om gebruik te maken van attestation voor een gesimuleerd apparaat met behulp van TPM- of X.509-certificaten, gaat het verzamelen van de beveiligingsartefacten als volgt:

- Voor een gesimuleerd TPM-apparaat:

   1. Open een Windows-opdrachtprompt, navigeer naar de submap `azure-iot-sdk-c` en voer de TPM-simulator uit. Deze luistert via een socket op poorten 2321 en 2322. Sluit dit opdrachtvenster niet. De simulator moet actief blijven tot het einde van de volgende snelstart. 

      Voer vanuit de `azure-iot-sdk-c`-submap de volgende opdracht uit om de simulator te starten:

      ```cmd/sh
      .\provisioning_client\deps\utpm\tools\tpm_simulator\Simulator.exe
      ```

      > [!NOTE]
      > Als u de Git Bash-opdrachtprompt voor deze stap gebruikt, moet u de backslash-tekens wijzigen in slashes, bijvoorbeeld: `./provisioning_client/deps/utpm/tools/tpm_simulator/Simulator.exe`.

   1. Open in Visual Studio de oplossing die is gegenereerd in de *cmake*-map met de naam `azure_iot_sdks.sln`, en bouw deze met behulp van de opdracht Build solution in het menu Build.

   1. In het deelvenster *Solution Explorer* van Visual Studio gaat u naar de map **Inrichten\_Extra**. Klik met de rechtermuisknop op het **tpm_device_provision**-project en selecteer **Set as Startup Project**. 

   1. Voer de oplossing uit met behulp van een van de twee opdrachten Start in het menu Debug. In het uitvoervenster worden de voor apparaatinschrijving en -registratie vereiste **_Registratie-id_** en **_Goedkeuringssleutel_** van de TPM-simulator weergegeven. Kopieer deze waarden voor later gebruik. U kunt dit venster (met de registratie-id en goedkeuringssleutel) sluiten, maar laat het venster voor de TPM-simulator uit stap 1 geopend.

- Voor een gesimuleerd X.509-apparaat:

  1. Open in Visual Studio de oplossing die is gegenereerd in de *cmake*-map met de naam `azure_iot_sdks.sln`, en bouw deze met behulp van de opdracht Build solution in het menu Build.

  1. In het deelvenster *Solution Explorer* van Visual Studio gaat u naar de map **Inrichten\_Extra**. Klik met de rechtermuisknop op het**dice\_device\_enrollment**-project en selecteer **Set as Startup Project**. 
  
  1. Voer de oplossing uit met behulp van een van de twee opdrachten Start in het menu Debug. Voer in het uitvoervenster **i** in voor individuele registratie wanneer hierom wordt gevraagd. In het uitvoervenster wordt een lokaal gegenereerd X.509-certificaat weergegeven voor uw gesimuleerde apparaat. Kopieer de uitvoer naar Klembord vanaf *-----BEGIN CERTIFICATE-----* tot en met de eerste *-----END CERTIFICATE-----*, en zorg ervoor dat deze beide regels ook zijn opgenomen. U hebt alleen het eerste certificaat uit het uitvoervenster nodig.
 
  1. Maak een bestand met de naam **_X509testcert.pem_**, open het in een teksteditor naar keuze en kopieer de inhoud van het Klembord naar dit bestand. Sla het bestand op. U gebruikt dit bestand later voor apparaatinschrijving. Wanneer de registratiesoftware wordt uitgevoerd, maakt deze gebruik van hetzelfde certificaat tijdens de automatische inrichting.    

Deze beveiligingsartefacten zijn vereist tijdens de inschrijving van het apparaat voor Device Provisioning Service. De inrichtingsservice wacht tot het apparaat op enig moment later is opgestart en verbonden met de service. Als het apparaat voor de eerste keer wordt opgestart, communiceert de Client SDK-logica met de chip (of simulator) om de beveiligingsartefacten te extraheren uit het apparaat, en wordt de registratie bij Device Provisioning Service geverifieerd. 

## <a name="create-the-device-registration-software"></a>De software voor apparaatregistratie maken

De laatste stap is het schrijven van een registratietoepassing die gebruikmaakt van de SDK van de Device Provisioning Service-client om het apparaat te registreren bij de IoT Hub-service. 

> [!NOTE]
> Voor deze stap wordt ervan uitgegaan dat u een gesimuleerd apparaat gebruikt, gerealiseerd door het uitvoeren van een SDK-voorbeeldtoepassing voor registratie vanuit uw werkstation. Dezelfde concepten zijn echter van toepassing als u een registratietoepassing bouwt die moet worden geïmplementeerd op een fysiek apparaat. 

1. Selecteer in Azure Portal de blade **Overzicht** voor Device Provisioning Service en kopieer de waarde bij **_Id-bereik_**. Het *Id-bereik* wordt gegenereerd met de service en is gegarandeerd uniek. Het is onveranderbaar en wordt gebruikt als een unieke identificatie van de registratie-id's.

    ![Device Provisioning Service-eindpuntgegevens uit de portalblade extraheren](./media/tutorial-set-up-device/extract-dps-endpoints.png) 

1. In *Solution Explorer* van Visual Studio op uw computer gaat u naar de map **Inrichten\_Voorbeelden**. Selecteer het voorbeeldproject met de naam **prov\_dev\_client\_sample** en open het bronbestand **prov\_dev\_client\_sample.c**.

1. Wijs de waarde _Id-bereik_ die u hebt verkregen in stap 1, toe aan de variabele `id_scope` (verwijder de haakjes links/`[` en rechts/`]`): 

    ```c
    static const char* global_prov_uri = "global.azure-devices-provisioning.net";
    static const char* id_scope = "[ID Scope]";
    ```

    Ter informatie, de variabele `global_prov_uri`, waarmee de IoT Hub API voor clientregistratie `IoTHubClient_LL_CreateFromDeviceAuth` verbinding kan maken met de toegewezen Device Provisioning Service-instantie.

1. In de functie **main()** in hetzelfde bestand, voegt u een opmerking toe aan of verwijdert u een opmerking bij de variabele `hsm_type` die overeenkomt met het attestation-mechanisme dat wordt gebruikt voor de registratiesoftware van het apparaat (TPM of X.509): 

    ```c
    hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    ```

1. Sla de wijzigingen op en bouw het voorbeeld **prov\_dev\_client\_sample** opnieuw door Build solution te selecteren in het menu Build. 

1. Klik met de rechtermuisknop op het project **prov\_dev\_client\_sample** project onder de map **Provision\_Samples**, en selecteer **Set as Startup Project**. Voer de voorbeeldtoepassing nog NIET uit.

> [!IMPORTANT]
> Start het apparaat nog niet! U moet het proces voltooien door het apparaat eerst in te schrijven bij Device Provisioning Service, voordat u het apparaat start. In de sectie Volgende stappen hieronder vindt u het volgende artikel.

### <a name="sdk-apis-used-during-registration-for-reference-only"></a>SDK API's die worden gebruikt tijdens de registratie (alleen ter informatie)

Ter informatie, de SDK biedt de volgende API's die de toepassing kan gebruiken tijdens de registratie. Deze API’s helpen om het apparaat te verbinden met en te registreren bij Device Provisioning Service wanneer het wordt gestart. Het apparaat ontvangt vervolgens de informatie die is vereist om een verbinding tot stand te brengen met de IoT Hub-instantie:

```C
// Creates a Provisioning Client for communications with the Device Provisioning Client Service.  
PROV_DEVICE_LL_HANDLE Prov_Device_LL_Create(const char* uri, const char* scope_id, PROV_DEVICE_TRANSPORT_PROVIDER_FUNCTION protocol)

// Disposes of resources allocated by the provisioning Client.
void Prov_Device_LL_Destroy(PROV_DEVICE_LL_HANDLE handle)

// Asynchronous call initiates the registration of a device.
PROV_DEVICE_RESULT Prov_Device_LL_Register_Device(PROV_DEVICE_LL_HANDLE handle, PROV_DEVICE_CLIENT_REGISTER_DEVICE_CALLBACK register_callback, void* user_context, PROV_DEVICE_CLIENT_REGISTER_STATUS_CALLBACK reg_status_cb, void* status_user_ctext)

// Api to be called by user when work (registering device) can be done
void Prov_Device_LL_DoWork(PROV_DEVICE_LL_HANDLE handle)

// API sets a runtime option identified by parameter optionName to a value pointed to by value
PROV_DEVICE_RESULT Prov_Device_LL_SetOption(PROV_DEVICE_LL_HANDLE handle, const char* optionName, const void* value)
```

Mogelijk moet u ook de Device Provisioning Service-clientoepassing voor registratie verfijnen, aanvankelijk met behulp van een gesimuleerd apparaat, en een testservice-installatie. Als de toepassing werkt in de testomgeving, kunt u het bouwen voor uw specifieke apparaat en het uitvoerbare bestand kopiëren naar de installatiekopie van het apparaat. 

## <a name="clean-up-resources"></a>Resources opschonen

Op dit punt worden Device Provisioning Service en IoT Hub mogelijk uitgevoerd in de portal. Als u de installatie van apparaatinrichting wilt afbreken, en/of de afronding van deze reeks zelfstudies wilt uitstellen, raden we u aan ze uit te schakelen om onnodige kosten te vermijden.

1. Klik in het linkermenu in de Azure Portal op **Alle resources** en selecteer uw Device Provisioning Service. Klik bovenaan de blade **All resources** op **Delete**.  
1. Klik in het linkermenu in de Azure Portal op **Alle resources** en selecteer vervolgens uw IoT-hub. Klik bovenaan de blade **All resources** op **Delete**.  

## <a name="next-steps"></a>Volgende stappen
In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Platformspecifieke SDK van de Device Provisioning Service-client bouwen
> * De beveiligingsartefacten extraheren
> * De software voor apparaatregistratie maken

Ga verder met de volgende zelfstudie voor informatie over het inrichten van het apparaat in de IoT-hub door het te registreren bij Azure IoT Hub Device Provisioning Service voor automatische inrichting.

> [!div class="nextstepaction"]
> [Het apparaat inrichten in de IoT-hub](tutorial-provision-device-to-hub.md)
