---
title: Ondersteunde besturingssystemen, containerengines - Azure IoT Edge
description: Ontdek welke besturingssystemen de Azure IoT Edge-daemon en runtime kunnen uitvoeren en ondersteunde containerengines voor uw productieapparaten
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/06/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 42c0a5d0c590f8c395c2afe366a00fcb9c83ce46
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79536935"
---
# <a name="azure-iot-edge-supported-systems"></a>Azure IoT Edge ondersteunde systemen

In dit artikel vindt u informatie over welke systemen en componenten worden ondersteund door IoT Edge, officieel of in preview.

Als u problemen ondervindt tijdens het gebruik van de Azure IoT Edge-service, zijn er verschillende manieren om ondersteuning te zoeken. Probeer een van de volgende kanalen voor ondersteuning:

**Bugs rapporteren** - De meeste ontwikkeling die in het Azure IoT Edge-product gaat, vindt plaats in het Open Source-project van IoT Edge. Bugs kunnen worden gemeld op de [uitgiftepagina](https://github.com/azure/iotedge/issues) van het project. Fixes snel hun weg van het project in om product updates.

**Microsoft Customer Support-team** : gebruikers met een [ondersteuningsplan](https://azure.microsoft.com/support/plans/) kunnen het Microsoft Customer Support-team inschakelen door rechtstreeks vanuit de [Azure-portal](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac)een ondersteuningsticket te maken.

**Functieaanvragen** – De Azure IoT Edge-productbijhoudt functieaanvragen bij via de [gebruikersstempagina van](https://feedback.azure.com/forums/907045-azure-iot-edge)het product.

## <a name="container-engines"></a>Containermotoren

Azure IoT Edge-modules worden geïmplementeerd als containers, dus IoT Edge heeft een containerengine nodig om ze te lanceren. Microsoft biedt een container engine, moby-engine, om aan deze eis te voldoen. Deze containermotor is gebaseerd op het Moby open-source project. Docker CE en Docker EE zijn andere populaire containermotoren. Ze zijn ook gebaseerd op het Open Source-project van Moby en zijn compatibel met Azure IoT Edge. Microsoft biedt de beste inspanning ondersteuning voor systemen die gebruik maken van deze container engines; Microsoft kan echter geen oplossingen verzenden voor problemen in deze problemen. Om deze reden raadt Microsoft aan om moby-engine te gebruiken op productiesystemen.

<br>
<center>

![De Moby-motor als containerruntime](./media/support/only-moby-for-production.png)
</center>

## <a name="operating-systems"></a>Besturingssystemen

Azure IoT Edge wordt uitgevoerd op de meeste besturingssystemen die containers kunnen uitvoeren; echter, niet al deze systemen worden gelijk ondersteund. Besturingssystemen zijn gegroepeerd in lagen die het niveau van ondersteuning vertegenwoordigen dat gebruikers kunnen verwachten.

* Tier 1-systemen worden ondersteund. For tier 1 systems, Microsoft:
  * heeft dit besturingssysteem in geautomatiseerde tests
  * biedt installatiepakketten voor hen
* Tier 2-systemen zijn compatibel met Azure IoT Edge en kunnen relatief eenvoudig worden gebruikt. Voor tier 2-systemen:
  * Microsoft heeft informele tests uitgevoerd op de platforms of weet van een partner die Azure IoT Edge succesvol uitvoert op het platform
  * Installatiepakketten voor andere platforms kunnen op deze platforms werken

De familie van het host-besturingssysteem moet altijd overeenkomen met de familie van het gastbesturingssysteem dat wordt gebruikt in de container van een module. Met andere woorden, u alleen Linux-containers gebruiken op Linux- en Windows-containers op Windows. Bij het gebruik van Windows worden alleen geïsoleerde containers ondersteund, niet hyper-V geïsoleerde containers.  

<br>
<center>

![Host OS komt overeen met gast-besturingssysteem](./media/support/edge-on-device.png)
</center>

### <a name="tier-1"></a>Tier 1

De systemen in de volgende tabel worden ondersteund door Microsoft, algemeen beschikbaar of in openbare preview, en worden getest met elke nieuwe release. 

| Besturingssysteem | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| Raspbian Stretch |  | ![Raspbian Stretch + ARM32v7](./media/tutorial-c-module/green-check.png) |  |
| [Ubuntu Server 16.04](https://wiki.ubuntu.com/XenialXerus/ReleaseNotes) | ![Ubuntu Server 16.04 + AMD64](./media/tutorial-c-module/green-check.png) |  | Openbare preview  |
| [Ubuntu Server 18.04](https://wiki.ubuntu.com/BionicBeaver/ReleaseNotes) | ![Ubuntu Server 18.04 + AMD64](./media/tutorial-c-module/green-check.png) |  | Openbare preview |
| [Windows 10 IoT Core](https://docs.microsoft.com/windows/iot-core/windows-iot-core), build 17763 | ![Windows IoT Core + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Windows 10 IoT Enterprise](https://docs.microsoft.com/windows/iot-core/windows-iot-enterprise), build 17763 | ![Windows 10 IoT Enterprise + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Windows Server 2019](https://docs.microsoft.com/windows-server/get-started-19/rel-notes-19), build 17763 | ![Windows Server 2019 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Windows Server IoT 2019](https://docs.microsoft.com/windows/iot-core/windows-server), build 17763 | ![Windows Server IoT 2019 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |

De hierboven genoemde Windows-besturingssystemen zijn de vereisten voor apparaten waarop Windows-containers op Windows worden uitgevoerd, wat de enige ondersteunde configuratie voor productie is. De Azure IoT Edge-installatiepakketten voor Windows staan het gebruik van Linux-containers op Windows toe; deze configuratie is echter alleen voor ontwikkeling en testen. Zie [IoT Edge op Windows gebruiken om Linux-containers uit te voeren voor](how-to-install-iot-edge-windows-with-linux.md)meer informatie.

### <a name="tier-2"></a>Tier 2

De systemen in de volgende tabel worden beschouwd als compatibel met Azure IoT Edge, maar worden niet actief getest of onderhouden door Microsoft.

| Besturingssysteem | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| [CentOS 7.5](https://wiki.centos.org/Manuals/ReleaseNotes/CentOS7.1804) | ![CentOS + AMD64](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM32v7](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 8](https://www.debian.org/releases/jessie/) | ![Debian 8 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 8 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 8 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 9](https://www.debian.org/releases/stretch/) | ![Debian 9 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 9 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 9 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 10](https://www.debian.org/releases/buster/) <sup>1</sup> | ![Debian 10 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 10 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 10 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Mentor Embedded Linux Flex OS](https://www.mentor.com/embedded-software/linux/mel-flex-os/) | ![Mentor Embedded Linux Flex OS + AMD64](./media/tutorial-c-module/green-check.png) | ![Mentor Embedded Linux Flex OS + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Mentor Embedded Linux Flex OS + ARM64](./media/tutorial-c-module/green-check.png) |
| [Mentor Embedded Linux Omni OS](https://www.mentor.com/embedded-software/linux/mel-omni-os/) | ![Mentor Embedded Linux Omni OS + AMD64](./media/tutorial-c-module/green-check.png) |  | ![Mentor Embedded Linux Omni OS + ARM64](./media/tutorial-c-module/green-check.png) |
| [RHEL 7.5](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/7.5_release_notes/index) | ![RHEL 7.5 + AMD64](./media/tutorial-c-module/green-check.png) | ![RHEL 7.5 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![RHEL 7,5 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 16.04](https://wiki.ubuntu.com/XenialXerus/ReleaseNotes) | ![Ubuntu 16.04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 16.04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 16.04 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 18.04](https://wiki.ubuntu.com/BionicBeaver/ReleaseNotes) | ![Ubuntu 18.04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18.04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18.04 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Wind River 8](https://docs.windriver.com/category/os-wind_river_linux) | ![Wind River 8 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Yocto](https://www.yoctoproject.org/) | ![Yocto + AMD64](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM64](./media/tutorial-c-module/green-check.png) |
| Raspbian Buster <sup>1</sup> |  | ![Raspbian Buster + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Raspbian Buster + ARM64](./media/tutorial-c-module/green-check.png) |

<sup>1</sup> Debian 10-systemen, waaronder Raspian Buster, gebruiken een versie van OpenSSL die IoT Edge niet ondersteunt. Gebruik de volgende opdracht om een eerdere versie te installeren voordat u IoT Edge installeert:

```bash
sudo apt-get install libssl1.0.2
```

## <a name="releases"></a>Releases

IoT Edge release assets en release notes zijn beschikbaar op de [azure-iotedge releases](https://github.com/Azure/azure-iotedge/releases) pagina. Deze sectie geeft informatie weer uit deze releasenotes om u te helpen de onderdelen van elke versie gemakkelijker te visualiseren.

IoT Edge-componenten kunnen afzonderlijk worden geïnstalleerd of bijgewerkt en zijn achterwaarts compatibel met componenten van oudere versies. In de volgende tabel worden de onderdelen vermeld die in elke release zijn opgenomen:

| Release   | Beveiliging daemon  | Randhub<br>Randagent | Libiothsm Libiothsm | Moby  |
| --------- | ---------------- | ---------------------- | --------- | ----- |
| **1.0.9** | 1.0.9            | 1.0.9                  | 1.0.9     |       |
| **1.0.8** | 1.0.8            | 1.0.8.5<br>1.0.8.4<br>1.0.8.3<br>1.0.8.2<br>1.0.8.1<br>1.0.8 | 1.0.8 | 3.0.6 |
| **1.0.7** | 1.0.7.1<br>1.0.7 | 1.0.7.1<br>1.0.7       | 1.0.7.1<br>1.0.7 | 3.0.5<br>3.0.4 (ARMv7hl, CentOS) |
| **1.0.6** | 1.0.6.1<br>1.0.6 | 1.0.6.1<br>1.0.6       | 1.0.6.1<br>1.0.6 |  |
| **1.0.5** | 1.0.5            | 1.0.5                  | 1.0.5     | 3.0.2 |

IoT Edge maakt gebruik van de Microsoft.Azure.Device.Client SDK. Zie de Azure [IoT C# SDK GitHub repo](https://github.com/Azure/azure-iot-sdk-csharp) of de [Azure SDK voor .NET-referentie-inhoud voor](https://docs.microsoft.com/dotnet/api/overview/azure/iot/client?view=azure-dotnet)meer informatie . In de volgende lijst wordt de versie van de client-SDK weergegeven waarop elke release is getest:

* **IoT Edge 1.0.9**: Client SDK 1.21.1
* **IoT Edge 1.0.8**: Client SDK 1.20.3
* **IoT Edge 1.0.7**: Client SDK 1.20.1
* **IoT Edge 1.0.6**: Client SDK 1.17.1
* **IoT Edge 1.0.5**: Client SDK 1.17.1

## <a name="virtual-machines"></a>Virtuele machines

Azure IoT Edge kan worden uitgevoerd in virtuele machines. Het gebruik van een virtuele machine als Een IoT Edge-apparaat is gebruikelijk wanneer klanten bestaande infrastructuur willen uitbreiden met edge intelligence. De familie van het vm-besturingssysteem van de host moet overeenkomen met de familie van het gastbesturingssysteem dat wordt gebruikt in de container van een module. Deze vereiste is hetzelfde als wanneer Azure IoT Edge rechtstreeks op een apparaat wordt uitgevoerd. Azure IoT Edge is agnost van de onderliggende virtualisatietechnologie en werkt in VM's die worden aangedreven door platforms zoals Hyper-V en vSphere.

<br>
<center>

![Azure IoT Edge in een VM](./media/support/edge-on-vm.png)
</center>

## <a name="minimum-system-requirements"></a>Minimale systeemvereisten

Azure IoT Edge draait geweldig op apparaten zo klein als een Raspberry Pi3 voor serverkwaliteit hardware. Het kiezen van de juiste hardware voor uw scenario is afhankelijk van de workloads die u wilt uitvoeren. Het maken van de uiteindelijke apparaatbeslissing kan ingewikkeld zijn; U echter eenvoudig beginnen met het prototypen van een oplossing op traditionele laptops of desktops.

Ervaring tijdens prototyping zal helpen bij het begeleiden van uw definitieve apparaatselectie. Vragen die u moet overwegen zijn onder meer:

* Hoeveel modules zitten er in uw werklast?
* Hoeveel lagen delen de containers van uw modules?
* In welke taal zijn uw modules geschreven?
* Hoeveel gegevens worden uw modules verwerkt?
* Hebben uw modules gespecialiseerde hardware nodig om hun workloads te versnellen?
* Wat zijn de gewenste prestatiekenmerken van uw oplossing?
* Wat is uw hardwarebudget?
