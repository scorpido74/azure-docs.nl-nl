---
title: Versie van de update IoT Edge op apparaten - Azure IoT Edge | Microsoft Docs
description: Het bijwerken van IoT Edge-apparaten voor het uitvoeren van de meest recente versies van de security-daemon en de IoT Edge-runtime
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 02/07/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 4a7c27beeb7208efcf6687e49193c8d3b68f5300
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77186511"
---
# <a name="update-the-iot-edge-security-daemon-and-runtime"></a>De IoT Edge security-daemon en runtime bijwerken

Wanneer de IoT Edge-service nieuwe versies uitbrengt, moet u uw IoT Edge-apparaten bijwerken voor de nieuwste functies en beveiligings verbeteringen. In dit artikel bevat informatie over het bijwerken van uw IoT Edge-apparaten wanneer een nieuwe versie beschikbaar is.

Twee onderdelen van een IoT Edge-apparaat moeten worden bijgewerkt als u wilt verplaatsen naar een nieuwere versie. De eerste is de beveiligings-daemon die op het apparaat wordt uitgevoerd en de runtime modules start wanneer het apparaat wordt gestart. De daemon voor beveiliging op dit moment kan alleen van het apparaat zelf worden bijgewerkt. Het tweede onderdeel is de runtime, bestaande uit de modules IoT Edge hub en IoT Edge agent. Afhankelijk van hoe u uw implementatie structureren, kan de runtime van het apparaat of extern worden bijgewerkt.

