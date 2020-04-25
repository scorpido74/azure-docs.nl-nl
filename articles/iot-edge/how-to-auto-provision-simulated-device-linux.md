---
title: Apparaat inrichten met een virtuele TPM op Linux VM-Azure IoT Edge
description: Een gesimuleerde TPM op een virtuele Linux-machine gebruiken om de Azure Device Provisioning Service voor Azure IoT Edge te testen
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 3/2/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 82bdc71a123a263fffd842a04f4837b34aaa8685
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82131071"
---
# <a name="create-and-provision-an-iot-edge-device-with-a-virtual-tpm-on-a-linux-virtual-machine"></a>Een IoT Edge apparaat maken en inrichten met een virtuele TPM op een virtuele Linux-machine

Azure IoT Edge apparaten kunnen automatisch worden ingericht met behulp van de [Device Provisioning Service](../iot-dps/index.yml). Als u niet bekend bent met het proces van automatische inrichting, raadpleegt u de [concepten voor automatische inrichting](../iot-dps/concepts-auto-provisioning.md) voordat u doorgaat.

In dit artikel leest u hoe u automatisch inrichten op een gesimuleerd IoT Edge apparaat kunt testen met de volgende stappen:

* Een virtuele Linux-machine (VM) maken in Hyper-V met een gesimuleerde Trusted Platform Module (TPM) voor hardware-beveiliging.
* Maak een instantie van IoT Hub Device Provisioning Service (DPS).
* Een afzonderlijke inschrijving voor het apparaat maken
* Installeer de IoT Edge runtime en verbind het apparaat met IoT Hub

