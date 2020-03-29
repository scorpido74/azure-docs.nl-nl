---
title: Azure IoT Edge-ontwikkelomgeving | Microsoft Documenten
description: Meer informatie over de ondersteunde systemen en ontwikkeltools van de eerste partij waarmee u IoT Edge-modules maken
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/04/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 96078be20e8048e481a994fefc169e48ab1d8459
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76511090"
---
# <a name="prepare-your-development-and-test-environment-for-iot-edge"></a>Bereid uw ontwikkel- en testomgeving voor op IoT Edge

Azure IoT Edge verplaatst uw bestaande bedrijfslogica naar apparaten die aan de rand werken. Om uw toepassingen en workloads voor te bereiden op het uitvoeren als [IoT Edge-modules,](iot-edge-modules.md)moet u ze bouwen als containers. In dit artikel vindt u richtlijnen voor het configureren van uw ontwikkelomgeving, zodat u met succes een IoT Edge-oplossing maken. Zodra u uw ontwikkelomgeving hebt ingesteld, u leren hoe [u uw eigen IoT Edge-modules ontwikkelen.](module-development.md)

In elke IoT Edge-oplossing zijn er ten minste twee machines om rekening mee te houden. Een daarvan is het IoT Edge-apparaat (of apparaten) zelf, dat de IoT Edge-module uitvoert. De andere is de ontwikkelmachine die u gebruikt om modules te bouwen, te testen en te implementeren. Dit artikel richt zich voornamelijk op de ontwikkelingsmachine. Voor testdoeleinden kunnen de twee machines hetzelfde zijn. U IoT Edge op uw ontwikkelingsmachine uitvoeren en er modules op implementeren.

## <a name="operating-system"></a>Besturingssysteem

Azure IoT Edge draait op een specifieke set [ondersteunde besturingssystemen.](support.md) Voor het ontwikkelen voor IoT Edge u de meeste besturingssystemen gebruiken die een containermotor kunnen draaien. De containermotor is een vereiste op de ontwikkelingsmachine om uw modules als containers te bouwen en hen aan een containerregister te duwen.