Zie [Azure IOT Edge releases](https://github.com/Azure/azure-iotedge/releases)om de nieuwste versie van Azure IOT Edge te vinden.

## <a name="update-the-security-daemon"></a>Update de daemon voor beveiliging

De daemon van de beveiliging IoT Edge is een systeemeigen onderdeel dat moet worden bijgewerkt met behulp van de package manager op het IoT Edge-apparaat.

Controleer de versie van de beveiligings-daemon die op uw apparaat wordt uitgevoerd met behulp van de opdracht `iotedge version`.

### <a name="linux-devices"></a>Linux-apparaten

Op Linux x64-apparaten gebruikt u apt-get of uw geschikte pakket beheerder om de beveiligings-daemon bij te werken naar de meest recente versie.

```bash
apt-get update
apt-get install libiothsm iotedge
```

Als u wilt bijwerken naar een specifieke versie van de beveiligings-daemon, zoekt u de versie die u wilt richten op basis van [IOT Edge releases](https://github.com/Azure/azure-iotedge/releases). Zoek in die versie de juiste **libiothsm-standaard-** en **iotedge** -bestanden voor uw apparaat. Voor elk bestand klikt u met de rechter muisknop op de bestands koppeling en kopieert u het koppelings adres. Gebruik het koppelings adres om de specifieke versies van die onderdelen te installeren:

```bash
curl -L <libiothsm-std link> -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb
curl -L <iotedge link> -o iotedge.deb && sudo dpkg -i ./iotedge.deb
```

### <a name="windows-devices"></a>Windows-apparaten

Gebruik het Power shell-script voor het bijwerken van de beveiligings-daemon op Windows-apparaten. Het script haalt automatisch de nieuwste versie van de beveiligings-daemon op.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge -ContainerOs <Windows or Linux>
```

Als u de opdracht update-IoTEdge uitvoert, wordt de beveiligings-daemon van uw apparaat verwijderd en bijgewerkt, samen met de twee runtime container installatie kopieën. Het bestand config. yaml wordt op het apparaat bewaard, evenals gegevens van de Moby-container Engine (als u Windows-containers gebruikt). Het houden van de configuratie gegevens betekent dat u de gegevens van de connection string of de Device Provisioning Service voor uw apparaat niet opnieuw hoeft op te geven tijdens het update proces.

Als u wilt bijwerken naar een specifieke versie van de beveiligings-daemon, zoekt u de versie die u wilt richten op basis van [IOT Edge releases](https://github.com/Azure/azure-iotedge/releases). In die versie downloadt u het bestand **Microsoft-Azure-IoTEdge. cab** . Gebruik vervolgens de para meter `-OfflineInstallationPath` om naar de locatie van het lokale bestand te verwijzen. Bijvoorbeeld:

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge -ContainerOs <Windows or Linux> -OfflineInstallationPath <absolute path to directory>
```

>[!NOTE]
>De para meter `-OfflineInstallationPath` zoekt naar een bestand met de naam **Microsoft-Azure-IoTEdge. cab** in de opgegeven map. Vanaf IoT Edge versie 1.0.9-RC4 zijn er twee cab-bestanden beschikbaar voor gebruik, één voor AMD64-apparaten en één voor ARM32. Down load het juiste bestand voor uw apparaat en wijzig de naam van het bestand om het structuur achtervoegsel te verwijderen.

Voor meer informatie over Update opties gebruikt u de opdracht `Get-Help Update-IoTEdge -full` of raadpleegt u [alle installatie parameters](how-to-install-iot-edge-windows.md#all-installation-parameters).

## <a name="update-the-runtime-containers"></a>Bijwerken van de runtimecontainers

De manier waarop u de IoT Edge agent-en IoT Edge hub-containers bijwerkt, is afhankelijk van of u rollende Tags (zoals 1,0) of specifieke tags (zoals 1.0.7) gebruikt in uw implementatie.

Controleer met behulp van de opdrachten `iotedge logs edgeAgent` of `iotedge logs edgeHub`de versie van de IoT Edge agent en IoT Edge hub-modules op het apparaat.

  ![Containerversie niet vinden in Logboeken](./media/how-to-update-iot-edge/container-version.png)

### <a name="understand-iot-edge-tags"></a>Informatie over IoT Edge-tags

De installatie kopieën van IoT Edge agent en IoT Edge hub worden gelabeld met de IoT Edge-versie waaraan ze zijn gekoppeld. Er zijn twee verschillende manieren om labels te gebruiken met de runtime-installatiekopieën:

* **Roulerende Tags** : gebruik alleen de eerste twee waarden van het versie nummer om de meest recente afbeelding op te halen die overeenkomt met die cijfers. Bijvoorbeeld wordt 1.0 bijgewerkt wanneer er sprake is van een nieuwe versie om te verwijzen naar de nieuwste versie van de 1.0.x. Als de container-runtime op uw IoT Edge-apparaat opnieuw ophaalt van de installatiekopie, worden de runtimemodules bijgewerkt naar de nieuwste versie. Deze methode wordt aangeraden voor ontwikkelingsdoeleinden. Implementaties van de Azure portal standaardwaarde voor rolling tags.

* **Specifieke labels** : gebruik alle drie de waarden van het versie nummer om de installatie kopie versie expliciet in te stellen. 1\.0.7 wordt bijvoorbeeld niet gewijzigd na de eerste release. Wanneer u bent klaar om te werken, kunt u een nieuwe versienummer in het manifest implementatie declareren. Deze methode wordt aangeraden voor productiedoeleinden.

### <a name="update-a-rolling-tag-image"></a>Een rolling tag installatiekopie bijwerken

Als u roulerende Tags in uw implementatie gebruikt (bijvoorbeeld mcr.microsoft.com/azureiotedge-hub:**1,0**), moet u de container runtime op het apparaat afdwingen om de nieuwste versie van de installatie kopie te halen.

De lokale versie van de installatiekopie van het verwijderen van uw IoT Edge-apparaat. Op Windows-computers verwijdert het verwijderen van de Security daemon ook de runtime-installatie kopieën. u hoeft deze stap dus niet opnieuw uit te voeren.

```bash
docker rmi mcr.microsoft.com/azureiotedge-hub:1.0
docker rmi mcr.microsoft.com/azureiotedge-agent:1.0
```

Mogelijk moet u de vlag geforceerd `-f` gebruiken om de installatie kopieën te verwijderen.

De IoT Edge-service ophalen van de meest recente versies van de runtime-afbeeldingen en automatisch start deze op uw apparaat opnieuw.

### <a name="update-a-specific-tag-image"></a>De installatiekopie van een specifieke tag bijwerken

Als u specifieke tags in uw implementatie gebruikt (bijvoorbeeld mcr.microsoft.com/azureiotedge-hub:**1.0.8**), hoeft u alleen de tag in het implementatie manifest bij te werken en de wijzigingen toe te passen op uw apparaat.

1. Selecteer uw IoT Edge apparaat in de IoT Hub in het Azure Portal en selecteer **modules instellen**.

1. Selecteer in de sectie **IOT Edge modules** **runtime-instellingen**.

   ![Runtime-instellingen configureren](./media/how-to-update-iot-edge/configure-runtime.png)

1. Werk in **runtime-instellingen**de **afbeeldings** waarde voor **Edge hub** bij met de gewenste versie. Selecteer nog niets **Opslaan** .

   ![Versie van Edge hub-installatie kopie bijwerken](./media/how-to-update-iot-edge/runtime-settings-edgehub.png)

1. Vouw de **hub** -instellingen van de Edge samen of schuif omlaag en werk de **afbeeldings** waarde voor de **rand agent** bij met dezelfde versie.

   ![Versie van de Edge hub-agent bijwerken](./media/how-to-update-iot-edge/runtime-settings-edgeagent.png)

1. Selecteer **Opslaan**.

1. Selecteer **controleren + maken**, Controleer de implementatie en selecteer **maken**.

## <a name="update-to-a-release-candidate-version"></a>Bijwerken naar een release Candi date-versie

Azure IoT Edge brengt regel matig nieuwe versies van de IoT Edge service uit. Voor elke stabiele versie zijn er een of meer release Candi date (RC)-versies. RC-versies bevatten alle geplande functies voor de release, maar zijn nog steeds bezig met testen en valideren. Als u een nieuwe functie vroegtijdig wilt testen, kunt u een RC-versie installeren en feedback geven via GitHub.

Release Candi date-versies volgen dezelfde Nummerings Conventie voor releases, maar hebben wel **-RC** plus een incrementeel nummer toegevoegd aan het einde. U kunt de release kandidaten in dezelfde lijst met [Azure IOT Edge releases](https://github.com/Azure/azure-iotedge/releases) zien als de stabiele versies. Zoek bijvoorbeeld naar **1.0.7-RC1** en **1.0.7-RC2**, de twee release kandidaten die zijn ontvangen vóór **1.0.7**. U kunt ook zien dat RC-versies zijn gemarkeerd met **voorlopige** labels.

De IoT Edge-agent-en-hub-modules hebben RC-versies die zijn gelabeld met dezelfde Conventie. Bijvoorbeeld **MCR.Microsoft.com/azureiotedge-hub:1.0.7-RC2**.

Als previews worden versies van release Candi date niet opgenomen als de meest recente versie die de reguliere installatie Programma's doel. In plaats daarvan moet u hand matig de assets richten voor de RC-versie die u wilt testen. Het installeren of bijwerken van een RC-versie is in het algemeen hetzelfde als het richten op een andere specifieke versie van IoT Edge, met uitzonde ring van één verschil voor Windows-apparaten. 

In een release Candi date kan het Power shell-script waarmee u de IoT Edge beveiligings-daemon kunt installeren en beheren op een Windows-apparaat, verschillende functionaliteit hebben dan de meest recente algemeen beschik bare versie. Naast het downloaden van het bestand IoT Edge. cab voor de RC, down load ook het script **IotEdgeSecurityDaemon. ps1** . Gebruik [punt bronnen](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_scripts?view=powershell-7#script-scope-and-dot-sourcing) om het gedownloade script uit te voeren in de huidige bron. Bijvoorbeeld: 

```powershell
. <path>\IoTEdgeSecurityDaemon.ps1
Update-IoTEdge -OfflineInstallationPath <path>
```

Gebruik de secties in dit artikel voor meer informatie over het bijwerken van een IoT Edge apparaat naar een specifieke versie van de Security daemon of runtime-modules.

Als u IoT Edge installeert op een nieuwe computer, gebruikt u de volgende koppelingen om te leren hoe u een specifieke versie installeert, afhankelijk van het besturings systeem van uw apparaat:

* [Linux](how-to-install-iot-edge-linux.md#install-a-specific-runtime-version)
* [Windows](how-to-install-iot-edge-windows.md#offline-or-specific-version-installation)

## <a name="next-steps"></a>Volgende stappen

Bekijk de nieuwste [Azure IOT Edge releases](https://github.com/Azure/azure-iotedge/releases).

Blijf up-to-date met recente updates en aankondigingen in het [Internet of Things blog](https://azure.microsoft.com/blog/topics/internet-of-things/)