> [!TIP]
> In dit artikel wordt beschreven hoe u DPS Provisioning test met een TPM-Simulator, maar veel van deze is van toepassing op fysieke TPM-hardware, zoals [INFINEON OPTIGA&trade; TPM](https://catalog.azureiotsolutions.com/details?title=OPTIGA-TPM-SLB-9670-Iridium-Board), een Azure Certified voor IOT-apparaat.
>
> Als u een fysiek apparaat gebruikt, kunt u door gaan naar de sectie [inrichtings gegevens ophalen van een fysiek apparaat](#retrieve-provisioning-information-from-a-physical-device) in dit artikel.

## <a name="prerequisites"></a>Vereisten

* Een Windows-ontwikkel computer waarop [Hyper-V is ingeschakeld](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v). In dit artikel wordt gebruikgemaakt van Windows 10 waarop een Ubuntu-Server-VM wordt uitgevoerd.
* Een actieve IoT Hub.
* Als u een gesimuleerde TPM gebruikt, wordt [Visual Studio](https://visualstudio.microsoft.com/vs/) 2015 of hoger met de werk belasting [' Desktop Development with C++ '](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) ingeschakeld.

> [!NOTE]
> TPM 2,0 is vereist voor het gebruik van TPM-Attestation met DPS en kan alleen worden gebruikt voor het maken van afzonderlijke, niet-groeps registraties.

## <a name="create-a-linux-virtual-machine-with-a-virtual-tpm"></a>Een virtuele Linux-machine maken met een virtuele TPM

In deze sectie maakt u een nieuwe virtuele Linux-machine in Hyper-V. U hebt deze virtuele machine met een gesimuleerde TPM geconfigureerd, zodat u deze kunt gebruiken om te testen hoe automatische inrichting werkt met IoT Edge.

### <a name="create-a-virtual-switch"></a>Een virtuele switch maken

Met een virtuele switch kan de virtuele machine verbinding maken met een fysiek netwerk.

1. Open Hyper-V-beheer op de Windows-computer.

2. Selecteer **Virtual Switch Manager**in het menu **acties** .

3. Kies een **externe** virtuele switch en selecteer **virtuele switch maken**.

4. Geef de nieuwe virtuele switch een naam, bijvoorbeeld **EdgeSwitch**. Zorg ervoor dat het verbindings type is ingesteld op **extern netwerk**en selecteer **OK**.

5. In een pop-upvenster wordt u gewaarschuwd dat de netwerk verbinding mogelijk wordt verstoord. Selecteer **Ja** om door te gaan.

Als er fouten worden weer geven tijdens het maken van de nieuwe virtuele switch, moet u ervoor zorgen dat er geen andere switches gebruikmaken van de Ethernet-adapter en dat er geen andere switches dezelfde naam gebruiken.

### <a name="create-virtual-machine"></a>Virtuele machine maken

1. Down load een schijf kopie bestand dat u voor de virtuele machine wilt gebruiken en sla het lokaal op. Bijvoorbeeld [Ubuntu Server](https://www.ubuntu.com/download/server).

2. Selecteer in Hyper-V-beheer opnieuw de optie **nieuwe** > **virtuele machine** in het menu **acties** .

3. Voltooi de **wizard Nieuwe virtuele machine** met de volgende specifieke configuraties:

   1. **Generatie opgeven**: Selecteer **generatie 2**. Voor virtuele machines van de 2e generatie is geneste virtualisatie ingeschakeld, wat vereist is om IoT Edge uit te voeren op een virtuele machine.
   2. **Netwerken configureren**: Stel de waarde in van **verbinding** met de virtuele switch die u hebt gemaakt in de vorige sectie.
   3. **Installatie opties**: Selecteer **een besturings systeem installeren vanaf een opstartbaar installatie kopie bestand** en blader naar het schijf kopie bestand dat u lokaal hebt opgeslagen.

4. Selecteer in de wizard **volt ooien** om de virtuele machine te maken.

Het kan een paar minuten duren voordat de nieuwe VM is gemaakt.

### <a name="enable-virtual-tpm"></a>Virtuele TPM inschakelen

Zodra de VM is gemaakt, opent u de instellingen om de Virtual trusted platform module (TPM) in te scha kelen waarmee u het apparaat automatisch kunt inrichten.

1. Selecteer de virtuele machine en open vervolgens de bijbehorende **instellingen**.

2. Navigeer naar **Beveiliging**.

3. **Schakel beveiligd opstarten inschakelen**uit.

4. **Schakel trusted platform module in**.

5. Klik op **OK**.  

### <a name="start-the-virtual-machine-and-collect-tpm-data"></a>De virtuele machine starten en TPM-gegevens verzamelen

Bouw in de virtuele machine een hulp programma dat u kunt gebruiken om de **registratie-id** en **goedkeurings sleutel**van het apparaat op te halen.

1. Start de virtuele machine en maak er verbinding mee.

1. Volg de aanwijzingen in de virtuele machine om het installatie proces te volt ooien en de computer opnieuw op te starten.

1. Meld u aan bij uw virtuele machine en volg de stappen in [een Linux-ontwikkel omgeving instellen](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) om de Azure IOT Device SDK voor C te installeren en te bouwen.

   >[!TIP]
   >In het kader van dit artikel kopieert u naar en plakt u de virtuele machine. Dit is niet eenvoudig via de verbindings toepassing Hyper-V-beheer. U kunt het beste een verbinding met de virtuele machine maken via Hyper-V-beheer om het IP- `ifconfig`adres op te halen:. Vervolgens kunt u het IP-adres gebruiken om verbinding te maken via `ssh <username>@<ipaddress>`SSH:.

1. Voer de volgende opdrachten uit om het SDK-hulp programma te bouwen dat de inrichtings gegevens van uw apparaat ophaalt uit de TPM-Simulator.

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. Ga vanuit een opdracht venster naar de `azure-iot-sdk-c` map en voer de TPM-Simulator uit. Deze luistert via een socket op poorten 2321 en 2322. Sluit dit opdracht venster niet. u moet deze simulator blijven uitvoeren.

   Voer in `azure-iot-sdk-c` de map de volgende opdracht uit om de Simulator te starten:

   ```bash
   ./provisioning_client/deps/utpm/tools/tpm_simulator/Simulator.exe
   ```

1. Open met behulp van Visual Studio de oplossing die `cmake` is gegenereerd `azure_iot_sdks.sln`in de map met de naam en bouw deze met behulp van de opdracht **Build Solution** in het menu **Build** .

1. In het deelvenster **Solution Explorer** van Visual Studio gaat u naar de map **Inrichten\_Extra**. Klik met de rechtermuisknop op het **tpm_device_provision**-project en selecteer **Set as Startup Project**.

1. Voer de oplossing uit met behulp van een van de **Start** opdrachten in het menu **fout opsporing** . In het uitvoer venster worden de **registratie-id** van de TPM-Simulator en de **goedkeurings sleutel**weer gegeven die u moet kopiëren voor later gebruik wanneer u een afzonderlijke inschrijving voor uw apparaat maakt in u kunt dit venster sluiten (met registratie-id en goedkeurings sleutel), maar het TPM-Simulator-venster actief laten.

## <a name="retrieve-provisioning-information-from-a-physical-device"></a>Inrichtings gegevens ophalen van een fysiek apparaat

Bouw op uw apparaat een hulp programma dat u kunt gebruiken om de inrichtings gegevens van het apparaat op te halen.

1. Volg de stappen in [een Linux-ontwikkel omgeving instellen](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) om de Azure IOT Device SDK voor C te installeren en te bouwen.

1. Voer de volgende opdrachten uit om het SDK-hulp programma te bouwen dat de inrichtings gegevens van uw apparaat ophaalt van het TPM-apparaat.

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. Kopieer de waarden voor de **registratie-id** en **goedkeurings sleutel**. U gebruikt deze waarden voor het maken van een afzonderlijke inschrijving voor uw apparaat in DPS.

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>De IoT Hub Device Provisioning Service instellen

Maak een nieuw exemplaar van de IoT Hub Device Provisioning Service in Azure en koppel deze aan uw IoT-hub. U kunt de instructies voor [het instellen van de IOT hub DPS](../iot-dps/quick-setup-auto-provision.md)volgen.

Nadat u de Device Provisioning Service hebt uitgevoerd, kopieert u de waarde van **id-bereik** van de pagina overzicht. U gebruikt deze waarde bij het configureren van de IoT Edge runtime.

## <a name="create-a-dps-enrollment"></a>Een DPS-inschrijving maken

Haal de inrichtings gegevens op van de virtuele machine en gebruik deze om een afzonderlijke registratie in Device Provisioning Service te maken.

Wanneer u een inschrijving in DPS maakt, hebt u de mogelijkheid om een **eerste dubbele toestand**van het apparaat te declareren. In het dubbele apparaat kunt u Tags instellen om apparaten te groeperen op elke gewenste metrische waarde in uw oplossing, zoals regio, omgeving, locatie of apparaattype. Deze tags worden gebruikt voor het maken van [automatische implementaties](how-to-deploy-at-scale.md).

> [!TIP]
> In de Azure CLI kunt u een [inschrijving](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/dps/enrollment) of een [registratie groep](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/dps/enrollment-group) maken en de vlag voor **rand ingeschakeld** gebruiken om op te geven dat een apparaat, of groep apparaten, een IOT edge apparaat is.

1. Ga in het [Azure Portal](https://portal.azure.com)naar uw exemplaar van IOT hub Device Provisioning Service.

2. Selecteer onder **instellingen**de optie **inschrijvingen beheren**.

3. Selecteer **Individuele inschrijving toevoegen** en voer de volgende stappen uit om de registratie te configureren:  

   1. Selecteer voor **mechanisme** **TPM**.

   2. Geef de **goedkeurings sleutel** en **registratie-id** op die u van de virtuele machine hebt gekopieerd.

      > [!TIP]
      > Als u een fysiek TPM-apparaat gebruikt, moet u de **goedkeurings sleutel**bepalen, die uniek is voor elke TPM-chip en die is verkregen van de fabrikant van de TPM-chip die eraan is gekoppeld. U kunt een unieke **registratie-id** voor uw TPM-apparaat afleiden door bijvoorbeeld een SHA-256-hash van de goedkeurings sleutel te maken.

   3. Selecteer **waar** om te declareren dat deze virtuele machine een IOT edge apparaat is.

   4. Kies de gekoppelde **IOT hub** waarmee u uw apparaat wilt verbinden. U kunt meerdere hubs kiezen en het apparaat wordt toegewezen aan een van deze op basis van het geselecteerde toewijzings beleid.

   5. Geef een ID op voor uw apparaat als u wilt. U kunt apparaat-Id's gebruiken om een afzonderlijk apparaat te richten op het implementeren van een module. Als u geen apparaat-ID opgeeft, wordt de registratie-ID gebruikt.

   6. Voeg indien gewenst een tag-waarde toe aan de **eerste dubbele toestand** van het apparaat. U kunt tags gebruiken om groepen apparaten te richten op het implementeren van modules. Bijvoorbeeld:

      ```json
      {
         "tags": {
            "environment": "test"
         },
         "properties": {
            "desired": {}
         }
      }
      ```

   7. Selecteer **Opslaan**.

Nu een inschrijving voor dit apparaat bestaat, kan de IoT Edge runtime automatisch het apparaat inrichten tijdens de installatie.

## <a name="install-the-iot-edge-runtime"></a>De IoT Edge runtime installeren

De IoT Edge-runtime wordt op alle IoT Edge-apparaten geïmplementeerd. De onderdelen worden in containers uitgevoerd en bieden u de mogelijkheid om extra containers op het apparaat te implementeren, zodat u code aan de rand kunt uitvoeren. Installeer de IoT Edge runtime op uw virtuele machine.

Ken uw DPS **-id-bereik** en **registratie-id** van het apparaat aan voordat u begint met het artikel dat overeenkomt met uw apparaattype. Als u de voor beeld-Ubuntu-Server hebt geïnstalleerd, gebruikt u de **x64** -instructies. Zorg ervoor dat u de IoT Edge-runtime configureert voor automatisch, niet hand matig, inrichten.

[Installeer de Azure IoT Edge runtime op Linux](how-to-install-iot-edge-linux.md)

## <a name="give-iot-edge-access-to-the-tpm"></a>IoT Edge toegang tot de TPM geven

Om ervoor te zorgen dat de IoT Edge-runtime automatisch uw apparaat inricht, moet het toegang tot de TPM hebben.

U kunt TPM-toegang verlenen aan de IoT Edge-runtime door de systeem instellingen te overschrijven zodat de `iotedge` service bevoegdheden heeft voor het hoofd niveau. Als u de service bevoegdheden niet wilt verhogen, kunt u ook de volgende stappen gebruiken om de toegang tot de TPM hand matig te bieden.

1. Zoek het bestandspad naar de TPM hardware-module op het apparaat en sla het bestand op als een lokale variabele.

   ```bash
   tpm=$(sudo find /sys -name dev -print | fgrep tpm | sed 's/.\{4\}$//')
   ```

2. Maak een nieuwe regel waarmee de IoT Edge runtime toegang krijgt tot tpm0.

   ```bash
   sudo touch /etc/udev/rules.d/tpmaccess.rules
   ```

3. Open het regel bestand.

   ```bash
   sudo nano /etc/udev/rules.d/tpmaccess.rules
   ```

4. Kopieer de volgende toegangs gegevens naar het regel bestand.

   ```input
   # allow iotedge access to tpm0
   KERNEL=="tpm0", SUBSYSTEM=="tpm", GROUP="iotedge", MODE="0660"
   ```

5. Sla het bestand op en sluit het af.

6. Activeer het udev-systeem om de nieuwe regel te evalueren.

   ```bash
   /bin/udevadm trigger $tpm
   ```

7. Controleer of de regel is toegepast.

   ```bash
   ls -l /dev/tpm0
   ```

   Geslaagde uitvoer ziet er als volgt uit:

   ```output
   crw-rw---- 1 root iotedge 10, 224 Jul 20 16:27 /dev/tpm0
   ```

   Als u niet ziet dat de juiste machtigingen zijn toegepast, start u de computer opnieuw op om udev te vernieuwen.

## <a name="restart-the-iot-edge-runtime"></a>De IoT Edge-runtime opnieuw starten

Start de IoT Edge-runtime opnieuw zodat alle configuratie wijzigingen die u op het apparaat hebt aangebracht, worden opgehaald.

   ```bash
   sudo systemctl restart iotedge
   ```

Controleer of de IoT Edge runtime wordt uitgevoerd.

   ```bash
   sudo systemctl status iotedge
   ```

Als er inrichtings fouten worden weer gegeven, is het mogelijk dat de configuratie wijzigingen nog niet van kracht zijn. Probeer de IoT Edge-daemon opnieuw op te starten.

   ```bash
   sudo systemctl daemon-reload
   ```

Of probeer de virtuele machine opnieuw op te starten om te controleren of de wijzigingen van kracht worden op een nieuwe start.

## <a name="verify-successful-installation"></a>Geslaagde installatie controleren

Als de runtime is gestart, kunt u naar uw IoT Hub gaan en zien dat het nieuwe apparaat automatisch is ingericht. Uw apparaat is nu klaar om IoT Edge-modules uit te voeren.

Controleer de status van de IoT Edge-daemon.

```cmd/sh
systemctl status iotedge
```

Raadpleeg daemon-Logboeken.

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

Een lijst met actieve modules weer geven.

```cmd/sh
iotedge list
```

U kunt controleren of de afzonderlijke registratie die u hebt gemaakt in Device Provisioning Service is gebruikt. Navigeer naar het Device Provisioning service-exemplaar in het Azure Portal. Open de inschrijvings gegevens voor de afzonderlijke inschrijving die u hebt gemaakt. U ziet dat de status van de registratie is **toegewezen** en dat de apparaat-id wordt vermeld.

## <a name="next-steps"></a>Volgende stappen

Met het inschrijvings proces voor Device Provisioning Service kunt u de apparaat-ID en de dubbele Tags van het apparaat instellen op hetzelfde moment als u het nieuwe apparaat inricht. U kunt deze waarden gebruiken om afzonderlijke apparaten of groepen apparaten te richten met behulp van automatische Apparaatbeheer. Meer informatie over [het implementeren en bewaken van IOT Edge modules op schaal met behulp van de Azure Portal](how-to-deploy-at-scale.md) of [met behulp van Azure cli](how-to-deploy-cli-at-scale.md).
