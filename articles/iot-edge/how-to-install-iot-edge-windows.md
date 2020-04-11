---
title: Azure IoT Edge installeren in Windows | Microsoft Documenten
description: Installatie-instructies voor Azure IoT Edge op Windows 10, Windows Server en Windows IoT Core
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: kgremban
ms.openlocfilehash: 61b382f1c286209a12d0be39a81e6817806d3251
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/10/2020
ms.locfileid: "81113465"
---
# <a name="install-the-azure-iot-edge-runtime-on-windows"></a>De Azure IoT Edge-runtime op Windows installeren

De Azure IoT Edge-runtime maakt van een apparaat een IoT Edge-apparaat. De runtime kan worden geïmplementeerd op apparaten zo klein als een Raspberry Pi of zo groot als een industriële server. Zodra een apparaat is geconfigureerd met de IoT Edge-runtime, u bedrijfslogica vanuit de cloud implementeren.

Zie [De runtime van Azure IoT Edge en de architectuur ervan begrijpen](iot-edge-runtime.md)voor meer informatie over de runtime van IoT Edge.

In dit artikel worden de stappen weergegeven om de Azure IoT Edge-runtime op uw Windows x64-systeem (AMD/Intel) te installeren met Windows-containers.

> [!NOTE]
> Een bekend probleem met het Windows-besturingssysteem voorkomt de overgang naar slaap- en sluimerstanden wanneer IoT Edge-modules (procesgeïsoleerde Windows Nano Server-containers) worden uitgevoerd. Dit probleem heeft gevolgen voor de levensduur van de batterij van het apparaat.
>
> Als tijdelijke oplossing gebruikt `Stop-Service iotedge` u de opdracht om het uitvoeren van IoT Edge-modules te stoppen voordat u deze energietoestanden gebruikt.

Het gebruik van Linux-containers op Windows-systemen is geen aanbevolen of ondersteunde productieconfiguratie voor Azure IoT Edge. Het kan echter worden gebruikt voor ontwikkelings- en testdoeleinden. Zie [IoT Edge in Windows gebruiken om Linux-containers uit te voeren voor](how-to-install-iot-edge-windows-with-linux.md)meer informatie.

