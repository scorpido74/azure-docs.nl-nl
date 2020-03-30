---
title: IoT Edge-versie bijwerken op apparaten - Azure IoT Edge | Microsoft Documenten
description: IoT Edge-apparaten bijwerken om de nieuwste versies van de beveiligingsdaemon en de IoT Edge-runtime uit te voeren
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 02/07/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 4a7c27beeb7208efcf6687e49193c8d3b68f5300
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77186511"
---
# <a name="update-the-iot-edge-security-daemon-and-runtime"></a>De IoT Edge-beveiligingsdaemon en -runtime bijwerken

Terwijl de IoT Edge-service nieuwe versies uitbrengt, wilt u uw IoT Edge-apparaten bijwerken voor de nieuwste functies en beveiligingsverbeteringen. In dit artikel vindt u informatie over het bijwerken van uw IoT Edge-apparaten wanneer er een nieuwe versie beschikbaar is.

Twee onderdelen van een IoT Edge-apparaat moeten worden bijgewerkt als u naar een nieuwere versie wilt gaan. De eerste is de beveiliging daemon, die draait op het apparaat en start de runtime modules wanneer het apparaat wordt gestart. Momenteel kan de beveiligingsdaemon alleen worden bijgewerkt vanaf het apparaat zelf. Het tweede onderdeel is de runtime, bestaande uit de IoT Edge-hub en IoT Edge-agentmodules. Afhankelijk van hoe u uw implementatie structureert, kan de runtime worden bijgewerkt vanaf het apparaat of op afstand.