Als uw ontwikkelmachine Azure IoT Edge niet kan uitvoeren, gaat u in dit artikel verder met [testtools](#testing-tools) waarmee u lokaal testen en debuggen.

Het besturingssysteem van uw ontwikkelmachine hoeft niet overeen te komen met het besturingssysteem van uw IoT Edge-apparaat. Het besturingssysteem voor containers moet echter consistent zijn tussen de ontwikkelingsmachine en het IoT Edge-apparaat. U bijvoorbeeld modules op een Windows-machine ontwikkelen en deze implementeren op een Linux-apparaat. De Windows-machine moet Linux-containers uitvoeren om de modules voor het Linux-apparaat te bouwen.

## <a name="container-engine"></a>Containermotor

Het centrale concept van IoT Edge is dat u uw bedrijfs- en cloudlogica op afstand implementeren op apparaten door het in containers te verpakken. Om containers te bouwen, heb je een containermotor op je ontwikkelingsmachine nodig.

De enige ondersteunde containerengine voor IoT Edge-apparaten in productie is Moby. Elke containermotor die compatibel is met het Open Container Initiative, zoals Docker, is echter in staat om IoT Edge-moduleafbeeldingen te bouwen.

## <a name="development-tools"></a>Ontwikkelhulpprogramma’s

Zowel Visual Studio als Visual Studio Code hebben add-on extensies om IoT Edge-oplossingen te helpen ontwikkelen. Deze extensies bieden taalspecifieke sjablonen om nieuwe IoT Edge-scenario's te maken en te implementeren. Met de Azure IoT Edge-extensies voor Visual Studio en Visual Studio Code u uw IoT Edge-oplossingen coderen, bouwen, implementeren en debuggen. U een volledige IoT Edge-oplossing maken die meerdere modules bevat en de extensies werken automatisch een implementatiemanifestsjabloon bij met elke nieuwe moduletoevoeging. Met de extensies u uw IoT-apparaten ook beheren vanuit Visual Studio of Visual Studio Code. Implementeer modules naar een apparaat, controleer de status en bekijk berichten wanneer ze aankomen bij IoT Hub. Beide extensies maken gebruik van de [IoT EdgeHub-dev-tool](#iot-edgehub-dev-tool) om ook lokale het uitvoeren en foutopsporing van modules op uw ontwikkelingsmachine mogelijk te maken.

Als u liever ontwikkelt met andere editors of vanuit de CLI, biedt het Azure IoT Edge-dev-hulpprogramma opdrachten zodat u ontwikkelen en testen vanaf de opdrachtregel. U nieuwe IoT Edge-scenario's maken, moduleafbeeldingen maken, modules uitvoeren in een simulator en berichten controleren die naar IoT Hub worden verzonden.

### <a name="visual-studio-code-extension"></a>Visual Studio Code-extensie

De Azure IoT Edge-extensie voor Visual Studio Code biedt IoT Edge-modulesjablonen die zijn gebouwd op programmeertalen, waaronder C, C#, Java, Node.js en Python, evenals Azure-functies in C#.

Zie [Azure IoT-hulpprogramma's voor Visual Studio-code voor](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)meer informatie en om te downloaden.

Naast de IoT Edge-extensies u het nuttig vinden om extra extensies te installeren voor het ontwikkelen. U bijvoorbeeld [Docker Support voor Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) gebruiken om uw afbeeldingen, containers en registers te beheren. Bovendien hebben alle belangrijke ondersteunde talen extensies voor Visual Studio Code die u kunnen helpen bij het ontwikkelen van modules.

#### <a name="prerequisites"></a>Vereisten

De modulesjablonen voor sommige talen en services hebben vereisten die nodig zijn om de projectmappen op uw ontwikkelmachine te bouwen met Visual Studio Code.

| Modulesjabloon | Vereiste |
| --------------- | ------------ |
| Azure Functions | [.NET Core 2.1 SDK](https://www.microsoft.com/net/download) |
| C | [Git](https://git-scm.com/) |
| C# | [.NET Core 2.1 SDK](https://www.microsoft.com/net/download) |
| Java | <ul><li>[Java SE Development Kit 10](https://aka.ms/azure-jdks) <li> [De JAVA_HOME-omgevingsvariabele instellen](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/) <li> [Maven](https://maven.apache.org/)</ul> |
| Node.js | <ul><li>[Node.js](https://nodejs.org/) <li> [Yeoman](https://www.npmjs.com/package/yo) <li> [Azure IoT Edge Node.js-modulegenerator](https://www.npmjs.com/package/generator-azure-iot-edge-module)</ul> |
| Python |<ul><li> [Python](https://www.python.org/downloads/) <li> [Pip](https://pip.pypa.io/en/stable/installing/#installation) <li> [Git](https://git-scm.com/) </ul> |

### <a name="visual-studio-20172019-extension"></a>Visual Studio 2017/2019 uitbreiding

De Azure IoT Edge-hulpprogramma's voor Visual Studio bieden een IoT Edge-modulesjabloon die is gebouwd op C# en C.

Zie [Azure IoT Edge Tools voor Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) of [Azure IoT Edge Tools voor Visual Studio 2019 voor](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools)meer informatie en om te downloaden.

### <a name="iot-edge-dev-tool"></a>IoT Edge-dev-gereedschap

Het Azure IoT Edge-dev-hulpprogramma vereenvoudigt de ontwikkeling van IoT Edge met opdrachtregelmogelijkheden. Deze tool biedt CLI-opdrachten om modules te ontwikkelen, debuggen en te testen. De IoT Edge-dev-tool werkt met uw ontwikkelingssysteem, of u nu handmatig de afhankelijkheden van uw machine hebt geïnstalleerd of de IoT Edge-dev-container gebruikt.

Zie [IoT Edge dev tool wiki](https://github.com/Azure/iotedgedev/wiki)voor meer informatie en om aan de slag te gaan.

## <a name="testing-tools"></a>Testhulpmiddelen

Er bestaan verschillende testtools om IoT Edge-apparaten of foutopsporingsmodules efficiënter te simuleren. In de volgende tabel ziet u een vergelijking op hoog niveau tussen de gereedschappen en afzonderlijke secties om elk gereedschap specifieker.

Alleen de IoT Edge-runtime wordt ondersteund voor productie-implementaties, maar met de volgende tools u IoT Edge-apparaten simuleren of eenvoudig maken voor ontwikkelings- en testdoeleinden. Deze tools sluiten elkaar niet uit, maar kunnen samenwerken voor een complete ontwikkelervaring.

| Hulpprogramma | Ook wel bekend als | Ondersteunde platforms | Ideaal voor |
| ---- | ------------- | ------------------- | --------- |
| IoT EdgeHub-dev-gereedschap  | iotedgehubdev | Windows, Linux, MacOS | Een apparaat simuleren om modules te debuggen. |
| IoT Edge-dev-container | microsoft/iotedgedev | Windows, Linux, MacOS | Ontwikkelen zonder afhankelijkheden te installeren. |
| IoT Edge-runtime in een container | iotedgec | Windows, Linux, MacOS, ARM | Testen op een apparaat dat de runtime mogelijk niet ondersteunt. |
| IoT Edge-apparaatcontainer | toolboc/azure-iot-edge-device-container | Windows, Linux, MacOS, ARM | Een scenario testen met veel IoT Edge-apparaten op schaal. |

### <a name="iot-edgehub-dev-tool"></a>IoT EdgeHub-dev-gereedschap

De Azure IoT EdgeHub-dev-tool biedt een lokale ontwikkel- en foutopsporingservaring. De tool helpt IoT Edge-modules te starten zonder de IoT Edge-runtime, zodat u IoT Edge-modules en -oplossingen lokaal maken, ontwikkelen, testen, uitvoeren en debuggen. U hoeft geen afbeeldingen naar een containerregister te pushen en deze te implementeren op een apparaat om te testen.

De IoT EdgeHub-dev-tool is ontworpen om samen te werken met de Visual Studio- en Visual Studio Code-extensies, evenals met de IoT Edge-dev-tool. Het ondersteunt de ontwikkeling van de binnenlus en het testen van de buitenste lus, dus integreert ook met de DevOps-tools.

Zie [Azure IoT EdgeHub dev tool](https://pypi.org/project/iotedgehubdev/)voor meer informatie en om te installeren.

### <a name="iot-edge-dev-container"></a>IoT Edge-dev-container

De Azure IoT Edge-dev-container is een Docker-container met alle afhankelijkheden die u nodig hebt voor de ontwikkeling van IoT Edge. Deze container maakt het gemakkelijk om aan de slag te gaan met welke taal je ook wilt ontwikkelen, waaronder C#, Python, Node.js en Java. Het enige wat u hoeft te installeren is een containermotor, zoals Docker of Moby, om de container naar uw ontwikkelingsmachine te trekken.

Zie [Azure IoT Edge dev-container](https://hub.docker.com/r/microsoft/iotedgedev/)voor meer informatie.

### <a name="iot-edge-runtime-in-a-container"></a>IoT Edge-runtime in een container

De IoT Edge-runtime in een container biedt een volledige runtime die de verbindingstekenreeks van uw apparaat als een omgevingsvariabele neemt. Met deze container u IoT Edge-modules en -scenario's testen op een systeem dat de runtime mogelijk niet native ondersteunt, zoals MacOS. Alle modules die u implementeert, worden buiten de runtimecontainer gestart. Als u wilt dat de runtime en de geïmplementeerde modules binnen dezelfde container bestaan, u in plaats daarvan de IoT Edge-apparaatcontainer overwegen.

Zie [Azure IoT Edge in een container uitvoeren](https://github.com/Azure/iotedgedev/tree/master/docker/runtime)voor meer informatie.

### <a name="iot-edge-device-container"></a>IoT Edge-apparaatcontainer

De IoT Edge-apparaatcontainer is een compleet IoT Edge-apparaat, klaar om te worden gelanceerd op elke machine met een containermotor. De apparaatcontainer bevat de IoT Edge-runtime en een containermotor zelf. Elk exemplaar van de container is een volledig functioneel iot edge-apparaat dat zichzelf indient. De apparaatcontainer ondersteunt het op afstand opsporen van modules, zolang er een netwerkroute naar de module is. De apparaatcontainer is goed voor het snel maken van grote aantallen IoT Edge-apparaten om scenario's op schaal of Azure Pipelines te testen. Het ondersteunt ook implementatie naar kubernetes via het roer.

Zie [Azure IoT Edge-apparaatcontainer](https://github.com/toolboc/azure-iot-edge-device-container)voor meer informatie .

## <a name="devops-tools"></a>DevOps-gereedschappen

Wanneer u klaar bent om op schaal oplossingen te ontwikkelen voor uitgebreide productiescenario's, profiteerdan van moderne DevOps-principes, waaronder automatisering, monitoring en gestroomlijnde softwareengineeringprocessen. IoT Edge heeft extensies voor ondersteuning van DevOps-hulpprogramma's, waaronder Azure DevOps, Azure DevOps Projects en Jenkins. Als u een bestaande pijplijn wilt aanpassen of een ander DevOps-hulpprogramma wilt gebruiken, zoals CircleCI of TravisCI, u dit doen met de CLI-functies die zijn opgenomen in het dev-gereedschap IoT Edge.

Zie de volgende pagina's voor meer informatie, richtlijnen en voorbeelden:

* [Continue integratie en continue implementatie naar Azure IoT Edge](how-to-ci-cd.md)
* [Een CI/CD-pijplijn maken voor IoT Edge met Azure DevOps-projecten](how-to-devops-project.md)
* [Azure IoT Edge Jenkins-plug-in](https://plugins.jenkins.io/azure-iot-edge)
* [IoT Edge DevOps GitHub repo](https://github.com/toolboc/IoTEdge-DevOps)
