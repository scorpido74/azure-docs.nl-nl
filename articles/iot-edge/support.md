---
title: Ondersteunde besturingssystemen, container-engines - Azure IoT Edge | Microsoft Docs
description: Informatie over welke besturingssystemen voor de Azure IoT Edge-daemon en de runtime en de ondersteunde container-engines voor uw productieapparaten kunt uitvoeren
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/13/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 0fe4a13a33b6d93266d68e632864e0b61a7eaf29
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2019
ms.locfileid: "74452525"
---
# <a name="azure-iot-edge-supported-systems"></a>Azure IoT Edge ondersteunde systemen

In dit artikel vindt u informatie over welke systemen en onderdelen worden ondersteund door IoT Edge, of dat nu officieel of in de preview-versie is. 

Als u problemen ondervindt bij het gebruik van de Azure IoT Edge-service, zijn er verschillende manieren om ondersteuning te zoeken. Probeer een van de volgende kanalen voor ondersteuning:

**Fouten rapporteren** : het meren deel van de ontwikkeling dat in het Azure IOT Edge product plaatsvindt, vindt plaats in het IOT Edge open-source project. U kunt fouten melden op de [pagina kwesties](https://github.com/azure/iotedge/issues) van het project. Oplossingen maken snel mogelijk naar productupdates van het project in.

**Klanten ondersteuning van micro soft** : gebruikers die een [ondersteunings plan](https://azure.microsoft.com/support/plans/) hebben, kunnen het micro soft Customer Support-team benaderen door rechtstreeks vanuit de [Azure Portal](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac)een ondersteunings ticket te maken.

**Functie aanvragen** – het Azure IOT Edge product traceert functie aanvragen via de [pagina gebruikers spraak](https://feedback.azure.com/forums/907045-azure-iot-edge)van het product.

## <a name="container-engines"></a>Container-engines

Azure IoT Edge-modules worden geïmplementeerd als containers, zodat IoT Edge een container Engine nodig hebt om deze te starten. Microsoft biedt een container-engine, moby-engine, om te voldoen aan deze vereiste. Deze container engine is gebaseerd op het open-source project Moby. Docker CE- en Docker EE zijn andere populaire container-engines. Ze zijn ook gebaseerd op het open-source project Moby en zijn compatibel met Azure IoT Edge. Micro soft biedt best mogelijke ondersteuning voor systemen die gebruikmaken van deze container motoren; Micro soft kan echter geen oplossingen verzenden voor problemen. Microsoft adviseert om deze reden moby-engine op productiesystemen.

<br>
<center>

![Moby als container runtime-](./media/support/only-moby-for-production.png)
</center>

## <a name="operating-systems"></a>Besturingssystemen
Azure IoT Edge wordt uitgevoerd op de meeste besturings systemen waarop containers kunnen worden uitgevoerd. al deze systemen worden echter niet gelijk ondersteund. Besturingssystemen zijn gegroepeerd in lagen, waarbij het niveau van ondersteuning voor gebruikers kunnen verwachten.
* Systemen met laag 1 worden ondersteund. Voor systemen met laag 1, micro soft:
    * heeft dit besturings systeem in geautomatiseerde tests
    * biedt de installatiepakketten van deze
* Systemen uit de laag 2 zijn compatibel met Azure IoT Edge en kunnen relatief eenvoudig worden gebruikt. Voor laag 2-systemen:
    * Micro soft heeft ad hoc tests uitgevoerd op de platforms of weet of een partner Azure IoT Edge op het platform heeft uitgevoerd
    * Installatiepakketten voor andere platforms werken op deze platforms
    
De familie van het hostbesturingssysteem moet altijd overeenkomen met de familie van het gast besturingssysteem dat in de container van een module wordt gebruikt. Met andere woorden, u kunt alleen Linux-containers gebruiken op Linux-en Windows-containers in Windows. Wanneer u Windows gebruikt, worden alleen geïsoleerde containers verwerken ondersteund, niet geïsoleerde Hyper-V-containers.  

<br>
<center>

![host-besturings systeem komt overeen met het gast besturingssysteem](./media/support/edge-on-device.png)
</center>

### <a name="tier-1"></a>Categorie 1

De systemen die worden vermeld in de volgende tabel worden ondersteund door micro soft, algemeen beschikbaar of in open bare preview, en worden getest met elke nieuwe release. 

| Besturingssysteem | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| Raspbian Stretch |  | ![Raspbian stretch + ARM32v7](./media/tutorial-c-module/green-check.png) |  |
| [Ubuntu-Server 16,04](https://wiki.ubuntu.com/XenialXerus/ReleaseNotes) | ![Ubuntu-Server 16,04 + AMD64](./media/tutorial-c-module/green-check.png) |  | Openbare preview  |
| [Ubuntu-Server 18,04](https://wiki.ubuntu.com/BionicBeaver/ReleaseNotes) | ![Ubuntu-Server 18,04 + AMD64](./media/tutorial-c-module/green-check.png) |  | Openbare preview |
| [Windows 10 IOT core](https://docs.microsoft.com/windows/iot-core/windows-iot-core), build 17763 | ![Windows IoT core + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Windows 10 IOT Enter prise](https://docs.microsoft.com/windows/iot-core/windows-iot-enterprise), build 17763 | ![Windows 10 IoT Enter prise + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Windows Server 2019](https://docs.microsoft.com/windows-server/get-started-19/rel-notes-19), build 17763 | ![Windows Server 2019 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Windows Server IoT 2019](https://docs.microsoft.com/windows/iot-core/windows-server), build 17763 | ![Windows Server IoT 2019 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |


De Windows-besturings systemen die hierboven worden vermeld, zijn de vereisten voor apparaten met Windows-containers in Windows. Dit is de enige ondersteunde configuratie voor productie. Met de Azure IoT Edge-installatie pakketten voor Windows is het gebruik van Linux-containers in Windows toegestaan. deze configuratie is echter alleen voor ontwikkelings-en test doeleinden. Zie [IOT Edge in Windows gebruiken om Linux-containers uit te voeren](how-to-install-iot-edge-windows-with-linux.md)voor meer informatie.

### <a name="tier-2"></a>Categorie 2

De systemen die in de volgende tabel worden vermeld, worden beschouwd als compatibel met Azure IoT Edge, maar worden niet actief getest of onderhouden door micro soft.

| Besturingssysteem | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| [CentOS 7,5](https://wiki.centos.org/Manuals/ReleaseNotes/CentOS7.1804) | ![CentOS + AMD64](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM32v7](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 8](https://www.debian.org/releases/jessie/) | ![Debian 8 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 8 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 8 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 9](https://www.debian.org/releases/stretch/) | ![Debian 9 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 9 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 9 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 10](https://www.debian.org/releases/buster/) <sup>1</sup> | ![Debian 10 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 10 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 10 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Inge sloten Linux Flex-besturings systeem voor docenten](https://www.mentor.com/embedded-software/linux/mel-flex-os/) | ![Mentor embedded Linux Flex OS + AMD64](./media/tutorial-c-module/green-check.png) | ![Met begeleiding embedded Linux Flex OS + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Met begeleiding embedded Linux Flex OS + ARM64](./media/tutorial-c-module/green-check.png) |
| [Het universeel-besturings systeem van mentor embedded Linux](https://www.mentor.com/embedded-software/linux/mel-omni-os/) | ![Mentor embedded Linux universeel OS + AMD64](./media/tutorial-c-module/green-check.png) |  | ![Mentor embedded Linux universeel OS + ARM64](./media/tutorial-c-module/green-check.png) |
| [RHEL 7,5](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/7.5_release_notes/index) | ![RHEL 7,5 + AMD64](./media/tutorial-c-module/green-check.png) | ![RHEL 7,5 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![RHEL 7,5 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 16,04](https://wiki.ubuntu.com/XenialXerus/ReleaseNotes) | ![Ubuntu 16,04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 16,04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 16,04 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 18,04](https://wiki.ubuntu.com/BionicBeaver/ReleaseNotes) | ![Ubuntu 18,04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18,04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18,04 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Wind stroom 8](https://docs.windriver.com/category/os-wind_river_linux) | ![Wind stroom 8 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Yocto](https://www.yoctoproject.org/) | ![Yocto + AMD64](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM64](./media/tutorial-c-module/green-check.png) |
| Raspbian Buster <sup>1</sup> |  | ![Raspbian Buster + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Raspbian Buster + ARM64](./media/tutorial-c-module/green-check.png) |

<sup>1</sup> Debian 10-systemen, met inbegrip van Raspian Buster, gebruiken een versie van OpenSSL die IOT Edge niet ondersteunt. Gebruik de volgende opdracht om een eerdere versie te installeren voordat u IoT Edge installeert: 

```bash
sudo apt-get install libssl1.0.2
```

## <a name="virtual-machines"></a>Virtuele machines
Azure IoT Edge kunnen worden uitgevoerd op virtuele machines. Het gebruik van een virtuele machine als een IoT Edge apparaat is gebruikelijk wanneer klanten bestaande infra structuur willen uitbreiden met Edge Intelligence. De serie van het VM-besturings systeem van de host moet overeenkomen met de familie van het gast besturingssysteem dat in de container van een module wordt gebruikt. Deze vereiste is hetzelfde als wanneer Azure IoT Edge rechtstreeks op een apparaat wordt uitgevoerd. Azure IoT Edge is neutraal van de onderliggende technologie en werkt in Vm's op basis van platforms, zoals Hyper-V en vSphere.

<br>
<center>

![Azure IoT Edge in een VM](./media/support/edge-on-vm.png)
</center>

## <a name="minimum-system-requirements"></a>Minimale systeem vereisten
Azure IoT Edge is uitstekend geschikt voor apparaten met een Raspberry-Pi3 op server kwaliteit. Het kiezen van de juiste hardware voor uw scenario is afhankelijk van de werk belastingen die u wilt uitvoeren. Het maken van de definitieve beslissing van het apparaat kan gecompliceerd zijn. u kunt echter eenvoudig beginnen met het prototypen van een oplossing op traditionele laptops of Bureau bladen.

Ervaring tijdens het prototypen helpt bij het selecteren van de uiteindelijke selectie van apparaten. Vragen die u moet overwegen: 

* Hoeveel modules bevinden zich in uw werk belasting?
* Hoeveel lagen delen de containers van de modules?
* In welke taal zijn uw modules geschreven? 
* Hoeveel gegevens worden er door uw modules verwerkt?
* Hebben uw modules gespecialiseerde hardware nodig om de werk belastingen te versnellen?
* Wat zijn de gewenste prestatie kenmerken van uw oplossing?
* Wat is uw hardware-budget?