Zie [Azure IoT Edge-releases](https://github.com/Azure/azure-iotedge/releases)voor informatie over wat er is opgenomen in de nieuwste versie van IoT Edge.

## <a name="prerequisites"></a>Vereisten

Gebruik deze sectie om te controleren of uw Windows-apparaat IoT Edge kan ondersteunen en om het voor te bereiden op een containerengine voordat het wordt geïnstalleerd.

### <a name="supported-windows-versions"></a>Ondersteunde Windows-versies

IoT Edge voor Windows vereist Windows-versie 1809/build 17763, dat is de nieuwste [Windows-ondersteuning op lange termijn te bouwen.](https://docs.microsoft.com/windows/release-information/) Bekijk voor Windows SKU-ondersteuning wat wordt ondersteund op basis van de vraag of u zich voorbereidt op productiescenario's of ontwikkelings- en testscenario's:

* **Productie:** Zie [Azure IoT Edge-ondersteunde systemen](support.md#operating-systems)voor de meest recente informatie over welke besturingssystemen momenteel worden ondersteund voor productiescenario's.
* **Ontwikkeling en test:** Voor ontwikkel- en testscenario's kan Azure IoT Edge met Windows-containers worden geïnstalleerd op elke versie van Windows 10 of Windows Server 2019 die de functie containers ondersteunt.

IoT Core-apparaten moeten de optionele functie IoT Core Windows Containers bevatten om de IoT Edge-runtime te ondersteunen. Gebruik de volgende opdracht in een [externe PowerShell-sessie](https://docs.microsoft.com/windows/iot-core/connect-your-device/powershell) om te controleren of Windows-containers op uw apparaat worden ondersteund:

```powershell
Get-Service vmcompute
```

Als de service aanwezig is, moet u een succesvol antwoord krijgen met de servicestatus die wordt vermeld als **actief**. Als `vmcompute` de service niet wordt gevonden, voldoet uw apparaat niet aan de vereisten voor IoT Edge. Neem contact op met uw hardwareprovider om te vragen naar ondersteuning voor deze functie.

### <a name="prepare-for-a-container-engine"></a>Voorbereiden op een containermotor

Azure IoT Edge is gebaseerd op een [OCI-compatibele](https://www.opencontainers.org/) containerengine. Gebruik voor productiescenario's de Moby-engine die in het installatiescript is opgenomen, om Windows-containers op uw Windows-apparaat uit te voeren.

## <a name="install-iot-edge-on-a-new-device"></a>IoT Edge installeren op een nieuw apparaat

>[!NOTE]
>Azure IoT Edge-softwarepakketten zijn onderworpen aan de licentievoorwaarden in de pakketten (in de licentiemap). Lees de licentievoorwaarden voordat u het pakket gebruikt. Uw installatie en gebruik van het pakket vormt uw aanvaarding van deze voorwaarden. Als u het niet eens bent met de licentievoorwaarden, gebruik dan het pakket niet.

Een PowerShell-script downloadt en installeert de Azure IoT Edge-beveiligingsdaemon. De security daemon start vervolgens de eerste van twee runtime modules, de IoT Edge-agent, die externe implementaties van andere modules mogelijk maakt.

>[!TIP]
>Voor IoT Core-apparaten raden we u aan de installatieopdrachten uit te voeren met behulp van een RemotePowerShell-sessie. Zie [PowerShell voor Windows IoT gebruiken voor](https://docs.microsoft.com/windows/iot-core/connect-your-device/powershell)meer informatie.

Wanneer u de IoT Edge-runtime voor de eerste keer op een apparaat installeert, moet u het apparaat voorzien van een identiteit van een IoT-hub. Eén IoT Edge-apparaat kan handmatig worden ingericht met behulp van een apparaatverbindingstekenreeks die wordt geleverd door IoT Hub. U de DpS (Device Provisioning Service) ook gebruiken om apparaten automatisch in te richten, wat handig is wanneer u veel apparaten moet instellen. Afhankelijk van uw inrichtende keuze kiest u het juiste installatiescript.

In de volgende secties worden de algemene use cases en parameters voor het IoT Edge-installatiescript op een nieuw apparaat beschreven.

### <a name="option-1-install-and-manually-provision"></a>Optie 1: Installeren en handmatig inrichten

In deze eerste optie biedt u een **apparaatverbindingstekenreeks** die door IoT Hub wordt gegenereerd om het apparaat in te richten.

In dit voorbeeld wordt een handmatige installatie met Windows-containers aangetoond:

1. Als u dit nog niet hebt gedaan, registreert u een nieuw IoT Edge-apparaat en haalt u de **tekenreeks voor apparaatverbinding**op. Kopieer de verbindingstekenreeks die u later in deze sectie wilt gebruiken. U deze stap voltooien met de volgende tools:

   * [Azure Portal](how-to-register-device.md#register-in-the-azure-portal)
   * [Azure-CLI](how-to-register-device.md#register-with-the-azure-cli)
   * [Visual Studio Code](how-to-register-device.md#register-with-visual-studio-code)

2. Voer PowerShell uit als beheerder.

   >[!NOTE]
   >Gebruik een AMD64-sessie van PowerShell om IoT Edge te installeren, niet PowerShell (x86). Als u niet zeker weet welk sessietype u gebruikt, voert u de volgende opdracht uit:
   >
   >```powershell
   >(Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   >```

3. De opdracht **Deploy-IoTEdge** controleert of uw Windows-machine een ondersteunde versie heeft, schakelt de functie containers in en downloadt vervolgens de moby-runtime en de IoT Edge-runtime. De opdracht is standaard ingesteld op het gebruik van Windows-containers.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

4. Op dit moment kunnen IoT Core-apparaten automatisch opnieuw worden opgestart. Andere Windows 10- of Windows Server-apparaten kunnen u vragen om opnieuw op te starten. Start het apparaat nu opnieuw op. Zodra uw apparaat klaar is, voert u PowerShell opnieuw uit als beheerder.

5. Met de opdracht **Initialize-IoTEdge** configureert u de runtime van IoT Edge op uw machine. De opdracht is standaard ingesteld op handmatiginrichten met Windows-containers.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge
   ```

6. Geef de tekenreeks voor de verbinding van het apparaat op die u in stap 1 hebt opgehaald. De tekenreeks voor apparaatverbinding koppelt het fysieke apparaat aan een apparaat-id in IoT Hub.

   De tekenreeks voor apparaatverbinding neemt de volgende indeling en mag geen aanhalingstekens bevatten:`HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

7. Gebruik de stappen in [Geslaagde installatie verifiëren](#verify-successful-installation) om de status van IoT Edge op uw apparaat te controleren.

Wanneer u een apparaat handmatig installeert en indient, u aanvullende parameters gebruiken om de installatie te wijzigen, waaronder:

* Direct verkeer om via een proxyserver te gaan
* Het installatieprogramma naar een offlinemap wijzen
* Een specifieke agentcontainerafbeelding declareren en referenties verstrekken als deze zich in een privéregister bevindt

Voor meer informatie over deze installatieopties u verder op de hoogte blijven van [alle installatieparameters.](#all-installation-parameters)

### <a name="option-2-install-and-automatically-provision"></a>Optie 2: Installeren en automatisch inrichten

In deze tweede optie indient u het apparaat in met behulp van de IoT Hub Device Provisioning Service. Geef de **scope-id** van een apparaatinrichtingsservice-instantie samen met alle andere informatie die specifiek is voor uw [voorkeursattestmechanisme:](../iot-dps/concepts-security.md#attestation-mechanism)

* [Een gesimuleerd IoT Edge-apparaat maken en inrichten met een virtuele TPM op Windows](how-to-auto-provision-simulated-device-windows.md)
* [Een gesimuleerd IoT Edge-apparaat maken en inrichten met X.509-certificaten](how-to-auto-provision-x509-certs.md)
* [Een IoT Edge-apparaat maken en inrichten met behulp van symmetrische sleutelattest](how-to-auto-provision-symmetric-keys.md)

Wanneer u een apparaat automatisch installeert en instelt, u aanvullende parameters gebruiken om de installatie te wijzigen, waaronder:

* Direct verkeer om via een proxyserver te gaan
* Het installatieprogramma naar een offlinemap wijzen
* Een specifieke agentcontainerafbeelding declareren en referenties verstrekken als deze zich in een privéregister bevindt

Voor meer informatie over deze installatieopties, lees verder dit artikel of overslaan om meer te weten te komen over [alle installatieparameters](#all-installation-parameters).

## <a name="offline-or-specific-version-installation"></a>Offline of specifieke versie-installatie

Tijdens de installatie worden drie bestanden gedownload:

* Een PowerShell-script, dat de installatie-instructies bevat
* Microsoft Azure IoT Edge-cabine, die de IoT Edge-beveiligingsdaemon (iotedged), Moby-containerengine en Moby CLI bevat
* Visual C++ redistributable package (VC runtime) installer

Als uw apparaat offline is tijdens de installatie of als u een specifieke versie van IoT Edge wilt installeren, u deze bestanden van tevoren downloaden naar het apparaat. Wanneer het tijd is om te installeren, richt u het installatiescript op de map met de gedownloade bestanden. De installatieprogramma controleert die map eerst en downloadt vervolgens alleen onderdelen die niet worden gevonden. Als alle bestanden offline beschikbaar zijn, u installeren zonder internetverbinding.

U ook de parameter offline installatiepad gebruiken om IoT Edge bij te werken. Zie [De IoT Edge-beveiligingsdaemon en runtime bijwerken](how-to-update-iot-edge.md)voor meer informatie.

1. Zie [Azure IoT Edge-releases](https://github.com/Azure/azure-iotedge/releases)voor de nieuwste IoT Edge-installatiebestanden en eerdere versies.

2. Zoek de versie die u wilt installeren en download de volgende bestanden uit het gedeelte **Assets** van de releasenotes op uw IoT-apparaat:

   * IoTEdgeSecurityDaemon.ps1
   * Microsoft-Azure-IoTEdge-amd64.cab van releases 1.0.9 of nieuwer, of Microsoft-Azure-IoTEdge.cab van releases 1.0.8 en ouder.

   Microsoft-Azure-IotEdge-arm32.cab is ook beschikbaar vanaf 1.0.9 alleen voor testdoeleinden. IoT Edge wordt momenteel niet ondersteund op Windows ARM32-apparaten.

   Het is belangrijk om het PowerShell-script te gebruiken vanaf dezelfde release als het .cab-bestand dat u gebruikt, omdat de functionaliteit verandert om de functies in elke release te ondersteunen.

3. Als het .cab-bestand dat u hebt gedownload een architectuurachtervoegsel bevat, wijzigt u de naam van het bestand in alleen **Microsoft-Azure-IoTEdge.cab**.

4. Download eventueel een installer voor Visual C++ redistributable. Het PowerShell-script gebruikt bijvoorbeeld deze versie: [vc_redist.x64.exe](https://download.microsoft.com/download/0/6/4/064F84EA-D1DB-4EAA-9A5C-CC2F0FF6A638/vc_redist.x64.exe). Sla het installatieprogramma op in dezelfde map op uw IoT-apparaat als de IoT Edge-bestanden.

5. Als u met offline componenten wilt installeren, [plaatst u](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_scripts?view=powershell-7#script-scope-and-dot-sourcing) de lokale kopie van het PowerShell-script. Gebruik vervolgens `-OfflineInstallationPath` de parameter als `Deploy-IoTEdge` onderdeel van de opdracht en geef het absolute pad naar de bestandsmap op. Bijvoorbeeld:

   ```powershell
   . <path>\IoTEdgeSecurityDaemon.ps1
   Deploy-IoTEdge -OfflineInstallationPath <path>
   ```

   De opdracht implementatie gebruikt alle onderdelen die in de lokale bestandsmap worden gevonden. Als het .cab-bestand of het Visual C++-installatieprogramma ontbreekt, wordt geprobeerd deze te downloaden.

6. Voer `Initialize-IoTEdge` de opdracht uit om uw apparaat in te richten met een identiteit in IoT Hub. Geef een apparaatverbindingstekenreeks op voor handmatige inrichting of kies een van de methoden die in de vorige [sectie automatisch instelwerk](#option-2-install-and-automatically-provision) zijn beschreven.

   Als uw apparaat opnieuw `Deploy-IoTEdge`is opgestart nadat het is `Initialize-IoTEdge`uitgevoerd, wordt het PowerShell-script opnieuw gebron voordat het wordt uitgevoerd .

Ga vooruit voor meer informatie over de optie offline installatie om meer te weten te komen over [alle installatieparameters.](#all-installation-parameters)

## <a name="verify-successful-installation"></a>Geslaagde installatie verifiëren

Controleer de status van de IoT Edge-service. Het moet worden vermeld als uitgevoerd.  

```powershell
Get-Service iotedge
```

Bestudeer servicelogs van de laatste 5 minuten. Als u net klaar bent met het installeren van de Runtime van IoT Edge, ziet u mogelijk een lijst met fouten uit de tijd tussen het uitvoeren van **Deploy-IoTEdge** en **Initialize-IoTEdge.** Deze fouten worden verwacht, omdat de service probeert te starten voordat deze wordt geconfigureerd.

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Voer een geautomatiseerde controle uit op de meest voorkomende configuratie- en netwerkfouten.

```powershell
iotedge check
```

Lijst met lopende modules. Na een nieuwe installatie is **edgeAgent**de enige module die u moet zien uitvoeren. Nadat u [IoT Edge-modules](how-to-deploy-modules-portal.md) voor de eerste keer hebt geïmplementeerd, wordt de andere systeemmodule, **edgeHub,** ook op het apparaat gestart.

```powershell
iotedge list
```

## <a name="manage-module-containers"></a>Modulecontainers beheren

De IoT Edge-service vereist een containerengine die op uw apparaat draait. Wanneer u een module op een apparaat implementeert, gebruikt de Runtime van IoT Edge de containerengine om de containerafbeelding uit een register in de cloud te halen. Met de IoT Edge-service u communiceren met uw modules en logboeken ophalen, maar soms wilt u de containerengine gebruiken om met de container zelf te communiceren.

Zie [Azure IoT Edge-modules begrijpen](iot-edge-modules.md)voor meer informatie over moduleconcepten.

Als u Windows-containers op uw Windows IoT Edge-apparaat gebruikt, is de Installatie van IoT Edge inclusief de Moby-containerengine. De Moby-motor was gebaseerd op dezelfde normen als Docker en is ontworpen om parallel te draaien op dezelfde machine als Docker Desktop. Om die reden, als u containers wilt targeten die door de Moby-motor worden beheerd, moet u zich specifiek richten op die motor in plaats van Docker.

Als u bijvoorbeeld alle Docker-afbeeldingen wilt weergeven, gebruikt u de volgende opdracht:

```powershell
docker images
```

Als u alle Moby-afbeeldingen wilt weergeven, wijzigt u dezelfde opdracht met een aanwijzer naar de Moby-engine:

```powershell
docker -H npipe:////./pipe/iotedge_moby_engine images
```

De engine URI wordt vermeld in de uitvoer van het installatiescript, of u het vinden in de sectie containerruntime-instellingen voor het config.yaml-bestand.

![moby_runtime uri in config.yaml](./media/how-to-install-iot-edge-windows/moby-runtime-uri.png)

Zie [Docker command-line interfaces](https://docs.docker.com/engine/reference/commandline/docker/)voor meer informatie over opdrachten die u gebruiken voor interactie met containers en afbeeldingen die op uw apparaat worden uitgevoerd.

## <a name="uninstall-iot-edge"></a>IoT-rand verwijderen

Als u de IoT Edge-installatie van uw Windows-apparaat wilt verwijderen, gebruikt u de volgende opdracht vanuit een administratief PowerShell-venster. Met deze opdracht wordt de Runtime van IoT Edge verwijderd, samen met uw bestaande configuratie en de Moby-enginegegevens.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Uninstall-IoTEdge
```

De opdracht Verwijderen-IoTEdge werkt niet op Windows IoT Core. Als u IoT Edge wilt verwijderen uit Windows IoT Core-apparaten, moet u uw Windows IoT Core-afbeelding opnieuw implementeren.

Gebruik de opdracht `Get-Help Uninstall-IoTEdge -full`voor meer informatie over de installatieopties.

## <a name="verify-installation-script"></a>Installatiescript verifiëren

De installatieopdrachten in dit artikel gebruiken de cmdlet Invoke-WebRequest `aka.ms/iotedge-win`om het installatiescript op te vragen bij . Deze koppeling verwijst`IoTEdgeSecurityDaemon.ps1` naar het script van de meest recente [IoT Edge-release.](https://github.com/Azure/azure-iotedge/releases) U dit script, of een versie van het script van een specifieke release, ook downloaden om de installatieopdrachten op uw IoT Edge-apparaat uit te voeren.

Het opgegeven script is ondertekend om de beveiliging te verhogen. U de handtekening verifiëren door het script naar uw apparaat te downloaden en vervolgens de volgende PowerShell-opdracht uit te voeren:

```powershell
Get-AuthenticodeSignature "C:\<path>\IotEdgeSecurityDaemon.ps1"
```

De uitvoerstatus is **geldig** als de handtekening is geverifieerd.

## <a name="all-installation-parameters"></a>Alle installatieparameters

In de vorige secties werden veelvoorkomende installatiescenario's geïntroduceerd met voorbeelden van het gebruik van parameters om het installatiescript te wijzigen. In deze sectie vindt u referentietabellen van de algemene parameters die worden gebruikt voor het installeren, bijwerken of verwijderen van IoT Edge.

### <a name="deploy-iotedge"></a>Deploy-IoTEdge

De opdracht Deploy-IoTEdge downloadt en implementeert de IoT Edge Security Daemon en de afhankelijkheden daarvan. De implementatieopdracht accepteert onder andere deze algemene parameters. Gebruik de opdracht `Get-Help Deploy-IoTEdge -full`voor de volledige lijst .  

| Parameter | Geaccepteerde waarden | Opmerkingen |
| --------- | --------------- | -------- |
| **ContainerOs** | **Windows** of **Linux** | Als er geen containerbesturingssysteem is opgegeven, is Windows de standaardwaarde.<br><br>Voor Windows-containers gebruikt IoT Edge de moby-containerengine die in de installatie is opgenomen. Voor Linux-containers moet u een containerengine installeren voordat u met de installatie begint. |
| **Proxy** | Proxy-URL | Neem deze parameter op als uw apparaat via een proxyserver moet gaan om het internet te bereiken. Zie [Een IoT Edge-apparaat configureren om te communiceren via een proxyserver](how-to-configure-proxy-support.md)voor meer informatie. |
| **OfflineInstallatiepad** | Mappad | Als deze parameter is opgenomen, controleert het installatieprogramma de vermelde map voor de IoT Edge-cabine- en VC Runtime MSI-bestanden die nodig zijn voor installatie. Alle bestanden die niet in de directory worden gevonden, worden gedownload. Als beide bestanden zich in de map bevinden, u IoT Edge installeren zonder internetverbinding. U deze parameter ook gebruiken om een specifieke versie te gebruiken. |
| **InvokeWebRequestParameters** | Hashtable met parameters en waarden | Tijdens de installatie worden verschillende webverzoeken gedaan. Gebruik dit veld om parameters in te stellen voor die webaanvragen. Deze parameter is handig om referenties voor proxyservers te configureren. Zie [Een IoT Edge-apparaat configureren om te communiceren via een proxyserver](how-to-configure-proxy-support.md)voor meer informatie. |
| **Opnieuw opstartenindien nodig** | geen | Met deze vlag kan het implementatiescript de machine opnieuw opstarten zonder te vragen, indien nodig. |

### <a name="initialize-iotedge"></a>Initialiseren-IoTEdge

Met de opdracht Initialize-IoTEdge configureert u IoT Edge met de tekenreeks van uw apparaatverbinding en operationele details. Veel van de informatie gegenereerd door deze opdracht wordt vervolgens opgeslagen in de iotedge \config.yaml bestand. De opdracht initialisatie accepteert onder andere deze algemene parameters. Gebruik de opdracht `Get-Help Initialize-IoTEdge -full`voor de volledige lijst .

| Parameter | Geaccepteerde waarden | Opmerkingen |
| --------- | --------------- | -------- |
| **Handmatig** | Geen | **De parameter Switch**. Als er geen inrichtingstype is opgegeven, is handmatig de standaardwaarde.<br><br>Hiermee verklaart u een apparaatverbindingstekenreeks om het apparaat handmatig in te richten |
| **Dps** | Geen | **De parameter Switch**. Als er geen inrichtingstype is opgegeven, is handmatig de standaardwaarde.<br><br>Hiermee verklaart u een DPS-scope-id (Device Provisioning Service) en de registratie-id van uw apparaat te verstrekken aan de voorziening via DPS.  |
| **DeviceConnectionString** | Een verbindingstekenreeks van een IoT Edge-apparaat dat is geregistreerd in een IoT-hub, in afzonderlijke aanhalingstekens | **Vereist** voor handmatige inrichting. Als u geen verbindingstekenreeks in de scriptparameters opgeeft, wordt u daarvoor gevraagd. |
| **ScopeId ScopeId** | Een scope-id van een instantie van apparaatinrichtingsservice die is gekoppeld aan uw IoT-hub. | **Vereist** voor DPS-provisioning. Als u geen scope-id in de scriptparameters opgeeft, wordt u daarvoor gevraagd. |
| **RegistratieId** | Een registratie-id gegenereerd door uw apparaat | **Vereist** voor DPS-inrichting bij gebruik van TPM of symmetrische sleutelattest. **Optioneel** als u x.509-certificaatattest gebruikt. |
| **X509Identiteitscertificaat** | Het URI-pad naar het identiteitscertificaat van het X.509-apparaat op het apparaat. | **Vereist** voor DPS-provisioning bij gebruik van X.509-certificaatattest. |
| **X509IdentityPrivateKey** | Het URI-pad naar de identiteitscertificaatsleutel van het X.509-apparaat op het apparaat. | **Vereist** voor DPS-provisioning bij gebruik van X.509-certificaatattest. |
| **SymmetrischSleutel** | De symmetrische sleutel die wordt gebruikt om de identiteit van het IoT Edge-apparaat in te richten bij het gebruik van DPS | **Vereist** voor DPS-inrichting bij gebruik van symmetrische sleutelattest. |
| **ContainerOs** | **Windows** of **Linux** | Als er geen containerbesturingssysteem is opgegeven, is Windows de standaardwaarde.<br><br>Voor Windows-containers gebruikt IoT Edge de moby-containerengine die in de installatie is opgenomen. Voor Linux-containers moet u een containerengine installeren voordat u met de installatie begint. |
| **InvokeWebRequestParameters** | Hashtable met parameters en waarden | Tijdens de installatie worden verschillende webverzoeken gedaan. Gebruik dit veld om parameters in te stellen voor die webaanvragen. Deze parameter is handig om referenties voor proxyservers te configureren. Zie [Een IoT Edge-apparaat configureren om te communiceren via een proxyserver](how-to-configure-proxy-support.md)voor meer informatie. |
| **AgentAfbeelding** | Afbeelding URI van iotedge-agent | Standaard gebruikt een nieuwe IoT Edge-installatie de nieuwste rolling tag voor de IoT Edge-agentafbeelding. Gebruik deze parameter om een specifieke tag in te stellen voor de afbeeldingsversie of om uw eigen agentafbeelding op te geven. Zie [IoT Edge-tags begrijpen](how-to-update-iot-edge.md#understand-iot-edge-tags)voor meer informatie . |
| **Gebruikersnaam** | Gebruikersnaam containerregister | Gebruik deze parameter alleen als u de parameter -AgentImage instelt op een container in een privéregister. Geef een gebruikersnaam met toegang tot het register. |
| **Wachtwoord** | Beveiligde wachtwoordtekenreeks | Gebruik deze parameter alleen als u de parameter -AgentImage instelt op een container in een privéregister. Geef het wachtwoord op om toegang te krijgen tot het register. |

### <a name="update-iotedge"></a>Update-IoTEdge

| Parameter | Geaccepteerde waarden | Opmerkingen |
| --------- | --------------- | -------- |
| **ContainerOs** | **Windows** of **Linux** | Als er geen containerbesturingssysteem is opgegeven, is Windows de standaardwaarde. Voor Windows-containers wordt een containermotor in de installatie opgenomen. Voor Linux-containers moet u een containerengine installeren voordat u met de installatie begint. |
| **Proxy** | Proxy-URL | Neem deze parameter op als uw apparaat via een proxyserver moet gaan om het internet te bereiken. Zie [Een IoT Edge-apparaat configureren om te communiceren via een proxyserver](how-to-configure-proxy-support.md)voor meer informatie. |
| **InvokeWebRequestParameters** | Hashtable met parameters en waarden | Tijdens de installatie worden verschillende webverzoeken gedaan. Gebruik dit veld om parameters in te stellen voor die webaanvragen. Deze parameter is handig om referenties voor proxyservers te configureren. Zie [Een IoT Edge-apparaat configureren om te communiceren via een proxyserver](how-to-configure-proxy-support.md)voor meer informatie. |
| **OfflineInstallatiepad** | Mappad | Als deze parameter is opgenomen, controleert het installatieprogramma de vermelde map voor de IoT Edge-cabine- en VC Runtime MSI-bestanden die nodig zijn voor installatie. Alle bestanden die niet in de directory worden gevonden, worden gedownload. Als beide bestanden zich in de map bevinden, u IoT Edge installeren zonder internetverbinding. U deze parameter ook gebruiken om een specifieke versie te gebruiken. |
| **Opnieuw opstartenindien nodig** | geen | Met deze vlag kan het implementatiescript de machine opnieuw opstarten zonder te vragen, indien nodig. |

### <a name="uninstall-iotedge"></a>Verwijderen-IoTEdge

| Parameter | Geaccepteerde waarden | Opmerkingen |
| --------- | --------------- | -------- |
| **Kracht** | geen | Deze vlag dwingt de niet-installatie in het geval de vorige poging om te verwijderen was mislukt.
| **Opnieuw opstartenindien nodig** | geen | Met deze vlag kan het script verwijderen de machine opnieuw opstarten zonder te vragen, indien nodig. |

## <a name="next-steps"></a>Volgende stappen

Nu u een IoT Edge-apparaat hebt ingericht met de runtime geïnstalleerd, u [IoT Edge-modules implementeren.](how-to-deploy-modules-portal.md)

Als u problemen ondervindt bij het correct installeren van IoT Edge, raadpleegt u de [pagina voor het oplossen van problemen.](troubleshoot.md)

Zie [De IoT Edge-beveiligingsdaemon en runtime bijwerken](how-to-update-iot-edge.md)als u een bestaande installatie wilt bijwerken naar de nieuwste versie van IoT Edge.
