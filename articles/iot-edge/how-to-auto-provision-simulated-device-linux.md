---
title: Apparaat voorzien van een virtuele TPM op Linux VM - Azure IoT Edge
description: Een gesimuleerde TPM op een Linux-VM gebruiken om azure-apparaatinrichtingsservice voor Azure IoT Edge te testen
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 3/2/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: b62f551e2532e0205159358b3618695524ae85c8
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/05/2020
ms.locfileid: "80666694"
---
# <a name="create-and-provision-an-iot-edge-device-with-a-virtual-tpm-on-a-linux-virtual-machine"></a>Een IoT Edge-apparaat maken en inrichten met een virtuele TPM op een virtuele Linux-machine

Azure IoT Edge-apparaten kunnen automatisch worden ingericht met behulp van de [Service Apparaatinrichting](../iot-dps/index.yml). Als u niet bekend bent met het proces van automatisch inrichten, bekijkt u de [concepten voor automatisch inrichten](../iot-dps/concepts-auto-provisioning.md) voordat u verdergaat.

In dit artikel ziet u hoe u automatische inrichting op een gesimuleerd IoT Edge-apparaat testen met de volgende stappen:

* Maak een Linux virtuele machine (VM) in Hyper-V met een gesimuleerde Trusted Platform Module (TPM) voor hardwarebeveiliging.
* Een exemplaar van de IoT Hub Device Provisioning Service (DPS) maken.
* Een individuele inschrijving voor het apparaat maken
* Installeer de 3T Edge-runtime en sluit het apparaat aan op IoT Hub