Zie [Azure IoT Edge-releases](https://github.com/Azure/azure-iotedge/releases)voor het vinden van de nieuwste versie van Azure IoT Edge.

## <a name="update-the-security-daemon"></a>De beveiliging daemon bijwerken

De IoT Edge security daemon is een native component die moet worden bijgewerkt met behulp van de package manager op het IoT Edge-apparaat.

Controleer de versie van de beveiligingsdaemon die `iotedge version`op uw apparaat wordt uitgevoerd met behulp van de opdracht .

### <a name="linux-devices"></a>Linux-apparaten

Gebruik op Linux x64-apparaten apt-get of uw juiste package manager om de beveiligingsdaemon bij te werken naar de nieuwste versie.

```bash
apt-get update
apt-get install libiothsm iotedge
```

Als u wilt updaten naar een specifieke versie van de beveiligingsdaemon, zoekt u de versie die u wilt targeten vanuit [IoT Edge-releases.](https://github.com/Azure/azure-iotedge/releases) Zoek in die versie de juiste **libiothsm-std-** en **iotedge-bestanden** voor uw apparaat. Klik voor elk bestand met de rechtermuisknop op de bestandskoppeling en kopieer het koppelingsadres. Gebruik het koppelingsadres om de specifieke versies van die onderdelen te installeren:

```bash
curl -L <libiothsm-std link> -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb
curl -L <iotedge link> -o iotedge.deb && sudo dpkg -i ./iotedge.deb
```

### <a name="windows-devices"></a>Windows-apparaten

Gebruik op Windows-apparaten het PowerShell-script om de beveiligingsdaemon bij te werken. Het script trekt automatisch de nieuwste versie van de beveiliging daemon.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge -ContainerOs <Windows or Linux>
```

Als u de opdracht Update-IoTEdge uitvoert, worden de beveiligingsdaemon van uw apparaat verwijderd en bijgewerkt, samen met de twee runtime-containerafbeeldingen. Het config.yaml-bestand wordt op het apparaat bewaard, evenals gegevens van de Moby-containerengine (als u Windows-containers gebruikt). Als u de configuratiegegevens bijhoudt, hoeft u tijdens het updateproces de verbindingstekenreeks of apparaatinrichtingsservicegegevens voor uw apparaat niet opnieuw te verstrekken.

Als u wilt updaten naar een specifieke versie van de beveiligingsdaemon, zoekt u de versie die u wilt targeten vanuit [IoT Edge-releases.](https://github.com/Azure/azure-iotedge/releases) Download in die versie het bestand **Microsoft-Azure-IoTEdge.cab.** Gebruik vervolgens `-OfflineInstallationPath` de parameter om naar de lokale bestandslocatie te wijzen. Bijvoorbeeld:

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge -ContainerOs <Windows or Linux> -OfflineInstallationPath <absolute path to directory>
```

>[!NOTE]
>De `-OfflineInstallationPath` parameter zoekt naar een bestand met de naam **Microsoft-Azure-IoTEdge.cab** in de opgegeven map. Vanaf IoT Edge versie 1.0.9-rc4 zijn er twee .cab-bestanden beschikbaar voor gebruik, een voor AMD64-apparaten en een voor ARM32. Download het juiste bestand voor uw apparaat en wijzig vervolgens de naam van het bestand om het achtervoegsel van de architectuur te verwijderen.

Voor meer informatie over updateopties `Get-Help Update-IoTEdge -full` gebruikt u de opdracht of raadpleegt u [alle installatieparameters](how-to-install-iot-edge-windows.md#all-installation-parameters).

## <a name="update-the-runtime-containers"></a>De runtime-containers bijwerken

De manier waarop u de IoT Edge-agent en IoT Edge-hubcontainers bijwerkt, is afhankelijk van of u rollende tags (zoals 1.0) of specifieke tags (zoals 1.0.7) gebruikt bij uw implementatie.

Controleer de versie van de IoT Edge-agent en IoT Edge-hubmodules die momenteel op uw apparaat staan met behulp van de opdrachten `iotedge logs edgeAgent` of `iotedge logs edgeHub`.

  ![Containerversie zoeken in logboeken](./media/how-to-update-iot-edge/container-version.png)

### <a name="understand-iot-edge-tags"></a>IoT Edge-tags begrijpen

De IoT Edge-agent en IoT Edge-hubafbeeldingen worden getagd met de IoT Edge-versie waarmee ze zijn gekoppeld. Er zijn twee verschillende manieren om tags te gebruiken met de runtime-afbeeldingen:

* **Rolling tags** - Gebruik alleen de eerste twee waarden van het versienummer om de nieuwste afbeelding die overeenkomt met die cijfers te krijgen. Bijvoorbeeld, 1.0 wordt bijgewerkt wanneer er een nieuwe release te wijzen op de nieuwste 1.0.x versie. Als de runtime van de container op uw IoT Edge-apparaat de afbeelding opnieuw trekt, worden de runtime-modules bijgewerkt naar de nieuwste versie. Deze aanpak wordt voorgesteld voor ontwikkelingsdoeleinden. Implementaties van de StandaardAzure-portal naar rollende tags.

* **Specifieke tags** : gebruik alle drie de waarden van het versienummer om de afbeeldingsversie expliciet in te stellen. Bijvoorbeeld, 1.0.7 zal niet veranderen na de eerste release. U een nieuw versienummer declareren in het implementatiemanifest wanneer u klaar bent om te worden bijgewerkt. Deze aanpak wordt voorgesteld voor productiedoeleinden.

### <a name="update-a-rolling-tag-image"></a>Een afbeelding van een rollende tag bijwerken

Als u rollende tags gebruikt in uw implementatie (bijvoorbeeld mcr.microsoft.com/azureiotedge-hub:**1.0),** moet u de containerruntime op uw apparaat forceren om de nieuwste versie van de afbeelding te trekken.

Verwijder de lokale versie van de afbeelding van uw IoT Edge-apparaat. Op Windows-machines verwijdert het verwijderen van de beveiligingsdaemon ook de runtime-afbeeldingen, zodat u deze stap niet opnieuw hoeft te zetten.

```bash
docker rmi mcr.microsoft.com/azureiotedge-hub:1.0
docker rmi mcr.microsoft.com/azureiotedge-agent:1.0
```

Mogelijk moet u de `-f` krachtvlag gebruiken om de afbeeldingen te verwijderen.

De IoT Edge-service trekt de nieuwste versies van de runtime-afbeeldingen en start ze automatisch weer op uw apparaat.

### <a name="update-a-specific-tag-image"></a>Een specifieke tagafbeelding bijwerken

Als u specifieke tags in uw implementatie gebruikt (bijvoorbeeld mcr.microsoft.com/azureiotedge-hub:**1.0.8),** hoeft u alleen de tag in uw implementatiemanifest bij te werken en de wijzigingen op uw apparaat toe te passen.

1. Selecteer in de IoT-hub in de Azure-portal uw IoT Edge-apparaat en selecteer **Modules instellen.**

1. Selecteer **Runtime-instellingen**in de sectie **IoT Edge Modules** .

   ![Runtime-instellingen configureren](./media/how-to-update-iot-edge/configure-runtime.png)

1. Werk in **Runtime-instellingen**de **afbeeldingswaarde** voor **Edge Hub** bij met de gewenste versie. Selecteer **Opslaan** nog niet.

   ![Edge Hub Image-versie bijwerken](./media/how-to-update-iot-edge/runtime-settings-edgehub.png)

1. Vouw de **edgehub-instellingen** samen of schuif omlaag en werk de **afbeeldingswaarde** voor **Edge Agent** bij met dezelfde gewenste versie.

   ![Edge Hub Agent-versie bijwerken](./media/how-to-update-iot-edge/runtime-settings-edgeagent.png)

1. Selecteer **Opslaan**.

1. Selecteer **Controleren + maken,** controleer de implementatie en selecteer **Maken**.

## <a name="update-to-a-release-candidate-version"></a>Update naar een release candidate versie

Azure IoT Edge brengt regelmatig nieuwe versies van de IoT Edge-service uit. Vóór elke stabiele release, is er een of meer release candidate (RC) versies. RC-versies bevatten alle geplande functies voor de release, maar zijn nog steeds gaan door middel van testen en validatie. Als u een nieuwe functie vroeg wilt testen, u een RC-versie installeren en feedback geven via GitHub.

Release kandidaat versies volgen dezelfde nummering conventie van releases, maar hebben **-rc** plus een incrementeel aantal toegevoegd aan het einde. U de releasekandidaten zien in dezelfde lijst met [Azure IoT Edge-releases](https://github.com/Azure/azure-iotedge/releases) als de stabiele versies. Zoek bijvoorbeeld **1.0.7-rc1** en **1.0.7-rc2**, de twee release kandidaten die vóór **1.0.7**kwamen . U ook zien dat **RC-versies** zijn gemarkeerd met pre-release labels.

De IoT Edge-agent en hubmodules hebben RC-versies die zijn gelabeld met dezelfde conventie. bijvoorbeeld **mcr.microsoft.com/azureiotedge-hub:1.0.7-rc2**.

Als previews worden releasecandidate-versies niet opgenomen als de nieuwste versie die de reguliere installateurs targeten. In plaats daarvan moet u de assets handmatig targeten voor de RC-versie die u wilt testen. Voor het grootste deel, installeren of bijwerken naar een RC-versie is hetzelfde als het richten van een andere specifieke versie van IoT Edge, behalve voor een verschil voor Windows-apparaten. 

In een releasekandidaat kan het PowerShell-script waarmee u de IoT Edge-beveiligingsdaemon op een Windows-apparaat installeren en beheren, mogelijk andere functionaliteit hebben dan de nieuwste algemeen beschikbare versie. Naast het downloaden van het IoT Edge .cab-bestand voor de RC, download je ook het **IotEdgeSecurityDaemon.ps1-script.** Gebruik [puntsourcing](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_scripts?view=powershell-7#script-scope-and-dot-sourcing) om het gedownloade script in de huidige bron uit te voeren. Bijvoorbeeld: 

```powershell
. <path>\IoTEdgeSecurityDaemon.ps1
Update-IoTEdge -OfflineInstallationPath <path>
```

Gebruik de secties in dit artikel om te leren hoe u een IoT Edge-apparaat bijwerken naar een specifieke versie van de beveiligingsdaemon of runtime-modules.

Als u IoT Edge installeert op een nieuwe machine, gebruikt u de volgende koppelingen om te leren hoe u een specifieke versie installeert, afhankelijk van het besturingssysteem van uw apparaat:

* [Linux](how-to-install-iot-edge-linux.md#install-a-specific-runtime-version)
* [Windows](how-to-install-iot-edge-windows.md#offline-or-specific-version-installation)

## <a name="next-steps"></a>Volgende stappen

Bekijk de nieuwste [Azure IoT Edge-releases.](https://github.com/Azure/azure-iotedge/releases)

Blijf op de hoogte van recente updates en aankondigingen in het [Internet of Things-blog](https://azure.microsoft.com/blog/topics/internet-of-things/)