> [!TIP]
> In dit artikel wordt beschreven hoe u DPS-provisioning testen met behulp van een TPM-simulator, maar veel daarvan is van toepassing op fysieke TPM-hardware, zoals de [Infineon&trade; OPTIGA TPM](https://catalog.azureiotsolutions.com/details?title=OPTIGA-TPM-SLB-9670-Iridium-Board), een Azure-gecertificeerd voor IoT-apparaat.
>
> Als u een fysiek apparaat gebruikt, u doorgaan naar de [informatie ophalen van het inrichten van een fysieke apparaatsectie](#retrieve-provisioning-information-from-a-physical-device) in dit artikel.

## <a name="prerequisites"></a>Vereisten

* Een Windows-ontwikkelingsmachine met [Hyper-V ingeschakeld.](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v) In dit artikel wordt gebruik gemaakt van Windows 10 met een Ubuntu Server VM.
* Een actieve IoT Hub.
* Als u een gesimuleerde TPM gebruikt, [is Visual Studio](https://visualstudio.microsoft.com/vs/) 2015 of hoger ingeschakeld met de werkbelasting ['Bureaubladontwikkeling met C++'.](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/)

> [!NOTE]
> TPM 2.0 is vereist bij het gebruik van TPM-attest met DPS en kan alleen worden gebruikt om individuele, niet groepsinschrijvingen te maken.

## <a name="create-a-linux-virtual-machine-with-a-virtual-tpm"></a>Maak een Linux virtuele machine met een virtuele TPM

In deze sectie maak je een nieuwe Linux virtuele machine op Hyper-V. U hebt deze virtuele machine geconfigureerd met een gesimuleerde TPM, zodat u deze gebruiken om te testen hoe automatische inrichting werkt met IoT Edge.

### <a name="create-a-virtual-switch"></a>Een virtuele switch maken

Met een virtuele schakelaar kan uw virtuele machine verbinding maken met een fysiek netwerk.

1. Open Hyper-V Manager op uw Windows-machine.

2. Selecteer **Virtuele schakelbeheer**in het menu **Acties** .

3. Kies een **externe** virtuele switch en selecteer **Virtuele switch maken**.

4. Geef uw nieuwe virtuele switch een naam, bijvoorbeeld **EdgeSwitch**. Controleer of het verbindingstype is ingesteld op **Extern netwerk**en selecteer **Ok**.

5. Een pop-up waarschuwt u dat de netwerkverbinding kan worden verstoord. Selecteer **Ja** om door te gaan.

Als u fouten ziet tijdens het maken van de nieuwe virtuele switch, moet u ervoor zorgen dat er geen andere switches de ethernetadapter gebruiken en dat geen andere switches dezelfde naam gebruiken.

### <a name="create-virtual-machine"></a>Virtuele machine maken

1. Download een schijfafbeeldingsbestand dat u wilt gebruiken voor uw virtuele machine en sla het lokaal op. [Ubuntu-server](https://www.ubuntu.com/download/server)bijvoorbeeld .

2. Selecteer in Hyper-V Manager opnieuw **Nieuwe** > **virtuele machine** in het menu **Acties.**

3. Voltooi de **wizard Nieuwe virtuele machine** met de volgende specifieke configuraties:

   1. **Generatie opgeven:** **Generatie 2**selecteren . Generatie 2 virtuele machines hebben geneste virtualisatie ingeschakeld, die nodig is om IoT Edge draaien op een virtuele machine.
   2. **Netwerken configureren:** stel de waarde van Verbinding in **op** de virtuele switch die u in de vorige sectie hebt gemaakt.
   3. **Installatieopties:** selecteer **Een besturingssysteem installeren vanuit een opstartbaar afbeeldingsbestand** en blader naar het schijfafbeeldingsbestand dat u lokaal hebt opgeslagen.

4. Selecteer **Voltooien** in de wizard om de virtuele machine te maken.

Het kan enkele minuten duren om de nieuwe VM te maken.

### <a name="enable-virtual-tpm"></a>Virtuele TPM inschakelen

Zodra uw vm is gemaakt, opent u de instellingen om de virtuele vertrouwde platformmodule (TPM) in te schakelen waarmee u het apparaat automatisch inrichten.

1. Selecteer de virtuele machine en open de **instellingen**.

2. Navigeer naar **Beveiliging**.

3. Schakel het **selectievakje Veilig opstarten inschakelen uit**.

4. Schakel **De module Vertrouwd platform inschakelen in**.

5. Klik op **OK**.  

### <a name="start-the-virtual-machine-and-collect-tpm-data"></a>Start de virtuele machine en verzamel TPM-gegevens

Bouw in de virtuele machine een tool die u gebruiken om de **registratie-id** en **goedkeuringssleutel**van het apparaat op te halen.

1. Start uw virtuele machine en maak er verbinding mee.

1. Volg de aanwijzingen in de virtuele machine om het installatieproces te voltooien en de machine opnieuw op te starten.

1. Meld u aan bij uw VM en volg de stappen in [Het instellen van een Linux-ontwikkelomgeving](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) om de Azure IoT-apparaat SDK voor C te installeren en te bouwen.

   >[!TIP]
   >In de loop van dit artikel kopieert en plakt u de virtuele machine, wat niet eenvoudig is via de Hyper-V Manager-verbindingstoepassing. U via Hyper-V Manager één keer verbinding maken met `ifconfig`de virtuele machine om het IP-adres op te halen:. Vervolgens u het IP-adres gebruiken `ssh <username>@<ipaddress>`om verbinding te maken via SSH:.

1. Voer de volgende opdrachten uit om het SDK-gereedschap te bouwen waarmee uw apparaat informatie uit de TPM-simulator ophaalt.

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. Navigeer vanuit een opdrachtvenster `azure-iot-sdk-c` naar de map en voer de TPM-simulator uit. Deze luistert via een socket op poorten 2321 en 2322. Sluit dit opdrachtvenster niet. je moet deze simulator draaiende te houden.

   Voer `azure-iot-sdk-c` in de map de volgende opdracht uit om de simulator te starten:

   ```bash
   ./provisioning_client/deps/utpm/tools/tpm_simulator/Simulator.exe
   ```

1. Open visual studio met behulp `cmake` van `azure_iot_sdks.sln`Visual Studio in de map met de naam en bouw deze met de opdracht **Oplossing bouwen** in het menu **Bouwen.**

1. In het deelvenster **Solution Explorer** van Visual Studio gaat u naar de map **Inrichten\_Extra**. Klik met de rechtermuisknop op het **tpm_device_provision**-project en selecteer **Set as Startup Project**.

1. Voer de oplossing uit met een van de opdrachten **Start** in het menu **Foutopsporing.** In het uitvoervenster worden de **registratie-id** van de TPM-simulator en de **goedkeuringssleutel**weergegeven, die u later moet kopiëren voor gebruik wanneer u een individuele inschrijving voor uw apparaat maakt in U dit venster sluiten (met registratie-id en goedkeuringssleutel), maar het tpm-simulatorvenster laten draaien.

## <a name="retrieve-provisioning-information-from-a-physical-device"></a>Informatie ophalen van een fysiek apparaat

Bouw op uw apparaat een tool die u gebruiken om de informatie over het inrichten van het apparaat op te halen.

1. Volg de stappen in [Het instellen van een Linux-ontwikkelomgeving](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) om de Azure IoT-apparaat SDK voor C te installeren en te bouwen.

1. Voer de volgende opdrachten uit om het SDK-gereedschap te bouwen waarmee uw apparaat informatie ophaalt van het TPM-apparaat.

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. Kopieer de waarden voor **registratie-id** en **goedkeuringssleutel**. U gebruikt deze waarden om een individuele inschrijving voor uw apparaat in DPS te maken.

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>De Service voor het inrichten van IoT-hub-apparaten instellen

Maak een nieuw exemplaar van de IoT Hub Device Provisioning Service in Azure en koppel deze aan uw IoT-hub. U de instructies volgen in [Het instellen van de IoT Hub DPS.](../iot-dps/quick-setup-auto-provision.md)

Nadat u de service voor apparaatinrichting hebt uitgevoerd, kopieert u de waarde van **id-bereik** vanaf de overzichtspagina. U gebruikt deze waarde wanneer u de Runtime van IoT Edge configureert.

## <a name="create-a-dps-enrollment"></a>Een DPS-inschrijving maken

Haal de informatie op voor het inrichten van uw virtuele machine en gebruik deze om een individuele inschrijving te maken in de service apparaatvoorziening.

Wanneer u een inschrijving maakt in DPS, u een **eerste apparaattweelingstatus**declareren. In de apparaattweeling u tags instellen op apparaten groeperen op elke statistiek die u nodig hebt in uw oplossing, zoals regio, omgeving, locatie of apparaattype. Deze tags worden gebruikt om [automatische implementaties](how-to-deploy-monitor.md)te maken.

> [!TIP]
> In azure cli u een [inschrijving](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/dps/enrollment) of een [inschrijvingsgroep](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/dps/enrollment-group) maken en de vlag met randfunctionaliteit gebruiken om op te geven dat een apparaat of groep apparaten een IoT **Edge-apparaat** is.

1. Navigeer in de [Azure-portal](https://portal.azure.com)naar uw exemplaar van de IoT Hub Device Provisioning Service.

2. Selecteer **onder Instellingen**de optie **Inschrijvingen beheren**.

3. Selecteer **Afzonderlijke inschrijving toevoegen** en voer de volgende stappen uit om de inschrijving te configureren:  

   1. Selecteer **TPM**voor **mechanisme**.

   2. Geef de **goedkeuringssleutel** en **registratie-id** op die u van uw virtuele machine hebt gekopieerd.

      > [!TIP]
      > Als u een fysiek TPM-apparaat gebruikt, moet u de **goedkeuringssleutel**bepalen, die uniek is voor elke TPM-chip en wordt verkregen van de TPM-chipfabrikant die eraan is gekoppeld. U een unieke **registratie-id** voor uw TPM-apparaat afleiden door bijvoorbeeld een SHA-256-hash van de goedkeuringssleutel te maken.

   3. Selecteer **True** om te verklaren dat deze virtuele machine een IoT Edge-apparaat is.

   4. Kies de gekoppelde **IoT-hub** waarmee u uw apparaat wilt verbinden. U meerdere hubs kiezen en het apparaat wordt aan een van deze hubs toegewezen volgens het geselecteerde toewijzingsbeleid.

   5. Geef een ID voor uw apparaat als u wilt. U apparaat-id's gebruiken om een individueel apparaat te targeten voor de implementatie van modules. Als u geen apparaat-id verstrekt, wordt de registratie-ID gebruikt.

   6. Voeg desgevraagd een tagwaarde toe aan de **twinstate van het eerste apparaat.** U tags gebruiken om doelgroepen van apparaten te gebruiken voor het implementeren van modules. Bijvoorbeeld:

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

Nu er een inschrijving voor dit apparaat bestaat, kan de IoT Edge-runtime het apparaat tijdens de installatie automatisch inrichten.

## <a name="install-the-iot-edge-runtime"></a>De runtime van IoT Edge installeren

De IoT Edge-runtime wordt op alle IoT Edge-apparaten geïmplementeerd. De onderdelen worden uitgevoerd in containers en u extra containers op het apparaat implementeren, zodat u code aan de rand uitvoeren. Installeer de IoT Edge-runtime op uw virtuele machine.

Ken uw DPS **ID-scope** en **apparaatregistratie-id** voordat u begint met het artikel dat overeenkomt met uw apparaattype. Als u de voorbeeld-Ubuntu-server hebt geïnstalleerd, gebruikt u de **x64-instructies.** Zorg ervoor dat u de 3T Edge-runtime configureert voor automatische, niet handmatige inprovisioning.

[De Azure IoT Edge-runtime op Linux installeren](how-to-install-iot-edge-linux.md)

## <a name="give-iot-edge-access-to-the-tpm"></a>IoT Edge toegang geven tot de TPM

Om ervoor te laten gaan dat de IoT Edge-runtime uw apparaat automatisch indient, heeft het toegang tot de TPM nodig.

U TPM toegang geven tot de IoT Edge-runtime door de systemd-instellingen te overschrijven, zodat de **iotedge-service** rootrechten heeft. Als u de servicebevoegdheden niet wilt verhogen, u ook de volgende stappen gebruiken om handmatig TPM-toegang te verlenen.

1. Zoek het bestandspad naar de TPM-hardwaremodule op uw apparaat en sla het op als een lokale variabele.

   ```bash
   tpm=$(sudo find /sys -name dev -print | fgrep tpm | sed 's/.\{4\}$//')
   ```

2. Maak een nieuwe regel die de IoT Edge runtime toegang geeft tot tpm0.

   ```bash
   sudo touch /etc/udev/rules.d/tpmaccess.rules
   ```

3. Open het regelsbestand.

   ```bash
   sudo nano /etc/udev/rules.d/tpmaccess.rules
   ```

4. Kopieer de volgende toegangsgegevens naar het regelsbestand.

   ```input
   # allow iotedge access to tpm0
   KERNEL=="tpm0", SUBSYSTEM=="tpm", GROUP="iotedge", MODE="0660"
   ```

5. Sla het bestand op en sluit deze af.

6. Activeer het udev-systeem om de nieuwe regel te evalueren.

   ```bash
   /bin/udevadm trigger $tpm
   ```

7. Controleer of de regel is toegepast.

   ```bash
   ls -l /dev/tpm0
   ```

   Succesvolle uitvoer ziet er als volgt uit:

   ```output
   crw-rw---- 1 root iotedge 10, 224 Jul 20 16:27 /dev/tpm0
   ```

   Als u niet ziet dat de juiste machtigingen zijn toegepast, probeert u de machine opnieuw op te starten om udev te vernieuwen.

## <a name="restart-the-iot-edge-runtime"></a>De runtime van IoT Edge opnieuw starten

Start de Runtime van IoT Edge opnieuw, zodat alle configuratiewijzigingen die u op het apparaat hebt aangebracht, worden opgenomen.

   ```bash
   sudo systemctl restart iotedge
   ```

Controleer of de Runtime van IoT Edge wordt uitgevoerd.

   ```bash
   sudo systemctl status iotedge
   ```

Als u indikfouten ziet, kan het zijn dat de configuratiewijzigingen nog niet van kracht zijn geworden. Probeer de IoT Edge-daemon opnieuw te starten.

   ```bash
   sudo systemctl daemon-reload
   ```

Of probeer uw virtuele machine opnieuw op te starten om te zien of de wijzigingen van kracht worden bij een nieuwe start.

## <a name="verify-successful-installation"></a>Geslaagde installatie verifiëren

Als de runtime succesvol is gestart, u naar uw IoT-hub gaan en zien dat uw nieuwe apparaat automatisch is ingericht. Nu is uw apparaat klaar om IoT Edge-modules uit te voeren.

Controleer de status van de IoT Edge Daemon.

```cmd/sh
systemctl status iotedge
```

Onderzoek daemon logs.

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

Lijst met lopende modules.

```cmd/sh
iotedge list
```

U controleren of de afzonderlijke inschrijving die u hebt gemaakt in de service voor apparaatinrichting is gebruikt. Navigeer naar de instantie Device Provisioning Service in de Azure-portal. Open de inschrijvingsgegevens voor de afzonderlijke inschrijving die u hebt gemaakt. De status van de inschrijving is **toegewezen** en de apparaat-id wordt weergegeven.

## <a name="next-steps"></a>Volgende stappen

Met het inschrijvingsproces voor apparaatinrichtingsservice u de apparaat-id en de dubbele tags van het apparaat instellen op hetzelfde moment als het nieuwe apparaat. U deze waarden gebruiken om afzonderlijke apparaten of groepen apparaten te targeten met automatisch apparaatbeheer. Meer informatie over het [implementeren en bewaken van IoT Edge-modules op schaal met behulp van de Azure-portal](how-to-deploy-monitor.md) of het gebruik van Azure [CLI.](how-to-deploy-monitor-cli.md)
